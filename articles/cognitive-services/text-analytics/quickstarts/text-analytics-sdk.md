---
title: 快速入门：使用文本分析客户端库进行文本挖掘
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Azure 认知服务的文本分析 API 执行情绪分析等操作。
services: cognitive-services
author: Johnnytechn
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 10/26/2020
ms.author: v-johya
keywords: 文本挖掘, 情绪分析, 文本分析
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: c4a3dd8add9d7e8249fdaf42b4f1b3e693cf3edb
ms.sourcegitcommit: 93309cd649b17b3312b3b52cd9ad1de6f3542beb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103577"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>快速入门：使用文本分析客户端库

参考本文开始使用文本分析客户端库。 按照以下步骤安装包，试用用于挖掘文本的示例代码。

使用文本分析客户端库执行：

* 情绪分析
* 语言检测
* 实体识别
* 关键短语提取
* 观点挖掘

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.0`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.0`。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.0`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。
> * 还可[在浏览器中](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)运行此版本的文本分析客户端库。

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * 文本分析 API 的最新稳定版本为 `3.0`。
>    * 确保只按所用版本的说明操作。
> * 为了简单起见，本文中的代码使用了同步方法和不受保护的凭据存储。 对于生产方案，我们建议使用批处理的异步方法来提高性能和可伸缩性。 请参阅下面的参考文档。 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>其他语言支持

如果已单击此选项卡，则可能看不到采用你偏好的编程语言的快速入门。 别担心，我们提供了其他快速入门。 使用表格查找适用于编程语言的示例。

| 语言 | 可用版本 | 
|----------|------------------------|
| Ruby     | [版本 2.1](ruby-sdk.md) | 
| Go       | [版本 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索解决方案](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
* [检测语言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [语言识别](../how-tos/text-analytics-how-to-language-detection.md)

