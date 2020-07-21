---
Description: 使用布局面板来为应用中的 UI 元素排列和分组。
title: Windows 应用的布局面板
ms.date: 04/02/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e5a796f95efb418f7d70062ca2d73bbea7220d95
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234851"
---
# <a name="layout-panels"></a>布局面板

布局面板是用来为应用中的 UI 元素排列和分组的容器。 内置 XAML 布局面板包括 [**RelativePanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel)、[**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel)、[**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)、[**VariableSizedWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VariableSizedWrapGrid) 和 [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas)。 在此处，我们介绍每个面板并显示如何使用它设置 XAML UI 元素的布局。

选择布局面板时有多个需要考虑的事项：
- 面板如何定位其子元素。
- 面板如何调整其子元素的大小。
- 如何交错放置重叠子元素（z 顺序）。
- 创建所需布局时需要的嵌套面板元素的数量和复杂度。

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请参阅<a href="xamlcontrolsgallery:/item/RelativePanel">RelativePanel</a><a href="xamlcontrolsgallery:/item/StackPanel">StackPanel</a>、<a href="xamlcontrolsgallery:/item/Grid">Grid</a>、<a href="xamlcontrolsgallery:/item/VariableSizedWrapGrid">VariableSizedWrapGrid</a> 和 <a href="xamlcontrolsgallery:/item/Canvas">Canvas</a> 实际操作。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="panel-properties"></a>面板属性

在讨论各个面板之前，让我们了解一下所有面板都具有的一些常见属性。 

### <a name="panel-attached-properties"></a>面板附加属性

大多数 XAML 布局面板使用附加属性，使其子元素通知父面板应如何在 UI 中放置它们。 附加属性使用语法 *AttachedPropertyProvider.PropertyName*。 如果你有嵌套在其他面板内的面板，则 UI 元素上用于将布局特征指定到父项的附加属性仅由关系最直接的父面板解释。

以下是一个有关如何才能在 XAML 中的 Button 控件上设置 [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left) 附加属性的示例。 这会通知父项 Canvas 应将 Button 放置在距 Canvas 的左边缘 50 个有效像素的位置。

```xaml
<Canvas>
  <Button Canvas.Left="50">Hello</Button>
</Canvas>
```

有关附加属性的详细信息，请参阅[附加属性概述](../../xaml-platform/attached-properties-overview.md)。

### <a name="panel-borders"></a>面板边框

RelativePanel、StackPanel 和 Grid 面板定义边框属性，可使你在面板周围绘制边框，而无需将它们包装在其他 Border 元素中。 这些边框属性为 **BorderBrush**、**BorderThickness**、**CornerRadius** 和 **Padding**。

下面是如何在 Grid 上设置边框属性的示例。

```xaml
<Grid BorderBrush="Blue" BorderThickness="12" CornerRadius="12" Padding="12">
    <TextBlock Text="Hello World!"/>
</Grid>
```

![带有边框的 Grid](images/layout-panel-grid-border.png)

使用内置边框属性可以减少 XAML 元素计数，从而改进应用的 UI 性能。 有关布局面板和 UI 性能的详细信息，请参阅[优化你的 XAML 布局](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-your-xaml-layout)。

## <a name="relativepanel"></a>RelativePanel

[RelativePanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel) 允许你设置 UI 元素的布局，方法是指定它们相对于其他元素的位置和相对于面板的位置。 默认情况下，将一个元素放置在面板的左上角。 可以将 RelativePanel 与 [VisualStateManager](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualStateManager) 和 [AdaptiveTrigger](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.AdaptiveTrigger) 一起使用，以针对不同的窗口大小重新排列 UI。

此表显示了可用于相对于面板或其他元素对齐某个元素的附加属性。

面板对齐 | 同级对齐 | 同级位置
----------------|-------------------|-----------------
[**AlignTopWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.aligntopwithpanelproperty) | [**AlignTopWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.aligntopwithproperty) | [**Above**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel)  
[**AlignBottomWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignbottomwithpanelproperty) | [**AlignBottomWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignbottomwithproperty) | [**Below**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.belowproperty)  
[**AlignLeftWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel) | [**AlignLeftWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.getalignleftwith) | [**LeftOf**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.leftofproperty)  
[**AlignRightWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignrightwithpanelproperty) | [**AlignRightWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignrightwithproperty) | [**RightOf**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.setrightof)  
[**AlignHorizontalCenterWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithpanelproperty) | [**AlignHorizontalCenterWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithproperty) | &nbsp;   
[**AlignVerticalCenterWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignverticalcenterwithpanelproperty) | [**AlignVerticalCenterWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignverticalcenterwithproperty) | &nbsp;   

 
此 XAML 演示了如何排列 RelativePanel 中的元素。

