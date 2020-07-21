---
Description: 平移或滚动允许用户在单个视图中导航，以显示在视口内不适合的视图内容。 示例视图包括计算机的文件夹结构、文档库或相册。
title: 平移
ms.assetid: b419f538-c7fb-4e7c-9547-5fb2494c0b71
label: Panning
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b6e391354b34f00460eb5988f4e03c1ff07a9296
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970112"
---
# <a name="guidelines-for-panning"></a>平移指南


平移或滚动允许用户在单个视图中导航，以显示在视口内不适合的视图内容。 示例视图包括计算机的文件夹结构、文档库或相册。

> **重要 API**：[**Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input)、[**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input)


## <a name="dos-and-donts"></a>准则


**平移指示器和滚动条**

-   在将内容加载到你的应用之前，确保可以进行平移/滚动。

-   显示平移指示器和滚动条以提供位置和大小提示。如果你提供自定义导航功能，则隐藏它们。

    **请注意**  ，与标准滚动条不同的是，平移指示器只是有意义的。 它们并不会向输入设备显示，因此无法采用任何方式对它们进行操作。

     

**单轴平移（一维溢出）**

-   对于超出一条视口边界（垂直或水平）的内容区域使用单轴平移。

    -   用于一维项列表的垂直平移。
    -   用于项网格的水平平移。
-   如果用户必须在吸附点之间平移和停止，则请勿使用具有单轴平移的强制吸附点。 强制吸附点确保用户将停在某个吸附点处。 转而使用邻近吸附点。

**自由平移（二维溢出）**

-   对于超出两条视口边界（垂直和水平）的内容区域使用双轴平移。

    -   对于用户想在多个方向移动的非结构化内容，覆盖默认围栏行为并使用自由平移。
-   自由平移通常适用于在图像或地图内导航。

**分页视图**

-   当内容由分散的元素组成或者你想显示整个元素时，可使用强制对齐点。 这可能包括书籍或杂志、一列项或单个图像的多个页面。

    -   吸附点应该放置在每个逻辑边界处。
    -   应通过调整大小或缩放来使每个元素适合视图。

**逻辑点和关键点**

-   如果内容中存在用户可能停止的关键点或逻辑点，则使用邻近吸附点。 例如，节标题。

-   如果定义了最大和最小大小限制或边界，则使用视觉反馈演示用户何时达到或超过这些边界。

**链接嵌入或嵌套的内容**

-   将单轴平移（通常为水平方向）和列布局用于基于文本和网格的内容。 在这些情况下，内容通常在列和列之间进行包装和流，并跨 Windows 应用保持用户体验的一致性和可发现性。

-   不要使用可平移的嵌入式区域来显示文本或项列表。 因为平移指示器和滚动条仅在区域内检测到输入触摸时显示，而这不是一种直观或易发现的用户体验。

-   如果平移方向相同，请不要链接或将一个可平移区域放置在另一个可平移区域之内，如此处所示。 这可能会导致达到子区域的边界时无意中平移父区域。 考虑使平移轴垂直。

    ![演示与其容器沿同一方向滚动的嵌入式可平移区域的图像。](images/scrolling-embedded3.png)

## <a name="additional-usage-guidance"></a>其他使用指南

与使用鼠标滚动类似，通过一个或多个手指轻扫或滑动，以使用触控进行平移。 平移交互与滚动鼠标滚轮或滑动滚动框而不是单击滚动条最为相似。 除了在 API 中进行区别，或受到特定于设备的 Windows UI 要求，否则，我们将两种交互都称为平移。

> <div id="main">
> <strong>Windows 10 秋季创建者更新-行为更改</strong>默认情况下，活动笔在 Windows 应用（如触控、触摸板和被动笔）中现在滚动/平移，而不是文本选择。  
> 如果你的应用取决于以前的行为，你可以替代笔滚动，并还原为以前的行为。 有关详细信息，请参阅 <a href="https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer">ScrollViewer 类</a>的 API 参考主题。
> </div>

根据输入设备的不同，用户使用以下方式之一在可平移区域内进行平移：

-   使用鼠标、触摸板或主动笔/触笔单击滚动箭头、拖动滚动框或在滚动条内单击。
-   使用鼠标的滚轮按钮可模拟拖动滚动框。
-   扩展按钮（XBUTTON1 和 XBUTTON2）（如果鼠标支持）。
-   使用键盘箭头键可模拟拖动滚动框，使用页面按键可模拟在滚动条内单击。
-   使用触摸、触摸板或主动笔/触笔按所需方向滑动或轻扫手指。

滑动涉及按平移方向缓慢移动手指。 这将导致一对一的关系，其中内容会随着手指以相同的速度和距离进行平移。 轻扫涉及迅速滑动并抬起手指，将导致对平移动画应用以下物理情景：

-   减速（延时）：抬起手指导致平移开始减速。 这类似于滑动到一个斜面上的挡块。
-   吸收：在减速期间，如果达到吸附点或内容区域边界，则平移动量导致轻微向后弹的效果。

**平移类型**

Windows 8 支持三种类型的平移：

-   单轴 - 仅支持一个方向（水平或垂直）的平移。
-   围栏 - 支持所有方向的平移。 但是，用户超过特定方向的距离阈值后，平移限制在该轴范围内。
-   任意 - 支持所有方向的平移。

**平移 UI**

平移的交互体验对于每种输入装置有所不同，但提供类似的功能。

**可平移区域**使用 JavaScript 开发人员在设计时通过级联样式表（CSS）向 Windows 应用公开可平移区域行为。

基于检测到的输入设备，存在两种平移显示模式：

-   触摸平移指示器。
-   用于其他输入设备（包括鼠标、触摸板、键盘和触笔）的滚动条。

