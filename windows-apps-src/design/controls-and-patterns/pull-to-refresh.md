---
Description: 使用下拉刷新控件将新内容加入列表。
title: 下拉刷新
label: Pull-to-refresh
template: detail.hbs
ms.date: 03/07/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: aaeb1e74-b795-4015-bf41-02cb1d6f467e
pm-contact: predavid
design-contact: kimsea
dev-contact: stpete
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: faff0857679d50f6995640bbf9bf0222bb0d2e37
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80081614"
---
# <a name="pull-to-refresh"></a>下拉刷新

下拉刷新使用户可以使用触控下拉数据列表以检索更多数据。 下拉刷新广泛用于具有触摸屏的设备。 可以使用这里显示的 API 在应用中实现下拉刷新。

> **重要的 API**：[RefreshContainer](/uwp/api/windows.ui.xaml.controls.refreshcontainer)、[RefreshVisualizer](/uwp/api/windows.ui.xaml.controls.refreshvisualizer)

![下拉刷新 gif](images/Pull-To-Refresh.gif)

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

如果你拥有用户希望定期刷新的数据列表或网格，并且应用可能在触摸优先的设备上运行，请使用下拉刷新。

你也可以使用 [RefreshVisualizer](/uwp/api/windows.ui.xaml.controls.refreshvisualizer) 创建以其他方式调用（例如，通过刷新按钮）的一致的刷新体验。

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处<a href="xamlcontrolsgallery:/item/PullToRefresh">打开此应用，了解 PullToRefresh 的实际应用</a>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="refresh-controls"></a>刷新控件

下拉刷新是通过两个控件启用的。

- **RefreshContainer** - 为下拉刷新体验提供包装器的 ContentControl。 它处理触控交互并管理其内部刷新可视化工具的状态。
- **RefreshVisualizer** - 封装下一部分中介绍的刷新可视化。

主要控件是 **RefreshContainer**，作为用户通过下拉以触发刷新的内容的包装器。 RefreshContainer 仅适用于触控，因此建议也要为没有触控界面的用户提供刷新按钮。 可以将刷新按钮放在应用中合适的位置，例如命令栏上或靠近所刷新面的位置。

## <a name="refresh-visualization"></a>刷新可视化

默认的刷新可视化是一个环形的进度旋转图标，用于告知用户刷新将发生的时间和刷新启动后的进度。 刷新可视化工具有 5 种状态。

 用户需要下拉列表以启动刷新的距离称为_阈值_。 可视化工具[状态](/uwp/api/windows.ui.xaml.controls.refreshvisualizer.State)是由下拉状态相对于此阈值的关系决定的。 可能的值包含在 [RefreshVisualizerState](/uwp/api/windows.ui.xaml.controls.refreshvisualizerstate) 枚举中。

### <a name="idle"></a>空闲

可视化工具的默认状态为“空闲”  。 用户目前不通过触控与 RefreshContainer 交互，也没有正在进行的刷新。

看不到刷新可视化工具的证据。

### <a name="interacting"></a>正在交互

当用户在 PullDirection 属性指定的方向下拉列表，在达到阈值前，可视化工具处于“正在交互”  状态。

- 如果用户在此状态期间释放控件，控件将返回“空闲”  。

    ![下拉刷新达到阈值前](images/ptr-prethreshold.png)

    可以看到，图标显示为已禁用（60% 不透明度）。 此外，图标随滚动操作旋转全程。

- 如果用户下拉列表的距离超过阈值，可视化工具会从“正在交互”  转换为“挂起”  。

    ![下拉刷新达到阈值](images/ptr-atthreshold.png)

    可以看到，在转换期间，图标切换到 100% 不透明度，并且脉冲大小增加到 150% 然后又回到 100%。

### <a name="pending"></a>Pending

当用户下拉列表的距离超过阈值时，可视化工具会处于“挂起”  状态。

- 如果用户向上移动列表回到阈值之上并且不释放列表，列表将返回“正在交互”  状态。
- 如果用户释放列表，会启动一个刷新请求并转换为“正在刷新”  状态。

![下拉刷新超过阈值后](images/ptr-postthreshold.png)

可以看到，图标大小和不透明度均为 100%。 在这种状态下，图标继续随滚动操作向下移动，但不再旋转。

### <a name="refreshing"></a>正在刷新

当用户释放可视化工具的距离超过阈值时，就会处于“正在刷新”  状态。

进入此状态时，会引发 **RefreshRequested** 事件。 这是应用的内容刷新的启动信号。 事件参数 ([RefreshRequestedEventArgs](/uwp/api/windows.ui.xaml.controls.refreshrequestedeventargs)) 包含一个 [Deferral](/uwp/api/windows.foundation.deferral) 对象，应在事件处理程序中为其提供一个图柄。 然后，当要执行刷新的代码完成后，应将此延迟标记为已完成。

刷新完成后，可视化工具将回到“空闲”  状态。

