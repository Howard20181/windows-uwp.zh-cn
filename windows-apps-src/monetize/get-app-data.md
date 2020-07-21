---
ms.assetid: 8D4AE532-22EF-4743-9555-A828B24B8F16
description: 使用 Microsoft Store 提交 API 中的这些方法检索已注册到合作伙伴中心帐户的应用的数据。
title: 获取应用数据
ms.date: 02/28/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API, 应用数据
ms.localizationpriority: medium
ms.openlocfilehash: cfbe8df46f51b41ccdd840f609caf2c593735e1f
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210973"
---
# <a name="get-app-data"></a>获取应用数据

使用 Microsoft Store 提交 API 中的以下方法获取合作伙伴中心帐户中现有应用的数据。 有关 Microsoft Store 提交 API 的介绍（包括使用 API 的先决条件），请参阅[使用 Microsoft Store 服务创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)。

在可以使用这些方法之前，应用必须已存在于合作伙伴中心帐户中。 若要创建或管理应用提交，请参阅 [管理应用提交](manage-app-submissions.md) 中的方法。

| 方法 | URI                                                                                             | 说明                                                 |
|------- |------------------------------------------------------------------------------------------------ |------------------------------------------------------------ |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications`                                   | [获取所有应用的数据](get-all-apps.md)               |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}`                   | [获取特定应用的数据](get-an-app.md)                |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/listinappproducts` | [获取应用程序的外接程序](get-add-ons-for-an-app.md)         |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/listflights`       | [获取应用的包裹航班](get-flights-for-an-app.md) |

## <a name="prerequisites"></a>先决条件

如果尚未开始操作，请先完成 Microsoft Store 提交 API 的所有[先决条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)，然后再尝试使用其中任何方法。

## <a name="data-resources"></a>数据资源

获取应用数据的 Microsoft Store 提交 API 方法使用以下 JSON 数据资源。

<span id="application_object" />

### <a name="application-resource"></a>应用程序资源

此资源表示已注册到你的帐户的应用。

```json
{
  "id": "9NBLGGH4R315",
  "primaryName": "ApiTestApp",
  "packageFamilyName": "30481DevCenterAPITester.ApiTestAppForDevbox_ng6try80pwt52",
  "packageIdentityName": "30481DevCenterAPITester.ApiTestAppForDevbox",
  "publisherName": "CN=…",
  "firstPublishedDate": "1601-01-01T00:00:00Z",
  "lastPublishedApplicationSubmission": {
    "id": "1152921504621086517",
    "resourceLocation": "applications/9NBLGGH4R315/submissions/1152921504621086517"
  },
  "pendingApplicationSubmission": {
    "id": "1152921504621243487",
    "resourceLocation": "applications/9NBLGGH4R315/submissions/1152921504621243487"
  },
  "hasAdvancedListingPermission": true
}
```

此资源具有以下值。

