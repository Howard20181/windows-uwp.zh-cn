---
Description: 查看与 Microsoft Store 分配给应用程序的唯一标识相关的详细信息，并获取应用商店列表的链接。
title: 查看应用标识的详细信息
ms.assetid: 86F05A79-EFBC-4705-9A71-3A056323AC65
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 07c2d3308d204d37e246a9a56c0a7203a1340dc0
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210423"
---
# <a name="view-app-identity-details"></a>查看应用标识的详细信息


可以通过**应用标识**页面上的 Microsoft Store 查看与分配给应用的唯一标识相关的详细信息。 你还可以在此页上获取应用商店列表的链接。

若要找到此信息，请导航到其中一个应用，然后展开左侧导航菜单中的“应用管理”。 选中**应用标识**查看这些详细信息。


## <a name="values-to-include-in-your-app-package-manifest"></a>要包含在应用程序包清单中的值

以下值必须包含在包清单中。 如果[使用 Microsoft Visual Studio 生成程序包](/windows/msix/package/packaging-uwp-apps)，并使用与你的开发者帐户关联的相同 Microsoft 帐户登录，则会自动包含这些详细信息。 如果手动生成程序包，则需要将以下各项添加到程序包中：

-   **包/标识/名称**
-   **包/标识/发布服务器**
-   **Package/Properties/PublisherDisplayName**

有关详细信息，请参阅[程序包清单架构参考](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root)中的 [**Identity**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity)。

同时，这些元素声明应用的标识、建立了所有程序包所属于的“程序包系列”。 单个程序包将具有其他详细信息，如体系结构和版本。


## <a name="additional-values-for-package-family"></a>程序包系列的其他值

以下值是指应用的程序包系列的其他值，但不包含在清单内。

-   **包系列名称（PFN）** ：此值用于某些 Windows Api。
-   **包 SID**：需要此值才能向应用程序发送 WNS 通知。 有关详细信息，请参阅 [Windows 推送通知服务 (WNS) 概述](../design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview.md)。


## <a name="link-to-your-apps-listing"></a>链接到应用一览

可以共享应用的页面直接链接来帮助客户在应用商店中查找该应用。 此链接采用格式 **`https://www.microsoft.com/store/apps/<your app's Store ID>`** 。 当客户单击此链接时，它将打开基于 Web 的应用一览页。 在 Windows 设备上，应用商店应用还将启动并显示应用一览。

你的应用的**应用商店 ID** 也会在本部分中显示。 此应用商店 ID 可以用来[生成应用商店锁屏提醒](https://developer.microsoft.com/store/badges)或标识你的应用。

**应用商店协议链接**可直接链接到应用商店中你的应用，无需打开浏览器，例如可从应用内部链接至你的应用。 有关详细信息，请参阅[链接到你的应用](link-to-your-app.md)。



 

 