可以看到，图标回到阈值位置并在刷新期间旋转。 此旋转用于显示刷新进度，并将替换为传入内容的动画。

### <a name="peeking"></a>正在扫视

当用户从不允许刷新的起始位置沿刷新方向下拉时，可视化工具会进入“正在扫视”  状态。 用户开始下拉时 ScrollViewer 不处于位置 0 时通常会发生这种情况。

- 如果用户在此状态期间释放控件，控件将返回“空闲”  。

## <a name="pull-direction"></a>拉动方向

默认情况下，用户从上到下拉动列表以启动刷新。 如果列表或网格具有不同的方向，则应更改刷新容器的拉动方向以便匹配列表或网格的方向。

[PullDirection](/uwp/api/windows.ui.xaml.controls.refreshcontainer.PullDirection) 属性采用以下 [RefreshPullDirection](/uwp/api/windows.ui.xaml.controls.refreshpulldirection) 值之一：**BottomToTop**、**TopToBottom**、**RightToLeft** 或 **LeftToRight**。

更改拉动方向时，可视化工具的进度旋转图标的起始位置会自动旋转，以便箭头与拉动方向的相应位置对应。 如果需要，可以更改 [RefreshVisualizer.Orientation](/uwp/api/windows.ui.xaml.controls.refreshvisualizer.Orientation) 属性以覆盖自动行为。 在大多数情况下，建议保留默认值 **Auto**。

## <a name="implement-pull-to-refresh"></a>实现下拉刷新

向列表添加下拉刷新功能只需要几个步骤。

1. 将列表包装到一个 **RefreshContainer** 控件中。
1. 处理 **RefreshRequested** 事件以刷新内容。
1. （可选）通过调用 **RequestRefresh**（例如，从按钮单击）启动刷新。

> [!NOTE]
> 可以实例化 RefreshVisualizer 本身。 但是，即使针对非触控场景，我们还是建议你将内容包装到一个 RefreshContainer 中并使用 RefreshContainer.Visualizer 属性提供的 RefreshVisualizer。 在本文中，我们假设可视化工具始终是从刷新容器获得的。

> 此外，为方便期间，应使用刷新容器的 RequestRefresh 和 RefreshRequested 成员。 `refreshContainer.RequestRefresh()` 与 `refreshContainer.Visualizer.RequestRefresh()` 等效，任何一个都会同时引发 RefreshContainer.RefreshRequested 事件和 RefreshVisualizer.RefreshRequested 事件。

### <a name="request-a-refresh"></a>请求刷新

刷新容器处理触控交互来让用户通过触控刷新内容。 建议提供适用于非触控界面的其他选择，例如刷新按钮或语音控制。

要启动刷新，请调用 [RequestRefresh](/uwp/api/windows.ui.xaml.controls.refreshcontainer.RequestRefresh)方法。

```csharp
// See the Examples section for the full code.
private void RefreshButtonClick(object sender, RoutedEventArgs e)
{
    RefreshContainer.RequestRefresh();
}
```

调用 RequestRefresh 时，可视化工具的状态会从“空闲”  直接转换为“正在刷新”  。

### <a name="handle-a-refresh-request"></a>处理刷新请求

要在需要时获取最新内容，请处理 RefreshRequested 事件。 在事件处理程序中，你需要特定于应用的代码来获取最新内容。

事件参数 ([RefreshRequestedEventArgs](/uwp/api/windows.ui.xaml.controls.refreshrequestedeventargs)) 包含一个 [Deferral](/uwp/api/windows.foundation.deferral) 对象。 在事件处理程序中获取延迟的图柄。 然后，当要执行刷新的代码完成后，将此延迟标记为已完成。

```csharp
// See the Examples section for the full code.
private async void RefreshContainer_RefreshRequested(RefreshContainer sender, RefreshRequestedEventArgs args)
{
    // Respond to a request by performing a refresh and using the deferral object.
    using (var RefreshCompletionDeferral = args.GetDeferral())
    {
        // Do some async operation to refresh the content

         await FetchAndInsertItemsAsync(3);

        // The 'using' statement ensures the deferral is marked as complete.
        // Otherwise, you'd call
        // RefreshCompletionDeferral.Complete();
        // RefreshCompletionDeferral.Dispose();
    }
}
```

### <a name="respond-to-state-changes"></a>响应状态更改

如果需要，你可以响应可视化工具状态的更改。 例如，为防止多个刷新请求，可以在可视化工具刷新时禁用刷新按钮。

```csharp
// See the Examples section for the full code.
private void Visualizer_RefreshStateChanged(RefreshVisualizer sender, RefreshStateChangedEventArgs args)
{
    // Respond to visualizer state changes.
    // Disable the refresh button if the visualizer is refreshing.
    if (args.NewState == RefreshVisualizerState.Refreshing)
    {
        RefreshButton.IsEnabled = false;
    }
    else
    {
        RefreshButton.IsEnabled = true;
    }
}
```

