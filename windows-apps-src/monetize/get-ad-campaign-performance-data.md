---
ms.assetid: A26A287C-B4B0-49E9-BB28-6F02472AE1BA
description: 使用 Microsoft Store 分析 API 中的此方法，可获取给定日期范围和其他可选筛选器内指定应用程序的广告市场活动性能聚合数据。
title: 获取广告市场活动性能数据
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 服务, Microsoft Store 分析 API, 广告市场活动
ms.localizationpriority: medium
ms.openlocfilehash: fd933f103bf8964997102731b653e125fae89e12
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89162441"
---
# <a name="get-ad-campaign-performance-data"></a>获取广告市场活动性能数据


在 Microsoft Store 分析 API 中使用此方法，可获取给定日期范围和其他可选筛选器内你的应用程序的促销广告市场活动性能数据的聚合摘要。 此方法返回采用 JSON 格式的数据。

此方法返回的数据与合作伙伴中心中的 [Ad 市场活动报表](/windows/uwp/publish/ad-campaign-report) 提供的数据相同。 有关广告市场活动的详细信息，请参阅[为应用创建广告市场活动](../publish/create-an-ad-campaign-for-your-app.md)。

若要创建、更新或检索广告市场活动的详细信息，你可以使用 [Microsoft Store 推广 API](run-ad-campaigns-using-windows-store-services.md) 中的[管理广告市场活动](manage-ad-campaigns.md)方法。

## <a name="prerequisites"></a>必备条件

若要使用此方法，首先需要执行以下操作：

