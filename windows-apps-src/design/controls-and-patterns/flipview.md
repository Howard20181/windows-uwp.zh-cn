---
description: 显示集锦中的图像（例如相册中的照片或产品详细信息页中的项目），一次显示一张图像。
title: 翻转视图控件指南
ms.assetid: A4E05D92-1A0E-4CDD-84B9-92199FF8A8A3
label: Flip view
template: detail.hbs
ms.date: 04/07/2021
ms.topic: article
keywords: windows 10, uwp
pm-contact: predavid
design-contact: kimsea
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 4196be2a8eaff179fa8654e844e168b11bf23d7e
ms.sourcegitcommit: 5dc630ecdc9c852bcad96c172c63d6f8d3a7e696
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107214014"
---
# <a name="flip-view"></a>翻转视图

使用翻转视图浏览集合中的图像或其他项目（例如相册中的照片或产品详细信息页中的项目），一次显示一个项目。 对于触摸设备，轻扫某个项将在整个集合中进行浏览。 对于鼠标，导航按钮显示在鼠标悬停位置上。 对于键盘，使用箭头键移动浏览该集合。

**获取 Windows UI 库**

<img src="images/winui-logo-64x64.png" alt="WinUI logo"
     style="float: left; margin-right: 15px;" />

Windows UI 库 2.2 或更高版本包含此控件的使用圆角的新模板。 有关详细信息，请参阅[圆角半径](../style/rounded-corner.md)。 WinUI 是一种 NuGet 包，其中包含用于 Windows 应用的新控件和 UI 功能。 有关详细信息（包括安装说明），请参阅 [Windows UI 库](/uwp/toolkits/winui/)。

> **平台 API：** [FlipView class](/uwp/api/windows.ui.xaml.controls.flipview)（FlipView 类），[ItemsSource 属性](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource)，[ItemTemplate 属性](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate)

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

翻转视图最适合浏览小到中型集合中的图像（最多 25 个项目左右）。 此类集合的示例包括产品详细信息页中的项目或相册中的照片。 虽然我们不建议将翻转视图用于大多数大型集合，但是该控件通用于查看相册中的个别图像。

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 XAML 控件库应用，请单击此处<a href="xamlcontrolsgallery:/item/FlipView">打开此应用，了解 FlipView 的实际应用</a><strong style="font-weight: semi-bold"></strong>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

水平浏览（从最左侧的项开始并向右翻转）是翻转视图的典型布局。 此布局非常适用于所有设备上的纵向或横向方向：

![水平翻转视图布局示例](images/controls_flipview_horizonal.jpg)

翻转视图同样可以垂直浏览：

![垂直翻转视图示例](images/controls_flipview_vertical.jpg)

## <a name="create-a-flip-view"></a>创建翻转视图

FlipView 是一个 [ItemsControl](/uwp/api/windows.ui.xaml.controls.itemscontrol)，因此可以包含任何类型的项目的集合。 若要填充视图，将项添加到 [**Items**](/uwp/api/windows.ui.xaml.controls.itemscontrol.items) 集合，或者将 [**ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) 属性设置为数据源。

在默认情况下，数据项以绑定到的数据对象的字符串表现形式显示在翻转视图中。 若要具体地指定翻转视图中项的显示方式，请创建 [**DataTemplate**](/uwp/api/windows.ui.xaml.datatemplate) 以定义用于显示各个项目的控件布局。 该布局中的控件可绑定到数据对象的属性，或者具有定义的嵌入内容。 将 DataTemplate 分配给 FlipView 的 [**ItemTemplate**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) 属性。

### <a name="add-items-to-the-items-collection"></a>将项添加到项集合

可以通过使用 XAML 或代码向 [**Items**](/uwp/api/windows.ui.xaml.controls.itemscontrol.items) 集合添加项。 在以下情况下通常采用这种方式添加项：具有不更改且使用 XAML 轻松定义的少量项，或者在运行时采用代码生成项。 以下是一个翻转视图，内含以内联方式定义的项目。

