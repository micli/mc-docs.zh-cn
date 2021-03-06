---
title: 适用于 Windows 的 Azure 自定义脚本扩展
description: 使用自定义脚本扩展自动执行 Windows VM 配置任务
services: virtual-machines-windows
manager: carmonm
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
origin.date: 08/31/2020
author: rockboyfor
ms.date: 11/02/2020
ms.testscope: yes
ms.testdate: 08/31/2020
ms.author: v-yeche
ms.openlocfilehash: 47379d2cb2edde8187704b932f5c8e25013ae205
ms.sourcegitcommit: 93309cd649b17b3312b3b52cd9ad1de6f3542beb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103794"
---
# <a name="custom-script-extension-for-windows"></a>适用于 Windows 的自定义脚本扩展

自定义脚本扩展在 Azure 虚拟机上下载并执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置或管理任务。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时会脚本提供给 Azure 门户。 自定义脚本扩展与 Azure 资源管理器模板集成，并且可以通过 Azure CLI、PowerShell、Azure 门户或 Azure 虚拟机 REST API 来运行。

本文档详细说明如何通过 Azure PowerShell 模块、Azure 资源管理器模板使用自定义脚本扩展，同时详细说明 Windows 系统上的故障排除步骤。

## <a name="prerequisites"></a>先决条件

> [!NOTE]  
> 不要在将同一 VM 作为参数的情况下使用自定义脚本扩展运行 Update-AzVM，因为它将等待它本身响应。  

### <a name="operating-system"></a>操作系统

适用于 Windows 的自定义脚本扩展将在扩展支持的扩展 OS 上运行；
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>脚本位置

可以将扩展配置为使用 Azure Blob 存储凭据来访问 Azure Blob 存储。 脚本位置可以是任何位置，只要 VM 可以路由到该终结点（例如 GitHub 或内部文件服务器）即可。

### <a name="internet-connectivity"></a>Internet 连接

