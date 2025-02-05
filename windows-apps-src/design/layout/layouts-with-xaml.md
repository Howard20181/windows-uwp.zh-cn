---
description: 了解如何使用 XAML 灵活的布局系统以及自动大小调整功能、布局面板、视觉状态和单独的 UI 定义来创建响应式 UI。
title: 采用 XAML 的响应式布局
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: contperf-fy21q2
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: e2bc29acc63a5363891ad78b873db6c8311ac970
ms.sourcegitcommit: 06d59b59a95aad009acb947a0dac7432116bdb60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544647"
---
# <a name="responsive-layouts-with-xaml"></a>采用 XAML 的响应式布局

XAML 布局系统提供自动调整元素、布局面板和视觉状态大小的功能，来帮助你创建响应式 UI。 利用响应式布局，你可以使应用在具有不同的应用窗口大小、分辨率、像素密度和方向的屏幕上都具有良好的外观。 你还可以使用 XAML 对应用的 UI 进行重新定位、大小调整、重新排列、显示/隐藏、替换或重新构建，如[响应式设计技术](responsive-design.md)中所述。 在这里，我们将讨论如何使用 XAML 实现响应式布局。

## <a name="fluid-layouts-with-properties-and-panels"></a>具有属性和面板的动态布局

响应式布局的基础是合理使用 XAML 布局属性和面板，以便以动态方式对内容进行重新定位、大小调整和重新排列。

XAML 布局系统支持静态布局和动态布局。 在静态布局中，你会对控件给定明确的像素大小和位置。 当用户更改其设备的分辨率或方向时，UI 保持不变。 静态布局可对不同的外形规格和显示尺寸进行剪裁。 另一方面，动态布局可缩小、放大和重新排列，从而响应设备上的可用视觉空间。

实际上，你可以结合使用静态元素和动态元素来创建你的 UI。 你仍可以在某些位置使用静态元素和值，但应确保整体 UI 可对不同的分辨率、屏幕大小和视图做出响应。

在这里，我们将讨论如何使用 XAML 属性和布局面板创建动态布局。

### <a name="layout-properties"></a>布局属性
布局属性控制元素的大小和位置。 要创建动态布局，请对元素应用自动或成比例大小调整，并允许布局面板根据需要定位其子元素。

下面介绍了一些常见布局属性以及如何使用它们来创建动态布局。

**Height 和 Width**

[Height](/uwp/api/windows.ui.xaml.frameworkelement.height) 和 [Width](/uwp/api/windows.ui.xaml.frameworkelement.width) 属性用于指定元素的大小   。 可以使用固定的值（以有效像素为单位测量），或者可以使用自动或成比例调整大小。

自动大小调整用于调整 UI 元素的大小以适应其内容或父容器。 还可以将自动调整大小用于网格的行和列。 若要使用自动调整大小，请将 UI 元素的高度和/或宽度设置为 **自动**。

