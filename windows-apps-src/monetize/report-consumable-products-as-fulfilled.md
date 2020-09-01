---
ms.assetid: E9BEB2D2-155F-45F6-95F8-6B36C3E81649
description: 使用 Microsoft Store 收集 API 中的此方法，以面向给定客户将可消费产品报告为已完成。 在用户可以重新购买可消费产品前，你的应用或服务必须面向该用户将可消费产品报告为已完成。
title: 将可消费产品报告为已完成。
ms.date: 03/19/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 收集 API, 完成, 可消费
ms.localizationpriority: medium
ms.openlocfilehash: 88ff4f9bd2c490c8fae4deb2cfa4cbf5c74956c8
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89174941"
---
# <a name="report-consumable-products-as-fulfilled"></a>将可消费产品报告为已完成。

使用 Microsoft Store 收集 API 中的此方法，以面向给定客户将可消费产品报告为已完成。 在用户可以重新购买可消费产品前，你的应用或服务必须面向该用户将可消费产品报告为已完成。

若要使用此方法来将可消费产品报告为已完成，可采用以下两种方式：

* 提供消费品的项目 ID（如[查询产品](query-for-products.md)的 **itemId** 参数中返回所示）和你提供的唯一跟踪 ID。 如果将同一跟踪 ID 用于多次尝试，那么即使该项目已经使用过，也仍然会返回相同的结果。 如果你不确定消耗请求是否成功，你的服务应重新提交具有同一跟踪 ID 的消耗请求。 跟踪 ID 始终与该消耗请求关联并且可以无限期地重新提交。
* 提供产品 ID（如[查询产品](query-for-products.md)的 **productId** 参数中返回所示）和从以下请求正文部分中的 **transactionId** 参数的描述中所列的源之一获取的事务 ID。

## <a name="prerequisites"></a>必备条件


若要使用此方法，你需要：

* 受众 URI 值为 `https://onestore.microsoft.com` 的 Azure AD 访问令牌。
* 表示你要为其将可消费产品报告为已完成的用户身份的 Microsoft Store ID 密钥。

有关详细信息，请参阅[管理来自服务的产品授权](view-and-grant-products-from-a-service.md)。

## <a name="request"></a>请求


### <a name="request-syntax"></a>请求语法

| 方法 | 请求 URI                                                   |
|--------|---------------------------------------------------------------|
| POST   | ```https://collections.mp.microsoft.com/v6.0/collections/consume``` |


### <a name="request-header"></a>请求头

| 标头         | 类型   | 描述                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| 授权  | 字符串 | 必需。 Azure AD 的访问令牌，采用的格式为**持有**者 &lt; *令牌* &gt; 。                           |
| 主机           | 字符串 | 必须设置为值 **collections.mp.microsoft.com**。                                            |
| Content-Length | 数字 | 请求正文的长度。                                                                       |
| Content-Type   | 字符串 | 指定请求和响应类型。 当前，唯一受支持的值为 **application/json**。 |


### <a name="request-body"></a>请求正文

| 参数     | 类型         | 描述         | 必需 |
|---------------|--------------|---------------------|----------|
| 受益人   | UserIdentity | 正在使用此项目的用户。 有关详细信息，请参阅下表。        | 是      |
| itemId        | 字符串       | [查询产品](query-for-products.md)返回的 *itemId* 值。 将此参数与 *trackingId* 一起使用      | 否       |
| trackingId    | guid         | 由开发人员提供的唯一跟踪 ID。 将此参数与 *itemId* 一起使用。         | 否       |
| productId     | 字符串       | [查询产品](query-for-products.md)返回的 *productId* 值。 将此参数与 *transactionId* 一起使用   | 否       |
| transactionId | guid         | 从以下源之一获取的事务 ID 值。 将此参数与 *productId* 一起使用。<ul><li>[PurchaseResults](/uwp/api/Windows.ApplicationModel.Store.PurchaseResults) 类的 [TransactionID](/uwp/api/windows.applicationmodel.store.purchaseresults.transactionid) 属性。</li><li>由 [RequestProductPurchaseAsync](/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync)、[RequestAppPurchaseAsync](/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync) 或 [GetAppReceiptAsync](/uwp/api/windows.applicationmodel.store.currentapp.getappreceiptasync) 返回的应用或产品收据。</li><li>[查询产品](query-for-products.md)返回的 *transactionId* 参数。</li></ul>   | 否       |