| 值           | 类型    | 说明       |
|-----------------|---------|---------------------|
| id            | string  | 应用的应用商店 ID。 有关应用商店 ID 的详细信息，请参阅[查看应用标识详细信息](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details)。   |
| primaryName   | string  | 应用的显示名称。      |
| packageFamilyName | string  | 应用的程序包系列名称。      |
| packageIdentityName          | string  | 应用的程序包标识名称。                       |
| publisherName       | string  | 与应用相关联的 Windows 发布者 ID。 这对应于合作伙伴中心应用的[应用标识](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details)页上显示的 "**包/标识/发布服务器**" 值。       |
| firstPublishedDate      | string  | 应用的首次发布日期，采用 ISO 8601 格式。   |
| lastPublishedApplicationSubmission       | 对象 | 提供有关应用的上次发布提交信息的 [提交资源](#submission_object)。    |
| pendingApplicationSubmission        | 对象  |  提供有关应用的当前挂起提交信息的 [提交资源](#submission_object)。   |   
| hasAdvancedListingPermission        | boolean  |  指示你是否可以配置[gamingOptions](manage-app-submissions.md#gaming-options-object)或[预告片](manage-app-submissions.md#trailer-object)以针对该应用提交。 2017 年 5 月之后创建的提交，此值为 true。 |  |


<span id="add-on-object" />

### <a name="add-on-resource"></a>加载项资源

该资源提供有关加载项的信息。

```json
{
    "inAppProductId": "9WZDNCRD7DLK"
}
```

此资源具有以下值。

| 值           | 类型    | 说明         |
|-----------------|---------|----------------------|
| inAppProductId            | string  | 加载项的应用商店 ID。 此值由应用商店提供。 应用商店 ID 的一个示例是 9NBLGGH4TNMP。   |


<span id="flight-object" />

### <a name="flight-resource"></a>外部测试版资源

该资源提供有关应用的软件包外部测试版的信息。

```json
{
    "flightId": "7bfc11d5-f710-47c5-8a98-e04bb5aad310",
    "friendlyName": "myflight",
    "lastPublishedFlightSubmission": {
        "id": "1152921504621086517",
        "resourceLocation": "flights/7bfc11d5-f710-47c5-8a98-e04bb5aad310/submissions/1152921504621086517"
    },
    "pendingFlightSubmission": {
        "id": "1152921504621215786",
        "resourceLocation": "flights/7bfc11d5-f710-47c5-8a98-e04bb5aad310/submissions/1152921504621215786"
    },
    "groupIds": [
        "1152921504606962205"
    ],
    "rankHigherThan": "Non-flighted submission"
}
```

此资源具有以下值。

| 值           | 类型    | 说明           |
|-----------------|---------|------------------------|
| flightId            | string  | 软件包外部测试版的 ID。 此值由合作伙伴中心提供。  |
| friendlyName           | string  | 软件包外部测试版的名称，如开发人员所指定。   |
| lastPublishedFlightSubmission       | 对象 | 提供有关软件包外部测试版的上次发布提交信息的 [提交资源](#submission_object)。   |
| pendingFlightSubmission        | 对象  |  提供有关软件包外部测试版的当前挂起提交信息的[提交资源](#submission_object)。  |    
| groupIds           | 数组  | 包含与软件包外部测试版关联的外部测试版组 ID 的字符串数组。 有关外部测试版组的详细信息，请参阅[软件包外部测试版](https://docs.microsoft.com/windows/uwp/publish/package-flights)。   |
| rankHigherThan           | string  | 排名紧跟在当前软件包外部测试版之后的软件包外部测试版的友好名称。 有关排名的外部测试版组的详细信息，请参阅 [软件包外部测试版](https://docs.microsoft.com/windows/uwp/publish/package-flights)。  |


<span id="submission_object" />

### <a name="submission-resource"></a>提交资源

该资源提供有关提交的信息。 以下示例演示了此资源的格式。

```json
{
  "pendingApplicationSubmission": {
    "id": "1152921504621243487",
    "resourceLocation": "applications/9WZDNCRD9MMD/submissions/1152921504621243487"
  }
}
```

此资源具有以下值。

| 值              | 类型   | 说明               |
|--------------------|--------|---------------------------|
| id                 | string | 提交的 ID。 |
| resourceLocation   | string | 可追加到基本 ```https://manage.devcenter.microsoft.com/v1.0/my/``` 请求 URI 的相对路径，用于检索提交的完整数据。 |

 
## <a name="related-topics"></a>相关主题

* [使用 Microsoft Store services 创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)
* [使用 Microsoft Store 提交 API 管理应用提交](manage-app-submissions.md)
* [获取所有应用](get-all-apps.md)
* [获取应用](get-an-app.md)
* [获取应用程序的外接程序](get-add-ons-for-an-app.md)
* [获取应用的包裹航班](get-flights-for-an-app.md)
