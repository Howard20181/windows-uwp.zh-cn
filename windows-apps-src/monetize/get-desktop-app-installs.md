---
description: 使用此 REST URI 可获取给定日期范围内的桌面应用程序的聚合安装数据和其他可选筛选器。
title: 获取桌面应用程序安装
ms.date: 03/01/2018
ms.topic: article
keywords: windows 10，桌面应用程序安装，Windows 桌面应用程序
localizationpriority: medium
ms.openlocfilehash: 1c4f7aaffc9e623d4863bee4de44daa75ae05852
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89175021"
---
# <a name="get-desktop-application-installs"></a>获取桌面应用程序安装


使用此 REST URI 以 JSON 格式获取已添加到 [Windows 桌面应用](/windows/desktop/appxpkg/windows-desktop-application-program)程序的桌面应用程序的聚合安装数据。 此 URI 使你能够在给定的日期范围和其他可选筛选器中获取安装数据。 此信息也可在合作伙伴中心的桌面应用程序 [安装报告](/windows/desktop/appxpkg/windows-desktop-application-program) 中找到。

## <a name="prerequisites"></a>必备条件


若要使用此方法，首先需要执行以下操作：

* 完成 Microsoft Store 分析 API 的所有[先决条件](access-analytics-data-using-windows-store-services.md#prerequisites)（如果尚未这样做）。
* [获取 Azure AD 访问令牌](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。

## <a name="request"></a>请求


### <a name="request-syntax"></a>请求语法

| 方法 | 请求 URI       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/installbasedaily```|


### <a name="request-header"></a>请求头

| 标头        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** &lt;*token*&gt; 。 |


### <a name="request-parameters"></a>请求参数

| 参数        | 类型   |  描述      |  必需  
|---------------|--------|---------------|------|
| applicationId | 字符串 | 要检索其安装数据的桌面应用程序的产品 ID。 若要获取桌面应用程序的产品 ID，请在合作伙伴中心 (（例如 "**安装" 报表**) 中）打开[适用于桌面应用程序的任何分析报表](/windows/desktop/appxpkg/windows-desktop-application-program)，并从 URL 中检索产品 id。 |  是  |
| startDate | 日期 | 要检索的安装数据日期范围中的开始日期。 默认值为90天之前的当前日期。 |  否  |
| endDate | 日期 | 要检索的安装数据日期范围中的结束日期。 默认值为当前日期。 |  否  |
| top | int | 要在请求中返回的数据行数。 如果未指定，最大值和默认值为 10000。 当查询中存在多行数据时，响应正文中包含的下一个链接可用于请求下一页数据。 |  否  |
| skip | int | 要在查询中跳过的行数。 使用此参数可以浏览较大的数据集。 例如，top=10000 和 skip=0，将检索前 10000 行数据；top=10000 和 skip=10000，将检索之后的 10000 行数据，依此类推。 |  否  |
| filter | 字符串  | 在响应中筛选行的一条或多条语句。 每条语句包含的响应正文中的字段名称和值使用 **eq** 或 **ne** 运算符进行关联，并且语句可以使用 **and** 或 **or** 进行组合。 *filter* 参数中的字符串值必须使用单引号引起来。 可以指定响应正文中的以下字段：<p/><ul><li><strong>applicationVersion</strong></li><li><strong>deviceType</strong></li><li><strong>营销</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li></ul> | 否   |
| orderby | 字符串 | 对每个安装的结果数据值进行排序的语句。 语法为 <em>orderby=field [order],field [order],...</em>，其中 <em>field</em> 参数可以是以下响应正文字段之一：<p/><ul><li><strong>productName</strong></li><li><strong>date</strong><li><strong>applicationVersion</strong></li><li><strong>deviceType</strong></li><li><strong>营销</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>installBase</strong></li></ul><p><em>order</em> 参数是可选的，可以是 <strong>asc</strong> 或 <strong>desc</strong>，用于指定每个字段的升序或降序排列。 默认值为 <strong>asc</strong>。</p><p>下面是一个 <em>orderby</em> 字符串的示例：<em>orderby=date,market</em></p> |  否  |
| groupby | 字符串 | 仅将数据聚合应用于指定字段的语句。 可以指定响应正文中的以下字段：<p/><ul><li><strong>applicationVersion</strong></li><li><strong>deviceType</strong></li><li><strong>营销</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li></ul><p>返回的数据行会包含 <em>groupby</em> 参数中指定的字段，以及以下字段：</p><ul><li><strong>applicationId</strong></li><li><strong>date</strong></li><li><strong>productName</strong></li><li><strong>installBase</strong></li></ul></p> |  否  |


### <a name="request-example"></a>请求示例

下面的示例演示了一些用于获取桌面应用程序安装数据的请求。 用桌面应用程序的产品 ID 替换 *applicationId* 值。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/installbasedaily?applicationId=1234567890&startDate=2018-01-01&endDate=2018-02-01&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/installbasedaily?applicationId=1234567890&startDate=2018-01-01&endDate=2018-02-01&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应


### <a name="response-body"></a>响应正文

| 值      | 类型   | 说明                  |
|------------|--------|-------------------------------------------------------|
| 值      | array  | 包含聚合安装数据的对象数组。 有关每个对象中的数据的详细信息，请参阅下表。 |
| @nextLink  | 字符串 | 如果存在其他数据页，则此字符串包含一个你可用来请求下一页数据的 URI。 例如，当请求的 **top** 参数设置为 10000，但查询的安装数据超过 10000 行时，就会返回此值。 |
| TotalCount | int    | 查询的数据结果中的行总数。 |


*Value* 数组中的元素包含以下值。

| 值               | 类型   | 描述                           |
|---------------------|--------|-------------------------------------------|
| date                | 字符串 | 与安装基础值相关联的日期。 |
| applicationId       | 字符串 | 检索其安装数据的桌面应用程序的产品 ID。 |
| productName         | 字符串 | 从关联可执行文件的元数据派生的桌面应用程序的显示名称。 |
| applicationVersion  | 字符串 | 已安装的应用程序可执行文件的版本。 |
| deviceType          | 字符串 | 指定安装桌面应用程序的设备类型的以下字符串之一：<p/><ul><li><strong>计算机</strong></li><li><strong>Server</strong></li><li><strong>平板电脑</strong></li><li><strong>Unknown</strong></li></ul> |
| market              | string | 安装桌面应用程序的市场的 ISO 3166 国家/地区代码。 |
| osVersion           | 字符串 | 用于指定在其上安装桌面应用程序的操作系统版本的以下字符串之一：<p/><ul><li><strong>Windows 7</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>Windows Server 2016</strong></li><li><strong>Windows Server 1709</strong></li><li><strong>Unknown</strong></li></ul>   |
| osRelease           | 字符串 | 用于指定安装了桌面应用程序的操作系统版本或外部测试 Ring（作为操作系统版本内的亚组）的以下字符串之一。<p/><p>对于 Windows 10：</p><ul><li><strong>版本1507</strong></li><li><strong>版本1511</strong></li><li><strong>版本1607</strong></li><li><strong>版本1703</strong></li><li><strong>版本1709</strong></li><li><strong>Release Preview</strong></li><li><strong>预览体验成员 - 快</strong></li><li><strong>预览体验成员 - 慢</strong></li></ul><p/><p>对于 Windows Server 1709：</p><ul><li><strong>RTM</strong></li></ul><p>对于 Windows Server 2016：</p><ul><li><strong>版本1607</strong></li></ul><p>对于 Windows 8.1：</p><ul><li><strong>更新 1</strong></li></ul><p>对于 Windows 7：</p><ul><li><strong>Service Pack 1</strong></li></ul><p>如果操作系统版本或外部测试 Ring 未知，则此字段的值为 <strong>Unknown</strong>。</p> |
| installBase         | 数字 | 将产品安装在指定聚合级别上的不同设备的数目。 |


### <a name="response-example"></a>响应示例

以下示例举例说明此请求的 JSON 响应正文。

```json
{
  "Value": [
    {
      "date": "2018-01-24",
      "applicationId": "123456789",
      "productName": "Contoso Demo",
      "applicationVersion": "1.0.0.0",
      "deviceType": "PC",
      "market": "All",
      "osVersion": "Windows 10",
      "osRelease": "Version 1709",
      "installBase": 348218.0
    }
  ],
  "@nextLink": "desktop/installbasedaily?applicationId=123456789&startDate=2018-01-01&endDate=2018-02-01&top=10000&skip=10000&groupby=applicationVersion,deviceType,osVersion,osRelease",
  "TotalCount": 23012
}
```

## <a name="related-topics"></a>相关主题

* [Windows 桌面应用程序](/windows/desktop/appxpkg/windows-desktop-application-program)