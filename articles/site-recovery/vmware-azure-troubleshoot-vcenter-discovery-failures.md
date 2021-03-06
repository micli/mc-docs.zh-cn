---
title: 排查 Azure Site Recovery 中的 VMware vCenter 发现失败问题
description: 本文介绍如何排查 Azure Site Recovery 中的 VMware vCenter 发现失败问题。
manager: digimobile
ms.service: site-recovery
ms.topic: conceptual
origin.date: 10/29/2019
author: rockboyfor
ms.date: 11/09/2020
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: f874d94f0afde9cc346323952a867d26a23fc097
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94328901"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>排查 vCenter Server 发现失败问题

本文帮助你排查由于 VMware vCenter 发现失败而发生的问题。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>maxSnapShots 属性中包含非数字值

在低于 9.20 的版本中，vCenter 在检索到 VM 上的 `snapshot.maxSnapShots` 属性的非数字值时会断开连接。

此问题由错误 ID 95126 标识。

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

若要解决问题，请执行以下操作：

- 标识 VM，并将值设置为数字值（使用 vCenter 中的“VM 编辑”设置）。

或

- 将配置服务器升级到 9.20 或更高版本。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>vCenter 连接的代理配置问题

vCenter 发现遵守 System 用户配置的系统默认代理设置。 DRA 服务遵守用户在使用统一安装程序安装程序或 .OVA 模板安装配置服务器过程中提供的代理设置。 

一般情况下，代理用于与公用网络通信；例如，与 Azure 通信。 如果已配置代理并且 vCenter 位于本地环境中，则它无法与 DRA 通信。

遇到此问题时，会发生以下情况：

- 由于以下错误，无法访问 vCenter 服务器 \<vCenter>：远程服务器返回了错误：(503) 服务器不可用
- 由于以下错误，无法访问 vCenter 服务器 \<vCenter>：远程服务器返回了错误：无法连接到远程服务器。
- 无法连接到 vCenter/ESXi 服务器。

若要解决问题，请执行以下操作：

下载 [PsExec 工具](https://aka.ms/PsExec)。 

使用 PsExec 工具访问系统用户上下文，确定是否已配置代理地址。 然后，可以使用以下过程将 vCenter 添加到绕过列表。

对于发现代理配置：

1. 使用 PsExec 工具在系统用户上下文中打开 IE。

    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. 修改 Internet Explorer 中的代理设置，以绕过 vCenter IP 地址。
3. 重启 tmanssvc 服务。

对于 DRA 代理配置：

1. 打开命令提示符，然后打开 Azure Site Recovery Provider 文件夹。

    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. 在命令提示符下运行以下命令。

   **DRCONFIGURATOR.EXE /configure /AddBypassUrls [IP 地址/添加 vCenter 时提供的 vCenter Server FQDN]**

4. 重启 DRA 提供程序服务。

## <a name="next-steps"></a>后续步骤

[管理用于 VMware VM 灾难恢复的配置服务器](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)

<!-- Update_Description: update meta properties, wording update, update link -->