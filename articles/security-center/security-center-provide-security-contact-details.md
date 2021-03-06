---
title: 针对 Azure 安全中心警报配置电子邮件通知
description: 了解如何针对安全警报微调 Azure 安全中心发送的电子邮件类型。
services: security-center
documentationcenter: na
author: Johnnytechn
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2020
ms.author: v-johya
origin.date: 05/09/2020
ms.openlocfilehash: c4aca69b010900cb00d13d5cb1cb4f770406fb5c
ms.sourcegitcommit: d30cf549af09446944d98e4bd274f52219e90583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637815"
---
# <a name="configure-email-notifications-for-security-alerts"></a>针对安全警报配置电子邮件通知 

安全警报需要传达至组织中适当的人员。 默认情况下，只要触发针对其订阅的高严重性警报，安全中心就会向订阅所有者发送电子邮件。 本页说明了如何自定义这些通知。

若要定义你自己的通知电子邮件首选项，Azure 安全中心的“电子邮件通知”设置页允许你选择：

- 应通知的人员 - 可将电子邮件发送给选定的个人或任何具有指定 Azure 订阅角色的人。 
- 他们应得到通知的内容 - 修改安全中心应发出通知的严重性级别。

为了避免警报疲劳，安全中心限制了外发邮件的数量。 对于每个订阅，安全中心都会发送以下数量的电子邮件：

- 每天最多四封电子邮件（适用于“高严重性”警报） 
- 每天最多两封电子邮件（适用于“中等严重性”警报） 
- 每天最多一封电子邮件（适用于“低严重性”警报） 

:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="配置将接收有关安全警报的电子邮件的联系人的详细信息。" :::
 
## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|免费|
|所需角色和权限：|**安全管理员**<br>**订阅所有者** |
|云：|![是](./media/icons/yes-icon.png) 中国云|
|||


## <a name="customize-the-security-alerts-email-notifications"></a>自定义安全警报电子邮件通知<a name="email"></a>

可以将电子邮件通知发送给具有特定 Azure 角色的个人或所有用户。

1. 从安全中心的“定价和设置”区域中选择相关订阅，然后选择“电子邮件通知” 。

1. 使用以下一个或两个选项定义通知的收件人：

    - 从下拉列表中，选择某个可用角色。
    - 输入用逗号分隔的特定电子邮件地址。 输入的电子邮件地址数量无限制。

1. 若要将安全联系人信息应用到订阅，请选择“保存”。


## <a name="next-steps"></a>后续步骤

若要了解有关安全警报的详细信息，请参阅以下页面：

- [安全警报 - 参考指南](alerts-reference.md) -- 了解 Azure 安全中心的威胁防护模块中可能会显示的安全警报
- [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报
- [自动执行对安全中心触发器的响应](workflow-automation.md) - 使用自动化功能通过自定义通知逻辑响应安全中心触发器

