---
title: sys.sp_cleanup_data_retention (Transact-SQL) - Azure SQL Edge
description: 了解如何在 Azure SQL Edge 中使用 sys.sp_cleanup_data_retention (Transact-SQL)
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: v-tawe
ms.reviewer: sstein
origin.date: 09/22/2020
ms.date: 09/25/2020
ms.openlocfilehash: 63f428e6c0d2f3812060bd2e447579a984f215a8
ms.sourcegitcommit: d89eba76d6f14be0b96c8cdf99decc208003e496
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91248470"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**适用于：** Azure SQL Edge

从启用了数据保留策略的表中清除已过时的记录。 有关详细信息，请参阅[数据保留](data-retention-overview.md)。 

## <a name="syntax"></a>语法 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>参数  
`[ @schema_name = ] schema_name`    
 是需要对其执行清除操作的表的主控架构的名称。 schema_name 是 sysname 类型的必需参数。
  
`[ @table_name = ] 'table_name'`    
 需要对其执行清除操作的表的名称。 table_name 是 sysname 类型的必需参数。

## <a name="output-parameter"></a>输出参数  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount 是一个可选的 OUTPUT 参数，表示从表中清除的记录数。 rowcount 是 int。
  
## <a name="permissions"></a>权限    
 需要 db_owner 权限。

## <a name="next-steps"></a>后续步骤
- [数据保留和数据自动清除](data-retention-overview.md)
- [使用保留策略管理历史数据](data-retention-cleanup.md) 
- [启用和禁用数据保留](data-retention-enable-disable.md)