* 完成 Microsoft Store 分析 API 的所有[先决条件](access-analytics-data-using-windows-store-services.md#prerequisites)（如果尚未这样做）。
* [获取 Azure AD 访问令牌](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。

## <a name="request"></a>请求


### <a name="request-syntax"></a>请求语法

| 方法 | 请求 URI                                                              |
|--------|--------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/promotion``` |


### <a name="request-header"></a>请求头

| 标头        | 类型   | 描述                |
|---------------|--------|---------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** &lt;*token*&gt; 。 |


### <a name="request-parameters"></a>请求参数

若要检索特定应用的广告市场活动性能数据，请使用 *applicationId* 参数。 若要检索与你的开发者帐户关联的所有应用的广告性能数据，请忽略 *applicationId* 参数。

| 参数     | 类型   | 描述     | 必需 |
|---------------|--------|-----------------|----------|
| applicationId   | 字符串    | 要检索其 ad 市场活动性能数据的应用的 [存储 ID](in-app-purchases-and-trials.md#store-ids) 。 |    否      |
|  startDate  |  日期   |  要检索的广告市场活动性能数据日期范围中的开始日期，格式为 YYYY/MM/DD。 默认值为当前日期减去 30 天。   |   否    |
| endDate   |  日期   |  要检索的广告市场活动性能数据日期范围中的结束日期，格式为 YYYY/MM/DD。 默认值为当前日期减去 1 天。   |   否    |
| top   |  int   |  要在请求中返回的数据行数。 如果未指定，最大值和默认值为 10000。 当查询中存在多行数据时，响应正文中包含的下一个链接可用于请求下一页数据。   |   否    |
| skip   | int    |  要在查询中跳过的行数。 使用此参数可以浏览较大的数据集。 例如，top=10000 和 skip=0，将检索前 10000 行数据；top=10000 和 skip=10000，将检索之后的 10000 行数据，依此类推。   |   否    |
| filter   |  字符串   |  在响应中筛选行的一条或多条语句。 唯一受支持的筛选器为 **campaignId**。 每条语句可以使用 **eq** 或 **ne** 运算符，多条语句还可以使用 **and** 或 **or** 进行组合。  下面是一个 *filter* 参数的示例：```filter=campaignId eq '100023'```。   |   否    |
|  aggregationLevel  |  字符串   | 指定用于检索聚合数据的时间范围。 可以是以下字符串之一：<strong>day</strong>、<strong>week</strong> 或 <strong>month</strong>。 如果未指定，默认值为 <strong>day</strong>。    |   否    |
| orderby   |  字符串   |  <p>对广告市场活动性能数据的结果数据值进行排序的语句。 语法为 <em>orderby=field [order],field [order],...</em>，其中 <em>field</em> 参数可以是以下字符串之一：</p><ul><li><strong>date</strong></li><li><strong>campaignId</strong></li></ul><p><em>order</em> 参数是可选的，可以是 <strong>asc</strong> 或 <strong>desc</strong>，用于指定每个字段的升序或降序排列。 默认值为 <strong>asc</strong>。</p><p>下面是一个 <em>orderby</em> 字符串的示例：<em>orderby=date,campaignId</em></p>   |   否    |
|  groupby  |  字符串   |  <p>仅将数据聚合应用于指定字段的语句。 可以指定的字段如下所示：</p><ul><li><strong>campaignId</strong></li><li><strong>applicationId</strong></li><li><strong>date</strong></li><li><strong>currencyCode</strong></li></ul><p><em>groupby</em> 参数可以与 <em>aggregationLevel</em> 参数结合使用。 例如： <em> &amp; Groupby = applicationId &amp; aggregationLevel = week</em></p>   |   否    |


### <a name="request-example"></a>请求示例

以下示例演示用于获取广告市场活动性能数据的多个请求。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/promotion?aggregationLevel=week&groupby=applicationId,campaignId,date  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/promotion?applicationId=9NBLGGH0XK8Z&startDate=2015/1/20&endDate=2016/8/31&skip=0&filter=campaignId eq '31007388' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应


### <a name="response-body"></a>响应正文

| 值      | 类型   | 说明  |
|------------|--------|---------------|
| 值      | array  | 包含广告市场活动性能聚合数据的对象数组。 有关每个对象中的数据的详细信息，请参阅下面的[市场活动性能对象](#campaign-performance-object)部分。          |
| @nextLink  | 字符串 | 如果存在其他数据页，则此字符串包含一个你可用来请求下一页数据的 URI。 例如，当请求的 **top** 参数设置为 5，但查询的数据超过 5 项时，就会返回此值。 |
| TotalCount | int    | 查询的数据结果中的行总数。                                |


<span id="campaign-performance-object" />


### <a name="campaign-performance-object"></a>市场活动性能对象

*Value* 数组中的元素包含以下值。

| 值               | 类型   | 描述            |
|---------------------|--------|------------------------|
| date                | 字符串 | 广告市场活动性能数据的日期范围内的第一个日期。 如果请求指定了某一天，此值就是该日期。 如果请求指定了一周、月或其他日期范围，此值是该日期范围内的第一个日期。 |
| applicationId       | 字符串 | 要检索广告市场活动性能数据的应用的应用商店 ID。                     |
| campaignId     | 字符串 | 广告市场活动的 ID。           |
| lineId     | 字符串 |    生成此性能数据的广告市场活动[传递行](manage-delivery-lines-for-ad-campaigns.md)的 ID。        |
| currencyCode              | 字符串 | 市场活动预算的货币代码。              |
| spend          | 字符串 |  已为广告市场活动花费的预算金额。     |
| impressions           | long | 市场活动的广告曝光数。        |
| installs              | long | 与市场活动有关的应用安装数。   |
| clicks            | long | 市场活动的广告点击数。      |
| iapInstalls            | long | 与市场活动有关的加载项（也称为应用内购买或 IAP）安装数。      |
| activeUsers            | long | 点击市场活动中的广告并返回到应用的用户数。      |


### <a name="response-example"></a>响应示例

以下示例举例说明此请求的 JSON 响应正文。

```json
{
  "Value": [
    {
      "date": "2015-04-12",
      "applicationId": "9WZDNCRFJ31Q",
      "campaignId": "4568",
      "lineId": "0001",
      "currencyCode": "USD",
      "spend": 700.6,
      "impressions": 200,
      "installs": 30,
      "clicks": 8,
      "iapInstalls": 0,
      "activeUsers": 0
    },
    {
      "date": "2015-05-12",
      "applicationId": "9WZDNCRFJ31Q",
      "campaignId": "1234",
      "lineId": "0002",
      "currencyCode": "USD",
      "spend": 325.3,
      "impressions": 20,
      "installs": 2,
      "clicks": 5,
      "iapInstalls": 0,
      "activeUsers": 0
    }
  ],
  "@nextLink": "promotion?applicationId=9NBLGGGZ5QDR&aggregationLevel=day&startDate=2015/1/20&endDate=2016/8/31&top=2&skip=2",
  "TotalCount": 1917
}
```

## <a name="related-topics"></a>相关主题

* [为你的应用创建广告市场活动](../publish/create-an-ad-campaign-for-your-app.md)
* [使用 Microsoft Store services 运行 ad 市场活动](run-ad-campaigns-using-windows-store-services.md)
* [使用 Microsoft Store 服务访问分析数据](access-analytics-data-using-windows-store-services.md)