---
pm-contact: kisai
design-contact: ksulliv
dev-contact: Shmazlou
doc-status: Published
Description: 轻扫命令是上下文菜单的触摸式加速键。
title: 轻扫
label: Swipe
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 315edbddccc51b7e742bf9beffad8497a104ce03
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80614094"
---
# <a name="swipe"></a>轻扫

轻扫命令是上下文菜单的加速键。通过它，用户无需在应用中更改状态即可以触控方式轻松访问常见菜单操作。

> **Windows UI 库 API**：[SwipeControl](/uwp/api/microsoft.ui.xaml.controls.swipecontrol)、[SwipeItem](/uwp/api/microsoft.ui.xaml.controls.swipeitem)
>
> **平台 API**：[SwipeControl](/uwp/api/windows.ui.xaml.controls.swipecontrol)、[SwipeItem](/uwp/api/windows.ui.xaml.controls.swipeitem)、[ListView 类](/uwp/api/Windows.UI.Xaml.Controls.ListView)

![“执行”和“显示”浅色主题](images/LightThemeSwipe.png)

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

轻扫命令可节省空间。 在用户可能要快速连续地对多个项目执行相同操作的情况下，此命令非常有用。 它对项目提供了“快速操作”，无需完全弹出或在页面中更改状态。

如果你有一组项目可能很大，且每个项目都有用户可能想要定期执行的 1 到 3 个操作，则应使用轻扫命令。 这些操作可能包括但不限于：

- 删除
- 标记或归档
- 保存或下载
- 答复

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 XAML 控件库应用，请单击此处<a href="xamlcontrolsgallery:/item/SwipeControl">打开此应用，查看 SwipeControl 的实际应用</a><strong style="font-weight: semi-bold"></strong>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="video-summary"></a>视频摘要

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev015/player]

## <a name="how-does-swipe-work"></a>“轻扫”的工作原理

UWP 轻扫命令有两种模式：[显示](/uwp/api/windows.ui.xaml.controls.swipemode)和[执行](/uwp/api/windows.ui.xaml.controls.swipemode)。 它还支持四种不同的轻扫方向：向上、向下、向左和向右。

### <a name="reveal-mode"></a>显示模式

在显示模式下，用户轻扫某个项目即可打开包含一个或多个命令的菜单，且必须明确地点击命令才能执行它。 当用户轻扫并释放某个项目时，菜单将保持打开状态，直到选中命令或者通过轻扫、点击关闭或将已打开的项目滚离屏幕来再次关闭菜单。

![轻扫以显示](images/SwipeCommand-Reveal_v2.gif)

显示模式是一种更安全、更通用的轻扫模式，可用于大部分类型的菜单操作，甚至是删除等可能有破坏性的操作。

当用户选择在显示的打开和休眠状态下出现的其中一个选项时，就会调用该项目的命令，且轻扫控制将关闭。

### <a name="execute-mode"></a>执行模式

在执行模式下，用户轻扫打开项目即可通过此轻扫显示并执行单个命令。 如果用户在轻扫通过阈值之前释放所轻扫的项目，菜单就会关闭，且命令不会执行。 如果用户在轻扫通过阈值后释放项目，则直接执行命令。

![轻扫以执行](images/SwipeCommand_Delete_v2.gif)

如果用户在到达阈值后不松开手指并再次下拉关闭的轻扫项目，则命令不会执行，且不对该项目执行任何操作。

轻扫项目时，执行模式可以通过颜色和标签方向提供更多视觉反馈。

当用户执行的操作为最常见操作时，最适合使用执行模式。

此外，它也适合用于更有破坏性的操作，如删除项目。 但请注意，“执行”只需在一个方向轻扫一次，这与“显示”不同，后者需要明确单击按钮。

### <a name="swipe-directions"></a>轻扫方向

轻扫适合所有基本方向，包括：向上、向下、向左和向右。 每个轻扫方向均可以保留其自己的轻扫项目或内容，但一次只能对单个可轻扫元素设置一个方向实例。

