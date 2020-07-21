---
title: 2017 年 9 月 Windows 文档中的新增功能
description: 新的功能、视频和开发人员指南已添加到 2017 年 9 月 Windows 10 开发人员文档
keywords: 新增功能, 更新, 功能, 开发人员指南, Windows 10, 1709
ms.date: 09/06/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: fb63a5f7cefbccbedc09b0c74d9daea9d26b63a8
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "74734952"
---
# <a name="whats-new-in-the-windows-developer-docs-in-september-2017"></a>2017 年 9 月 Windows 开发人员文档中的新增功能

Windows 开发人员文档持续更新对整个 Windows 平台的开发人员提供的新功能的信息。 最近提供了以下功能概述、开发人员指南和示例，包含面向 Windows 开发人员的新的和更新的信息。

当然，Fall Creators Update 即将推出，敬请继续关注即将在下个月发布的大量文档！

只需在 Windows 10 上[安装工具和 SDK](https://developer.microsoft.com/windows/downloads#_blank)，你便可以随时[创建新的通用 Windows 应用](../get-started/your-first-app.md)，或了解如何使用 [Windows 上的现有应用代码](../porting/index.md)。

## <a name="features"></a>功能

### <a name="xbox-live-creators-program"></a>Xbox Live 创意者计划

Xbox Live 创意者计划现已推出，可以通过该计划轻松构建和发布可以在 Windows 10 电脑和 Xbox One 主机上运行的 UWP 游戏。 有关更多信息，请参阅 [Xbox Live 创意者计划入门](https://docs.microsoft.com/gaming/xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md)。

## <a name="developer-guidance"></a>开发人员指南

### <a name="xaml-basics-tutorials"></a>XAML 基础知识教程

我们已编写了四个 [XAML 基础知识教程](https://docs.microsoft.com/windows/uwp/design/basics/xaml-basics-ui) 以补充新的 [PhotoLab 示例](https://github.com/Microsoft/Windows-appsample-photo-lab)，包括 XAML 编程的四个核心方面：用户界面、数据绑定、自定义样式和自适应布局。 每个教程均从部分完整版的 PhotoLab 示例开始，并且分步构建最终应用的一个缺少组件。 

![显示照片库页面的 PhotoLab 示例的屏幕截图](images/PhotoLab-gallery-page.png)  

以下是新文章的快速概览：

+ [创建用户界面**介绍如何创建基本照片库界面**](https://docs.microsoft.com/windows/uwp/design/basics/xaml-basics-ui)。
+ [创建数据绑定**介绍如何将数据绑定添加到照片库中并为其填充实际图像数据**](https://docs.microsoft.com/windows/uwp/data-binding/xaml-basics-data-binding)。
+ [创建自定义样式**介绍如何向照片编辑菜单添加奇特的自定义样式**](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-basics-style)。
+ [创建自适应布局**介绍如何让照片库实现自适应，以便适用于每台设备和尺寸各异的屏幕**](https://docs.microsoft.com/windows/uwp/design/basics/xaml-basics-adaptive-layout)。

### <a name="get-started-tutorials"></a>入门教程

UWP 文档的“入门”部分已更新为[新的教程登陆页面部分](https://docs.microsoft.com/windows/uwp/get-started/create-uwp-apps)。 本部分提供了经过改进的全新“入门”结构体验，可以帮助用户轻松查找和使用最适合于他们的教程，包括上面提到的 XAML 基础知识教程。

### <a name="voice-and-tone"></a>语音和声调

我们添加了新的 [UWP 应用中的语音和声调指南](https://docs.microsoft.com/windows/uwp/in-app-help/voice-and-tone)，为你提供了有关在应用中编写文本的建议。 无论你创建什么内容，用语应亲切、友好并提供有用信息。

## <a name="samples"></a>示例

### <a name="photolab-sample"></a>PhotoLab 示例

[PhotoLab 示例](https://github.com/Microsoft/windows-appsample-photo-lab)提供基本的照片库和照片编辑体验。

![显示照片编辑页面的 PhotoLab 示例的屏幕截图](images/PhotoLab-editing-page.png)  

### <a name="customer-orders"></a>客户订单

[客户订单数据库](https://github.com/Microsoft/Windows-appsample-customers-orders-database)示例已更新为使用新的 .NET Core 2.0 和实体框架。