```xaml
<RelativePanel BorderBrush="Gray" BorderThickness="1">
    <Rectangle x:Name="RedRect" Fill="Red" Height="44" Width="44"/>
    <Rectangle x:Name="BlueRect" Fill="Blue"
               Height="44" Width="88"
               RelativePanel.RightOf="RedRect" />

    <Rectangle x:Name="GreenRect" Fill="Green" 
               Height="44"
               RelativePanel.Below="RedRect" 
               RelativePanel.AlignLeftWith="RedRect" 
               RelativePanel.AlignRightWith="BlueRect"/>
    <Rectangle Fill="Orange"
               RelativePanel.Below="GreenRect" 
               RelativePanel.AlignLeftWith="BlueRect" 
               RelativePanel.AlignRightWithPanel="True"
               RelativePanel.AlignBottomWithPanel="True"/>
</RelativePanel>
```

结果如下所示。 

![相对面板](images/layout-panel-relative-panel.png)

以下是一些在调整矩形大小时需要注意的事项：
- 红色矩形给定 44x44 的显式大小。 它放置在面板的左上角，该位置是默认位置。
- 绿色矩形给定 44 的显式高度。 它的左边与红色矩形对齐，它的右边与蓝色矩形对齐，这决定了它的宽度。
- 橙色矩形未给定显式大小。 它的左边与蓝色矩形对齐。 它的右边和底边与面板的边缘对齐。 它的大小由这些对齐来决定，而且它将随着面板调整大小而调整大小。

## <a name="stackpanel"></a>StackPanel

[StackPanel](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) 可以将其子元素按水平或垂直方向排列到单行中的布局面板。 StackPanel 通常用于在页面上排列一小部分 UI。

你可以使用 [**Orientation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.stackpanel.orientation) 属性来指定子元素的方向。 默认方向是 [**Vertical**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Orientation)。

以下 XAML 显示了如何创建项目的垂直 StackPanel。

```xaml
<StackPanel>
    <Rectangle Fill="Red" Height="44"/>
    <Rectangle Fill="Blue" Height="44"/>
    <Rectangle Fill="Green" Height="44"/>
    <Rectangle Fill="Orange" Height="44"/>
</StackPanel>
```


结果如下所示。

![堆栈面板](images/layout-panel-stack-panel.png)

在 StackPanel 中，如果未明确设置子元素的大小，则该元素会拉伸以填满可用宽度（或如果 Orientation 为 **Horizontal**，则为高度）。 在本示例中，未设置矩形的宽度。 扩展矩形以填充 StackPanel 的整个宽度。

## <a name="grid"></a>网格

[Grid](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) 面板支持动态布局并允许你以多行或多列布局排列控件。 可以通过使用 [RowDefinitions](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.rowdefinitions) 和 [ColumnDefinitions](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.columndefinitions) 属性来指定 Grid 的行和列。

要将对象放置到 Grid 的特定单元格中，请使用 [Grid.Column](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.column) 和 [Grid.Row](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.row) 附加属性。

要使内容分散到多个行和列中，请使用 [Grid.RowSpan](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/ms605035(v=vs.95)) 和 [Grid.ColumnSpan](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.columnspan) 附加属性。

此 XAML 示例显示了如何创建包含两行和两列的网格。

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition/>
        <RowDefinition Height="44"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <Rectangle Fill="Red" Width="44"/>
    <Rectangle Fill="Blue" Grid.Row="1"/>
    <Rectangle Fill="Green" Grid.Column="1"/>
    <Rectangle Fill="Orange" Grid.Row="1" Grid.Column="1"/>
</Grid>
```


结果如下所示。

![网格](images/layout-panel-grid.png)

在此示例中，大小调整的工作方式如下所示： 
- 第二行的显式高度为 44 个有效像素。 默认情况下，第一行的高度将填充所遗留的任何空间。
- 第一列的宽度设置为 **Auto**，因此它是其子项所需的宽度。 在此情况下，它是 44 个有效像素的宽度，以适应红色矩形的宽度。
- 对矩形没有其他大小约束，因此每一个都进行拉伸以填充它所在的网格单元格。

通过使用 **Auto** 缩放或比例缩放，你可以分配列或行中的空间。 自动调整大小可用于 UI 元素调整大小以适应其内容或父容器。 还可以将自动调整大小用于网格的行和列。 若要使用自动调整大小，请将 UI 元素的高度和/或宽度设置为**自动**。

你可以使用成比例调整大小（也称为*比例缩放*）以按加权比例来分配网格的行和列的可用空间。 在 XAML 中，比例缩放值用 \* 表示（或使用 n\* 表示加权比例缩放）。 例如，若要在一个两列布局中指定一列比另一列宽五倍，则在 [ColumnDefinition](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ColumnDefinition) 元素中对 [Width](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.columndefinition.width) 属性分别使用“5\*”和“\*” 。

此示例在具有 4 列的 [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) 中，结合使用了固定、自动、成比例调整大小。

&nbsp;|&nbsp;|&nbsp;
------|------|------
Column_1 | **Auto** | 列会调整为适合其内容的大小。
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
    <TextBlock Text="Column 1 sizes to its conent." FontSize="24"/>
</Grid>
```