UserIdentity 对象包含以下参数。

| 参数            | 类型   | 描述       | 必需 |
|----------------------|--------|-------------------|----------|
| IdentityType         | 字符串 | 指定字符串值 **b2b**。    | 是      |
| identityValue        | 字符串 | 表示你要为其将可消费产品报告为已完成的用户身份的 [Microsoft Store ID 密钥](view-and-grant-products-from-a-service.md#step-4)。      | 是      |
| localTicketReference | 字符串 | 已返回响应的请求标识符。 建议你使用与 Microsoft Store ID 密钥中的 *userId*  [claim](view-and-grant-products-from-a-service.md#claims-in-a-microsoft-store-id-key) 相同的值。 | 是      |


### <a name="request-examples"></a>请求示例

以下示例使用 *itemId* 和 *trackingId*。

```syntax
POST https://collections.mp.microsoft.com/v6.0/collections/consume HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1…..
Host: collections.mp.microsoft.com
Content-Length: 2050
Content-Type: application/json

{
    "beneficiary": {
        "localTicketReference": "testreference",
        "identityValue": "eyJ0eXAiOi…..",
        "identityType": "b2b"
    },
    "itemId": "44c26106-4979-457b-af34-609ae97a084f",
    "trackingId": "44db79ca-e31d-49e9-8896-fa5c7f892b40"
}
```

以下示例使用 *productId* 和 *transactionId*。

```syntax
POST https://collections.mp.microsoft.com/v6.0/collections/consume HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1……
Content-Length: 1880
Content-Type: application/json
Host: collections.md.mp.microsoft.com

{
    "beneficiary" : {
        "localTicketReference" : "testReference",
        "identityValue" : "eyJ0eXAiOiJ…..",
        "identitytype" : "b2b"
    },
    "productId" : "9NBLGGH5WVP6",
    "transactionId" : "08a14c7c-1892-49fc-9135-190ca4f10490"
}
```


## <a name="response"></a>响应

如果已成功执行消耗，将不返回任何内容。

### <a name="response-example"></a>响应示例

```syntax
HTTP/1.1 204 No Content
Content-Length: 0
MS-CorrelationId: 386f733d-bc66-4bf9-9b6f-a1ad417f97f0
MS-RequestId: e488cd0a-9fb6-4c2c-bb77-e5100d3c15b1
MS-CV: 5.1
MS-ServerId: 030011326
Date: Tue, 22 Sep 2015 20:40:55 GMT
```

## <a name="error-codes"></a>错误代码


| 代码 | 错误        | 内部错误代码           | 描述           |
|------|--------------|----------------------------|-----------------------|
| 401  | 未授权 | AuthenticationTokenInvalid | Azure AD 访问令牌无效。 在某些情况下，ServiceError 的详细信息包含更多信息，例如令牌到期或 *appid* 声明丢失的时间。 |
| 401  | 未授权 | PartnerAadTicketRequired   | 在授权标头中，Azure AD 访问令牌不会 传递到服务。                                                                                                   |
| 401  | 未授权 | InconsistentClientId       | 请求正文的 Microsoft Store ID 密钥中的 *clientId* 声明与授权标头的 Azure AD 访问令牌中的 *appid* 声明不匹配。                     |

<span/> 

## <a name="related-topics"></a>相关主题

* [管理来自服务的产品授权](view-and-grant-products-from-a-service.md)
* [查询产品](query-for-products.md)
* [授予免费产品](grant-free-products.md)
* [续订 Microsoft Store ID 密钥](renew-a-windows-store-id-key.md)