> [!NOTE]
> 元素是否根据其内容或容器调整大小取决于父容器如何处理其子级的大小调整。 有关详细信息，请参阅本文后面部分的[布局面板](#layout-panels)。

成比例大小调整（也称为比例缩放）按加权比例分配行和列的可用空间  。 在 XAML 中，比例缩放值用 \* 表示（或使用 n\* 表示加权比例缩放）  。 例如，若要在一个两列布局中指定一列比另一列宽五倍，则在 [ColumnDefinition](/uwp/api/Windows.UI.Xaml.Controls.ColumnDefinition) 元素中对 [Width](/uwp/api/windows.ui.xaml.controls.columndefinition.width) 属性分别使用“5\*”和“\*”   。

此示例在具有 4 列的 [**Grid**](/uwp/api/Windows.UI.Xaml.Controls.Grid) 中，结合使用了固定、自动、成比例调整大小。

| 列 | 调整大小 | 说明 |
| ------ | ------ | ----------- |
Column_1 | **自动** | 列会调整为适合其内容的大小。
Column_2 | * | Auto 列经过计算后，列获得剩余宽度的一部分。 Column_2 将是 Column_4 宽度的一半。
Column_3 | **44** | 该列宽度将是 44 像素。
Column_4 | **2**\* | Auto 列经过计算后，列获得剩余宽度的一部分。 Column_4 将是 Column_2 的两倍宽。

由于默认列宽度是“*”，因此无需显式设置第二列的此值。

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition/>
        <ColumnDefinition Width="44"/>
        <ColumnDefinition Width="2*"/>
    </Grid.ColumnDefinitions>
    <TextBlock Text="Column 1 sizes to its content." FontSize="24"/>
</Grid>
```

在 Visual Studio XAML 设计器中，结果如下所示。

![Visual Studio 设计器中含有 4 列网格](images/xaml-layout-grid-in-designer.png)

若要在运行时获取元素的大小，请使用只读 [ActualHeight](/uwp/api/windows.ui.xaml.frameworkelement.actualheight) 和 [ActualWidth](/uwp/api/windows.ui.xaml.frameworkelement.actualwidth) 属性，而不是 Height 和 Width   。

**大小约束**

在 UI 中使用自动调整大小时，仍可能需要对元素的大小施加约束。 可以设置 [**MinWidth**](/uwp/api/windows.ui.xaml.frameworkelement.minwidth)/[**MaxWidth**](/uwp/api/windows.ui.xaml.frameworkelement.maxwidth) 和 [**MinHeight**](/uwp/api/windows.ui.xaml.frameworkelement.minheight)/[**MaxHeight**](/uwp/api/windows.ui.xaml.frameworkelement.maxheight) 属性，以便指定用于约束元素大小，但同时允许动态调整大小的值。

在 Grid 中，MinWidth/MaxWidth 还可以与列定义一起使用，而 MinHeight/MaxHeight 可以与行定义一起使用。

**对齐方式**

使用 [**HorizontalAlignment**](/uwp/api/windows.ui.xaml.frameworkelement.horizontalalignment) 和 [**VerticalAlignment**](/uwp/api/windows.ui.xaml.frameworkelement.verticalalignment) 属性来指定应如何将元素放置在其父容器中。
- **HorizontalAlignment** 的值是 **Left**、**Center**、**Right** 和 **Stretch**。
- **VerticalAlignment** 的值是 **Top**、**Center**、**Bottom** 和 **Stretch**。

使用 **Stretch** 对齐，元素可以填充父容器提供给它们的全部空间。 这两个对齐属性的默认值均为 Stretch。 但是，某些控件（如 [**Button**](/uwp/api/Windows.UI.Xaml.Controls.Button)）会替换其默认样式中的此值。
可以将拥有子元素的任何元素唯一视为 HorizontalAlignment 和 VerticalAlignment 属性的 Stretch 值。 例如，使用默认值 Stretch 值并处于 Grid 拉伸中的元素可填充包含它的单元格。 置于 Canvas 中的相同元素会调整大小以容纳其内容。 有关每个面板如何处理 Stretch 值的详细信息，请参阅[布局面板](layout-panels.md)文章。

有关详细信息，请参阅 [对齐、边距和填充](alignment-margin-padding.md)文章以及 [**HorizontalAlignment**](/uwp/api/windows.ui.xaml.frameworkelement.horizontalalignment) 和 [**VerticalAlignment**](/uwp/api/windows.ui.xaml.frameworkelement.verticalalignment) 参考页面。

**可见性**

可以通过将元素的 [Visibility](/uwp/api/windows.ui.xaml.uielement.visibility) 属性设置为以下其中一个 [Visibility 枚举值](/uwp/api/Windows.UI.Xaml.Visibility)来显示或隐藏该元素   ：Visible 或 Collapsed   。 当元素为 Collapsed 时，它不会占用任何 UI 布局空间。

可以在代码中或视觉状态中更改元素的 Visibility 属性。 更改元素的 Visibility 后，其所有子元素也会相应更改。 可以通过折叠一个面板的同时显示另一个面板来替换 UI 部分。

> [!Tip]
> 默认情况下，当 UI 中的元素是 Collapsed 时，启动时仍会创建这些对象，即使它们不可见  。 可以延迟加载这些元素，直至通过将 **x:DeferLoadStrategy 属性** 设置为“Lazy”以使它们显示。 这可以改善启动性能。 有关详细信息，请参阅 [x:DeferLoadStrategy 属性](../../xaml-platform/x-deferloadstrategy-attribute.md)。

### <a name="style-resources"></a>样式资源

你无需在控件上单独设置每个属性值。 通常更高效的做法是将属性值分组到 [**Style**](/uwp/api/Windows.UI.Xaml.Style) 资源，并将 Style 应用到控件。 当你需要将相同的属性值应用于许多控件时更是如此。 有关使用样式的详细信息，请参阅[设置控件样式](../controls-and-patterns/xaml-styles.md)。

### <a name="layout-panels"></a>布局面板

若要定位视觉对象，你必须将它们放置在面板中或其他容器对象中。 XAML 框架提供了各种面板类（例如 [**Canvas**](/uwp/api/Windows.UI.Xaml.Controls.Canvas)、[**Grid**](/uwp/api/Windows.UI.Xaml.Controls.Grid)、[**RelativePanel**](/uwp/api/Windows.UI.Xaml.Controls.RelativePanel) 和 [**StackPanel**](/uwp/api/Windows.UI.Xaml.Controls.StackPanel)），这些类可用作容器并且允许你定位和排列其中的 UI 元素。

选择布局面板时要考虑的主要事项是面板如何设置其子元素的位置和大小。 可能还需要考虑如何交错放置重叠子元素。

下面对 XAML 框架中提供的面板控件的主要功能做一个比较。

面板控件 | 说明
--------------|------------
[**Canvas**](/uwp/api/Windows.UI.Xaml.Controls.Canvas) | **Canvas** 不支持动态 UI；控制设置子元素位置和大小的所有方面。 通常将其用于特殊情况（例如创建图形），或用于定义较大自适应 UI 的小静态区域。 可以使用代码或视觉状态来在运行时重新放置元素。<li>元素使用 Canvas.Top 和 Canvas.Left 附加属性进行绝对定位。</li><li>可以使用 Canvas.ZIndex 附加属性明确指定分层。</li><li>HorizontalAlignment/VerticalAlignment 的 Stretch 值将忽略。 如果未显式设置元素的大小，它会调整大小以容纳其内容。</li><li>如果子内容超出面板，则视觉上不会被截断。 </li><li>子内容不受面板边界限制。</li>
[**Grid**](/uwp/api/Windows.UI.Xaml.Controls.Grid) | **Grid** 支持对子元素进行动态调整大小。 可以使用代码或视觉状态来重新定位和重新排列元素。<li>元素使用 Grid.Row 和 Grid.Column 附加属性在行和列中进行排列。</li><li>通过使用 Grid.RowSpan 和 Grid.ColumnSpan 附加属性，元素可跨越多行和多列。</li><li>将遵循 HorizontalAlignment/VerticalAlignment 的 Stretch 值。 如果未明确设置元素的大小，则该元素会拉伸以填满网格单元格中的可用空间。</li><li>如果子内容超出面板，则视觉上会被截断。</li><li>由于内容大小受面板边界限制，因此可滚动的内容会显示滚动条（如果需要）。</li>
[**RelativePanel**](/uwp/api/Windows.UI.Xaml.Controls.RelativePanel) | <li>根据面板边缘或中心以及元素相互之间的间距排列元素。</li><li>使用控制面板对齐、同级对齐和同级位置的各种附加属性来定位元素。 </li><li>将忽略 HorizontalAlignment/VerticalAlignment 的 Stretch 值，除非对齐的 RelativePanel 附加属性导致拉伸（例如，元素与面板的左右两边对齐）。 如果未显式设置元素的大小，它不会拉伸，而是会调整大小以容纳其内容。</li><li>如果子内容超出面板，则视觉上会被截断。</li><li>由于内容大小受面板边界限制，因此可滚动的内容会显示滚动条（如果需要）。</li>
[**StackPanel**](/uwp/api/Windows.UI.Xaml.Controls.StackPanel) |<li>元素在单行中以垂直或水平方向进行堆叠。</li><li>在与 Orientation 属性相反的方向上，将遵循 HorizontalAlignment/VerticalAlignment 的 Stretch 值。 如果未显式设置元素的大小，则该元素会拉伸以填满可用宽度（或如果 Orientation 为 Horizontal，则为高度）。 在 Orientation 属性指定的方向上，元素会调整大小以容纳其内容。</li><li>如果子内容超出面板，则视觉上会被截断。</li><li>由于在 Orientation 属性指定的方向上内容大小不受面板边界限制，因此可滚动内容拉伸超过面板边界，但不显示滚动条。 必须显式限制子内容的高度（或宽度）以使其滚动条显示。</li>
[**VariableSizedWrapGrid**](/uwp/api/Windows.UI.Xaml.Controls.VariableSizedWrapGrid) |<li>元素以行或列排列，当达到 MaximumRowsOrColumns 值时会自动换行至新行或新列。</li><li>由 Orientation 属性指定是按行还是列排列元素。</li><li>通过使用 VariableSizedWrapGrid.RowSpan 和 VariableSizedWrapGrid.ColumnSpan 附加属性，内容可跨越多行和多列。</li><li>HorizontalAlignment/VerticalAlignment 的 Stretch 值将忽略。 根据 ItemHeight 和 ItemWidth 属性的指定设置元素大小。 如果未设置这些属性，它们将从第一个单元格的大小获取其值。</li><li>如果子内容超出面板，则视觉上会被截断。</li><li>由于内容大小受面板边界限制，因此可滚动的内容会显示滚动条（如果需要）。</li>

有关这些面板的详细信息和示例，请参阅[布局面板](layout-panels.md)。 另请参阅[响应技术示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlResponsiveTechniques)。

可以使用布局面板将你的 UI 组织到控件的逻辑组中。 当你将它们与相应的属性设置结合使用时，针对 UI 元素的自动调整大小、重新定位和重新排列，你会获得一些支持。 但是，当对窗口大小进行重大更改后，大多数 UI 布局需要进一步修改。 为此，可以使用视觉状态。

## <a name="adaptive-layouts-with-visual-states-and-state-triggers"></a>采用视觉状态和状态触发器的自适应布局
使用视觉状态可基于窗口大小或其他更改对 UI 进行重大更改。

当你的应用窗口增大或缩小超出一定限度时，你可能希望更改布局属性来重新定位、调整大小、重排、显示或者替换 UI 部分。 可以为你的 UI 定义不同的视觉状态，以便在窗口宽度或窗口高度超过指定的阈值时应用这些视觉状态。

当属性处于特定状态下时，[**VisualState**](/uwp/api/Windows.UI.Xaml.VisualState) 定义的属性值会应用到元素。 当满足特定条件时，可在应用相应 VisualState 的 [**VisualStateManager**](/uwp/api/Windows.UI.Xaml.VisualStateManager) 中对视觉状态分组。 [AdaptiveTrigger](/uwp/api/Windows.UI.Xaml.AdaptiveTrigger) 提供了一个简单方法来以 XAML 设置要应用状态的阈值（也称为“断点”）。 或者，可以在代码中调用 [VisualStateManager.GoToState](/uwp/api/windows.ui.xaml.visualstatemanager.gotostate) 方法以应用视觉状态。 下面的部分展示了这两种方法的示例。

### <a name="set-visual-states-in-code"></a>使用代码设置视觉状态

若要通过代码应用视觉状态，请调用 [**VisualStateManager.GoToState**](/uwp/api/windows.ui.xaml.visualstatemanager.gotostate) 方法。 例如，若要在应用窗口处于特定大小时应用某个状态，请处理 [**SizeChanged**](/uwp/api/windows.ui.xaml.window.sizechanged) 事件并调用 **GoToState** 以应用相应状态。

在这里，[VisualStateGroup](/uwp/api/Windows.UI.Xaml.VisualStateGroup) 包含 2 个 VisualState 定义  。 第一个 `DefaultState` 为空。 当应用时，将应用 XAML 页面中定义的值。 第二个 `WideState` 将 [**SplitView**](/uwp/api/Windows.UI.Xaml.Controls.SplitView) 的 [**DisplayMode**](/uwp/api/windows.ui.xaml.controls.splitview.displaymode) 属性更改为 **Inline** 并打开该窗格。 如果窗口宽度大于 640 个有效像素，将在 SizeChanged 事件处理程序中应用此状态。

> [!NOTE]
> Windows 不提供可使应用检测运行它的特定设备的方法。 但它可以指示运行了该应用的设备系列（移动设备、桌面设备等）、有效分辨率，以及应用的可用屏幕空间大小（应用窗口大小）。 我们建议为[屏幕大小和断点](screen-sizes-and-breakpoints-for-responsive-design.md)定义视觉状态。

```xaml
<Page ...
    SizeChanged="CurrentWindow_SizeChanged">
    <Grid>
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState x:Name="DefaultState">
                        <Storyboard>
                        </Storyboard>
                    </VisualState>

                <VisualState x:Name="WideState">
                    <Storyboard>
                        <ObjectAnimationUsingKeyFrames
                            Storyboard.TargetProperty="SplitView.DisplayMode"
                            Storyboard.TargetName="mySplitView">
                            <DiscreteObjectKeyFrame KeyTime="0">
                                <DiscreteObjectKeyFrame.Value>
                                    <SplitViewDisplayMode>Inline</SplitViewDisplayMode>
                                </DiscreteObjectKeyFrame.Value>
                            </DiscreteObjectKeyFrame>
                        </ObjectAnimationUsingKeyFrames>
                        <ObjectAnimationUsingKeyFrames
                            Storyboard.TargetProperty="SplitView.IsPaneOpen"
                            Storyboard.TargetName="mySplitView">
                            <DiscreteObjectKeyFrame KeyTime="0" Value="True"/>
                        </ObjectAnimationUsingKeyFrames>
                    </Storyboard>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <SplitView x:Name="mySplitView" DisplayMode="CompactInline"
                   IsPaneOpen="False" CompactPaneLength="20">
            <!-- SplitView content -->

            <SplitView.Pane>
                <!-- Pane content -->
            </SplitView.Pane>
        </SplitView>
    </Grid>
</Page>
```

```csharp
private void CurrentWindow_SizeChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
{
    if (e.Size.Width > 640)
        VisualStateManager.GoToState(this, "WideState", false);
    else
        VisualStateManager.GoToState(this, "DefaultState", false);
}
```

```cppwinrt
// YourPage.h
void CurrentWindow_SizeChanged(winrt::Windows::Foundation::IInspectable const& sender, winrt::Windows::UI::Xaml::SizeChangedEventArgs const& e);

// YourPage.cpp
void YourPage::CurrentWindow_SizeChanged(IInspectable const& sender, SizeChangedEventArgs const& e)
{
    if (e.NewSize.Width > 640)
        VisualStateManager::GoToState(*this, "WideState", false);
    else
        VisualStateManager::GoToState(*this, "DefaultState", false);
}

```

### <a name="set-visual-states-in-xaml-markup"></a>使用 XAML 标记设置视觉状态

在 Windows 10 之前，属性所需 [**Storyboard**](/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) 对象的 VisualState 定义发生更改，而且必须使用代码调用 **GoToState** 以应用该状态。 上述示例说明了这种情况。 你仍会看到使用此语法的多个示例，或者你可能有使用它的现有代码。

从 Windows 10 开始，可以使用此处显示的简化 [**Setter**](/uwp/api/Windows.UI.Xaml.Setter) 语法并在 XAML 标记中使用 [**StateTrigger**](/uwp/api/Windows.UI.Xaml.StateTrigger) 以应用状态。 使用状态触发器创建的简单规则可自动触发视觉状态更改以响应应用事件。

此示例执行的操作与前面示例相同，但使用简化 **Setter** 语法而不是 Storyboard 来定义属性更改。 它使用内置 [**AdaptiveTrigger**](/uwp/api/Windows.UI.Xaml.AdaptiveTrigger) 状态触发器应用状态，而不是调用 GoToState。 使用状态触发器时，无需定义一个空 `DefaultState`。 当不再满足状态触发器条件时，将自动重新应用默认设置。

```xaml
<Page ...>
    <Grid>
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState>
                    <VisualState.StateTriggers>
                        <!-- VisualState to be triggered when the
                             window width is >=640 effective pixels. -->
                        <AdaptiveTrigger MinWindowWidth="640" />
                    </VisualState.StateTriggers>

                    <VisualState.Setters>
                        <Setter Target="mySplitView.DisplayMode" Value="Inline"/>
                        <Setter Target="mySplitView.IsPaneOpen" Value="True"/>
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <SplitView x:Name="mySplitView" DisplayMode="CompactInline"
                   IsPaneOpen="False" CompactPaneLength="20">
            <!-- SplitView content -->

            <SplitView.Pane>
                <!-- Pane content -->
            </SplitView.Pane>
        </SplitView>
    </Grid>
</Page>
```

> [!Important]
> 在上一个示例中，对 Grid 元素设置了 VisualStateManager.VisualStateGroups 附加属性  。 使用 StateTrigger 时，请务必将 VisualStateGroups 附加到根元素的第一个子元素，以便触发器自动生效。 （在此处，**Grid** 是根 **Page** 元素的第一个子元素。）

### <a name="attached-property-syntax"></a>附加属性语法

在 VisualState 中，通常设置控件属性的值，或设置包含控件的面板的其中一个附加属性的值。 当设置附加属性时，请对附加属性名称使用括号。

本示例介绍了如何对名为 `myTextBox` 的 TextBox 设置 [**RelativePanel.AlignHorizontalCenterWithPanel**](/uwp/api/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithpanelproperty) 附加属性。 第一个 XAML 使用 [**ObjectAnimationUsingKeyFrames**](/uwp/api/Windows.UI.Xaml.Media.Animation.ObjectAnimationUsingKeyFrames) 语法，而第二个使用 **Setter** 语法。

```xaml
<!-- Set an attached property using ObjectAnimationUsingKeyFrames. -->
<ObjectAnimationUsingKeyFrames
    Storyboard.TargetProperty="(RelativePanel.AlignHorizontalCenterWithPanel)"
    Storyboard.TargetName="myTextBox">
    <DiscreteObjectKeyFrame KeyTime="0" Value="True"/>
</ObjectAnimationUsingKeyFrames>

<!-- Set an attached property using Setter. -->
<Setter Target="myTextBox.(RelativePanel.AlignHorizontalCenterWithPanel)" Value="True"/>
```

### <a name="custom-state-triggers"></a>自定义状态触发器

可以扩展 [**StateTrigger**](/uwp/api/Windows.UI.Xaml.StateTrigger) 类以为更大范围的方案创建自定义触发器。 例如，可以创建 StateTrigger 以根据输入类型触发不同状态，然后在输入类型为触摸时增大控件周围的边距。 或者创建 StateTrigger 以根据运行应用的设备系列来应用不同的状态。 有关如何从单个 XAML 视图中生成自定义触发器并使用它们创建优化的 UI 体验的示例，请参阅[状态触发器示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlStateTriggers)。

### <a name="visual-states-and-styles"></a>视觉状态和样式

可以在视觉状态中使用 Style 资源以将一组属性更改应用到多个控件。 有关使用样式的详细信息，请参阅[设置控件样式](../controls-and-patterns/xaml-styles.md)。

在此来自状态触发器示例的简化 XAML 中，Style 资源应用于 Button 以调整鼠标或触控输入的大小和边距。 有关自定义状态触发器的完整代码和定义，请参阅[状态触发器示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlStateTriggers)。

```xaml
<Page ... >
    <Page.Resources>
        <!-- Styles to be used for mouse vs. touch/pen hit targets -->
        <Style x:Key="MouseStyle" TargetType="Rectangle">
            <Setter Property="Margin" Value="5" />
            <Setter Property="Height" Value="20" />
            <Setter Property="Width" Value="20" />
        </Style>
        <Style x:Key="TouchPenStyle" TargetType="Rectangle">
            <Setter Property="Margin" Value="15" />
            <Setter Property="Height" Value="40" />
            <Setter Property="Width" Value="40" />
        </Style>
    </Page.Resources>

    <RelativePanel>
        <!-- ... -->
        <Button Content="Color Palette Button" x:Name="MenuButton">
            <Button.Flyout>
                <Flyout Placement="Bottom">
                    <RelativePanel>
                        <Rectangle Name="BlueRect" Fill="Blue"/>
                        <Rectangle Name="GreenRect" Fill="Green" RelativePanel.RightOf="BlueRect" />
                        <!-- ... -->
                    </RelativePanel>
                </Flyout>
            </Button.Flyout>
        </Button>
        <!-- ... -->
    </RelativePanel>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="InputTypeStates">
            <!-- Second set of VisualStates for building responsive UI optimized for input type.
                 Take a look at InputTypeTrigger.cs class in CustomTriggers folder to see how this is implemented. -->
            <VisualState>
                <VisualState.StateTriggers>
                    <!-- This trigger indicates that this VisualState is to be applied when MenuButton is invoked using a mouse. -->
                    <triggers:InputTypeTrigger TargetElement="{x:Bind MenuButton}" PointerType="Mouse" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="BlueRect.Style" Value="{StaticResource MouseStyle}" />
                    <Setter Target="GreenRect.Style" Value="{StaticResource MouseStyle}" />
                    <!-- ... -->
                </VisualState.Setters>
            </VisualState>
            <VisualState>
                <VisualState.StateTriggers>
                    <!-- Multiple trigger statements can be declared in the following way to imply OR usage.
                         For example, the following statements indicate that this VisualState is to be applied when MenuButton is invoked using Touch OR Pen.-->
                    <triggers:InputTypeTrigger TargetElement="{x:Bind MenuButton}" PointerType="Touch" />
                    <triggers:InputTypeTrigger TargetElement="{x:Bind MenuButton}" PointerType="Pen" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="BlueRect.Style" Value="{StaticResource TouchPenStyle}" />
                    <Setter Target="GreenRect.Style" Value="{StaticResource TouchPenStyle}" />
                    <!-- ... -->
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Page>
```

## <a name="related-topics"></a>相关主题

- [教程：创建自适应布局](../basics/xaml-basics-adaptive-layout.md)
- [响应性技术示例 (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlResponsiveTechniques)
- [状态触发器示例 (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlStateTriggers)
- [定制多视图示例 (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlTailoredMultipleViews)
