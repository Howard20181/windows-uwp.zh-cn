---
title: 2017 年 8 月 Windows 文档中的新增功能 - 开发 UWP 应用
description: 新的功能、视频和开发人员指南已添加到 2017 年 8 月 Windows 10 开发人员文档
keywords: 新增功能, 更新, 功能, 开发人员指南, Windows 10, 1708
ms.date: 08/03/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 824cdb74a809ae3f9ae2ed202a9889535021f711
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75684725"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2017"></a>2017 年 8 月 Windows 开发人员文档中的新增功能

Windows 开发人员文档持续更新对整个 Windows 平台的开发人员提供的新功能的信息。 最近提供了以下功能概述、开发人员指南和视频，包含面向 Windows 开发人员的新的和更新的信息。

只需在 Windows 10 上[安装工具和 SDK](https://developer.microsoft.com/windows/downloads#_blank)，你便可以随时[创建新的通用 Windows 应用](../get-started/your-first-app.md)，或了解如何使用 [Windows 上的现有应用代码](../porting/index.md)。

## <a name="features"></a>功能

### <a name="windows-template-studio"></a>Windows Template Studio

使用适用于 Visual Studio 2019 的新 [Windows Template Studio](https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio) 扩展快速生成具有所需页面、框架和功能的 UWP 应用。 此基于向导的体验实现成熟的模式和最佳做法，以便为你节省时间和更加轻松地在你的应用中添加功能。

![Windows Template Studio](images/template-studio.png)

### <a name="conditional-xaml"></a>条件 XAML

现在可以预览[条件 XAML](../debug-test-perf/conditional-xaml.md) 以创建[版本自适应应用](../debug-test-perf/version-adaptive-apps.md)。 使用条件 XAML，可以在 XAML 标记中使用 ApiInformation.IsApiContractPresent 方法，这样就可以基于是否存在 API 在标记中设置属性并例示对象，而无需在之后使用代码。

### <a name="game-mode"></a>游戏模式

适用于通用 Windows 平台 (UWP) 的[游戏模式](https://docs.microsoft.com/previous-versions/windows/desktop/gamemode/game-mode-portal) API 让你可以利用 Windows 10 中的游戏模式产生最优化的游戏体验。 这些 API 位于 **&lt;expandedresources.h&gt;** 标头中。

![游戏模式](images/game-mode.png)

### <a name="submission-api-supports-video-trailers-and-gaming-options"></a>提交 API 支持视频预告片和游戏选项

[Microsoft Store 提交 API](../monetize/create-and-manage-submissions-using-windows-store-services.md) 现在可以让你在应用提交中包括[视频预告片](../monetize/manage-app-submissions.md#trailer-object)和[游戏选项](../monetize/manage-app-submissions.md#gaming-options-object)。


## <a name="developer-guidance"></a>开发人员指南

### <a name="data-schemas-for-store-products"></a>Microsoft Store 产品的数据架构

我们添加了 [Store 产品的数据架构](../monetize/data-schemas-for-store-products.md)文章。 此文章提供了对 [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) 命名空间中的多个对象可用的与 Store 相关的数据的架构，包括 [StoreProduct](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct) 和 [StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense)。

### <a name="desktop-bridge"></a>桌面桥

我们添加了两个指南帮助你添加让 Windows 10 用户感到喜悦的现代化体验。

请参阅[增强用于 Windows 10 的桌面应用程序](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-enhance)查找和引用正确的文件，然后编写能够增强 Windows 10 用户 UWP 体验的代码。  

请参阅[使用现代 UWP 组件扩展桌面应用程序](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-extend)，以整合必须在 UWP 应用容器中运行的现代 XAML UI 以及其他的 UWP 体验。

### <a name="getting-started-with-point-of-service"></a>服务点入门

我们添加了帮助你[了解服务点设备](https://docs.microsoft.com/windows/uwp/devices-sensors/pos-get-started)的新指南。 它涵盖的主题包括设备枚举、检查设备功能、声明设备和设备共享等。 

### <a name="xbox-live"></a>Xbox Live

我们添加了面向 Xbox Live 开发人员、适用于 UWP 和 Xbox 开发人员工具包 (XDK) 游戏的文档。

请参阅 [Xbox Live 开发人员指南](https://docs.microsoft.com//gaming/xbox-live/index)了解如何使用 Xbox Live API 将游戏连接到 Xbox Live 社交游戏网络。

借助 [Xbox Live 创意者计划](https://docs.microsoft.com//gaming/xbox-live/get-started-with-creators/get-started-with-xbox-live-creators)，任何 UWP 游戏开发人员都可以在电脑和 Xbox One 上开发和发布支持 Xbox Live 的游戏。

请参阅 [Xbox Live 开发人员计划概述](https://docs.microsoft.com//gaming/xbox-live/developer-program-overview)，获取有关 Xbox Live 开发人员可用程序和功能的信息。

## <a name="videos"></a>视频

### <a name="mixed-reality"></a>混合现实

发布了适用于 [Microsoft HoloLens 课程 250](https://developer.microsoft.com/windows/mixed-reality/mixed_reality_250) 的一系列新的教程视频。 如果你已安装工具并熟悉开发混合现实的基础知识，请查看这些视频课程以获取关于跨混合现实设备创建共享体验的信息。

### <a name="narrator-and-dev-mode"></a>讲述人和开发人员模式

你可能已知道可以使用[讲述人](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator)测试应用的屏幕阅读体验。 但“讲述人”还提供开发人员模式，以显示对其公开的信息的良好的视觉表示形式。 [观看视频](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Narrator-and-Dev-Mode)，然后详细了解[讲述人开发人员模式](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Narrator-and-Dev-Mode)。

### <a name="windows-template-studio"></a>Windows Template Studio

[此视频](https://channel9.msdn.com/Blogs/One-Dev-Minute/Getting-Started-with-Windows-Template-Studio)提供了关于 Windows Template Studio 的更详细概述。 准备就绪后，请[安装扩展](https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio)或[查看源代码和文档](https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio)。