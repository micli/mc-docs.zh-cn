---
title: 设置 Azure Cache for Redis 的 Redis 版本（预览）
description: 了解如何配置 Redis 版本
author: yegu-ms
ms.author: v-junlch
ms.service: cache
ms.topic: conceptual
ms.date: 11/16/2020
ms.openlocfilehash: bf1858b3c036ba9bf83d884f3958daac4dfc755d
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849466"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>设置 Azure Cache for Redis 的 Redis 版本（预览）
本文介绍如何配置要与缓存实例一起使用的 Redis 软件版本。 Azure Cache for Redis 提供 Redis 的最新主版本和至少一个早期版本。 由于会发布新的 Redis 软件，它将定期更新这些版本。 可在两个可用版本之间进行选择。 请记住，如果缓存当前使用的版本不再受支持，缓存会自动升级到下一版本。

## <a name="prerequisites"></a>先决条件
* Azure 订阅 - [创建订阅](https://www.azure.cn/pricing/1rmb-trial/)

## <a name="create-a-cache"></a>创建缓存
若要创建缓存，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.cn)，然后选择“创建资源”。
  
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="选择 Azure Cache for Redis。":::
   
1. 在“基本信息”页面上，配置新缓存的设置。
   
    | 设置      | 建议的值  | 描述 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 选择订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
    | **资源组** | 选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
    | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名将是 *\<DNS name>.redis.cache.chinacloudapi.cn*。 | 
    | **位置** | 选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
    | **缓存类型** | 选择[缓存层和大小](https://www.azure.cn/pricing/details/redis-cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 在“高级”页面上，选择要使用的 Redis 版本。
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis 版本。":::

1. 单击“创建”。 
   
    创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 

    > [!NOTE]
    > 目前，一旦创建缓存，就无法更改 Redis 版本。
    >

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

> [!div class="nextstepaction"]
> [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