如果需要从外部（例如 GitHub 或 Azure 存储）下载脚本，则需要打开其他防火墙和网络安全组端口。 例如，如果脚本位于 Azure 存储中，可以使用 Azure NSG 服务标记对[存储](../../virtual-network/network-security-groups-overview.md#service-tags)进行访问。

如果脚本位于本地服务器上，则可能仍需要打开其他防火墙和网络安全组端口。

### <a name="tips-and-tricks"></a>提示和技巧

* 脚本中的语法错误会导致此扩展失败率最高，应测试脚本运行正确无误，同时在脚本中添加其他日志记录，以便更轻松地找到失败位置。
* 编写幂等脚本。 这样可以确保即使它们再次意外运行，也不会引起系统更改。
* 确保这些脚本在运行时不需要用户输入。
* 脚本可以运行 90 分钟，若运行时间超过 90 分钟，将导致扩展的预配失败。
* 不要重启置于脚本内，此操作会导致所安装的其他扩展出现问题。 扩展不会在重启之后继续。
* 如果你有可导致重启的脚本，则安装应用程序并运行该脚本，可使用 Windows 计划任务或 DSC、Chef 或 Puppet 扩展等工具来计划重启。
* 建议不要运行会导致 VM 代理停止或更新的脚本。 这会使扩展处于“正在转换”状态，从而导致超时。
* 扩展将只运行脚本一次，如果想要在每次启动时运行脚本，则需要使用扩展创建 Windows 计划任务。
* 如果想要计划脚本何时运行，应使用扩展创建 Windows 计划任务。
* 脚本运行时，Azure 门户或 CLI 中只会显示“正在转换”扩展状态。 如果希望更频繁地更新正在运行的脚本的状态，需要创建自己的解决方案。
* 自定义脚本扩展本身不支持代理服务器，但可以在脚本中使用支持代理服务器的文件传输工具，如 Curl
* 请注意脚本或命令可能依赖的非默认目录位置，按逻辑对这种情况进行处理。
* 自定义脚本扩展将在 LocalSystem 帐户下运行
* 如果你计划使用 storageAccountName 和 storageAccountKey 属性，这些属性必须并置在 protectedSettings 中  。

## <a name="extension-schema"></a>扩展架构

自定义脚本扩展配置指定脚本位置和要运行命令等设置。 可将此配置存储在配置文件中、在命令行中指定，或者在 Azure 资源管理器模板中指定该配置。

可将敏感数据存储在受保护的配置中，此配置经过加密，只能在虚拟机内部解密。 当执行命令包含机密（例如密码）时，受保护的配置相当有用。

这些项目应视为敏感数据，并在扩展的受保护设置配置中指定。 Azure VM 扩展的受保护设置数据已加密，并且只能在目标虚拟机上解密。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountEndPoint": "https://core.chinacloudapi.cn/",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> managedIdentity 属性 **不能** 与 storageAccountName 或 storageAccountKey 属性结合使用

> [!NOTE]
> 在某个时间点，一个 VM 上只能安装一个扩展版本，在同一资源管理器模板中为同一 VM 指定两次自定义脚本将会失败。

> [!NOTE]
> 可以在 VirtualMachine 资源内部使用此架构，也可将其用作独立的资源。 如果在 ARM 模板中将此扩展用作独立的资源，则该资源的名称必须采用“virtualMachineName/extensionName”格式。

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | 字符串 |
| type | CustomScriptExtension | 字符串 |
| typeHandlerVersion | 1.10 | int |
| fileUris（例如） | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp（示例） | 123456789 | 32-bit integer |
| commandToExecute（例如） | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | 字符串 |
| storageAccountName（例如） | examplestorageacct | 字符串 |
| storageAccountKey（例如） | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | 字符串 |
| managedIdentity（例如） | { } 或 { "clientId":"31b403aa-c364-4240-a7ff-d85fb6cd7232" } 或 { "objectId":"12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json 对象 |

>[!NOTE]
>这些属性名称区分大小写。 要避免部署问题，请使用如下所示的名称。

#### <a name="property-value-details"></a>属性值详细信息

* `commandToExecute`：（ **必需** ，字符串）要执行的入口点脚本。 如果命令包含机密（例如密码）或者 fileUris 敏感，请改用此字段。
* `fileUris`：（可选，字符串数组）要下载的文件的 URL。
* `timestamp`（可选，32 位整数）仅当需要更改此字段的值来触发脚本的重新运行时，才使用此字段。  任何整数值都是可以接受的，前提是必须不同于以前的值。
* `storageAccountName`：（可选，字符串）存储帐户的名称。 如果指定存储凭据，所有 `fileUris` 都必须是 Azure Blob 的 URL。
* `storageAccountKey`：（可选，字符串）存储帐户的访问密钥
* `managedIdentity`：（可选，json 对象）用于下载文件的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
    * `clientId`：（可选，字符串）托管标识的客户端 ID
    * `objectId`：（可选，字符串）托管标识的对象 ID

可以在公共设置或受保护设置中设置以下值，但扩展会拒绝任何同时在公共设置和受保护设置中设置以下值的配置。

* `commandToExecute`

虽然使用公共设置可能对调试很有用，但建议使用受保护设置。

公共设置会以明文形式发送到将执行脚本的 VM。  受保护设置使用只有 Azure 和 VM 知道的密钥进行加密。 这些设置会在发送时保存到 VM 中，也就是说，如果设置已加密，则会在 VM 上加密保存。 用于对已加密值解密的证书存储在 VM 上，该证书用于在运行时对设置解密（如必要）。

#### <a name="property-managedidentity"></a>属性：managedIdentity
> [!NOTE]
> 此属性只能在受保护的设置中指定。

CustomScript（版本 1.10 及更高版本）支持用于通过“fileUris”设置中提供的 URL 下载文件的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 它允许 CustomScript 访问 Azure 存储专用 Blob 或容器，而无需用户传递 SAS 令牌或存储帐户密钥等机密。

若要使用此功能，用户必须将[系统分配的](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)或[用户分配的](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)标识添加到需要运行 CustomScript 的 VM 或 VMSS，并[授予托管标识访问 Azure 存储容器或 Blob 的权限](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access)。

若要在目标 VM/VMSS 上使用系统分配的标识，请将“managedidentity”字段设置为空的 json 对象。 

> 示例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.chinacloudapi.cn/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

若要在目标 VM/VMSS 上使用用户分配的标识，请将“managedidentity”字段配置为托管标识的客户端 ID 或对象 ID。

> 示例：
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.chinacloudapi.cn/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.chinacloudapi.cn/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity 属性 **不能** 与 storageAccountName 或 storageAccountKey 属性结合使用

## <a name="template-deployment"></a>模板部署

可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可以在 Azure 资源管理器模板中使用上一部分中详细介绍的 JSON 架构，以便在部署过程中运行自定义脚本扩展。 以下示例显示如何使用自定义脚本扩展：

* [教程：使用 Azure 资源管理器模板部署虚拟机扩展](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [在 Windows 和 Azure SQL DB 上部署双层应用程序](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 `Set-AzVMCustomScriptExtension` 命令将自定义脚本扩展添加到现有虚拟机。 有关详细信息，请参阅 [Set-AzVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmcustomscriptextension)。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>其他示例

### <a name="using-multiple-scripts"></a>使用多个脚本

在本示例中，你有三个用于构建服务器的脚本。 commandToExecute 调用第一个脚本，然后可以选择调用其他脚本的方式。 例如，可以使用主脚本来控制执行，其中包含正确的错误处理、日志记录和状态管理。 将脚本下载到本地计算机以运行。 例如，在 `1_Add_Tools.ps1` 中，可以通过在脚本中添加 `.\2_Add_Features.ps1` 来调用 `2_Add_Features.ps1`，并对在 `$settings` 中定义的其他脚本重复此过程。

```powershell
$fileUri = @("https://xxxxxxx.blob.core.chinacloudapi.cn/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.chinacloudapi.cn/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.chinacloudapi.cn/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>从本地共享运行脚本

在本示例中，建议使用本地 SMB 服务器作为脚本位置。 这样就不需要提供任何其他设置，但 commandToExecute 除外。

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>如何使用 CLI 多次运行自定义脚本

如果想要多次运行自定义脚本扩展，则只能在以下条件下执行此操作：

* 扩展的“Name”参数与之前部署的扩展相同。
* 请更新配置，否则不会重新执行命令。 可以将动态属性添加到命令中，如时间戳。

或者，可以将 [ForceUpdateTag](https://docs.azure.cn/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) 属性设置为“true”。

### <a name="using-invoke-webrequest"></a>使用 Invoke-WebRequest

如果在脚本中使用 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)，则必须指定参数 `-UseBasicParsing`，否则你将在检查详细信息状态时收到以下错误：

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

要在规模集上部署自定义脚本扩展，请参阅 [Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>经典 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

要在经典 VM 上部署自定义脚本扩展，可以使用 Azure 门户或经典 Azure PowerShell cmdlet。

### <a name="azure-portal"></a>Azure 门户

导航到经典 VM 资源。 选择“设置”下面的“扩展” 。

单击“+ 添加”，并在资源列表中选择“自定义脚本扩展” 。

在“安装扩展”页上，选择本地 PowerShell 文件，然后填写任何参数并单击“确定” 。

### <a name="powershell"></a>PowerShell

可使用 [Set-AzureVMCustomScriptExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) cmdlet 将自定义脚本扩展添加到现有虚拟机。

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.chinacloudapi.cn/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure PowerShell 模块进行检索。 若要查看给定 VM 的扩展部署状态，请运行以下命令：

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

扩展输出将记录到可在目标虚拟机上的以下目录中找到的文件中。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定的文件将下载到目标虚拟机上的以下文件夹中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

其中，`<n>` 是一个十进制整数，可以在不同的扩展执行之间更改。  `1.*` 值与扩展的 `typeHandlerVersion` 的当前实际值匹配。  例如，实际目录可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

执行 `commandToExecute` 命令时，扩展会将该目录（例如 `...\Downloads\2`）设置为当前工作目录。 此过程使得可以通过 `fileURIs` 属性使用相对路径查找下载的文件。 请参阅下表中的示例。

绝对下载路径可能会随时间而变化，因此在可能情况下，最好是在 `commandToExecute` 字符串中选择使用相对的脚本/文件路径。 例如：

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

对于通过 `fileUris` 属性列表下载的文件，将保留第一个 URI 段之后的路径信息。  如下表所示，下载的文件映射到下载子目录中，以便反映 `fileUris` 值的结构。  

#### <a name="examples-of-downloaded-files"></a>下载的文件的示例

| fileUris 中的 URI | 相对下载位置 | 绝对下载位置 <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.chinacloudapi.cn/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.chinacloudapi.cn/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> 绝对目录路径可在 VM 的生存期内更改，但不可在 CustomScript 扩展的某次执行期间更改。

### <a name="support"></a>支持

如果对本文中的任何观点存在疑问，可以联系 [Azure 支持](https://support.azure.cn/support/contact/)上的 Azure 专家。 还可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://support.azure.cn/support/support-azure/)提交请求。 有关使用 Azure 支持的信息，请阅读 [Azure 支持常见问题](https://www.azure.cn/support/faq/)。

<!-- Update_Description: update meta properties, wording update, update link -->