**注意**  仅当 touch 触点在可平移区域内时才会显示平移指示器。 同样，只有当鼠标光标、笔/触笔光标或键盘焦点位于可滚动的区域内时才能看到滚动条。

 

**平移指示器** 平移指示器类似于滚动条中的滚动框。 它们指示所显示内容与全部可平移区域的比例以及所显示内容在可平移区域中的相对位置。

下图显示了两个长度不同的可平移区域及其平移指示器。

![显示两个长度不同的可平移区域及其平移指示器的图像。](images/scrolling-indicators.png)

**平移行为**
使**点**平移与滑动手势在提起触摸触点时向交互引入延迟行为。 无需用户直接输入，内容将借助惯性继续平移，直到达到某些距离阈值为止。 使用吸附点修改此类惯性行为。

吸附点可以在你的应用内容中指定逻辑停止。 从认知的角度看，在较大的可平移区域中，吸附点为用户充当分页机制，并尽量减少因过度滑动或轻扫而带来的疲劳。 借助它们，你可以处理不精确的用户输入，并确保在视口中显示内容或关键信息的特定子集。

吸附点有两种类型：

-   邻近 - 抬起接触之后，如果延时停止在吸附点的距离阈值之内，则选择该吸附点。 平移仍然可以在邻近吸附点之间停止。
-   强制 - 所选择的吸附点紧跟抬起接触之前划过的最后一个吸附点之前或之后（取决于手势的方向和速度）。 平移必须停止于强制吸附点上。

对于能够模拟分页内容或具有可动态重组以适合视口或显示的项逻辑组的应用程序（如 Web 浏览器和相册），平移吸附点非常有用。

下图显示了平移到某个点并释放如何使内容自动平移到逻辑位置。

|                                                                |                                                                                         |                                                                                                                 |
|----------------------------------------------------------------|-----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| ![显示可平移区域的图像。](images/ux-panning-snap1.png) | ![显示要向左平移的可平移区域的图像。](images/ux-panning-snap2.png) | ![显示在逻辑吸附点上停止平移的可平移区域的图像。](images/ux-panning-snap3.png) |
| 轻扫以平移。                                                  | 抬起触摸接触。                                                                     | 可平移区域在吸附点停止，不是在抬起触摸接触的位置停止。                                |

 

**围栏** 内容可能宽于和高于屏幕设备的尺寸和分辨率。 因此，经常需要进行二维平移（水平和垂直）。 在此类情况下，围栏可以通过强调沿运动轴（垂直或水平）平移来改善用户体验。

下图演示了围栏的概念。

![具有限制平移的围栏的屏幕图](images/ux-panning-rails.png)

**链接嵌入或嵌套的内容**

当用户在嵌套在另一可缩放或可滚动元素中的元素上达到缩放或滚动限制时，你可以指定父元素是否应继续执行在其子元素中开始的缩放或滚动操作。 这称为缩放或滚动链接。

链接用于在包含一个或多个单轴的单轴内容区域或自由平移区域（当触摸点位于这些子区域中的一个子区域内时）中进行平移。 当在特定方向达到子区域的平移边界时，则父区域中会沿同一方向开始平移。

当一个可平移区域嵌套在另一个可平移区域之内时，请务必在容器和嵌入内容之间指定足够的空间。 在下图中，一个可平移区域放置在另一个可平移区域之内，并且平移方向彼此垂直。 每个区域中必须有足够的空间才能供用户进行平移。

![演示嵌入的可平移区域的图像。](images/scrolling-embedded.png)

如果没有足够的空间，如下图所示，则嵌入的可平移区域可能会干扰容器中的平移，并导致一个或多个可平移区域中出现意外平移的情况。

![演示嵌入的可平移区域填充不足的图像。](images/ux-panning-embedded-wrong.png)

本指南也可用于相册或地图应用，这些应用支持在单个图像或地图中进行不受约束的平移，同时还支持相册中的单轴平移（转到上一个或下一个图像）或详细信息区域中的单轴平移。 在提供对应自由形式的平移图像或地图的详细信息或选项区域的应用中，我们建议使用详细信息和选项区域开始进行页面布局，因为图像或地图的不受约束的平移区域可能会干扰向详细信息区域平移。

## <a name="related-articles"></a>相关文章

- [自定义用户交互](https://docs.microsoft.com/windows/uwp/design/layout/index)
- [优化 ListView 和 GridView](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-gridview-and-listview)
- [键盘辅助功能](https://docs.microsoft.com/windows/uwp/accessibility/keyboard-accessibility)

**示例**
- [基本输入示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [低延迟输入示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [用户交互模式示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [焦点视觉对象示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

**存档示例**
- [输入：XAML 用户输入事件示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20XAML%20user%20input%20events%20sample)
- [输入：设备功能示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/Input%20Device%20capabilities%20sample%20(Windows%208))
- [输入：触摸点击测试示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20desktop%20samples/%5BC%2B%2B%5D-Windows%208%20desktop%20samples/C%2B%2B/Windows%208%20desktop%20samples/Input%20Touch%20hit%20testing%20sample)
- [XAML 滚动、平移和缩放示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Universal%20Windows%20app%20samples/111487-Universal%20Windows%20app%20samples/XAML%20scrolling%2C%20panning%2C%20and%20zooming%20sample)
- [输入：简化的墨迹示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Simplified%20ink%20sample)
- [输入：Windows 8 手势示例](https://docs.microsoft.com/samples/browse/?redirectedfrom=MSDN-samples)
- [输入：操作和笔势示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Gestures%20and%20manipulations%20with%20GestureRecognizer)
- [DirectX 触控输入示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%2B%2B%5D-Windows%208%20app%20samples/C%2B%2B/Windows%208%20app%20samples/DirectX%20touch%20input%20sample%20(Windows%208))
