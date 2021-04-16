---
description: 每个 UWP 应用中所包含的通用设计功能都有助于生成跨各种设备完美缩放的应用。
title: Windows 应用设计简介（Windows 应用）
ms.assetid: 50A5605E-3A91-41DB-800A-9180717C1E86
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 57612b41cc1ee31d2e7fd7c8e91e7b7772dab38d
ms.sourcegitcommit: 77af97719a439f5e73a6109b42fd3110bcb2843b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107219030"
---
# <a name="introduction-to-windows-app-design"></a>Windows 应用设计简介

![示例照明应用](images/introUWP-header.jpg)

Windows 应用设计指南可以帮助你设计和构建美观、精良的应用。

它不是一份说明性规则的清单 - 它是一个动态文档，旨在适应我们不断演变的 [Fluent Design System](/windows/apps/fluent-design-system) 以及应用构建社区的需求。

此引言概述了各项 UWP 应用中包含的通用设计功能，并帮助你构建可在多台设备中完美扩展的用户界面 (UI)。

## <a name="effective-pixels-and-scaling"></a>有效像素和缩放

UWP 应用可以在所有 [Windows 10 设备](../devices/index.md)（从电视到平板电脑或电脑）上运行。 那如何设计在各种设备上以及各种尺寸的屏幕上呈现效果优秀的 UI？

![各种设备上的同一应用](images/universal-image-1.jpg)

UWP 可以发挥促进作用，即通过自动调整 UI 元素的大小，以使它们在所有设备和屏幕大小上清晰可见并易于交互。

当你的应用在设备上运行时，系统将使用算法来使 UI 元素在屏幕上的显示方式规范化。 此缩放算法考虑了观看距离和屏幕密度（每英寸像素），以针对感知大小（而不是物理大小）进行优化。 该缩放算法确保用户可从 10 英尺远处识别 Surface Hub 上高 24 像素的字体，正如从几英寸远处识别 5 英寸手机上高 24 像素的字体。

![不同设备的观看距离](images/scaling-chart.png)

基于缩放系统的工作原理，在设计 UWP 应用时，要以有效像素而不是实际物理像素为单位进行设计。 有效像素 (epx) 是一个虚拟度量单位，用于表示布局尺寸和间距（独立于屏幕密度）。 （在我们的指南中，epx、ep 和 px 可以互换使用。）

在设计时，你可以忽略像素密度和实际屏幕分辨率。 而是针对同一大小级别的有效分辨率（以有效像素为单位的分辨率）进行设计（有关详细信息，请参阅 [屏幕大小和断点文章](../layout/screen-sizes-and-breakpoints-for-responsive-design.md)）。

> [!TIP]
> 在使用图像编辑程序创建屏幕原型时，将 DPI 设置为 72，并将图像尺寸设置为目标大小级别的有效分辨率。 要获得大小类和有效分辨率的列表，请参阅[屏幕大小和断点文章](../layout/screen-sizes-and-breakpoints-for-responsive-design.md)。

### <a name="multiples-of-four"></a>四的倍数

:::row:::
    :::column span:::
在 UWP 应用中，UI 元素的大小、边距和位置应始终为 **4 epx 的倍数**。

UWP 可以跨一系列设备进行缩放，缩放比例为 100%、125%、150%、175%、200%、225%、250%、300%、350%、400%。 基础单位是 4，因为它可以整数形式缩放到这些比例（例如 4 x 125% = 5，4 x 150% = 6）。 使用 4 的倍数可以将所有 UI 元素与整个像素匹配，并可确保 UI 元素具有清晰的锐边。 （请注意，文本不会有此要求；文本的大小和位置可以是任意值。）
    :::column-end:::
    :::column:::
![网格](images/4epx.svg)
    :::column-end:::
:::row-end:::

## <a name="layout"></a>布局

由于 UWP 应用会自动扩展到所有设备，因此为任意设备设计 UWP 应用都遵循相同的结构。 我们从 UWP 应用的 UI 开始。

### <a name="windows-frames-and-pages"></a>窗口、框架和页面

:::row:::
    :::column:::
在任意 Windows 10 设备上启动 UWP 应用时，它会启动一个包含[框架](/uwp/api/windows.ui.xaml.controls.frame)（可以在[页面](/uwp/api/windows.ui.xaml.controls.page)实例间导航）的[窗口](/uwp/api/windows.ui.xaml.window)。
    :::column-end:::
    :::column:::
![带有框架的窗口的屏幕截图。](images/frame.svg)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
可以将应用的 UI 看作一组页面。 每个页面显示何种内容以及页面之间的关系由你决定。

若要了解如何组织页面，请参阅[导航基础知识](navigation-basics.md)。
    :::column-end:::
    :::column:::
![“集合”页的屏幕截图。](images/collection-pages.svg)
    :::column-end:::
:::row-end:::

### <a name="page-layout"></a>页面布局

应如何显示页面？ 大多数页面遵循一种公用结构来提供一致性，以便用户能够轻松地在应用的页面之间和页面内导航。 页面通常包含三种类型的 UI 元素：

- [导航](navigation-basics.md)元素帮助用户选择他们希望显示的内容。
- [命令](commanding-basics.md)元素启动操作，例如处理、保存或共享内容。
- [内容](content-basics.md)元素显示应用内容。

![常见布局模式](../layout/images/page-components.svg)

要详细了解如何实现常见的 UWP 应用模式，请参阅[页面布局](../layout/page-layout.md)文章。

