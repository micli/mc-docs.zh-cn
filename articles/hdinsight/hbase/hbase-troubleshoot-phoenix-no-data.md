---
title: HDP 升级导致 Azure HDInsight 的 Apache Phoenix 视图中没有数据
description: HDP 升级导致 Azure HDInsight 的 Apache Phoenix 视图中没有数据
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: v-yiso
origin.date: 08/08/2019
ms.date: 11/11/2019
ms.openlocfilehash: 4af4f40b6c5af82036085aae30324e400924e5dd
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "73426063"
---
# <a name="scenario-hdp-upgrade-causes-no-data-in-apache-phoenix-views-in-azure-hdinsight"></a>方案：HDP 升级导致 Azure HDInsight 的 Apache Phoenix 视图中没有数据

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从 HDP 2.4 升级到 HDP 2.5 后，Apache Phoenix 视图不包含日期。

## <a name="cause"></a>原因

视图的索引表（视图的所有索引都存储在单个物理 Apache HBase 表中）在升级过程中被截断

## <a name="resolution"></a>解决方法

升级后，删除并重新创建所有视图索引。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道以获取更多支持：

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
