---
ms.assetid: C78176D6-47BB-4C63-92F8-426719A70F04
description: 在 Microsoft Store 提交 API 中使用此方法，可获取软件包外部测试版提交的状态。
title: 获取软件包外部测试版提交的状态
ms.date: 04/17/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API, 外部测试版提交, 状态
ms.localizationpriority: medium
ms.openlocfilehash: 28eb08ff5f1dd6a5c7887c100edfe2f38780f6fe
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89175011"
---
# <a name="get-the-status-of-a-package-flight-submission"></a>获取软件包外部测试版提交的状态

在 Microsoft Store 提交 API 中使用此方法，可获取软件包外部测试版提交的状态。 有关通过使用 Microsoft Store 提交 API 创建软件包外部测试版提交过程的详细信息，请参阅[管理软件包外部测试版提交](manage-flight-submissions.md)。

## <a name="prerequisites"></a>必备条件

若要使用此方法，首先需要执行以下操作：

* 如果尚未开始操作，请先完成 Microsoft Store 提交 API 的所有[先决条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)。
* [获取 Azure AD 访问令牌](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。
* 为某个应用创建包航班提交。 可以在合作伙伴中心执行此操作，也可以通过使用 [创建包航班提交](create-a-flight-submission.md) 方法来执行此操作。

## <a name="request"></a>请求

此方法具有以下语法。 请参阅以下部分，获取标头和请求正文的使用示例和描述。

| 方法 | 请求 URI                                                      |
|--------|------------------------------------------------------------------|
| GET   | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status` |


### <a name="request-header"></a>请求头

| 标头        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** &lt;*token*&gt; 。 |


### <a name="request-parameters"></a>请求参数

| 名称        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | 字符串 | 必需。 应用（包含要获取状态的软件包外部测试版提交）的应用商店 ID。 有关应用商店 ID 的详细信息，请参阅[查看应用标识详细信息](../publish/view-app-identity-details.md)。  |
| flightId | 字符串 | 必需。 软件包外部测试版（包含要获取状态的提交）的 ID。 [创建软件包外部测试版](create-a-flight.md)和[获取应用的软件包外部测试版](get-flights-for-an-app.md)请求的响应数据中包含此 ID。 对于在合作伙伴中心创建的航班，还可以在合作伙伴中心的 "航班" 页的 URL 中使用此 ID。  |
| submissionId | 字符串 | 必需。 要获取状态的提交的 ID。 此 ID 包含在[创建软件包外部测试版提交](create-a-flight-submission.md)请求的响应数据中。 对于在合作伙伴中心创建的提交，此 ID 还可用于合作伙伴中心中的提交页的 URL。  |


### <a name="request-body"></a>请求正文

请勿为此方法提供请求正文。

### <a name="request-example"></a>请求示例

以下示例演示如何获取软件包外部测试版提交的状态。

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649/status HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应

以下示例演示了成功调用此方法的 JSON 响应正文。 响应正文包含指定提交的相关信息。 有关响应正文中这些值的详细信息，请参阅以下部分。

```json
{
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
}
```

### <a name="response-body"></a>响应正文

| 值      | 类型   | 说明                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| status           | 字符串  | 提交的状态。 这可以是以下值之一： <ul><li>无</li><li>已取消</li><li>PendingCommit</li><li>CommitStarted</li><li>CommitFailed</li><li>PendingPublication</li><li>发布</li><li>已发布</li><li>PublishFailed</li><li>PreProcessing</li><li>PreProcessingFailed</li><li>认证</li><li>CertificationFailed</li><li>Release</li><li>ReleaseFailed</li></ul>   |
| statusDetails           | 对象 (object)  |  包含有关提交状态的附加详细信息，其中包括任何错误的相关信息。 有关详细信息，请参阅[状态详细信息资源](manage-flight-submissions.md#status-details-object)。 |


## <a name="error-codes"></a>错误代码

如果无法成功完成请求，该响应中会包含以下 HTTP 错误代码之一。

| 错误代码 |  说明   |
|--------|------------------|
| 404  | 找不到提交。 |
| 409  | 此应用使用的合作伙伴中心功能 [当前不受 Microsoft Store 提交 API 支持](create-and-manage-submissions-using-windows-store-services.md#not_supported)。  |


## <a name="related-topics"></a>相关主题

* [使用 Microsoft Store 服务创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)
* [管理软件包外部测试版提交](manage-flight-submissions.md)
* [获取应用提交](get-an-app-submission.md)
* [创建应用提交](create-an-app-submission.md)
* [确认应用提交](commit-an-app-submission.md)
* [更新应用提交](update-an-app-submission.md)
* [删除应用提交](delete-an-app-submission.md)