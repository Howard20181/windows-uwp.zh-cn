---
ms.assetid: dc632a4c-ce48-400b-8e6e-1dddbd13afff
description: 在 Microsoft Store 促销 API 中使用此方法管理促销性广告活动的投放渠道。
title: 管理投放渠道
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 促销 API, 广告活动
ms.localizationpriority: medium
ms.openlocfilehash: e7b370d8eea61033092d833cdf751f1dade86c6d
ms.sourcegitcommit: 5d84d8fe60e83647fa363b710916cf8b92c6e331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91878560"
---
# <a name="manage-delivery-lines"></a>管理投放渠道

在 Microsoft Store 促销 API 中使用这些方法可以创建一个或多个*投放渠道*，以便为促销性广告活动购买广告资源和投放广告。 你可以针对每个投放渠道设定目标市场、标价，并通过设定预算及与你希望启用的创意连接来判断你的预期费用。

有关投放渠道与广告活动、目标市场配置文件和创意之间关系的详细信息，请参阅[使用 Microsoft Store 服务开展广告活动](run-ad-campaigns-using-windows-store-services.md#call-the-windows-store-promotions-api)。

>**Note** &nbsp; 注意 &nbsp;必须先[使用合作伙伴中心的 " **ad 市场活动**" 页创建一个付费广告活动](./index.md)，然后在此页上至少添加一种付款方式，然后才能成功使用此 API 创建广告活动的传递行。 完成以上操作之后，你就能够使用此 API 为广告活动成功创建计费投放渠道。 使用 API 创建的 ad 活动将自动对在合作伙伴中心的 " **Ad 市场活动** " 页上选择的默认付款方式进行计费。

## <a name="prerequisites"></a>必备条件

若要使用这些方法，首先需要执行以下操作：

* 如果尚未开始操作，请先完成 Microsoft Store 促销 API 的所有[先决条件](run-ad-campaigns-using-windows-store-services.md#prerequisites)。

  > [!NOTE]
  > 作为先决条件的一部分，请确保在 [合作伙伴中心创建至少一个付费广告活动](./index.md) ，并为合作伙伴中心中的广告营销活动至少添加一个付款方式。 使用此 API 创建的交货行将自动对在合作伙伴中心的 " **Ad 市场活动** " 页上选择的默认付款方式进行计费。

* [获取 Azure AD 访问令牌](run-ad-campaigns-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在这些方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。

## <a name="request"></a>请求

这些方法具有以下 URI。

| 方法类型 | 请求 URI         |  说明  |
|--------|---------------------------|---------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/line``` |  创建新的投放渠道。  |
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/line/{lineId}``` |  编辑通过 *lineId* 指定的投放渠道。  |
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/line/{lineId}``` |  获取通过 *lineId* 指定的投放渠道。  |


### <a name="header"></a>Header

| Header        | 类型   | 说明         |
|---------------|--------|---------------------|
| 授权 | 字符串 | 必需。 Azure AD 的访问令牌，采用的格式为**持有**者 &lt; *令牌* &gt; 。 |
| 跟踪 ID   | GUID   | 可选。 跟踪调用流的 ID。                                  |


### <a name="request-body"></a>请求正文

POST 和 PUT 方法需要一个 JSON 请求正文，其中包含[投放渠道](#line)对象的必填字段以及你要设置或更改的任何其他字段。


### <a name="request-examples"></a>请求示例

下面的示例演示如何调用 POST 方法来创建投放渠道。

```json
POST https://manage.devcenter.microsoft.com/v1.0/my/promotion/line HTTP/1.1
Authorization: Bearer <your access token>

{
    "name": "Contoso App Campaign - Paid Line",
    "configuredStatus": "Active",
    "startDateTime": "2017-01-19T12:09:34Z",
    "endDateTime": "2017-01-31T23:59:59Z",
    "bidAmount": 0.4,
    "dailyBudget": 20,
    "targetProfileId": {
        "id": 310021746
    },
    "creatives": [
        {
            "id": 106851
        }
    ],
    "campaignId": 31043481,
    "minMinutesPerImp ": 1
}
```

下面的示例演示如何调用 GET 方法来检索投放渠道。

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/promotion/line/31019990  HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应

这些方法返回一个含有[投放渠道](#line)对象的 JSON 响应正文，投放渠道对象包含有关已创建、已更新或已检索的投放渠道的信息。 下面的示例展示了这些方法的响应正文。

```json
{
    "Data": {
        "id": 31043476,
        "name": "Contoso App Campaign - Paid Line",
        "configuredStatus": "Active",
        "effectiveStatus": "Active",
        "effectiveStatusReasons": [
            "{\"ValidationStatusReasons\":null}"
        ],
        "startDateTime": "2017-01-19T12:09:34Z",
        "endDateTime": "2017-01-31T23:59:59Z",
        "createdDateTime": "2017-01-17T10:28:34Z",
        "bidType": "CPM",
        "bidAmount": 0.4,
        "dailyBudget": 20,
        "targetProfileId": {
            "id": 310021746
        },
        "creatives": [
            {
                "id": 106126
            }
        ],
        "campaignId": 31043481,
        "minMinutesPerImp ": 1,
        "pacingType ": "SpendEvenly",
        "currencyId ": 732
    }
}

```


<span id="line"/>

## <a name="delivery-line-object"></a>投放渠道对象

这些方法的请求和响应正文包含以下字段。 这张表列出了 POST 或 PUT 方法请求正文中的哪些字段是只读字段（意味着不能在 PUT 方法中更改它们）以及哪些字段是必填字段。

| 字段        | 类型   |  说明      |  只读  | 默认  | POST/PUT 必填字段 |   
|--------------|--------|---------------|------|-------------|------------|
|  id   |  整型   |  投放渠道的 ID。     |   是    |      |  否      |    
|  name   |  字符串   |   投放渠道的名称。    |    否   |      |  POST     |     
|  configuredStatus   |  字符串   |  以下值之一，用于指定开发人员指定的投放渠道的状态： <ul><li>**活动**</li><li>**非活动**</li></ul>     |  否     |      |   POST    |       
|  effectiveStatus   |  字符串   |   以下值之一，用于根据系统验证情况指定投放渠道的有效状态： <ul><li>**活动**</li><li>**非活动**</li><li>**Processing**</li><li>失败</li></ul>    |    是   |      |  否      |      
|  effectiveStatusReasons   |  array   |  以下值中的一个或多个，用于指定投放渠道处于此有效状态的原因： <ul><li>**AdCreativesInactive**</li><li>**ValidationFailed**</li></ul>      |  是     |     |    否    |           
|  startDatetime   |  字符串   |  投放渠道的开始日期和时间（ISO 8601 格式）。 如果此值为过去的时间，则不能更改。     |    否   |      |    POST、PUT     |
|  endDatetime   |  字符串   |  投放渠道的结束日期和时间（ISO 8601 格式）。 如果此值为过去的时间，则不能更改。     |   否    |      |  POST、PUT     |
|  createdDatetime   |  字符串   |  投放渠道的创建日期和时间（ISO 8601 格式）。      |    是   |      |  否      |
|  bidType   |  字符串   |  此值用于指定投放渠道的出价类型。 当前，唯一受支持的值为 **CPM**。      |    否   |  CPM    |  否     |
|  bidAmount   |  Decimal   |  用于对任意广告请求出价的出价金额。      |    否   |  基于目标市场的平均 CPM（此值定期修订）。    |    否    |  
|  dailyBudget   |  Decimal   |  投放渠道的每日预算。 必须设置 *dailyBudget* 或 *lifetimeBudget*。      |    否   |      |   POST、PUT（如果未设置 *lifetimeBudget* 的话）       |
|  lifetimeBudget   |  Decimal   |   投放渠道的生存期预算。 必须设置 lifetimeBudget 或 *dailyBudget* 。      |    否   |      |   POST、PUT（如果未设置 *dailyBudget* 的话）    |
|  targetingProfileId   |  object   |  一个用于标识描述了此投放渠道所针对的用户、地理位置和广告资源类型的[目标市场配置文件](manage-targeting-profiles-for-ad-campaigns.md#targeting-profile)的对象。 此对象由指定目标市场配置文件 ID 的单个 *id* 字段组成。     |    否   |      |  否      |  
|  creatives   |  array   |  一个或多个代表与投放渠道关联的[创意](manage-creatives-for-ad-campaigns.md#creative)的对象。 此字段中的每个对象都由指定创意 ID 的单个 *id* 字段组成。      |    否   |      |   否     |  
|  campaignId   |  整型   |  父广告活动的 ID。      |    否   |      |   否     |  
|  minMinutesPerImp   |  整型   |  指定从此投放渠道向同一用户进行两次展示之间的最小时间间隔（分钟）。      |    否   |  4000    |  否      |  
|  pacingType   |  字符串   |  以下值之一，用于指定速率类型： <ul><li>**SpendEvenly**</li><li>**SpendAsFastAsPossible**</li></ul>      |    否   |  SpendEvenly    |  否      |
|  currencyId   |  整型   |  活动货币 ID。      |    是   |  开发人员帐户的货币（你不必在 POST 或 PUT 调用中指定此字段）    |   否     |      |


## <a name="related-topics"></a>相关主题

* [使用 Microsoft Store 服务开展广告市场活动](run-ad-campaigns-using-windows-store-services.md)
* [管理广告活动](manage-ad-campaigns.md)
* [管理广告活动的目标市场配置文件](manage-targeting-profiles-for-ad-campaigns.md)
* [管理广告活动的创意](manage-creatives-for-ad-campaigns.md)
* [获取广告市场活动性能数据](get-ad-campaign-performance-data.md)