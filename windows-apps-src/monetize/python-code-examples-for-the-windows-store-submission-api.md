---
ms.assetid: 8AC56AAF-8D8C-4193-A6B3-BB5D0669D994
description: 使用此部分中的 Python 代码示例了解有关使用 Microsoft Store 提交 API 的详细信息。
title: 用于提交应用、加载项和航班的 Python 代码
ms.date: 07/10/2017
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 提交 API, 代码示例, python
ms.localizationpriority: medium
ms.openlocfilehash: 3fd29164dd91199b1557496985577ea956893250
ms.sourcegitcommit: 7e8dfd83b181fe720b4074cb42adc908e1ba5e44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811262"
---
# <a name="python-sample-submissions-for-apps-add-ons-and-flights"></a>Python 示例：应用、加载项和外部测试版的提交

本文提供 Python 代码示例演示如何使用 [Microsoft Store 提交 API](create-and-manage-submissions-using-windows-store-services.md) 执行以下任务：

* [获取 Azure AD 访问令牌](#token)
* [创建加载项](#create-add-on)
* [创建软件包外部测试版](#create-package-flight)
* [创建应用提交](#create-app-submission)
* [创建加载项提交](#create-add-on-submission)
* [创建软件包外部测试版提交](#create-flight-submission)

<span id="token" />

## <a name="obtain-an-azure-ad-access-token"></a>获取 Azure AD 访问令牌

以下示例演示如何[获取 Azure AD 访问令牌](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)，你可以使用此令牌在 Microsoft Store 提交 API 中调用方法。 获取令牌后，可以在 60 分钟的令牌有效期内，使用该令牌调用“Microsoft Store 提交 API”。 该令牌到期后，可以重新生成一个。

:::code language="python" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_Submission/python/Examples.py" range="1-20":::

<span id="create-add-on" />

## <a name="create-an-add-on"></a>创建加载项

以下示例演示如何[创建](create-an-add-on.md)再[删除](delete-an-add-on.md)加载项。

:::code language="python" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_Submission/python/Examples.py" range="26-52":::

<span id="create-package-flight" />

## <a name="create-a-package-flight"></a>创建软件包外部测试版

以下示例演示如何[创建](create-a-flight.md) 和[删除](delete-a-flight.md) 软件包外部测试版。

:::code language="python" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_Submission/python/Examples.py" range="58-87":::

<span id="create-app-submission" />

## <a name="create-an-app-submission"></a>创建应用提交

以下示例介绍如何使用 Microsoft Store 提交 API 中的多种方法创建应用提交。 为此，该代码会创建一个新的提交，作为上次发布的提交的克隆，然后将克隆的提交更新并提交到合作伙伴中心。 具体来说，示例执行以下任务：

1. 首先，此示例[获取指定应用的数据](get-an-app.md)。
2. 接下来，此方法会[删除应用的挂起提交](delete-an-app-submission.md)（如果存在）。
3. 然后，此方法会[创建新的应用提交](create-an-app-submission.md)（新提交时是上次发布的提交副本）。
4. 它会更改新提交的某些详细信息，并上传新包以提交到 Azure Blob 存储。
5. 接下来，它将 [更新](update-an-app-submission.md) 并 [提交](commit-an-app-submission.md) 到合作伙伴中心的新提交。
6. 最后，它会定期[检查新提交的状态](get-status-for-an-app-submission.md)，直到其成功提交。

:::code language="python" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_Submission/python/Examples.py" range="93-166":::

<span id="create-add-on-submission" />

## <a name="create-an-add-on-submission"></a>创建加载项提交

以下示例介绍如何使用 Microsoft Store 提交 API 中的多种方法创建加载项提交。 为此，该代码会创建一个新的提交，作为上次发布的提交的克隆，然后将克隆的提交更新并提交到合作伙伴中心。 具体来说，示例执行以下任务：

1. 首先，此示例[获取指定加载项的数据](get-an-add-on.md)。
2. 接下来，此方法会[删除加载项的挂起提交](delete-an-add-on-submission.md)（如果存在）。
3. 然后，该方法会 [创建新的加载项提交](create-an-add-on-submission.md)（新提交是上次发布的提交的副本）。
4. 它上传包含用于提交到 Azure Blob 存储的图标的 ZIP 存档。 有关详细信息，请参阅 [创建外接程序提交](manage-add-on-submissions.md#create-an-add-on-submission)中有关将 ZIP 存档上传到 Azure Blob 存储的相关说明。
5. 接下来，它将 [更新](update-an-add-on-submission.md) 并 [提交](commit-an-add-on-submission.md) 到合作伙伴中心的新提交。
6. 最后，它会定期[检查新提交的状态](get-status-for-an-add-on-submission.md)，直到其成功提交。

:::code language="python" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_Submission/python/Examples.py" range="172-245":::

<span id="create-flight-submission" />

## <a name="create-a-package-flight-submission"></a>创建软件包外部测试版提交

以下示例介绍如何使用 Microsoft Store 提交 API 中的多种方法创建软件包外部测试版提交。 为此，该代码会创建一个新的提交，作为上次发布的提交的克隆，然后将克隆的提交更新并提交到合作伙伴中心。 具体来说，示例执行以下任务：

1. 首先，此示例[获取指定软件包外部测试版的数据](get-a-flight.md)。
2. 接下来，此方法会[删除软件包外部测试版的挂起提交](delete-a-flight-submission.md)（如果存在）。
3. 然后，此方法[会创建新的软件包外部测试版提交](create-a-flight-submission.md)（新提交时是上次发布的提交副本）。
4. 它将上载新包，以便提交到 Azure Blob 存储。 有关详细信息，请参阅 [创建包航班提交](manage-flight-submissions.md#create-a-package-flight-submission)中有关将 ZIP 存档上传到 Azure Blob 存储的相关说明。
5. 接下来，它将 [更新](update-a-flight-submission.md) 并 [提交](commit-a-flight-submission.md) 到合作伙伴中心的新提交。
6. 最后，它会定期[检查新提交的状态](get-status-for-a-flight-submission.md)，直到其成功提交。

:::code language="python" source="~/../snippets-windows/windows-uwp/monetize/StoreServicesExamples_Submission/python/Examples.py" range="251-325":::

## <a name="related-topics"></a>相关主题

* [使用 Microsoft Store 服务创建和管理提交](create-and-manage-submissions-using-windows-store-services.md)
