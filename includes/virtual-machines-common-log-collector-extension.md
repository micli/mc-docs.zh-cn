---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 05/20/2019
ms.author: v-yeche
ms.openlocfilehash: dbe630514a8cfcb1e83ba152c0e5a3abe1fe043a
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "66039220"
---
若要诊断 Azure 云服务的问题，需要在问题发生时收集虚拟机上该服务的日志文件。 可以使用 AzureLogCollector 扩展按需从一个或多个云服务 VM（通过 Web 角色和辅助角色）执行一次性日志收集，并将收集到的文件传输到 Azure 存储帐户 - 所有这些操作都无需远程登录到任何 VM。

> [!NOTE]
> 有关大多数记录的信息的说明，请参阅 http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp 。
> 
> 

有两种收集模式，其使用取决于要收集的文件的类型。

* **仅 Azure 来宾代理日志 (GA)** 。 此收集模式包括与 Azure 来宾代理以及其他 Azure 组件相关的所有日志。
* **所有日志（完整）** 。 此收集模式会收集 GA 模式下的所有文件以及：

    * 系统和应用程序事件日志
    * HTTP 错误日志
    * IIS 日志
    * 安装程序日志
    * 其他系统日志

在两种收集模式下，均可使用以下结构的集合来指定额外的数据收集文件夹：

* **Name**：集合的名称，用作已收集文件所在的 zip 文件中子文件夹的名称。
* **Location**：要收集文件所在虚拟机上的文件夹路径。
* **SearchPattern**：要收集的文件名的样式。 默认值为“\*”
* **Recursive**：如果要收集的文件以递归方式列于指定位置下。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* 有一个用于扩展的存储帐户，保存生成的 zip 文件。
* Azure PowerShell。 有关安装说明，请参阅[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

## <a name="add-the-extension"></a>添加扩展
可以使用 [Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) cmdlet 或[服务管理 REST API](https://msdn.microsoft.com/library/ee460799.aspx) 添加 AzureLogCollector 扩展。

对于云服务，可以使用现有的 Azure Powershell cmdlet **Set-AzureServiceExtension** 启用云服务角色实例上的扩展。 每次通过此 cmdlet 启用此扩展时，都会在所选角色的所选角色实例上触发日志收集。

对于虚拟机，可以使用现有的 Azure Powershell cmdlet **Set-AzureVMExtension** 启用虚拟机上的扩展。 每次通过 cmdlet 启用此扩展时，都会在每个实例上触发日志收集。

在内部，此扩展使用基于 JSON 的 PublicConfiguration 和 PrivateConfiguration。 下面是公共和私有配置的示例 JSON 的布局。

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> 此扩展不需要 **privateConfiguration**。 可以只为 **–PrivateConfiguration** 参数提供一个空的结构。
> 
> 

可以遵循以下两个步骤之一，将 AzureLogCollector 添加到所选角色的云服务或虚拟机的一个或多个实例，以便在每个 VM 上触发收集操作，从而运行收集的文件并将其发送给指定的 Azure 帐户。

## <a name="adding-as-a-service-extension"></a>作为服务扩展添加
1. 按照说明将 Azure PowerShell 连接到订阅。
2. 指定服务名、槽、角色和角色实例，以便向其添加 AzureLogCollector 扩展并启用该扩展。

    ```powershell
    #Specify your cloud service name
    $ServiceName = 'extensiontest2'

    #Specify the slot. 'Production' or 'Staging'
    $slot = 'Production'

    #Specified the roles on which the extension will be installed and enabled
    $roles = @("WorkerRole1","WebRole1")

    #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
    $instances = @("*")

    #Specify the collection mode, "Full" or "GA"
    $mode = "GA"
    ```

3. 指定更多需要为其收集文件的数据文件夹（此步骤为可选）。

    ```powershell
    #add one location
    $a1 = New-Object PSObject

    $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
    $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
    $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
    $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

    $AdditionalDataList+= $a1
    #more locations can be added....
    ```

    > [!NOTE]
    > 可以使用令牌 `%roleroot%` 指定角色根驱动器，因为该角色不使用固定驱动器。
    > 
    > 
4. 提供要向其上传所收集文件的 Azure 存储帐户名称和密钥。

    ```powershell
    $StorageAccountName = 'YourStorageAccountName'
    $StorageAccountKey  = 'YourStorageAccountKey'
    ```

5. 按如下所示调用 SetAzureServiceLogCollector.ps1（本文末尾提供），以便为云服务启用 AzureLogCollector 扩展。 执行完以后，可以在 `https://YourStorageAccountName.blob.core.chinacloudapi.cn/vmlogs` 下找到上传的文件

    ```powershell
    .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances -Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
    ```

下面是传递给脚本的参数的定义。 （也在下面复制。）

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**：云服务名称。
* **Roles**：角色列表，例如“WebRole1”或“WorkerRole1”。
* **Instances**：逗号分隔的角色实例名称列表 — 使用通配符字符串（“*”）代表所有角色实例。
* **Slot**：槽名称。 “生产”或“过渡”。
* **Mode**：收集模式。 “完整”或“GA”。
* **StorageAccountName**：用于存储所收集数据的 Azure 存储帐户的名称。
* **StorageAccountKey**：Azure 存储帐户密钥的名称。
* **AdditionalDataLocationList**：以下结构的列表：

    ```powershell
    {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
    }
    ```

## <a name="adding-as-a-vm-extension"></a>作为 VM 扩展添加
按照说明将 Azure PowerShell 连接到订阅。

1. 指定服务名称、VM 和收集模式。

    ```powershell
    #Specify your cloud service name
    $ServiceName = 'YourCloudServiceName'

    #Specify the VM name
    $VMName = "'YourVMName'"

    #Specify the collection mode, "Full" or "GA"
    $mode = "GA"

    Specify the additional data folder for which files will be collected (this step is optional).

    #add one location
    $a1 = New-Object PSObject

    $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
    $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
    $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
    $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

    $AdditionalDataList+= $a1
        #more locations can be added....
    ```

2. 提供要向其上传所收集文件的 Azure 存储帐户名称和密钥。

    ```powershell
    $StorageAccountName = 'YourStorageAccountName'
    $StorageAccountKey  = 'YourStorageAccountKey'
    ```

3. 按如下所示调用 SetAzureVMLogCollector.ps1（本文末尾提供），以便为云服务启用 AzureLogCollector 扩展。 执行完以后，可以在 `https://YourStorageAccountName.blob.core.chinacloudapi.cn/vmlogs` 下找到上传的文件

下面是传递给脚本的参数的定义。 （也在下面复制。）

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**：云服务名称。
* **VMName**：VM 的名称。
* **Mode**：收集模式。 “完整”或“GA”。
* **StorageAccountName**：用于存储所收集数据的 Azure 存储帐户的名称。
* **StorageAccountKey**：Azure 存储帐户密钥的名称。
* **AdditionalDataLocationList**：以下结构的列表：

    ```
    {
      String Name,
      String Location,
      String SearchPattern,
      Bool   Recursive
    }
    ```

## <a name="extention-powershell-script-files"></a>扩展 PowerShell 脚本文件
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Environment AzureChinaCloud -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Environment AzureChinaCloud -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>后续步骤
现在，可以从一个简单的位置查看或复制日志。

<!--Update_Description: wording update, update meta properties-->