---
ms.assetid: 79CF3927-25DE-43DD-B41A-87E6768D5C35
title: 优化 XAML 布局
description: 布局可能是 XAML \#应用中最耗费资源的部分，无论在 CPU 使用率方面还是内存开销方面。 以下是可为提高 XAML 应用的布局性能而采取的步骤。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 92dca27a4cfb02f5d1bcb722683eca89ec16a6d6
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "66362214"
---
# <a name="optimize-your-xaml-layout"></a>优化 XAML 布局


**重要的 API**

-   [**面板**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel)

布局是为 UI 定义可视结构的过程。 描述 XAML 中的布局的主要机制是通过面板，面板是可使你在其中定位和安排 UI 元素的容器对象。 布局可能是 XAML 应用中最耗费资源的部分，无论在 CPU 使用率还是内存开销方面。 以下是可为提高 XAML 应用的布局性能而采取的步骤。

## <a name="reduce-layout-structure"></a>减少布局结构

布局性能的最大收益来自于简化 UI 元素树的层次结构。 可视化树中存在面板，但它们是结构元素，而不是*像素生成元素*（如 [**Button**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) 或 [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle)）。 通过减少非像素生成元素来简化树通常可以显著提高性能。

许多 UI 通过嵌套面板来实现，这可能导致更深层、复杂的面板和元素树。 嵌套面板很方便，但在许多情况下，使用更复杂的单个面板可以实现相同的 UI。 使用单个面板可提供更好的性能。

### <a name="when-to-reduce-layout-structure"></a>何时减少布局结构

通过琐碎的方式减少布局结构（例如，从你的顶层页面中减少一个嵌套面板）没有明显的影响。

最大的性能收益来自于减少 UI 中（如在 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 或 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) 中）的重复布局结构。 这些 [**ItemsControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl) 元素使用 [**DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate)，后者可定义多次实例化的 UI 元素的子树。 当在应用中多次重复相同的子树时，对该子树的任何性能改进都对应用的整体性能具有倍增效果。

### <a name="examples"></a>示例

请考虑以下 UI。

![表单布局示例](images/layout-perf-ex1.png)

这些示例显示了实现相同 UI 的 3 种方法。 每个实现选择都会在屏幕上产生几乎相同的像素，但在实现细节方面截然不同。

选项 1：嵌套 [**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) 元素

虽然这是最简单的模型，它仍然使用 5 个面板元素并导致大量的开销。

```xml
  <StackPanel>
  <TextBlock Text="Options:" />
  <StackPanel Orientation="Horizontal">
      <CheckBox Content="Power User" />
      <CheckBox Content="Admin" Margin="20,0,0,0" />
  </StackPanel>
  <TextBlock Text="Basic information:" />
  <StackPanel Orientation="Horizontal">
      <TextBlock Text="Name:" Width="75" />
      <TextBox Width="200" />
  </StackPanel>
  <StackPanel Orientation="Horizontal">
      <TextBlock Text="Email:" Width="75" />
      <TextBox Width="200" />
  </StackPanel>
  <StackPanel Orientation="Horizontal">
      <TextBlock Text="Password:" Width="75" />
      <TextBox Width="200" />
  </StackPanel>
  <Button Content="Save" />
</StackPanel>
```

选项 2：单个 [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)

[  **Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) 增加了一些复杂性，但仅使用单个面板元素。

```xml
<Grid>
  <Grid.RowDefinitions>
      <RowDefinition Height="Auto" />
      <RowDefinition Height="Auto" />
      <RowDefinition Height="Auto" />
      <RowDefinition Height="Auto" />
      <RowDefinition Height="Auto" />
      <RowDefinition Height="Auto" />
      <RowDefinition Height="Auto" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
      <ColumnDefinition Width="Auto" />
      <ColumnDefinition Width="Auto" />
  </Grid.ColumnDefinitions>
  <TextBlock Text="Options:" Grid.ColumnSpan="2" />
  <CheckBox Content="Power User" Grid.Row="1" Grid.ColumnSpan="2" />
  <CheckBox Content="Admin" Margin="150,0,0,0" Grid.Row="1" Grid.ColumnSpan="2" />
  <TextBlock Text="Basic information:" Grid.Row="2" Grid.ColumnSpan="2" />
  <TextBlock Text="Name:" Width="75" Grid.Row="3" />
  <TextBox Width="200" Grid.Row="3" Grid.Column="1" />
  <TextBlock Text="Email:" Width="75" Grid.Row="4" />
  <TextBox Width="200" Grid.Row="4" Grid.Column="1" />
  <TextBlock Text="Password:" Width="75" Grid.Row="5" />
  <TextBox Width="200" Grid.Row="5" Grid.Column="1" />
  <Button Content="Save" Grid.Row="6" />
</Grid>
```

选项 3：单个 [**RelativePanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel)：

此单个面板同样比使用嵌套面板更复杂一些，但可能比 [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) 更易于理解和维护。

