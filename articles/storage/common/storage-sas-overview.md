---
title: 使用共享访问签名 (SAS) 授予对数据的有限访问权限
titleSuffix: Azure Storage
description: 了解使用共享访问签名 (SAS) 委托对 Azure 存储资源（包括 Blob、队列、表和文件）的访问权限。
services: storage
author: WenJason
ms.service: storage
ms.topic: conceptual
origin.date: 08/17/2020
ms.date: 11/16/2020
ms.author: v-jay
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 060f25fff2f859998b0c75bf1fd22db2905ac584
ms.sourcegitcommit: 5f07189f06a559d5617771e586d129c10276539e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94552338"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限

使用共享访问签名 (SAS) 可以安全委托对存储帐户中的资源的访问权限，而不会损害数据的安全性。 使用 SAS 可以精细控制客户端访问数据的方式。 你可以控制客户端可以访问哪些资源、客户端对这些资源拥有哪些权限、SAS 的有效期，以及其他参数。

## <a name="types-of-shared-access-signatures"></a>共享访问签名的类型

Azure 存储支持三种类型的共享访问签名：

- 用户委托 SAS。 用户委托 SAS 使用 Azure Active Directory (Azure AD) 凭据以及为 SAS 指定的权限进行保护。 用户委托 SAS 仅适用于 Blob 存储。

    有关用户委托 SAS 的详细信息，请参阅[创建用户委托 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)。

- **服务 SAS。** 使用存储帐户密钥保护的服务 SAS。 一个服务 SAS 只会委托对一个 Azure 存储服务中的资源的访问权限：Blob 存储、队列存储、表存储或 Azure 文件。

    有关服务 SAS 的详细信息，请参阅[创建服务 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-service-sas)。

- **帐户 SAS。** 帐户 SAS 使用存储帐户密钥进行保护。 帐户 SAS 可委派对一个或多个存储服务中的资源的访问权限。 通过服务或用户委托 SAS 提供的所有操作也可以通过帐户 SAS 提供。 此外，使用帐户 SAS 还可以委托对在服务级别应用的操作（例如“获取/设置服务属性”和“获取服务统计信息”操作）的访问权限。   还可以委派对 blob 容器、表、队列和文件共享执行读取、写入和删除操作的访问权限，而这是服务 SAS 所不允许的。 

    有关帐户 SAS 的详细信息，请参阅[创建帐户 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-account-sas)。

> [!NOTE]
> 作为安全最佳做法，Azure 建议尽可能地使用 Azure AD 凭据，而不要使用更容易透露的帐户密钥。 当应用程序设计需要共享访问签名以访问 Blob 存储时，请尽可能使用 Azure AD 凭据创建用户委托 SAS 以提高安全性。 有关详细信息，请参阅[使用 Azure Active Directory 授予对 Blob 和队列的访问权限](storage-auth-aad.md)。

共享访问签名可以采取以下两种形式的一种：

- **临时 SAS：** 创建临时 SAS 时，该 SAS 的开始时间、过期时间和权限都在 SAS URI 中指定（如果省略开始时间，则以隐含方式指定）。 任何类型的 SAS 都可以用作临时 SAS。
- **具有存储访问策略的服务 SAS：** 存储访问策略是针对资源容器（可以是 Blob 容器、表、队列或文件共享）定义的。 可以使用存储访问策略来管理一个或多个服务共享访问签名的约束。 将某个服务 SAS 与某个存储访问策略关联时，该 SAS 将继承对该存储访问策略定义的约束 &mdash; 开始时间、过期时间和权限。

> [!NOTE]
> 用户委托 SAS 或帐户 SAS 必须是临时 SAS。 用户委托 SAS 或帐户 SAS 不支持存储的访问策略。

## <a name="how-a-shared-access-signature-works"></a>共享访问签名的工作方式

共享访问签名是一种签名 URI，它指向一个或多个存储资源并且包括包含一组特定的查询参数的令牌。 该令牌指示客户端可以如何访问资源。 作为一个查询参数，签名是基于 SAS 参数构造的，已通过用来创建该 SAS 的密钥签名。 Azure 存储使用该签名授予对存储资源的访问权限。

### <a name="sas-signature-and-authorization"></a>SAS 签名和授权

可通过以下两种方式之一对 SAS 令牌进行签名：

- 使用通过 Azure Active Directory (Azure AD) 凭据创建的用户委托密钥。 用户委托 SAS 是使用用户委托密钥进行签名的。

    若要获取用户委托密钥并创建 SAS，必须为 Azure AD 安全主体分配一个包含 Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey 操作的 Azure 角色。 关于有权获取用户委托密钥的 Azure 角色的详细信息，请参阅[创建用户委托 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)。

- 使用存储帐户密钥（共享密钥）。 服务 SAS 和帐户 SAS 都是使用存储帐户密钥签名的。 若要创建使用帐户密钥签名的 SAS，应用程序必须有权访问该帐户密钥。

