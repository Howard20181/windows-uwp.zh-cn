---
description: 使用此部分中的 C# 代码示例了解有关使用 Microsoft Store 提交 API 提交游戏选项和预报片的详细信息。
title: C# 示例 - 使用游戏选项和预告片的应用提交
ms.date: 07/10/2017
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API, 代码示例, 游戏选项, 预告片, 高级应用一览, C#
ms.localizationpriority: medium
ms.openlocfilehash: 16d51a12c6d703be3a76c1c90d5ab6a2e9442603
ms.sourcegitcommit: c3ca68e87eb06971826087af59adb33e490ce7da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89363190"
---
# <a name="c-sample-app-submission-with-game-options-and-trailers"></a>C \# 示例：带游戏选项和尾端的应用提交

本文提供 C# 代码示例演示如何使用 [Microsoft Store 提交 API](create-and-manage-submissions-using-windows-store-services.md) 执行以下任务：

* 获取要用于 Microsoft Store 提交 API 的 Azure AD 访问令牌。
* 创建应用提交
* 配置用于应用提交的应用商店一览数据，包括[游戏](manage-app-submissions.md#gaming-options-object)和[预告片](manage-app-submissions.md#trailer-object)高级应用一览选项。
* 上传 ZIP 文件，其中包含程序包、应用一览图像和用于应用提交的预告片文件。
* 确认应用提交。

你可以查看每个示例，了解有关它所演示的任务的详细信息，也可以将本文中的所有代码示例生成到控制台应用程序。 要生成示例，请在 Visual Studio 中创建名为 **DevCenterApiSample** 的 C# 控制台应用程序、将每个示例复制到项目中单独的代码文件，然后生成该项目。

## <a name="prerequisites"></a>先决条件

这些示例有以下要求：

* 在你的项目中添加对 System.Web 程序集的引用。
* 从 Newtonsoft 向你的项目中安装 [Newtonsoft.Json](https://www.newtonsoft.com/json) NuGet 程序包。

<span id="create-app-submission" />

## <a name="create-an-app-submission"></a>创建应用提交

```CreateAndSubmitSubmissionExample``` 类定义公用 ```Execute``` 方法，该方法调用其他示例方法，以使用 Microsoft Store 提交 API 来创建并确认包含游戏选项和预告片的应用提交。 要调整此代码以供自己使用，请执行以下操作：

* 将 ```tenantId``` 变量指定为你应用的租户 ID，将 ```clientId``` 和 ```clientSecret``` 变量指定为你应用的客户端 ID 和密钥。 有关详细信息，请参阅 [如何将 Azure AD 应用程序与合作伙伴中心帐户关联](create-and-manage-submissions-using-windows-store-services.md#how-to-associate-an-azure-ad-application-with-your-partner-center-account)
* 将 ```applicationId``` 变量指定为要为其创建提交的应用的[应用商店 ID](in-app-purchases-and-trials.md#store-ids)。

> [!div class="tabbedCodeSnippets"]
:::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_SubmissionAdvancedListings/cs/CreateAndSubmitSubmissionExample.cs" id="CreateAndSubmitSubmissionExample":::

<span id="token" />

## <a name="obtain-an-azure-ad-access-token"></a>获取 Azure AD 访问令牌

```DevCenterAccessTokenClient``` 类定义一个帮助程序方法，该方法使用你的 ```tenantId```、```clientId``` 和 ```clientSecret``` 值来创建要用于 Microsoft Store 提交 API 的 Azure AD 访问令牌。

> [!div class="tabbedCodeSnippets"]
:::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterAccessTokenClient.cs" id="DevCenterAccessTokenClient":::

<span id="utilities" />

## <a name="helper-methods-to-invoke-the-submission-api-and-upload-submission-files"></a>用于调用提交 API 和上传提交文件的帮助程序方法

```DevCenterClient``` 类定义帮助程序方法，这些方法在 Microsoft Store 提交 API 中调用多种方法并上传一个 ZIP 文件，其中包含应用提交的程序包、应用一览图像和预告片文件。

> [!div class="tabbedCodeSnippets"]
:::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_SubmissionAdvancedListings/cs/DevCenterClient.cs" id="DevCenterClient":::

## <a name="related-topics"></a>相关主题

* [使用 Microsoft Store 服务创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)