```xaml
<FlipView x:Name="flipView1">
    <Image Source="Assets/Logo.png" />
    <Image Source="Assets/SplashScreen.png" />
    <Image Source="Assets/SmallLogo.png" />
</FlipView>
```

```csharp
// Create a new flip view, add content, 
// and add a SelectionChanged event handler.
FlipView flipView1 = new FlipView();
flipView1.Items.Add("Item 1");
flipView1.Items.Add("Item 2");

// Add the flip view to a parent container in the visual tree.
stackPanel1.Children.Add(flipView1);
```

向翻转视图添加项时，这些项目会自动放置在 [**FlipViewItem**](/uwp/api/Windows.UI.Xaml.Controls.FlipViewItem) 容器中。 要更改项的显示方式，可通过设置 [**ItemContainerStyle**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle) 属性将样式应用到该项容器。 

使用 XAML 定义项时，这些项会自动添加到项集合。

### <a name="set-the-items-source"></a>设置项目源

通常使用翻转视图显示源（例如数据库或 Internet）中的数据。 若要填充数据源中的翻转视图，请将其 [**ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) 属性设置为数据项集合。

此时，直接在代码中将翻转视图的 ItemsSource 设置为集合实例。

```csharp
// Data source.
List<String> itemsList = new List<string>();
itemsList.Add("Item 1");
itemsList.Add("Item 2");

// Create a new flip view, add content, 
// and add a SelectionChanged event handler.
FlipView flipView1 = new FlipView();
flipView1.ItemsSource = itemsList;
flipView1.SelectionChanged += FlipView_SelectionChanged;

// Add the flip view to a parent container in the visual tree.
stackPanel1.Children.Add(flipView1);
```

还可以将 ItemsSource 属性绑定到 XAML 中的集合。 有关详细信息，请参阅[使用 XAML 进行数据绑定](../../data-binding/data-binding-quickstart.md)。

在下面的代码中，ItemsSource 绑定到名为 `itemsViewSource` 的 [**CollectionViewSource**](/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource)。 

```xaml
<Page.Resources>
    <!-- Collection of items displayed by this page -->
    <CollectionViewSource x:Name="itemsViewSource" Source="{Binding Items}"/>
</Page.Resources>

...

<FlipView x:Name="itemFlipView" 
          ItemsSource="{Binding Source={StaticResource itemsViewSource}}"/>
```

> [!CAUTION]
> 可以通过将项目添加到其 Items 集合或设置其 ItemsSource 属性来填充翻转视图，但不能同时使用这两种方式。 如果你设置 ItemsSource 属性并使用 XAML 添加项目，将忽略添加的项目。 如果 ItemsSource 属性已设置且使用代码向项集合中添加项，则会引发异常。

### <a name="specify-the-look-of-the-items"></a>指定项目的外观

在默认情况下，数据项以绑定到的数据对象的字符串表现形式显示在翻转视图中。 你通常希望更丰富地呈现你的数据。 若要具体地指定翻转视图中项的显示方式，可以创建 [**DataTemplate**](/uwp/api/Windows.UI.Xaml.DataTemplate)。 DataTemplate 中的 XAML 定义用于显示各项的控件的布局和外观。 该布局中的控件可绑定到数据对象的属性，或者具有定义的嵌入内容。 将 DataTemplate 分配给 FlipView 控件的 [**ItemTemplate**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) 属性。

在本示例中，FlipView 的 ItemTemplate 采用内联方式定义。 会将一个覆盖添加到图像以显示图像名称。 

```XAML
<FlipView x:Name="flipView1" Width="480" Height="270" 
          BorderBrush="Black" BorderThickness="1">
    <FlipView.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Image Width="480" Height="270" Stretch="UniformToFill"
                       Source="{Binding Image}"/>
                <Border Background="#75000000" Height="80" VerticalAlignment="Bottom">
                    <TextBlock Text="{Binding Name}" 
                               FontFamily="Segoe UI" FontSize="26.667" 
                               Foreground="#CCFFFFFF" Padding="15,20"/>
                </Border>
            </Grid>
        </DataTemplate>
    </FlipView.ItemTemplate>
</FlipView>
```

