---
title: 使用 Azure Active Directory 进行目录同步
description: 实现此同步模式的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: v-junlch
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda2873fec8556e77320174c4ea442c3edf648a4
ms.sourcegitcommit: ca5e5792f3c60aab406b7ddbd6f6fccc4280c57e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754789"
---
# <a name="directory-synchronization"></a>目录同步

许多组织都有同时包含本地组件和云组件的混合基础结构。 通过在本地目录和云目录之间同步用户标识，用户可以使用一组凭据访问资源。 

同步是指以下过程： 

* 基于特定条件创建对象
* 始终更新对象
* 在不再满足条件时删除对象。 

本地预配涉及到从本地源（例如 Active Directory）预配到 Azure Active Directory (Azure AD)。 

## <a name="use-when"></a>何时使用

需要将标识数据从本地 Active Directory 环境同步到 Azure AD。

![体系结构图](./media/authentication-patterns/dir-sync-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户** ：使用 Azure AD 访问应用程序。

* **Web 浏览器** ：用户与之交互以访问应用程序外部 URL 的组件。

* **应用程序** ：依赖于使用 Azure AD 进行身份验证和授权的 Web 应用。

* **Azure AD** ：通过 Azure AD Connect 同步组织本地目录中的标识信息。 

* **Azure AD Connect** ：用于将本地标识基础结构连接到 Azure AD 的工具。 向导和引导式体验有助于部署和配置连接所需的先决条件和组件，包括从 Active Directory 到 Azure AD 的同步和登录。 

* **Active Directory** ：Active Directory 是大多数 Windows Server 操作系统中包含的一项目录服务。 运行 Active Directory 域服务 (AD DS) 的服务器称为域控制器。 它们对域中的所有用户和计算机进行身份验证和授权。

## <a name="implement-directory-synchronization-with-azure-ad"></a>使用 Azure AD 实现目录同步

* [Azure AD Connect 安装路线图](/active-directory/hybrid/how-to-connect-install-roadmap)

