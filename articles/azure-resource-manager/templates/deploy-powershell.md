---
title: 使用 PowerShell 和模板部署资源
description: 使用 Azure Resource Manager 和 Azure PowerShell 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
ms.topic: conceptual
origin.date: 09/08/2020
author: rockboyfor
ms.date: 11/23/2020
ms.testscope: yes
ms.testdate: 08/24/2020
ms.author: v-yeche
ms.openlocfilehash: c87cbace6bb632e1bf2213e69207f3298e080f64
ms.sourcegitcommit: c2c9dc65b886542d220ae17afcb1d1ab0a941932
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978164"
---
<!--Verify Successfully-->
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>使用 ARM 模板和 Azure PowerShell 部署资源

本文介绍如何配合使用 Azure PowerShell 与 Azure 资源管理器模板（ARM 模板），以将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅[模版部署概述](overview.md)。

## <a name="prerequisites"></a>先决条件

你需要使用模板进行部署。 如果还没有模板，请从 Azure 快速入门模板存储库下载并保存一个[示例模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)。 本文中使用的本地文件名称为 **c:\MyTemplates\azuredeploy.json**。

需安装 Azure PowerShell 并连接到 Azure：

- **在本地计算机上安装 Azure PowerShell cmdlet。** 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。
- **使用 [Connect-AZAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) 连接到 Azure**。 如果有多个 Azure 订阅，则可能还需要运行 [Set-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Set-AzContext)。 有关详细信息，请参阅[使用多个 Azure 订阅](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps)。

<!--Not Available on [Deploy ARM templates from local Shell](deploy-cloud-shell.md)-->

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为资源组、订阅、管理组或租户。 根据部署范围使用不同的命令。

* 若要部署到 **资源组**，请使用 [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment)：

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
    ```

* 若要部署到订阅，请使用 New-AzSubscriptionDeployment：

    ```azurepowershell
    New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
    ```

    有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

* 若要部署到管理组，请使用 [New-AzManagementGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/New-AzManagementGroupDeployment)。

    ```azurepowershell
    New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
    ```

    有关管理组级部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

* 若要部署到租户，请使用 [New-AzTenantDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-aztenantdeployment)。

    ```azurepowershell
    New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
    ```

    有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

对于每一个范围，部署模板的用户必须具有创建资源所必需的权限。

<a name="deploy-a-template-from-your-local-machine"></a>
## <a name="deploy-local-template"></a>部署本地模板

可以部署本地计算机中的模板，也可以部署存储在外部的模板。 本节介绍如何部署本地模板。

如果要部署到不存在的资源组，请创建该资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 名称不能以句点结尾。

```azurepowershell
Connect-AZAccount -Environment AzureChinaCloud
New-AzResourceGroup -Name ExampleGroup -Location "China North"
```

若要部署本地模板，请在部署命令中使用 `-TemplateFile` 参数。 下面的示例还显示了如何设置来自该模板的参数值。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

部署可能需要几分钟才能完成。

## <a name="deploy-remote-template"></a>部署远程模板

你可能更愿意将 ARM 模板存储在外部位置，而不是存储在本地计算机上。 可以将模板存储在源控件存储库（例如 GitHub）中。 另外，还可以将其存储在 Azure 存储帐户中，以便在组织中共享访问。

如果要部署到不存在的资源组，请创建该资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 名称不能以句点结尾。

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "China North"
```

若要部署外部模板，请使用 `-TemplateUri` 参数。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。

<!--Not Available on  However, if you want to manage access to the template, consider using template specs-->
<!--Not Available on  [template specs](#deploy-template-spec)-->


## <a name="deployment-name"></a>部署名称

部署 ARM 模板时，可以为部署指定名称。 此名称可以帮助你从部署历史记录中检索该部署。 如果没有为部署提供名称，将使用模板文件的名称。 例如，如果部署一个名为 `azuredeploy.json` 的模板，但未指定部署名称，则该部署将命名为 `azuredeploy`。

每次运行部署时，一个包含部署名称的条目会添加到资源组的部署历史记录中。 如果运行另一个部署并为其指定了相同的名称，则会将先前的条目替换为当前部署。 如果要在部署历史记录中保持唯一条目，请为每个部署指定唯一名称。

若要创建唯一名称，你可以分配一个随机数。

```powershell
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

或者，添加日期值。

```powershell
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

如果使用相同的部署名称对同一资源组运行并发部署，则仅会完成最后一个部署。 尚未完成的具有相同名称的任何部署都将被最后一个部署所替换。 例如，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将仅部署一个存储帐户。 生成的存储帐户名为 `storage2`。

但是，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；在该部署完成时你又立即运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将有两个存储帐户。 一个名为 `storage1`，另一个名为 `storage2`。 但是，部署历史记录中只有一个条目。

为每个部署指定唯一的名称时，可以并发运行它们而不会发生冲突。 如果你运行一个名为 `newStorage1` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你又运行了另一个名为 `newStorage2` 的部署，它部署了一个名为 `storage2` 的存储帐户，则部署历史记录中将有两个存储帐户和两个条目。

为避免与并发部署冲突并确保部署历史记录中的条目是唯一的，请为每个部署指定唯一的名称。


<!--Not Available on ## Deploy template spec-->
<!--REASON: TEMPLATE SPEC IS PRIVATE PREVIEW-->
## <a name="preview-changes"></a>预览更改

在部署模板之前，可以预览模板将对环境做出的更改。 使用[假设操作](template-deploy-what-if.md)验证模板是否进行了预期的更改。 模拟操作还验证模板是否有错误。

<!--Not Available on ## Deploy from Azure Cloud shell-->

## <a name="pass-parameter-values"></a>粘贴参数值

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

若要传递内联参数，请使用 `New-AzResourceGroupDeployment` 命令提供参数的名称。 例如，若要将字符串和数组传递给模板，请使用：

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

还可以获取文件的内容并将该内容作为内联参数提供。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

如果需要传入对象数组，请在 PowerShell 中创建哈希表并将其添加到数组中。 在部署过程中将该数组作为参数传递。

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>参数文件

你可能会发现，与在脚本中以内联值的形式传递参数相比，使用包含参数值的 JSON 文件更为容易。 参数文件可以是本地文件，也可以是具有可访问 URI 的外部文件。

有关参数文件的详细信息，请参阅[创建资源管理器参数文件](parameter-files.md)。

若要传递本地参数文件，请使用 TemplateParameterFile 参数：

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要传递外部参数文件，请使用 TemplateParameterUri 参数：

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>后续步骤

- 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](rollback-on-error.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。

<!-- Update_Description: update meta properties, wording update, update link -->