以下是数据模板所定义布局的外观。

![使用数据模板的翻转视图示例](images/flip-view-template.png)

### <a name="set-the-orientation-of-the-flip-view"></a>设置翻转视图的方向

在默认情况下，翻转视图以水平反向翻转。 若要使其垂直翻转，请使用堆叠面板，以垂直方向作为翻转视图的 [**ItemsPanel**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel)。

此示例显示如何以垂直方向作为 FlipView 的 ItemsPanel 来使用堆叠面板。

```XAML
<FlipView x:Name="flipViewVertical" Width="480" Height="270" 
          BorderBrush="Black" BorderThickness="1">
    
    <!-- Use a vertical stack panel for vertical flipping. -->
    <FlipView.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Vertical"/>
        </ItemsPanelTemplate>
    </FlipView.ItemsPanel>
    
    <FlipView.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Image Width="480" Height="270" Stretch="UniformToFill"
                       Source="{Binding Image}"/>
                <Border Background="#A5000000" Height="80" VerticalAlignment="Bottom">
                    <TextBlock Text="{Binding Name}" 
                               FontFamily="Segoe UI" FontSize="26.667" 
                               Foreground="#CCFFFFFF" Padding="15,20"/>
                </Border>
            </Grid>
        </DataTemplate>
    </FlipView.ItemTemplate>
</FlipView>
```

以下是翻转视图使用垂直方向时的外观。

![垂直翻转视图示例](images/controls_flipview_vertical.jpg)

## <a name="adding-a-context-indicator"></a>添加上下文指示器

翻转视图中的上下文指示器提供了有用的参考点。 标准上下文指示器中的点不交互。 如此示例中所示，最佳的放置位置通常居中和位于库的下方：

![页面指示器示例](images/controls_pageindicator.png)

对于较大的集合（10-25 个项目），请考虑使用提供更多上下文（如一个包含缩略图的幻灯片）的指示器。 不同于使用简单点的上下文指示器，幻灯片中的每张缩略图将显示较小版本的相应图像并应该可供选择：

![上下文指示器示例](images/controls_contextindicator.jpg)

有关显示如何向 FlipView 添加上下文指示器的示例代码，请参阅 [XAML FlipView 示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/XAML%20FlipView%20control%20sample%20(Windows%208))。

## <a name="dos-and-donts"></a>应做事项和禁止事项

-   翻转视图最适用于含有最多 25 个项目的集合。
-   避免将翻转视图控件用于较大的集合，因为翻转浏览每个项目的重复性动作可能会很麻烦。 但相册是个例外，其中通常包含成百上千张图像。 在网格视图布局下选择一张照片后，相册几乎总是会切换到翻转视图。 对于其他较大集合，请考虑[列表视图或网格视图](lists.md)。
-   对于上下文指示器：
    -   点的顺序（或你选择的任何视觉标记）在居中和位于水平平移的库下方时会获得最佳效果。
    -   如果希望上下文指示器位于垂直平移的库中，它在居中和位于图像的右侧时会获得最佳效果。
    -   突出显示的点表示当前项。 通常突出显示的点是白色的，而其他点是灰色的。
    -   点数可能不同，但如果点数不够多，用户可能无法找到自己的位置，10 个点通常是显示的最大数目。

## <a name="globalization-and-localization-checklist"></a>全球化和本地化清单

<table>
<tr>
<th>双向注意事项</th><td>使用适用于 RTL 语言的标准镜像。 后退和前进控件应基于语言的方向，因此对于 RTL 语言，右侧按钮应该向后导航，而左侧按钮应向前导航。</td>
</tr>

</table>

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。

## <a name="related-articles"></a>相关文章

- [列表指南](lists.md)
- [**FlipView 类**](/uwp/api/Windows.UI.Xaml.Controls.FlipView)
