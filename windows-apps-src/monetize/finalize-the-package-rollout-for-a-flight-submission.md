---
description: 使用 Microsoft Store 提交 API 中的此方法完成软件包外部测试版提交的软件包推出。
title: 完成外部测试版提交的推出
ms.date: 04/17/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API, 软件包推出, 外部测试版提交, 完成
ms.assetid: e4a645f6-1f00-4af5-80d6-d2ee179acc8a
ms.localizationpriority: medium
ms.openlocfilehash: a60da322ccd76989b43d13c336ecde26671c6873
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89171522"
---
# <a name="finalize-the-rollout-for-a-flight-submission"></a>完成外部测试版提交的推出


使用 Microsoft Store 提交 API 中的此方法为软件包外部测试版提交[完成软件包推出](../publish/gradual-package-rollout.md#completing-the-rollout)。 有关通过使用 Microsoft Store 提交 API 创建软件包外部测试版提交过程的详细信息，请参阅[管理软件包外部测试版提交](manage-flight-submissions.md)。


## <a name="prerequisites"></a>必备条件

若要使用此方法，首先需要执行以下操作：

* 如果尚未开始操作，请先完成 Microsoft Store 提交 API 的所有[先决条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)。
* [获取 Azure AD 访问令牌](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。
* 为合作伙伴中心中的应用创建提交。 可以在合作伙伴中心执行此操作，也可以使用 [创建应用提交](create-an-app-submission.md) 方法来执行此操作。
* 启用提交的逐步软件包推出。 可以在 [合作伙伴中心](../publish/gradual-package-rollout.md)执行此操作，也可以通过 [使用 Microsoft Store 提交 API](manage-flight-submissions.md#manage-gradual-package-rollout)来实现此目的。

## <a name="request"></a>请求

此方法具有以下语法。 请参阅以下部分，获取标头和请求参数的使用示例和描述。

| 方法 | 请求 URI                                                      |
|--------|------------------------------------------------------------------|
| POST   | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/finalizepackagerollout` |


### <a name="request-header"></a>请求头

| 标头        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** &lt;*token*&gt; 。 |


### <a name="request-parameters"></a>请求参数

| 名称        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | 字符串 | 必需。 应用（包含要完成软件包推出的软件包外部测试版提交）的应用商店 ID。 有关应用商店 ID 的详细信息，请参阅[查看应用标识详细信息](../publish/view-app-identity-details.md)。  |
| flightId | 字符串 | 必需。 软件包外部测试版（包含要完成软件包推出的提交）的 ID。 [创建软件包外部测试版](create-a-flight.md)和[获取应用的软件包外部测试版](get-flights-for-an-app.md)请求的响应数据中包含此 ID。 对于在合作伙伴中心创建的航班，还可以在合作伙伴中心的 "航班" 页的 URL 中使用此 ID。 |
| submissionId | 字符串 | 必需。 要完成软件包推出的提交的 ID。 此 ID 包含在[创建软件包外部测试版提交](create-a-flight-submission.md)请求的响应数据中。 对于在合作伙伴中心创建的提交，此 ID 还可用于合作伙伴中心中的提交页的 URL。  |


### <a name="request-body"></a>请求正文

请勿为此方法提供请求正文。

### <a name="request-example"></a>请求示例

以下示例演示如何完成软件包外部测试版提交的软件包推出。

```json
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243680/finalizepackagerollout HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应

以下示例演示了成功调用此方法的 JSON 响应正文。 有关响应正文中这些值的更多详细信息，请参阅[软件包推出资源](manage-flight-submissions.md#package-rollout-object)。

```json
{
    "isPackageRollout": true,
    "packageRolloutPercentage": 100.0,
    "packageRolloutStatus": "PackageRolloutComplete",
    "fallbackSubmissionId": "1212922684621243058"
}
```

## <a name="error-codes"></a>错误代码

如果无法成功完成请求，该响应中会包含以下 HTTP 错误代码之一。

| 错误代码 |  说明   |
|--------|------------------|
| 404  | 找不到软件包外部测试版提交。 |
| 409  | 此代码指示以下错误之一：<br/><br/><ul><li>对于逐步推出操作，该提交未处于有效状态（在调用此方法之前，必须发布该提交，并且必须将 [packageRolloutStatus](manage-flight-submissions.md#package-rollout-object) 值设置为 **PackageRolloutInProgress**）。</li><li>提交不属于指定应用。</li><li>此应用使用的合作伙伴中心功能 [当前不受 Microsoft Store 提交 API 支持](create-and-manage-submissions-using-windows-store-services.md#not_supported)。</li></ul> |   


## <a name="related-topics"></a>相关主题

* [逐步部署程序包](../publish/gradual-package-rollout.md)
* [使用 Microsoft Store 提交 API 管理软件包外部测试版提交](manage-flight-submissions.md)
* [使用 Microsoft Store 服务创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)