例如，不能在同一 SwipeControl 上设置两个 [LeftItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.LeftItems) 定义。

## <a name="how-to-create-a-swipe-command"></a>如何创建轻扫命令

轻扫命令具有两个需要定义的组件：

- 在内容四周环绕的 [SwipeControl](/uwp/api/windows.ui.xaml.controls.swipecontrol)。 在集合（例如 ListView）中，它位于 DataTemplate 中。
- 轻扫菜单项，可以是一个或多个 [SwipeItem](/uwp/api/windows.ui.xaml.controls.swipeitem) 对象，放在轻扫控件的方向容器中：[LeftItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.LeftItems)、[RightItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.RightItems)、[TopItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.TopItems) 或 [BottomItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.BottomItems)

轻扫内容可内联放置，或在页面或应用的“资源”部分定义。

下面是用于环绕某些文本的简单 SwipeControl 示例。 它显示了创建轻扫命令所需的 XAML 元素的层次结构。

```xaml
<SwipeControl HorizontalAlignment="Center" VerticalAlignment="Center">
    <SwipeControl.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Pin">
                <SwipeItem.IconSource>
                    <SymbolIconSource Symbol="Pin"/>
                </SwipeItem.IconSource>
            </SwipeItem>
        </SwipeItems>
    </SwipeControl.LeftItems>

     <!-- Swipeable content -->
    <Border Width="180" Height="44" BorderBrush="Black" BorderThickness="2">
        <TextBlock Text="Swipe to Pin" Margin="4,8,0,0"/>
    </Border>
</SwipeControl>
```

现在我们来看一个更完整的示例，了解通常在列表中如何使用轻扫命令。 在此示例中，将设置一个使用执行模式的删除命令，还有一个使用模式模式的其他命令的菜单。 这两组命令都是在页面的“资源”部分定义的。 你将对一个 ListView 中的项目应用轻扫命令。

首先，创建轻扫项目，它们将命令表示为页面级资源。 SwipeItem 使用 [IconSource](/uwp/api/windows.ui.xaml.controls.iconsource) 作为其图标。 同样，以资源的形式创建图标。

```xaml
<Page.Resources>
    <SymbolIconSource x:Key="ReplyIcon" Symbol="MailReply"/>
    <SymbolIconSource x:Key="DeleteIcon" Symbol="Delete"/>
    <SymbolIconSource x:Key="PinIcon" Symbol="Pin"/>

    <SwipeItems x:Key="RevealOptions" Mode="Reveal">
        <SwipeItem Text="Reply" IconSource="{StaticResource ReplyIcon}"/>
        <SwipeItem Text="Pin" IconSource="{StaticResource PinIcon}"/>
    </SwipeItems>

    <SwipeItems x:Key="ExecuteDelete" Mode="Execute">
        <SwipeItem Text="Delete" IconSource="{StaticResource DeleteIcon}"
                   Background="Red"/>
    </SwipeItems>
</Page.Resources>
```

记住，应将轻扫内容中的菜单项保留为简短的文本标签。 这些操作应当是用户想要在短时间内多次执行的主要操作。

除在 DataTemplate 中定义 SwipeControl 以使其应用于集合中的每个项目外，设置在集合或 ListView 中运行的轻扫命令与定义单个轻扫命令（如前面所述）完全相同。

下面是项目 DataTemplate 应用了 SwipeControl 的 ListView。 LeftItems 和 RightItems 属性引用你作为资源创建的轻扫项目。

```xaml
<ListView x:Name="sampleList" Width="300">
    <ListView.ItemContainerStyle>
        <Style TargetType="ListViewItem">
            <Setter Property="HorizontalContentAlignment" Value="Stretch"/>
            <Setter Property="VerticalContentAlignment" Value="Stretch"/>
        </Style>
    </ListView.ItemContainerStyle>
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="x:String">
            <SwipeControl x:Name="ListViewSwipeContainer"
                          LeftItems="{StaticResource RevealOptions}"
                          RightItems="{StaticResource ExecuteDelete}"
                          Height="60">
                <StackPanel Orientation="Vertical">
                    <TextBlock Text="{x:Bind}" FontSize="18"/>
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit..." FontSize="12"/>
                    </StackPanel>
                </StackPanel>
            </SwipeControl>
        </DataTemplate>
    </ListView.ItemTemplate>
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
    <x:String>Item 4</x:String>
    <x:String>Item 5</x:String>
</ListView>
```

