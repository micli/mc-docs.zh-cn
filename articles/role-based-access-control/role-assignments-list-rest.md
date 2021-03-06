---
title: 使用 REST API 列出 Azure 角色分配 - Azure RBAC
description: 了解如何使用 REST API 和 Azure 基于角色的访问控制 (Azure RBAC) 来确定用户、组、服务主体和托管标识有权访问的资源。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: v-junlch
ms.reviewer: bagovind
ms.openlocfilehash: b8936840067b5bdad7df85592a1019e32afc731d
ms.sourcegitcommit: 7429daf26cff014b040f69cdae75bdeaea4f4e93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83991659"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>使用 REST API 列出 Azure 角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍如何使用 REST API 列出角色分配。

## <a name="list-role-assignments"></a>列出角色分配

在 Azure RBAC 中，若要列出访问权限，请列出角色分配。 若要列出角色分配，可以使用其中一个[角色分配 - List](https://docs.microsoft.com/rest/api/authorization/roleassignments/list) REST API。 若要优化结果，请指定一个范围和可选的筛选器。

1. 从下面的请求开始：

    ```http
    GET https://management.chinacloudapi.cn/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，将“{scope}”替换为要列出角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 资源 |

    在前面的示例中，microsoft.web 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)和支持的 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)。  
     
1. 将“{filter}”替换为筛选角色分配列表时要应用的条件。

    > [!div class="mx-tableFixed"]
    > | 筛选器 | 说明 |
    > | --- | --- |
    > | `$filter=atScope()` | 只列出指定范围内的角色分配，而不包括子范围内的角色分配。 |
    > | `$filter=assignedTo('{objectId}')` | 列出指定用户或服务主体的角色分配。<br/>如果用户是具有角色分配的组的成员，则该角色分配也会列出。 此筛选器对于组是可传递的，这意味着如果用户是组的成员，并且该组是具有角色分配的另一个组的成员，则该角色分配也会列出。<br/>此筛选器仅接受用户或服务主体的对象 ID。 不能传递组的对象 ID。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定范围内指定用户或服务主体的角色分配。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定用户、组或服务主体的角色分配。 |

以下请求列出订阅范围中指定用户的所有角色分配：

```http
GET https://management.chinacloudapi.cn/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

下面显示了输出示例：

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

- [使用 REST API 添加或删除 Azure 角色分配](role-assignments-rest.md)
- [Azure REST API 参考](https://docs.microsoft.com/rest/api/azure/)

