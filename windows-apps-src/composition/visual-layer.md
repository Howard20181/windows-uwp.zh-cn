---
ms.assetid: a2751e22-6842-073a-daec-425fb981bafe
title: 可视化层
description: Windows.UI.Composition API 使你能够访问框架层 (XAML) 和图形层 (DirectX) 之间的合成层。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ac41d461982a39a939e460b7a81b144e5a08fdb3
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74255524"
---
# <a name="visual-layer"></a>可视化层

可视化层为图形、效果和动画提供高性能的保留模式 API，是 Windows 设备上所有 UI 的基础。 你可以采用声明性方式定义 UI，并且视觉层依赖于图形硬件加速，以确保你的内容、效果和动画以与应用的 UI 线程无关且无故障的方式呈现。

值得注意的要点：

* 熟悉的 WinRT API
* 为更动态的 UI 和交互而设计
* 符合设计工具的概念
* 无突发性能下降的线性可扩展性

Windows UWP 应用已在通过 UI 框架之一使用可视化层。 还可以非常轻松地直接将可视化层用于自定义呈现、效果和动画。

![UI 框架分层：框架层 (Windows.UI.XAML) 基于可视化层 (Windows.UI.Composition) 生成，而可视化层基于图形层 (DirectX) 生成](images/layers-win-ui-composition.png)

## <a name="whats-in-the-visual-layer"></a>可视化层中是什么？

可视化层的主要功能有：

1. **内容**：自定义绘制内容的轻型合成
1. **效果**：实时 UI 效果系统，其效果可以进行动画处理、链接和自定义
1. **动画**：独立于 UI 线程运行、与框架无关的富有表现力的动画

### <a name="content"></a>内容

内容由使用视觉对象的动画和效果系统进行托管、转换并提供进行使用。 类层次结构的底层是 [**Visual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.Visual) 类，这是应用进程中用于合成器中的视觉状态的轻型线程敏捷代理。 视觉对象的子类包括  [**system.windows.media.containervisual>** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.ContainerVisual) ，以允许儿童创建包含内容的视觉对象树和[**SpriteVisual**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.SpriteVisual) ，并可通过纯色、自定义绘制内容或视觉效果进行绘制。 这些 Visual 类型一起组成 2D UI 的可视化树结构并支持大多数可见 XAML FrameworkElement。

有关详细信息，请参阅[合成视觉对象](composition-visual-tree.md)概述。

### <a name="effects"></a>效果

可视化层中的效果系统使你可以将一系列筛选器和透明效果应用于视觉对象或视觉对象树。 这是 UI 效果系统，不要与图像和媒体效果混淆。 效果与动画系统配合使用，从而使用户可以实现效果属性的流畅动态动画（独立于 UI 线程呈现）。 可视化层中的效果提供有创意的构建基块，它们可以进行组合和动画处理，以构造定制的交互体验。

除了可动画处理的效果链之外，可视化层还支持一个照明模型，它使视觉对象可以通过响应可动画处理的光来模拟材料属性。 视觉对象还可能会投影。 照明和阴影可以组合起来以创造深度和现实感。

有关详细信息，请参阅[合成效果](composition-effects.md)概述。

### <a name="animations"></a>动画

可视化层中的动画系统使你可以移动视觉对象、对效果进行动画处理以及驱动转换、剪辑和其他属性。  它是一个框架不可知的系统，它是从头开始设计的，并具有性能。  它独立于 UI 线程运行，以确保平滑度和可伸缩性。  尽管它允许你使用熟悉的关键帧动画来驱动一段时间的属性更改，但它还允许你在不同的属性（包括用户输入）之间设置数学关系，使你可以直接制作无缝编排体验。

有关详细信息，请参阅[合成动画](composition-animation.md)概述。

### <a name="working-with-your-xaml-uwp-app"></a>使用 XAML UWP 应用

可以访问 XAML 框架创建的视觉对象，并使用 [**Windows.UI.Xaml.Hosting**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Hosting.ElementCompositionPreview) 中的 [**ElementCompositionPreview**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Hosting) 类支持可见 FrameworkElement。 请注意，框架创建的视觉对象对自定义具有一些限制。 这是因为框架在管理偏移、转换和生存期。 但是可以创建自己的视觉对象，并通过 ElementCompositionPreview 将它们附加到现有 XAML 元素（或通过将其添加到可视化树结构中某个位置的现有 ContainerVisual）。

有关详细信息，请参阅[将可视化层与 XAML 结合使用](using-the-visual-layer-with-xaml.md)概述。

### <a name="working-with-your-desktop-app"></a>使用桌面应用

您可以使用可视层来增强您的 WPF、Windows 窗体和C++ Win32 桌面应用程序的外观和功能。 可以迁移内容孤岛来使用视觉对象层，并将用户界面的其余部分保留在现有框架中。 这意味着你可以对应用程序 UI 进行重大更新和增强，而无需对现有基本代码进行大量更改。

有关详细信息，请参阅[使用可视化层实现桌面应用的现代化](/windows/apps/desktop/modernize/visual-layer-in-desktop-apps)。

## <a name="additional-resources"></a>其他资源

* [**API 的完整参考文档**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition)
* [WindowsUIDevLabs GitHub](https://github.com/microsoft/WindowsCompositionSamples) 中的高级 UI 和复合示例
* [Windows UI 组合示例库](https://www.microsoft.com/store/apps/9pp1sb5wgnww)
* [@windowsui Twitter 源](https://twitter.com/windowsui)
* 阅读 Kenny Kerr 的关于此 API 的 MSDN 文章：[图形和动画 - Windows Composition 支持 10 倍缩放](https://msdn.microsoft.com/magazine/mt590968)
