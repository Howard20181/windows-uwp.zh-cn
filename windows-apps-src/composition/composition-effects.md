---
ms.assetid: 6e9b9ff2-234b-6f63-0975-1afb2d86ba1a
title: 合成效果
description: 效果 API 使开发人员可以自定义其 UI 的呈现方式。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 57236b6780a7afe996fb1e68ac474d8d8077ca69
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74255904"
---
# <a name="composition-effects"></a>合成效果

[  **Windows.UI.Composition**](https://docs.microsoft.com/uwp/api/Windows.UI.Composition) API 允许将实时效果应用于图像和附带动画效果属性的 UI。 在此概述中，我们将详细描述可用于允许效果应用于合成视觉对象的功能。

为了支持[通用 Windows 平台 \(UWP\)](https://docs.microsoft.com/windows/uwp/get-started/whats-a-uwp) 一致性以供开发人员在其应用程序中描述效果，合成效果利用 Win2D 的 IGraphicsEffect 接口通过 [Microsoft.Graphics.Canvas.Effects](https://microsoft.github.io/Win2D/html/N_Microsoft_Graphics_Canvas_Effects.htm) 命名空间来使用效果描述。

画笔效果通过将效果应用于一组现有图像来绘制应用程序的区域。 Windows 10 合成效果 API 专用于子画面视觉效果。 在创建颜色、图形和效果方面，SpriteVisual 具有灵活性和互动性。 SpriteVisual 是合成视觉对象类型，该效果可以使用画笔填充 2D 矩形。 该视觉效果定义矩形的边框，而画笔定义用于绘制矩形的像素。

将会针对合成树视觉效果（其内容来自效果图的输出）使用效果画笔。 效果可以引用现有图面/纹理，但不可以引用其他合成树的输出。

效果还可以使用效果画笔 [**XamlCompositionBrushBase**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase) 应用于 XAML UIElement。

## <a name="effect-features"></a>效果功能

- [效果库](./composition-effects.md#effect-library)
- [链接效果](./composition-effects.md#chaining-effects)
- [动画支持](./composition-effects.md#animation-support)
- [常量与动画效果属性](./composition-effects.md#constant-vs-animated-effect-properties)
- [具有独立属性的多个效果实例](./composition-effects.md#multiple-effect-instances-with-independent-properties)

### <a name="effect-library"></a>效果库

目前，合成支持以下效果：

| 作用               | 说明                                                                                                                                                                                                                |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2D 仿射变换  | 将 2D 仿射变换矩阵应用到图像。 我们使用此效果在我们的效果[示例](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects)中对 alpha 蒙板进行动画处理。       |
| 算术合成 | 使用灵活方程组合两张图像。 我们使用算术合成在我们的[示例](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects)中创建交叉淡入淡出效果。 |
| 混合效果         | 创建组合两张图像的混合效果。 合成提供 Win2D 中受支持的 21 种[混合模式](https://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_Effects_BlendEffectMode.htm)（共 26 种）。        |
| 颜色源         | 生成包含纯色的图像。                                                                                                                                                                               |
| 合成            | 组合两张图像。 合成提供 Win2D 中受支持的全部 13 种[合成模式](https://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_CanvasComposite.htm)。                                              |
| 对比度             | 增加或降低图像的对比度。                                                                                                                                                                           |
| 曝光             | 增加或降低图像的曝光。                                                                                                                                                                           |
| 灰度            | 将图像转换为单色灰度。                                                                                                                                                                                   |
| 伽玛转换       | 通过应用每个通道伽玛转换函数来更改图像的颜色。                                                                                                                                           |
| 色调旋转           | 通过旋转图像的色调值来更改其颜色。                                                                                                                                                                   |
| Invert               | 反转图像的颜色。                                                                                                                                                                                            |
| 饱和度             | 更改图像的饱和度。                                                                                                                                                                                         |
| 棕褐                | 将图像转换为棕褐色调。                                                                                                                                                                                          |
| 温度和色调 | 调整图像的温度和/或色调。                                                                                                                                                                           |

有关更多详细信息，请参阅 Win2D 的 [Microsoft.Graphics.Canvas.Effects](https://microsoft.github.io/Win2D/html/N_Microsoft_Graphics_Canvas_Effects.htm) 命名空间。 组合中不支持的效果被标注为 \[NoComposition\]。

### <a name="chaining-effects"></a>链接效果

可以链接效果，以便允许应用程序针对某张图像同时使用多个效果。 效果图支持可以引用一个和其他效果的多个效果。 当描述效果时，只需将效果作为输入添加到你的效果。

```cs
IGraphicsEffect graphicsEffect =
new Microsoft.Graphics.Canvas.Effects.ArithmeticCompositeEffect
{
  Source1 = new CompositionEffectSourceParameter("source1"),
  Source2 = new SaturationEffect
  {
    Saturation = 0,
    Source = new CompositionEffectSourceParameter("source2")
  },
  MultiplyAmount = 0,
  Source1Amount = 0.5f,
  Source2Amount = 0.5f,
  Offset = 0
}
```

上面示例说明了拥有两种输入的算术合成效果。 第二种输入具有附带 0.5 饱和度属性的效果。

### <a name="animation-support"></a>动画支持

效果属性支持动画，在效果编译期间，可以指定可进行动画处理并且可“烘焙”为常数的效果属性。 通过“effect name.property name”形式的字符串指定可进行动画处理的属性。 可以通过多个效果实例化独立地对这些属性进行动画处理。

### <a name="constant-vs-animated-effect-properties"></a>常量与动画效果属性

在效果编译期间，可以将效果属性指定为动态或作为常量的“烘焙”属性。 动态属性通过“<effect name>.<property name>”形式的字符串指定。 动态属性可设置为特定值或可使用合成动画系统进行动画处理。

当编译上面的效果描述时，你可以在饱和度等于 0.5 时灵活地进行烘焙或使其动态化，并进行动态设置或进行动画处理。

使用烘焙时采用的饱和度编译效果：

```cs
var effectFactory = _compositor.CreateEffectFactory(graphicsEffect);
```

使用动态饱和度编译效果：

```cs
var effectFactory = _compositor.CreateEffectFactory(graphicsEffect, new[]{"SaturationEffect.Saturation"});
_catEffect = effectFactory.CreateBrush();
_catEffect.SetSourceParameter("mySource", surfaceBrush);
_catEffect.Properties.InsertScalar("saturationEffect.Saturation", 0f);
```

然后可通过 Expression 或 ScalarKeyFrame 动画将上面的效果饱和度属性设置为静态值或动画处理。

你可以创建将用于对效果的“饱和度”属性进行动画处理的 ScalarKeyFrame，如下所示：

```cs
ScalarKeyFrameAnimation effectAnimation = _compositor.CreateScalarKeyFrameAnimation();
            effectAnimation.InsertKeyFrame(0f, 0f);
            effectAnimation.InsertKeyFrame(0.50f, 1f);
            effectAnimation.InsertKeyFrame(1.0f, 0f);
            effectAnimation.Duration = TimeSpan.FromMilliseconds(2500);
            effectAnimation.IterationBehavior = AnimationIterationBehavior.Forever;
```

开始对效果的“饱和度”属性进行动画处理，如下所示：

```cs
catEffect.Properties.StartAnimation("saturationEffect.Saturation", effectAnimation);
```

对于使用关键帧进行动画处理的效果属性，请参阅[去饱和度 - 动画示例](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects/Desaturation - Animation)；若要使用效果和表达式，请参阅 [AlphaMask 示例](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects/AlphaMask)。

### <a name="multiple-effect-instances-with-independent-properties"></a>具有独立属性的多个效果实例

效果编译期间，通过指定应为动态的参数，就可以根据每个效果实例更改该参数。 这允许两个视觉效果使用同一效果，但以不同的效果属性进行呈现。 有关详细信息，请参阅 ColorSource 和 Blend [示例](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/BasicCompositonEffects/ColorSource and Blend)。

## <a name="getting-started-with-composition-effects"></a>合成效果入门

本快速入门教程向你介绍如何使用效果的一些基本功能。

- [安装 Visual Studio](./composition-effects.md#installing-visual-studio)
- [创建新项目](./composition-effects.md#creating-a-new-project)
- [安装 Win2D](./composition-effects.md#installing-win2d)
- [设置你的撰写基础](./composition-effects.md#setting-your-composition-basics)
- [创建 CompositionSurface 画笔](./composition-effects.md#creating-a-compositionsurface-brush)
- [创建、编译和应用效果](./composition-effects.md#creating-compiling-and-applying-effects)

### <a name="installing-visual-studio"></a>安装 Visual Studio

- 如果尚未安装受支持版本的 Visual Studio，请转到[此处](https://visualstudio.microsoft.com/downloads/download-visual-studio-vs)的 Visual Studio 下载页。

### <a name="creating-a-new-project"></a>创建新项目

- 转到“文件”-&gt;“新建”-&gt;“项目”...
- 选择“Visual C#”
- 创建“空白应用\(Windows 通用)”\(Visual Studio 2015\)
- 输入你选择的项目名称
- 单击“确定”

### <a name="installing-win2d"></a>安装 Win2D

Win2D 发布为 Nuget.org 程序包，并且只有安装它之后才可以使用效果。

有两个程序包版本，一个适用于 Windows 10，而另一个适用于 Windows 8.1。 对于合成效果，使用 Windows 10 版本。

- 通过转到“工具”→“NuGet 程序包管理器”→“管理解决方案的 NuGet 程序包”来启动 NuGet 程序包管理器。
- 搜索“Win2D”，并选择适用于你的目标版本的 Windows 的程序包。 由于 Windows.UI。 合成支持 Windows 10（不支持 8.1），请选择 Win2D.uwp。
- 接受许可协议
- 单击“关闭”

在接下来的几个步骤中，我们将使用合成 API 来将饱和度效果应用到这张猫咪图像，这会删除所有饱和度。 在此模型中，创建该效果，然后将其应用到图像。

![源图像](images/composition-cat-source.png)
### <a name="setting-your-composition-basics"></a>设置合成基本要素

有关如何设置 Windows.UI.Composition 合成器、对 ContainerVisual 进行根处理和与核心窗口相关联的示例，请参阅我们在 GitHub 上的[合成可视化树示例](https://github.com/microsoft/WindowsCompositionSamples/tree/master/Demos/Reference Demos/CompositionImageSample)。

```cs
_compositor = new Compositor();
_root = _compositor.CreateContainerVisual();
_target = _compositor.CreateTargetForCurrentView();
_target.Root = _root;
_imageFactory = new CompositionImageFactory(_compositor)
Desaturate();
```

### <a name="creating-a-compositionsurface-brush"></a>创建 CompositionSurface 画笔

```cs
CompositionSurfaceBrush surfaceBrush = _compositor.CreateSurfaceBrush();
LoadImage(surfaceBrush);
```

### <a name="creating-compiling-and-applying-effects"></a>创建、编译和应用效果

1. 创建图形效果

    ```cs
    var graphicsEffect = new SaturationEffect
    {
      Saturation = 0.0f,
      Source = new CompositionEffectSourceParameter("mySource")
    };
    ```

1. 编译效果并创建效果画笔

    ```cs
    var effectFactory = _compositor.CreateEffectFactory(graphicsEffect);

    var catEffect = effectFactory.CreateBrush();
    catEffect.SetSourceParameter("mySource", surfaceBrush);
    ```

1. 在合成树中创建 SpriteVisual 并应用效果

    ```cs
    var catVisual = _compositor.CreateSpriteVisual();
      catVisual.Brush = catEffect;
      catVisual.Size = new Vector2(219, 300);
      _root.Children.InsertAtBottom(catVisual);
    }
    ```

1. 创建要加载的图像源。

    ```cs
    CompositionImage imageSource = _imageFactory.CreateImageFromUri(new Uri("ms-appx:///Assets/cat.png"));
    CompositionImageLoadResult result = await imageSource.CompleteLoadAsync();
    if (result.Status == CompositionImageLoadStatus.Success)
    ```

1. 调整 SpriteVisual 上的图面大小并涂刷

    ```cs
    brush.Surface = imageSource.Surface;
    ```

1. 运行你的应用：产生的效果应是一只去饱和的猫：

![去饱和图像](images/composition-cat-desaturated.png)

## <a name="more-information"></a>详细信息

- [Microsoft –组合 GitHub](https://github.com/microsoft/WindowsCompositionSamples)
- [**Windows 用户界面。** ](https://docs.microsoft.com/uwp/api/Windows.UI.Composition)
- [Twitter 上的 Windows 撰写团队](https://twitter.com/wincomposition)
- [撰写概述](https://blogs.windows.com/buildingapps/2015/12/08/awaken-your-creativity-with-the-new-windows-ui-composition/)
- [可视化树基础知识](composition-visual-tree.md)
- [组合画笔](composition-brushes.md)
- [XamlCompositionBrushBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.xamlcompositionbrushbase)
- [动画概述](composition-animation.md)
- [结合 BeginDraw 和 EndDraw 组合本机 DirectX 和 Direct2D 互操作](composition-native-interop.md)