也可以在 Visual Studio 中使用 [Windows Template Studio](https://github.com/Microsoft/WindowsTemplateStudio) 为应用设计布局。

## <a name="controls"></a>控制

UWP 的设计平台提供了一系列保证在所有支持 Windows 的设备上都能正常工作的常用控件，它们遵循 [Fluent Design 系统](/windows/apps/fluent-design-system)原则。 这些控件包括从简单控件（如按钮和文本元素）到复杂控件（可从一组数据和一个模板生成列表）的一切控件。

![UWP 控件](../style/images/color/windows-controls.svg)

有关 UWP 控件以及可从中创建的模式的完整列表，请参阅[控件和模式部分](../controls-and-patterns/index.md)部分。

## <a name="style"></a>样式

常用控件自动反映系统主题和主题色，使用所有输入类型并扩展到所有设备。 它们通过这种方式体现 Fluent Design 系统的特点 - 自适应、能够引发共鸣并且美观。 常用控件的默认样式使用了灯光、运动和深度，因此一旦使用它们，就等于将 Fluent Design 系统集成到了你的应用之中。

常用控件可以灵活自定义，可以更改控件的前景色或完全自定义其外观。 要覆盖控件中的默认样式，请使用[轻型样式设置](../controls-and-patterns/xaml-styles.md#lightweight-styling)或在 XAML 中创建[自定义控件](../controls-and-patterns/control-templates.md)。

![主题色 gif](images/intro-style.gif)

## <a name="shell"></a>Shell

:::row:::
    :::column:::
UWP 应用将与 Windows [Shell](../shell/tiles-and-notifications/creating-tiles.md) 中的磁贴和通知进行 Windows 体验范围更广泛的交互。

应用启动时，磁贴显示在“开始”菜单上，可以通过它们大致了解应用中的情况。 其功能来自背后的内容及其体现的智能和工艺。

UWP 应用具有四种磁贴大小（小、中、宽和大），可使用应用的图标和标识来自定义这几种大小。 有关为 UWP 应用设计磁贴的指南，请参阅[磁贴和图标资产指南](../style/app-icons-and-logos.md)。
    :::column-end:::
    :::column:::
![“开始”菜单上的磁贴](images/shell.svg)
    :::column-end:::
:::row-end:::

## <a name="inputs"></a>输入

:::row:::
    :::column:::
UWP 应用依赖于智能交互。 你可以围绕单击交互进行设计，而无需知道或定义该单击是来自鼠标、触笔还是手指点击。 不过，你也可以针对[特定输入模式](../input/input-primer.md)设计应用。
    :::column-end:::
    :::column:::
![指定不同输入模式的图标的屏幕截图。](images/inputs.svg)
    :::column-end:::
:::row-end:::

## <a name="devices"></a>设备

![设备](../layout/images/size-classes.svg)

同样，虽然 UWP 会自动缩放应用以适应不同的设备，但你也可以[针对特定设备优化 UWP 应用](../devices/index.md)。

## <a name="usability"></a>可用性

:::image type="content" source="https://img-prod-cms-rt-microsoft-com.akamaized.net/cms/api/am/imageFileData/REYaAb" alt-text="一个简笔卡通动画短片，其中显示了形态各异的人。":::

最后一点也很重要：可用即对所有用户开放你的应用体验。 真正具有包容性的用户体验可以让每个人受益 - 请参阅 [UWP 应用中的可用性](../usability/index.md)，了解如何让每个人都能轻松使用你的应用。

如果是面向全球用户设计，你可能需要了解[全球化和本地化](../globalizing/globalizing-portal.md)。

你还可能需要考虑面向在视觉、听觉和行动方面受限的用户的[辅助功能](../accessibility/accessibility-overview.md)。 如果辅助功能从一开始便内置于设计中，那么应该几乎不用花费额外的时间和精力就可以[使你的应用易于访问](../accessibility/accessibility-in-the-store.md)。

## <a name="tools-and-design-toolkits"></a>工具和设计工具包

现在你已了解基本的设计功能，那么如何开始设计 UWP 应用呢？

我们提供了各种工具来帮助你实施设计过程：

- 有关 XD、Illustrator、Photoshop、Framer 和 Sketch 工具包以及其他设计工具和字体下载的信息，请参阅我们的[设计工具包页面](../downloads/index.md)。

- 要设置计算机来为 UWP 应用编写代码，请参阅[入门 &gt; 准备工作](/windows/apps/get-started/get-set-up)文章。

- 有关如何实现 UWP UI 的灵感，请参阅端到端[示例 UWP 应用](https://developer.microsoft.com/windows/samples)。

## <a name="video-summary"></a>视频摘要

> [!VIDEO https://channel9.msdn.com/Blogs/One-Dev-Minute/Designing-Universal-Windows-Platform-apps/player]

## <a name="next-fluent-design-system"></a>下一步：Fluent Design System

如果要了解 Fluent Design（Microsoft 提供的设计系统）背后的原则，并查看可融入 UWP 应用中的更多功能，请继续参阅 [Fluent Design 系统](/windows/apps/fluent-design-system)。

## <a name="related-articles"></a>相关文章

- [UWP 应用是什么？](../../get-started/universal-application-platform-guide.md)
- [Fluent 设计系统](/windows/apps/fluent-design-system)
- [XAML 平台概述](../../xaml-platform/index.md)
