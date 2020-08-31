---
ms.assetid: 24C5F796-5FB8-4B5D-B428-C3154B3098BD
description: 在 Microsoft Store 提交 API 中使用此方法，更新现有软件包外部测试版提交。
title: 更新软件包外部测试版提交
ms.date: 04/17/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API, 外部测试版提交, 更新
ms.localizationpriority: medium
ms.openlocfilehash: d2603319e2a1fc242210f79ef38c2ba5362c9c3e
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89171331"
---
# <a name="update-a-package-flight-submission"></a>更新软件包外部测试版提交


在 Microsoft Store 提交 API 中使用此方法，更新现有软件包外部测试版提交。 使用此方法成功更新提交后，必须[确认提交](commit-a-flight-submission.md)才可以实现引入和发布。

有关此方法如何适用通过使用 Microsoft Store 提交 API 创建软件包外部测试版提交过程的详细信息，请参阅[管理软件包外部测试版提交](manage-flight-submissions.md)。

## <a name="prerequisites"></a>必备条件

若要使用此方法，首先需要执行以下操作：

* 如果尚未开始操作，请先完成 Microsoft Store 提交 API 的所有[先决条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)。
* [获取 Azure AD 访问令牌](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。
* 为某个应用创建包航班提交。 可以在合作伙伴中心执行此操作，也可以通过使用 [创建包航班提交](create-a-flight-submission.md) 方法来执行此操作。

## <a name="request"></a>请求

此方法具有以下语法。 请参阅以下部分，获取标头和请求正文的使用示例和描述。

| 方法 | 请求 URI                                                      |
|--------|------------------------------------------------------------------|
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}``` |


### <a name="request-header"></a>请求头

| 标头        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** &lt;*token*&gt; 。 |


### <a name="request-parameters"></a>请求参数

| 名称        | 类型   | 描述                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | 字符串 | 必需。 要更新软件包外部测试版提交的应用的应用商店 ID。 有关应用商店 ID 的详细信息，请参阅[查看应用标识详细信息](../publish/view-app-identity-details.md)。  |
| flightId | 字符串 | 必需。 要更新提交的软件包外部测试版的 ID。 [创建软件包外部测试版](create-a-flight.md)和[获取应用的软件包外部测试版](get-flights-for-an-app.md)请求的响应数据中包含此 ID。 对于在合作伙伴中心创建的航班，还可以在合作伙伴中心的 "航班" 页的 URL 中使用此 ID。  |
| submissionId | 字符串 | 必需。 要更新的提交的 ID。 此 ID 包含在[创建软件包外部测试版提交](create-a-flight-submission.md)请求的响应数据中。 对于在合作伙伴中心创建的提交，此 ID 还可用于合作伙伴中心中的提交页的 URL。  |


### <a name="request-body"></a>请求正文

请求正文具有以下参数。

| 值      | 类型   | 描述                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| flightPackages           | array  | 包含提供提交中关于每个程序包详细信息的对象。 有关响应正文中这些值的更多详细信息，请参阅[外部测试版软件包资源](manage-flight-submissions.md#flight-package-object)。 调用此方法更新应用提交时，请求正文中仅需要这些对象的 *fileName*、*fileStatus*、*minimumDirectXVersion* 和 *minimumSystemRam* 值。 其他值由合作伙伴中心填充。 |
| packageDeliveryOptions    | 对象 (object)  | 包含提交的逐步软件包推出和强制更新设置。 有关详细信息，请参阅[软件包传递选项对象](manage-flight-submissions.md#package-delivery-options-object)部分。  |
| targetPublishMode           | 字符串  | 提交的发布模式。 这可以是以下值之一： <ul><li>即时</li><li>手动</li><li>SpecificDate</li></ul> |
| targetPublishDate           | 字符串  | 提交的发布日期采用 ISO 8601 格式（如果 *targetPublishMode* 设为“SpecificDate”）。  |
| notesForCertification           | 字符串  |  提供认证测试人员的其他信息，例如测试帐户凭据以及访问和验证功能的步骤。 有关详细信息，请参阅[认证说明](../publish/notes-for-certification.md)。 |


### <a name="request-example"></a>请求示例

以下示例演示如何为应用更新软件包外部测试版提交。

```json
PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649 HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
      "fileStatus": "PendingUpload",
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

## <a name="response"></a>响应

以下示例演示了成功调用此方法的 JSON 响应正文。 该响应正文包含已更新提交的相关信息。 有关响应正文中这些值的更多详细信息，请参阅[软件包外部测试版提交资源](manage-flight-submissions.md#flight-submission-object)。

```json
{
  "id": "1152921504621243649",
  "flightId": "cd2e368a-0da5-4026-9f34-0e7934bc6f23",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
      "fileStatus": "PendingUpload",
      "id": "",
      "version": "1.0.0.0",
      "languages": ["en-us"],
      "capabilities": [],
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/8b389577-5d5e-4cbe-a744-1ff2e97a9eb8?sv=2014-02-14&sr=b&sig=wgMCQPjPDkuuxNLkeG35rfHaMToebCxBNMPw7WABdXU%3D&se=2016-06-17T21:29:44Z&sp=rwl",
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

## <a name="error-codes"></a>错误代码

如果无法成功完成请求，该响应中会包含以下 HTTP 错误代码之一。

| 错误代码 |  说明   |
|--------|------------------|
| 400  | 由于请求无效，无法更新软件包外部测试版提交。 |
| 409  | 由于应用的当前状态，无法更新包航班提交，或者该应用使用的合作伙伴中心功能 [当前不受 Microsoft Store 提交 API 支持](create-and-manage-submissions-using-windows-store-services.md#not_supported)。 |   


## <a name="related-topics"></a>相关主题

* [使用 Microsoft Store 服务创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)
* [管理软件包外部测试版提交](manage-flight-submissions.md)
* [获取软件包外部测试版提交](get-a-flight-submission.md)
* [创建软件包外部测试版提交](create-a-flight-submission.md)
* [确认软件包外部测试版提交](commit-a-flight-submission.md)
* [删除软件包外部测试版提交](delete-a-flight-submission.md)
* [获取软件包外部测试版提交的状态](get-status-for-a-flight-submission.md)