---
title: 从命令行创建 JavaScript 函数 - Azure Functions
description: 了解如何从命令行创建 JavaScript 函数，然后将本地 Node.js 项目发布到 Azure Functions 中托管的无服务器实例。
ms.date: 11/17/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85fa33a58700f94db3f1b2232d100225744c6453
ms.sourcegitcommit: b072689d006cbf9795612acf68e2c4fee0eccfbc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "95970676"
---
# <a name="quickstart-create-a-javascript-function-in-azure-from-the-command-line"></a>快速入门：在 Azure 中从命令行创建 JavaScript 函数


[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

在本文中，我们使用命令行工具创建一个响应 HTTP 请求的 JavaScript 函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。 

完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。

本文还提供了[基于 Visual Studio 代码的版本](create-first-function-vs-code-node.md)。

## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

+ 具有活动订阅的 Azure 帐户。 [创建帐户](https://www.azure.cn/pricing/1rmb-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](./functions-run-local.md#v2) 版本 3.x。

+ 以下用于创建 Azure 资源的工具之一：

    + [Azure CLI](/cli/install-azure-cli) 版本 2.4 或更高版本。

    + [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) 版本 5.0 或更高版本。

+ [Node.js](https://nodejs.org/) 版本 12。 还支持 Node.js 版本 10。

### <a name="prerequisite-check"></a>先决条件检查

验证你的先决条件，这取决于你是使用 Azure CLI 还是使用 Azure PowerShell 创建 Azure 资源：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `az --version` 以检查 Azure CLI 版本是否为 2.4 或更高版本。

+ 运行 `az login` 登录到 Azure 并验证活动订阅。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 在终端或命令窗口中，运行 `func --version` 以检查 Azure Functions Core Tools 的版本是否为 3.x。

+ 运行 `(Get-Module -ListAvailable Az).Version` 并验证版本是否为 5.0 或更高版本。 

+ 运行 `Connect-AzAccount -Environment AzureChinaCloud` 登录到 Azure 并验证活动订阅。

---

## <a name="create-a-local-function-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分，你将创建包含单个函数的函数项目。

1. 按如下所示运行 `func init` 命令，在名为 LocalFunctionProj 的文件夹中创建使用指定运行时的函数项目：  

    ```console
    func init LocalFunctionProj --javascript
    ```

1. 导航到项目文件夹：

    ```console
    cd LocalFunctionProj
    ```

    此文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数 (HttpExample) 的唯一名称，`--template` 参数指定该函数的触发器 (HTTP)。

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
    
    `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。
    
### <a name="optional-examine-the-file-contents"></a>（可选）检查文件内容

如果需要，可以跳到[在本地运行函数](#run-the-function-locally)并稍后再检查文件内容。

#### <a name="indexjs"></a>index.js

*index.js* 导出一个根据 *function.json* 中的配置触发的函数。

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

对于 HTTP 触发器，该函数将接收 *function.json* 中定义的变量 `req` 中的请求数据。 在 *function.json* 中定义为 `$return` 的返回对象是响应。 要了解详细信息，请参阅 [Azure Functions HTTP 触发器和绑定](./functions-bindings-http-webhook.md?tabs=javascript)。

#### <a name="functionjson"></a>function.json

*function.json* 是一个配置文件，其中定义了函数的输入和输出 `bindings`，包括触发器类型。 

```json
{
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

每个绑定都需要一个方向、类型和唯一的名称。 HTTP 触发器具有类型为 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 的输入绑定和类型为 [`http`](functions-bindings-http-webhook-output.md) 的输出绑定。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. 在 Azure 中创建函数应用：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location chinanorth2 --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    [az functionapp create](/cli/functionapp#az-functionapp-create) 命令可在 Azure 中创建函数应用。 如果使用的是 Node.js 10，请同时将 `--runtime-version` 更改为 `10`。
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'China North 2'
    ```
    
    [New-AzFunctionApp](https://docs.microsoft.com/powershell/module/az.functions/new-azfunctionapp) cmdlet 可在 Azure 中创建函数应用。 如果使用的是 Node.js 10，请将 `-RuntimeVersion` 更改为 `10`。
    
    ---
    
    在上一个示例中，请将 `<STORAGE_NAME>` 替换为在上一步骤中使用的帐户的名称，并将 `<APP_NAME>` 替换为适合自己的全局唯一名称。 `<APP_NAME>` 也是函数应用的默认 DNS 域。 
    
    此命令将创建一个函数应用，该应用在 [Azure Functions 消耗计划](functions-scale.md#consumption-plan)下指定的语言运行时中运行，根据本教程产生的用量，此操作是免费的。 该命令还会在同一资源组中预配关联的 Azure Application Insights 实例，可以使用它来监视函数应用和查看日志。 有关详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。 该实例在激活之前不会产生费用。

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接到 Azure 存储队列](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)