## <a name="handle-an-invoked-swipe-command"></a>处理调用的轻扫命令

要对轻扫命令执行操作，可以处理其 [Invoked](/uwp/api/windows.ui.xaml.controls.swipeitem.Invoked) 事件。 （要详细了解用户可如何调用命令，请查看本文前面的[“轻扫”的工作原理]部分。  ）通常，轻扫命令位于 ListView 和类似列表的方案中。 在这种情况下，调用命令时，表示你要对所轻扫的项目执行操作。

下面介绍如何处理你之前创建的 delete 轻扫项目的 Invoked 事件  。

```xaml
<SwipeItems x:Key="ExecuteDelete" Mode="Execute">
    <SwipeItem Text="Delete" IconSource="{StaticResource DeleteIcon}"
               Background="Red" Invoked="Delete_Invoked"/>
</SwipeItems>
```

该数据项目是 SwipeControl 的 DataContext。 在你的代码中，可以通过从事件参数中获取 SwipeControl.DataContext 属性来访问该项目，如下面所示。

```csharp
 private void Delete_Invoked(SwipeItem sender, SwipeItemInvokedEventArgs args)
 {
     sampleList.Items.Remove(args.SwipeControl.DataContext);
 }
```

> [!NOTE]
> 在这里，为简单起见，项目是直接添加到 ListView.Items 集合中的，所以删除方式也相同。 如果你将 ListView.ItemsSource 设置为更为常见的集合，则需要从源集合中删除项目。

在此特定实例中，你从列表中删除了项目，因此所轻扫项目的最终可视状态并不重要。 但是，在只希望执行一项操作后就再次折叠轻扫的情况下，可以将 [BehaviorOnInvoked](/uwp/api/windows.ui.xaml.controls.swipeitem.BehaviorOnInvoked) 属性设置为 [SwipeBehaviorOnInvoked](/uwp/api/windows.ui.xaml.controls.swipebehavioroninvoked) 枚举值之一。

- **自动**
  - 在执行模式下，打开的轻扫项目在调用时将保持打开状态。
  - 在显示模式下，打开的轻扫项目在调用时将折叠。
- **关闭**
  - 项目被调用时，无论处于何种模式，轻扫控件将始终折叠并返回至正常状态。
- **RemainOpen**
  - 项目被调用时，无论处于何种模式，轻扫控件将始终处于打开状态。

在这里，一个 reply 轻扫项目设置为在调用后关闭  。

```xaml
<SwipeItem Text="Reply" IconSource="{StaticResource ReplyIcon}"
           Invoked="Reply_Invoked"
           BehaviorOnInvoked = "Close"/>
```

## <a name="dos-and-donts"></a>应做事项和禁止事项

- 请勿在 FlipView、Hub 或 Pivot 中使用轻扫。 用户可能会因轻扫指令冲突而混淆组合。
- 请勿将水平轻扫和水平导航组合在一起，也不要将垂直轻扫和垂直导航混在一起。
- 务必确保用户轻扫的内容为相同操作，并且在可轻扫的所有相关项目中保持一致。
- 务必对用户将要执行的主要操作使用轻扫。
- 务必对需要多次重复执行相同操作的项目使用轻扫。
- 务必对较宽的项目执行水平轻扫，对较高的项目执行垂直轻扫。
- 务必使用简短的文本标签。

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。

## <a name="related-articles"></a>相关文章

- [L列表视图和网格视图](listview-and-gridview.md)
- [项目容器和模板](item-containers-templates.md)
- [下拉刷新](pull-to-refresh.md)