在 Visual Studio XAML 设计器中，结果如下所示。

![Visual Studio 设计器中含有 4 列网格](images/xaml-layout-grid-in-designer.png)

## <a name="variablesizedwrapgrid"></a>VariableSizedWrapGrid

[VariableSizedWrapGrid](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VariableSizedWrapGrid) 是一个网格样式的布局面板，当达到 [MaximumRowsOrColumns](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.maximumrowsorcolumns) 值时，网格中的行或列会自动换行至新行或新列。 

[  **Orientation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.orientation) 属性在换行前指定网格将在行还是列中添加其项目。 默认方向为 **Vertical**，这意味着网格将从上到下添加项目，直到列填满，然后换行到新列。 当该值为 **Horizontal** 时，网格从左到右添加项目，然后换行到新行。

单元格尺寸由 [**ItemHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.itemheight) 和 [**ItemWidth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.itemwidth) 指定。 每个单元格的大小相同。 如果未指定 ItemHeight 或 ItemWidth，则第一个单元格调整大小以适应其内容，并且每个其他单元格都采用第一个单元格的大小。

你可以使用 [**VariableSizedWrapGrid.ColumnSpan**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid) 和 [**VariableSizedWrapGrid.RowSpan**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.getrowspan) 附加属性来指定子元素应填充多少个相邻单元格。

以下是在 XAML 中使用 VariableSizedWrapGrid 的方法。

```xaml
<VariableSizedWrapGrid MaximumRowsOrColumns="3" ItemHeight="44" ItemWidth="44">
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" 
               VariableSizedWrapGrid.RowSpan="2"/>
    <Rectangle Fill="Green" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
    <Rectangle Fill="Orange" 
               VariableSizedWrapGrid.RowSpan="2" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
</VariableSizedWrapGrid>
```


结果如下所示。

![大小可变的换行网格](images/layout-panel-variable-size-wrap-grid.png)

在此示例中，每列中的最大行数是 3。 第一列仅包含 2 个项目（红色和蓝色矩形），因为蓝色矩形跨 2 个行。 然后绿色矩形换行到下一列的顶部。

## <a name="canvas"></a>画布

[Canvas](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) 面板使用固定的坐标点定位其子元素并且不支持动态布局。 通过在每个元素上设置 [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left) 和 [**Canvas.Top**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.top) 附加属性，你可以在单独子元素上指定点。 父级 Canvas 在布局的 [Arrange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) 传递期间从其子级中读取这些附加属性值。

Canvas 中的对象可以重叠，即在一个对象顶部绘制另一个对象。 默认情况下，Canvas 以声明子对象的顺序呈现子对象，因此最后一个子对象在顶部呈现（每个元素的默认 z-index 为 0）。 这与其他内置面板相同。 但是，Canvas 还支持 [**Canvas.ZIndex**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc190397(v=vs.95)) 附加属性，可在每个子元素上设置。 你可以在代码中设置此属性，以在运行时期间更改元素的绘制顺序。 带有最高 Canvas.ZIndex 值的元素将最后绘制，因此它将在位于相同位置的任何其他元素上重叠绘制，或者将以任何方式进行重叠。 请注意，因为需要遵循 alpha 值（透明度），所以即使元素重叠，在重叠区域中显示的内容也可能混合（如果顶部元素具有非最大 alpha 值）。

Canvas 不对其子素的大小进行任何调整。 每个元素都必须指定其大小。

以下是 XAML 中 Canvas 的示例。

```xaml
<Canvas Width="120" Height="120">
    <Rectangle Fill="Red" Height="44" Width="44"/>
    <Rectangle Fill="Blue" Height="44" Width="44" Canvas.Left="20" Canvas.Top="20"/>
    <Rectangle Fill="Green" Height="44" Width="44" Canvas.Left="40" Canvas.Top="40"/>
    <Rectangle Fill="Orange" Height="44" Width="44" Canvas.Left="60" Canvas.Top="60"/>
</Canvas>
```

结果如下所示。

![画布](images/layout-panel-canvas.png)

谨慎使用 Canvas 面板。 尽管在某些情况下，在 UI 中精确控制元素的位置很简单，但固定定位布局面板会导致你的 UI 区域不太适应整个应用窗口大小的更改。 应用窗口大小可能会随设备方向更改、拆分应用窗口、更改监视器和许多其他用户方案而进行调整。

## <a name="panels-for-itemscontrol"></a>ItemsControl 的面板

存在多种只能用作 [**ItemsPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel) 来显示 [**ItemsControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl) 中的项的特殊用途面板。 它们是 [**ItemsStackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsStackPanel)、[**ItemsWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid)、[**VirtualizingStackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VirtualizingStackPanel) 和 [**WrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid)。 你无法将这些面板用于常规 UI 布局。

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。
