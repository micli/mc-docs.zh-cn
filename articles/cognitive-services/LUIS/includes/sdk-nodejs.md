---
title: include 文件
description: include 文件
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/23/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: f3c9e4df6c5e34af0305977a249ab7e895376f45
ms.sourcegitcommit: f1d0f81918b8c6fca25a125c17ddb80c3a7eda7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2020
ms.locfileid: "96306220"
---
使用适用于 Node.js 的语言理解 (LUIS) 客户端库来执行以下操作：

* 创建应用
* 使用示例言语添加意向（机器学习实体）
* 训练和发布应用
* 查询预测运行时

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest) |  [创作](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring)和[预测](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)库源代码 | [创作](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)和[预测](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org)
* Azure 订阅 - [创建试用订阅](https://www.azure.cn/pricing/details/cognitive-services)
* 有了 Azure 订阅后，在 Azure 门户中[创建语言理解创作资源](https://portal.azure.cn/#create/Microsoft.CognitiveServicesLUISAllInOne)，以获取创作密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从[创建](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal)的资源获取密钥和终结点，以便将应用程序连接到语言理解创作。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。 可以使用免费定价层 (`F0`) 来试用该服务。

## <a name="setting-up"></a>设置

### <a name="create-a-new-javascript-application"></a>创建新的 JavaScript 应用程序

1. 在控制台窗口中，为应用程序创建一个新目录，并将其移动到该目录中。

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. 通过创建 `package.json` 文件，将该目录初始化为 JavaScript 应用程序。

    ```console
    npm init -y
    ```

1. 为 JavaScript 代码创建名为 `index.js` 的文件。

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>安装 NPM 库

在应用程序目录中，使用以下命令安装依赖项，每次执行一行：

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

`package.json` 应如下所示：

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>创作对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 对象，其中包含创作密钥。

## <a name="code-examples-for-authoring"></a>创作的代码示例

创建客户端后，可以使用此客户端访问如下所述的功能：

* 应用 - [添加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#add-applicationcreateobject--msrest-requestoptionsbase-)、[删除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#deletemethod-string--models-appsdeletemethodoptionalparams-)、[发布](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* 示例言语 - [按批添加](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#batch-string--string--examplelabelobject----msrest-requestoptionsbase-)、[按 ID 删除](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#deletemethod-string--string--number--msrest-requestoptionsbase-)
* 特征 - 管理[短语列表](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/features?view=azure-node-latest#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-)
* 模型 - 管理[意向](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-)和实体
* 模式 - 管理[模式](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/pattern?view=azure-node-latest#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-)
* 训练 - [训练](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-)应用和轮询[训练状态](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-)
* [版本](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/versions?view=azure-node-latest) - 使用克隆、导出和删除操作进行管理

## <a name="prediction-object-model"></a>预测对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 对象，其中包含创作密钥。

## <a name="code-examples-for-prediction-runtime"></a>预测运行时的代码示例

创建客户端后，可以使用此客户端访问如下所述的功能：

* 按 `staging` 或 `production` 槽进行[预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)
* [按版本进行预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>添加依赖项

在首选编辑器或 IDE 中打开 `index.js` 文件，并添加以下依赖项。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="add-boilerplate-code"></a>添加样板代码

1. 添加 `quickstart` 方法及其调用。 此方法保存大部分剩余代码。 在文件末尾调用此方法。

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. 在 quickstart 方法中添加剩余代码（除非另有指定）。

## <a name="create-variables-for-the-app"></a>为应用创建变量

创建两组变量：第一组为你更改的变量，第二组保留在代码示例中显示的状态。 

1. 创建用于保存创作密钥和资源名称的变量。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

1. 创建用于保存终结点、应用名称、版本和意向名称的变量。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="authenticate-the-client"></a>验证客户端

使用密钥创建 [CognitiveServicesCredentials](https://docs.microsoft.com/javascript/api/@azure/ms-rest-js/apikeycredentials?view=azure-node-latest) 对象，并在终结点中使用该对象创建一个 [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient?view=azure-node-latest) 对象。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="create-a-luis-app"></a>创建 LUIS 应用

LUIS 应用包含自然语言处理 (NLP) 模型，包括意向、实体和示例言语。

创建 [AppsOperation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 对象的 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest) 方法，用于创建应用。 名称和语言区域性是必需的属性。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```


## <a name="create-intent-for-the-app"></a>为应用创建意向
LUIS 应用模型中的主要对象是意向。 意向与用户言语意向的分组相符。 用户可以提问，或者做出表述，指出希望机器人（或其他客户端应用程序）提供特定的有针对性响应。 意向的示例包括预订航班、询问目的地城市的天气，以及询问客户服务的联系信息。

将 [model.add_intent](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) 方法与唯一意向的名称配合使用，然后传递应用 ID、版本 ID 和新的意向名称。

`intentName` 值硬编码为 `OrderPizzaIntent`，作为[为应用创建变量](#create-variables-for-the-app)中变量的一部分。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="create-entities-for-the-app"></a>为应用创建实体

尽管实体不是必需的，但在大多数应用中都可以看到实体。 实体从用户言语中提取信息，只有使用这些信息才能实现用户的意向。 有多种类型的[预生成](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-)实体和自定义实体，每种实体具有自身的数据转换对象 (DTO) 模型。  在应用中添加的常见预生成实体包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md) 和 [ordinal](../luis-reference-prebuilt-ordinal.md)。

必须知道，实体不会使用意向进行标记。 它们可以并且通常应用到多个意向。 只会为特定的单个意向标记示例用户言语。

实体的创建方法属于 [Model](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/model?view=azure-node-latest) 类的一部分。 每个实体类型有自身的数据转换对象 (DTO) 模型。

实体创建代码会创建机器学习实体，其中包含子实体以及应用于 `Quantity` 子实体的功能。

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="显示创建的实体的门户的部分屏幕截图，其中为包含子实体以及应用于 `Quantity` 子实体的功能的机器学习实体。":::

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

将以下方法置于 `quickstart` 方法之上，查找 Quantity 子实体的 ID，以便将功能分配给该子实体。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="add-example-utterance-to-intent"></a>将示例言语添加到意向

为了确定言语的意向并提取实体，应用需要言语示例。 这些示例需要针对特定的单个意向，并且应该标记所有自定义实体。 无需标记预生成实体。

通过创建 [ExampleLabelObject](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject?view=azure-node-latest) 对象的列表来添加示例言语（每个示例言语对应于一个对象）。 每个示例应使用实体名称和实体值的名称/值对字典来标记所有实体。 实体值应与示例言语文本中显示的值完全相同。

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="显示门户中标记的示例言语的部分屏幕截图。":::

结合应用 ID、版本 ID 和示例调用 [examples.add](https://docs.microsoft.com//javascript/api/@azure/cognitiveservices-luis-authoring/examples?view=azure-node-latest#add-string--string--examplelabelobject--models-examplesaddoptionalparams-)。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="train-the-app"></a>训练应用

创建模型后，需要为此模型版本训练 LUIS 应用。 训练后的模型可在[容器](../luis-container-howto.md)中使用，或者将其[发布](../luis-how-to-publish-app.md)到过渡槽或生产槽。

[train.trainVersion](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#trainversion-string--string--msrest-requestoptionsbase-) 方法需要应用 ID 和版本 ID。

极小的模型（如本快速入门中所示的模型）很快就能完成训练。 对于生产级应用程序，应用的训练应该包括轮询调用 [get_status](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/train?view=azure-node-latest#getstatus-string--string--msrest-requestoptionsbase-) 方法以确定训练何时成功或者是否成功。 响应是一个 [ModelTrainingInfo](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo?view=azure-node-latest) 对象列表，其中分别列出了每个对象的状态。 所有对象必须成功，才能将训练视为完成。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="publish-app-to-production-slot"></a>将应用发布到生产槽

使用 [app.publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/apps?view=azure-node-latest#publish-string--applicationpublishobject--msrest-requestoptionsbase-) 方法发布 LUIS 应用。 这会将当前已训练的版本发布到终结点上的指定槽。 客户端应用程序使用此终结点发送用户言语，以预测意向和提取实体。

```javascript
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```


## <a name="authenticate-the-prediction-runtime-client"></a>对预测运行时客户端进行身份验证

将 msRest.ApiKeyCredentials 对象与密钥一起使用，并在终结点中使用该对象创建一个 [LUIS.LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) 对象。

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

```javascript 
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

## <a name="get-prediction-from-runtime"></a>从运行时获取预测

添加以下代码以创建对预测运行时的请求。 用户言语是 [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) 对象的一部分。

**[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** 方法需要多个参数，如应用 ID、槽名称、用于满足请求的预测请求对象。 其他选项（如详细、显示所有意向和日志）都是可选的。

```javascript 
/* To run this sample, install the following modules.
 * npm install @azure/ms-rest-js @azure/cognitiveservices-luis-authoring @azure/cognitiveservices-luis-runtime
 */
// <Dependencies>
const msRest = require("@azure/ms-rest-js");
const LUIS_Authoring = require("@azure/cognitiveservices-luis-authoring");
const LUIS_Prediction = require("@azure/cognitiveservices-luis-runtime");
// </Dependencies>

// <Main>
const quickstart = async () => {

    // <VariablesYouChange>
    const authoringKey = 'REPLACE-WITH-YOUR-ASSIGNED-AUTHORING-KEY';

    const authoringResourceName = "REPLACE-WITH-YOUR-AUTHORING-RESOURCE-NAME";
    const predictionResourceName = "REPLACE-WITH-YOUR-PREDICTION-RESOURCE-NAME";
    // </VariablesYouChange>

    // <VariablesYouDontNeedToChangeChange>
    const authoringEndpoint = `https://${authoringResourceName}.cognitiveservices.azure.cn/`;
    const predictionEndpoint = `https://${predictionResourceName}.cognitiveservices.azure.cn/`;

    const appName = "Contoso Pizza Company";
    const versionId = "0.1";
    const intentName = "OrderPizzaIntent";
    // </VariablesYouDontNeedToChangeChange>

    // <AuthoringCreateClient>
    const luisAuthoringCredentials = new msRest.ApiKeyCredentials({
        inHeader: { "Ocp-Apim-Subscription-Key": authoringKey }
    });
    const client = new LUIS_Authoring.LUISAuthoringClient(
        luisAuthoringCredentials,
        authoringEndpoint
    );
    // </AuthoringCreateClient>

    // Create app
    const appId = await createApp(client, appName, versionId);

    // <AddIntent>
    await client.model.addIntent(
        appId,
        versionId,
        { name: intentName }
    );
    // </AddIntent>

    // Add Entities
    await addEntities(client, appId, versionId);
    
    // Add Labeled example utterance
    await addLabeledExample(client, appId, versionId, intentName);

    // <TrainAppVersion>
    await client.train.trainVersion(appId, versionId);
    while (true) {
        const status = await client.train.getStatus(appId, versionId);
        if (status.every(m => m.details.status == "Success")) {
            // Assumes that we never fail, and that eventually we'll always succeed.
            break;
        }
    }
    // </TrainAppVersion>

    // <PublishVersion>
    await client.apps.publish(appId, { versionId: versionId, isStaging: false });
    // </PublishVersion>

    // <PredictionCreateClient>
    const luisPredictionClient = new LUIS_Prediction.LUISRuntimeClient(
        luisAuthoringCredentials,
        predictionEndpoint
    );
    // </PredictionCreateClient>

    // <QueryPredictionEndpoint>
    // Production == slot name
    const request = { query: "I want two small pepperoni pizzas with more salsa" };
    const response = await luisPredictionClient.prediction.getSlotPrediction(appId, "Production", request);
    console.log(JSON.stringify(response.prediction, null, 4 ));
    // </QueryPredictionEndpoint>

}


const createApp = async (client, appName, versionId) => {

    // <AuthoringCreateApplication>
    const create_app_payload = {
        name: appName,
        initialVersionId: versionId,
        culture: "en-us"
    };

    const createAppResult = await client.apps.add(
        create_app_payload
    );

    const appId = createAppResult.body
    // </AuthoringCreateApplication>

    console.log(`Created LUIS app with ID ${appId}`);

    return appId;
}



const addEntities = async (client, appId, versionId) => {

    // <AuthoringAddEntities>
    // Add Prebuilt entity
    await client.model.addPrebuilt(appId, versionId, ["number"]);

    // Define ml entity with children and grandchildren
    const mlEntityDefinition = {
        name: "Pizza order",
        children: [
            {
                name: "Pizza",
                children: [
                    { name: "Quantity" },
                    { name: "Type" },
                    { name: "Size" }
                ]
            },
            {
                name: "Toppings",
                children: [
                    { name: "Type" },
                    { name: "Quantity" }
                ]
            }
        ]
    };

    // Add ML entity 
    const response = await client.model.addEntity(appId, versionId, mlEntityDefinition);
    const mlEntityId = response.body;

    // Add phraselist feature
    const phraselistResponse = await client.features.addPhraseList(appId, versionId, {
        enabledForAllModels: false,
        isExchangeable: true,
        name: "QuantityPhraselist",
        phrases: "few,more,extra"
    });
    const phraseListId = phraselistResponse.body;

    // Get entity and subentities
    const model = await client.model.getEntity(appId, versionId, mlEntityId);
    const toppingQuantityId = getModelGrandchild(model, "Toppings", "Quantity");
    const pizzaQuantityId = getModelGrandchild(model, "Pizza", "Quantity");

    // add model as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, pizzaQuantityId, { modelName: "number", isRequired: true });
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { modelName: "number" });
    // <AuthoringAddModelAsFeature>

    // add phrase list as feature to subentity model
    await client.features.addEntityFeature(appId, versionId, toppingQuantityId, { featureName: "QuantityPhraselist" });
    // </AuthoringAddEntities>
}


const addLabeledExample = async (client, appId, versionId, intentName) => {

    // <AuthoringAddLabeledExamples>
    // Define labeled example
    const labeledExampleUtteranceWithMLEntity =
    {
        text: "I want two small seafood pizzas with extra cheese.",
        intentName: intentName,
        entityLabels: [
            {
                startCharIndex: 7,
                endCharIndex: 48,
                entityName: "Pizza order",
                children: [
                    {
                        startCharIndex: 7,
                        endCharIndex: 30,
                        entityName: "Pizza",
                        children: [
                            {
                                startCharIndex: 7,
                                endCharIndex: 9,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 11,
                                endCharIndex: 15,
                                entityName: "Size"
                            },
                            {
                                startCharIndex: 17,
                                endCharIndex: 23,
                                entityName: "Type"
                            }]
                    },
                    {
                        startCharIndex: 37,
                        endCharIndex: 48,
                        entityName: "Toppings",
                        children: [
                            {
                                startCharIndex: 37,
                                endCharIndex: 41,
                                entityName: "Quantity"
                            },
                            {
                                startCharIndex: 43,
                                endCharIndex: 48,
                                entityName: "Type"
                            }]
                    }
                ]
            }
        ]
    };

    console.log("Labeled Example Utterance:", JSON.stringify(labeledExampleUtteranceWithMLEntity, null, 4 ));

    // Add an example for the entity.
    // Enable nested children to allow using multiple models with the same name.
    // The quantity subentity and the phraselist could have the same exact name if this is set to True
    await client.examples.add(appId, versionId, labeledExampleUtteranceWithMLEntity, { enableNestedChildren: true });
    // </AuthoringAddLabeledExamples>
}


// <AuthoringSortModelObject>
const getModelGrandchild = (model, childName, grandchildName) => {

    return model.children.find(c => c.name == childName).children.find(c => c.name == grandchildName).id

}
// </AuthoringSortModelObject>


quickstart()
    .then(result => console.log("Done"))
    .catch(err => {
        console.log(`Error: ${err}`)
    })
```

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node index.js` 命令运行应用程序。

```console
node index.js
```