当请求包含 SAS 令牌时，将根据此 SAS 令牌的签名方式对该请求进行授权。 Azure 存储还使用用于创建 SAS 令牌的访问密钥或凭据来向拥有 SAS 的客户端授予访问权限。

下表总结了当每种类型的 SAS 令牌包含在对 Azure 存储的请求中时如何对其进行授权：

| SAS 类型 | 授权类型 |
|-|-|
| 用户委托 SAS（仅限 Blob 存储） | Azure AD |
| 服务 SAS | 共享密钥 |
| 帐户 SAS | 共享密钥 |

Azure 建议尽可能使用用户委托 SAS 以提高安全性。

### <a name="sas-token"></a>SAS 令牌

SAS 令牌是在客户端生成的字符串，例如，使用某个 Azure 存储客户端库生成。 Azure 存储不会以任何方式跟踪 SAS 令牌。 可以在客户端上创建不限数量的 SAS 令牌。 创建 SAS 后，可将其分发到需要访问存储帐户中的资源的客户端应用程序。

当客户端应用程序提供 Azure 存储的 SAS URI 作为请求的一部分时，服务将检查 SAS 参数和签名，以验证该 SAS 是否可用于授权请求。 如果服务确认签名有效，则请求获得授权。 否则，请求被拒绝，错误代码为 403（禁止访问）。

下面是服务 SAS URI 的一个示例，其中显示了资源 URI 和 SAS 令牌：