## <a name="examples"></a>示例

### <a name="using-a-scrollviewer-in-a-refreshcontainer"></a>在 RefreshContainer 中使用 ScrollViewer
> [!NOTE]
> RefreshContainer 的内容必须是可滚动的控件，例如 ScrollViewer、GridView、ListView，等等。将控件的内容设置为网格会导致未定义的行为。

本示例介绍如何对滚动查看器使用下拉刷新。

```xaml
<RefreshContainer>
    <ScrollViewer VerticalScrollMode="Enabled"
                  VerticalScrollBarVisibility="Auto"
                  HorizontalScrollBarVisibility="Auto">
 
        <!-- Scrollviewer content -->

    </ScrollViewer>
</RefreshContainer>
```

### <a name="adding-pull-to-refresh-to-a-listview"></a>向 ListView 添加下拉刷新

本示例介绍如何对列表视图使用下拉刷新。

```xaml
<StackPanel Margin="0,40" Width="280">
    <CommandBar OverflowButtonVisibility="Collapsed">
        <AppBarButton x:Name="RefreshButton" Click="RefreshButtonClick"
                      Icon="Refresh" Label="Refresh"/>
        <CommandBar.Content>
            <TextBlock Text="List of items" 
                       Style="{StaticResource TitleTextBlockStyle}"
                       Margin="12,8"/>
        </CommandBar.Content>
    </CommandBar>

    <RefreshContainer x:Name="RefreshContainer">
        <ListView x:Name="ListView1" Height="400">
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:ListItemData">
                    <Grid Height="80">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <TextBlock Text="{x:Bind Path=Header}"
                                   Style="{StaticResource SubtitleTextBlockStyle}"
                                   Grid.Row="0"/>
                        <TextBlock Text="{x:Bind Path=Date}"
                                   Style="{StaticResource CaptionTextBlockStyle}"
                                   Grid.Row="1"/>
                        <TextBlock Text="{x:Bind Path=Body}"
                                   Style="{StaticResource BodyTextBlockStyle}"
                                   Grid.Row="2"
                                   Margin="0,4,0,0" />
                    </Grid>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </RefreshContainer>
</StackPanel>
```

```csharp
public sealed partial class MainPage : Page
{
    public ObservableCollection<ListItemData> Items { get; set; } 
        = new ObservableCollection<ListItemData>();

    public MainPage()
    {
        this.InitializeComponent();

        Loaded += MainPage_Loaded;
        ListView1.ItemsSource = Items;
    }

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        Loaded -= MainPage_Loaded;
        RefreshContainer.RefreshRequested += RefreshContainer_RefreshRequested;
        RefreshContainer.Visualizer.RefreshStateChanged += Visualizer_RefreshStateChanged;

        // Add some initial content to the list.
        await FetchAndInsertItemsAsync(2);
    }

    private void RefreshButtonClick(object sender, RoutedEventArgs e)
    {
        RefreshContainer.RequestRefresh();
    }

    private async void RefreshContainer_RefreshRequested(RefreshContainer sender, RefreshRequestedEventArgs args)
    {
        // Respond to a request by performing a refresh and using the deferral object.
        using (var RefreshCompletionDeferral = args.GetDeferral())
        {
            // Do some async operation to refresh the content

            await FetchAndInsertItemsAsync(3);

            // The 'using' statement ensures the deferral is marked as complete.
            // Otherwise, you'd call
            // RefreshCompletionDeferral.Complete();
            // RefreshCompletionDeferral.Dispose();
        }
    }

    private void Visualizer_RefreshStateChanged(RefreshVisualizer sender, RefreshStateChangedEventArgs args)
    {
        // Respond to visualizer state changes.
        // Disable the refresh button if the visualizer is refreshing.
        if (args.NewState == RefreshVisualizerState.Refreshing)
        {
            RefreshButton.IsEnabled = false;
        }
        else
        {
            RefreshButton.IsEnabled = true;
        }
    }

    // App specific code to get fresh data.
    private async Task FetchAndInsertItemsAsync(int updateCount)
    {
        for (int i = 0; i < updateCount; ++i)
        {
            // Simulate delay while we go fetch new items.
            await Task.Delay(1000);
            Items.Insert(0, GetNextItem());
        }
    }

    private ListItemData GetNextItem()
    {
        return new ListItemData()
        {
            Header = "Header " + DateTime.Now.Second.ToString(),
            Date = DateTime.Now.ToLongDateString(),
            Body = DateTime.Now.ToLongTimeString()
        };
    }
}

public class ListItemData
{
    public string Header { get; set; }
    public string Date { get; set; }
    public string Body { get; set; }
}
```

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。

## <a name="related-articles"></a>相关文章

- [触控交互](../input/touch-interactions.md)
- [列表视图和网格视图](listview-and-gridview.md)
- [项目容器和模板](item-containers-templates.md)
- [表达式动画](../../composition/composition-animation.md)