```xml
<RelativePanel>
  <TextBlock Text="Options:" x:Name="Options" />
  <CheckBox Content="Power User" x:Name="PowerUser" RelativePanel.Below="Options" />
  <CheckBox Content="Admin" Margin="20,0,0,0" 
            RelativePanel.RightOf="PowerUser" RelativePanel.Below="Options" />
  <TextBlock Text="Basic information:" x:Name="BasicInformation"
           RelativePanel.Below="PowerUser" />
  <TextBlock Text="Name:" RelativePanel.AlignVerticalCenterWith="NameBox" />
  <TextBox Width="200" Margin="75,0,0,0" x:Name="NameBox"               
           RelativePanel.Below="BasicInformation" />
  <TextBlock Text="Email:"  RelativePanel.AlignVerticalCenterWith="EmailBox" />
  <TextBox Width="200" Margin="75,0,0,0" x:Name="EmailBox"
           RelativePanel.Below="NameBox" />
  <TextBlock Text="Password:" RelativePanel.AlignVerticalCenterWith="PasswordBox" />
  <TextBox Width="200" Margin="75,0,0,0" x:Name="PasswordBox"
           RelativePanel.Below="EmailBox" />
  <Button Content="Save" RelativePanel.Below="PasswordBox" />
</RelativePanel>
```

如这些示例所示，有许多实现相同的 UI 的方法。 你应该在选择之前权衡所有利弊，包括性能、可读性和可维护性。

## <a name="use-single-cell-grids-for-overlapping-ui"></a>为重叠 UI 使用单个单元网格

常见的 UI 要求是具有元素互相重叠的布局。 通常填充、边距、对齐和转换用于以这种方式定位元素。 已优化 XAML [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) 控件以提高重叠元素的布局性能。

**重要提示**  若要查看改进，请使用单个单元 [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)。 不要定义 [**RowDefinitions**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.rowdefinitions) 或 [**ColumnDefinitions**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.columndefinitions)。

### <a name="examples"></a>示例

```xml
<Grid>
    <Ellipse Fill="Red" Width="200" Height="200" />
    <TextBlock Text="Test" 
               HorizontalAlignment="Center" 
               VerticalAlignment="Center" />
</Grid>
```

![覆盖在圆形上的文本](images/layout-perf-ex2.png)

```xml
<Grid Width="200" BorderBrush="Black" BorderThickness="1">
    <TextBlock Text="Test1" HorizontalAlignment="Left" />
    <TextBlock Text="Test2" HorizontalAlignment="Right" />
</Grid>
```

![网格中的两个文本块](images/layout-perf-ex3.png)

## <a name="use-a-panels-built-in-border-properties"></a>使用面板的内置边框属性

[**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)、[**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel)、[**RelativePanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel) 和 [**ContentPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentPresenter) 控件具有内置边框属性，可用于在其周围绘制边框，而无需向 XAML 添加额外的 [**Border**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border) 元素。 支持内置边框的新属性是：**BorderBrush**、**BorderThickness**、**CornerRadius** 和 **Padding**。 其中每一属性都是 [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty)，因此你可以将其用于绑定和动画。 它们设计为一个单独 **Border** 元素的完整替代。

如果你的 UI 具有围绕这些面板的 [**Border**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border) 元素，请改用内置边框，这可以在应用的布局结构中节省额外的元素。 如前面所述，这可以节省大量资源，在重复 UI 的情况下尤其如此。

### <a name="examples"></a>示例

```xml
<RelativePanel BorderBrush="Red" BorderThickness="2" CornerRadius="10" Padding="12">
    <TextBox x:Name="textBox1" RelativePanel.AlignLeftWithPanel="True"/>
    <Button Content="Submit" RelativePanel.Below="textBox1"/>
</RelativePanel>
```

## <a name="use-sizechanged-events-to-respond-to-layout-changes"></a>使用 **SizeChanged** 事件响应布局更改

[**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) 类公开两个相似的事件，用于响应布局更改：[**LayoutUpdated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.layoutupdated) 和 [**SizeChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.sizechanged)。 当在布局期间调整元素大小时，你可能正在使用其中一个事件接收通知。 两个事件的语义是不同的，因为在两者之间进行选择时有重要的性能注意事项。

若要获取良好性能，选择 [**SizeChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.sizechanged) 在大多数情况下都没有错。 **SizeChanged** 具有直观的语义。 当 [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) 的大小已更新时，将在布局期间引发它。

[**LayoutUpdated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.layoutupdated) 还会在布局期间引发，但它具有全局语义，无论何时更新元素，都会将其引发。 通常只在事件处理程序中进行本地处理，在此情况下将以多于必需的频率运行代码。 仅当你需要知道元素何时在不更改大小的情况下重新定位（此情况不常见）时使用 **LayoutUpdated**。

## <a name="choosing-between-panels"></a>在面板之间选择

在个别面板之间进行选择时，性能通常不是一个注意事项。 进行该选择时通常考虑哪个面板提供最接近与你要实现的 UI 的布局行为。 例如，如果你正在 [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)、[**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) 和 [**RelativePanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel) 之间进行选择，你应该选择提供最接近于你对该实现的心理模型的面板。

优化每个 XAML 面板以实现良好性能，并且所有面板都为相似的 UI 提供相似的性能。