![服务 SAS URI 的组成部分](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>何时使用共享访问签名

若要为任何客户端提供对存储帐户中的资源的安全访问权限（否则该客户端无权访问这些资源），请使用 SAS。

SAS 通常适用于用户需要在存储帐户中读取和写入其数据的服务情形。 在存储帐户存储用户数据的情形中，有两种典型的设计模式：

1. 客户端通过执行身份验证的前端代理服务上传和下载数据。 此前端代理服务的优势在于允许验证业务规则，但对于大量数据或大量事务，创建可扩展以匹配需求的服务可能成本高昂或十分困难。

   ![方案示意图：前端代理服务](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. 轻型服务按需对客户端进行身份验证，并生成 SAS。 客户端应用程序收到 SAS 后，可以直接使用 SAS 定义的权限根据 SAS 允许的间隔访问存储帐户资源。 SAS 减少了通过前端代理服务路由所有数据的需要。

   ![方案示意图：SAS 提供程序服务](./media/storage-sas-overview/sas-storage-provider-service.png)

许多实际服务可能会混合使用这两种方法。 例如，可能通过前端代理对某些数据进行处理和验证，同时使用 SAS 直接保存和/或读取其他数据。

此外，在某些方案的复制操作中，需要使用 SAS 来授权访问源对象：

- 将一个 Blob 复制到驻留在其他存储帐户中的另一个 Blob 时，必须使用 SAS 授予对源 Blob 的访问权限。 还可以选择使用 SAS 授予对目标 Blob 的访问权限。
- 将一个文件复制到驻留在其他存储帐户中的另一个文件时，必须使用 SAS 授予对源文件的访问权限。 还可以选择使用 SAS 授予对目标文件的访问权限。
- 将一个 Blob 复制到一个文件，或将一个文件复制到一个 Blob 时，必须使用 SAS 授予对源对象的访问权限，即使源对象和目标对象驻留在同一存储帐户中。

## <a name="best-practices-when-using-sas"></a>使用 SAS 的最佳实践

在应用程序中使用共享访问签名时，需要知道以下两个可能的风险：

- 如果 SAS 泄露，则获取它的任何人都可以使用它，这可能会损害存储帐户。
- 如果提供给客户端应用程序的 SAS 到期并且应用程序无法从服务检索新 SAS，则可能会影响该应用程序的功能。

下面这些针对使用共享访问签名的建议可帮助降低这些风险：

- 始终使用 HTTPS  创建或分发 SAS。 如果某一 SAS 通过 HTTP 传递并且被截取，则执行中间人攻击的攻击者将能够读取 SAS、并使用它，就像目标用户本可执行的操作一样，这可能会暴露敏感数据或者使恶意用户能够损坏数据。
- **尽可能使用用户委托 SAS。** 用户委托 SAS 比服务 SAS 或帐户 SAS提供的安全性更高。 用户委托 SAS 是使用 Azure AD 凭据保护的，这样便不需要使用你的代码存储帐户密钥。
- **为 SAS 准备好吊销计划。** 确保已做好在 SAS 透露时的应对准备。
- **定义服务 SAS 的存储访问策略。** 存储访问策略可让你选择撤消服务 SAS 的权限，且无需重新生成存储帐户密钥。 将针对 SAS 的到期时间设置为很久之后的某一时间（或者无限远），并且确保定期对其进行更新以便将到期时间移到将来的更远时间。
- **对临时 SAS 服务 SAS 或帐户 SAS 使用短期过期时间。** 这样，即使某一 SAS 泄露，它也只会在短期内有效。 如果无法参照某一存储访问策略，该行为尤其重要。 临时到期时间还通过限制可用于上传到它的时间来限制可以写入 Blob 的数据量。
- **如果需要，让客户端自动续订 SAS。** 客户端应在到期时间之前很久就续订 SAS，这样，即使提供 SAS 的服务不可用，客户端也有时间重试。 如果 SAS 旨在用于少量即时的短期操作，这些操作应在到期时间内完成，则上述做法可能是不必要的，因为不应续订 SAS。 但是，如果客户端定期通过 SAS 发出请求，那么“SAS 可能会过期”就将开始起作用了。 需要考虑的主要方面就是在以下两者间进行权衡：对短期 SAS 的需求（如前文所述）以及确保客户端尽早请求续订（以免在成功续订前因 SAS 到期而中断）。
- 要注意 SAS 开始时间。  如果将 SAS 的开始时间设置为当前时间，则可能会在前几分钟间歇性地发生故障，因为不同计算机的当前时间略有不同（称为时钟偏差）。 通常，将开始时间至少设置为 15 分钟前。 或者根本不设置，这会使它在所有情况下都立即生效。 同样原则也适用于到期时间 - 请记住，对于任何请求，在任一方向可能会观察到最多 15 分钟的时钟偏移。 对于使用 2012-02-12 之前的 REST 版本的客户端，未参照某一存储访问策略的 SAS 的最大持续时间是 1 小时，指定超过 1 小时持续时间的任何策略都会失败。
- **请注意 SAS 日期/时间格式。** 如果为 SAS 设置了开始时间和/或到期时间，则对于某些实用程序（例如，命令行实用程序 AzCopy），需要将日期/时间格式设置为“+%Y-%m-%dT%H:%M:%SZ”，特别是需要包括秒数，以便它使用 SAS 令牌工作。
- **对要访问的资源要具体。** 一种安全性最佳做法是向用户提供所需最小权限。 如果某一用户仅需要对单个实体的读取访问权限，则向该用户授予对该单个实体的读取访问权限，而不要授予针对所有实体的读取/写入/删除访问权限。 如果 SAS 泄露，这也有助于降低损失，因为攻击者手中掌握的 SAS 的权限较为有限。
- **知道每次使用都会对帐户收费，包括通过 SAS 使用。** 如果你提供某个 Blob 的写入访问权限，则用户可以选择上传 200 GB 的 Blob。 如果还向用户提供了对 Blob 的读访问权限，他们可能会选择下载 Blob 10 次，对你产生 2 TB 的传出费用。 此外，提供受限权限，帮助降低恶意用户的潜在操作威胁。 使用短期 SAS 以便减少这一威胁（但要注意结束时间上的时钟偏移）。
- **验证使用 SAS 写入的数据。** 在某一客户端应用程序将数据写入存储帐户时，请记住对于这些数据可能存在问题。 如果应用程序要求在数据可供使用前对数据进行验证或授权，应该在写入数据后、但在应用程序使用这些数据前执行此验证。 这一实践还有助于防止损坏的数据或恶意数据写入帐户，这些数据可能是正常要求 SAS 的用户写入的，也可能是利用泄露的 SAS 的用户写入的。
- **知道何时不使用 SAS。** 有时，针对存储帐户执行特定操作所带来的风险超过了 SAS 所带来的好处。 对于此类操作，应创建一个中间层服务，该服务在执行业务规则验证、身份验证和审核后写入存储帐户。 此外，有时候以其他方式管理访问会更简单。 例如，如果想要使某一容器中的所有 Blob 都可以公开读取，则可以使该容器成为公共的，而不是为每个客户端都提供 SAS 来进行访问。
- **使用 Azure Monitor 和 Azure 存储日志来监视应用程序。** 可以使用 Azure Monitor 和存储分析日志记录来观察由于 SAS 提供程序服务中断或无意中删除存储访问策略而导致授权失败的任何高发情形。 有关详细信息，请参阅 [Azure Monitor 中的 Azure 存储指标](monitor-storage.md?toc=%2fstorage%2fblobs%2ftoc.json)和 [Azure 存储分析日志记录](storage-analytics-logging.md?toc=%2fstorage%2fblobs%2ftoc.json)。

## <a name="get-started-with-sas"></a>开始使用 SAS

若要开始使用共享访问签名，请参阅以下适用于每种 SAS 类型的文章。

### <a name="user-delegation-sas"></a>用户委托 SAS

- [使用 PowerShell 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [使用 Azure CLI 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [使用 .NET 为容器或 blob 创建用户委托 SAS](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>服务 SAS

- [使用 .NET 为容器或 Blob 创建服务 SAS](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>帐户 SAS

- [使用 .NET 创建帐户 SAS](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>后续步骤

- [使用共享访问签名委托访问权限 (REST API)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [创建用户委托 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)
- [创建服务 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-service-sas)
- [创建帐户 SAS (REST API)](https://docs.microsoft.com/rest/api/storageservices/create-account-sas)
