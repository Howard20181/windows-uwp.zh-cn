---
description: 了解如何使用命令栏控件让用户轻松访问应用最常见的命令和任务。
title: 命令栏
label: App bars/command bars
template: detail.hbs
op-migration-status: ready
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 868b4145-319b-4a97-82bd-c98d966144db
pm-contact: yulikl
design-contact: ksulliv
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f3d21f60a26f7a1c6a63d678c60e1d9538580d55
ms.sourcegitcommit: 77af97719a439f5e73a6109b42fd3110bcb2843b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107219040"
---
# <a name="command-bar"></a>命令栏

命令栏使用户能够轻松地访问应用的最常见任务。 命令栏可以提供对应用级别或特定于页面的命令的访问，并且可以与任何导航模式一起使用。

> **平台 API：** [CommandBar 类](/uwp/api/windows.ui.xaml.controls.commandbar)、[AppBarButton 类](/uwp/api/windows.ui.xaml.controls.appbarbutton)、[AppBarToggleButton 类](/uwp/api/windows.ui.xaml.controls.appbartogglebutton)、[AppBarSeparator 类](/uwp/api/windows.ui.xaml.controls.appbarseparator)

![带有图标的命令栏示例](images/controls_appbar_icons.png)

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

CommandBar 控件是一款通用、灵活、轻型的控件，可显示复杂内容（如图像或文本块）以及简单的命令（如 [AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton)、[AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton) 和 [AppBarSeparator](/uwp/api/windows.ui.xaml.controls.appbarseparator) 控件）。

> [!NOTE]
> XAML 提供 [AppBar](/uwp/api/windows.ui.xaml.controls.appbar) 控件和 [CommandBar](/uwp/api/windows.ui.xaml.controls.commandbar) 控件。 应仅在升级使用 AppBar 的通用 Windows 8 应用时使用 AppBar，并且需要最大程序减少更改。 对于 Windows 10 中的新应用，我们建议改用 CommandBar 控件。 此文档假定你将要使用 CommandBar 控件。

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处<a href="xamlcontrolsgallery:/item/CommandBar">打开此应用，了解 CommandBar 的实际应用</a>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

展开的命令栏。

![展开的命令栏](images/control-examples/command-bar-photos.png)

## <a name="anatomy"></a>结构

默认情况下，命令栏显示一行图标按钮和一个可选的“查看更多”按钮，该按钮由省略号 \[•••\] 表示。 下面是由稍后所示的示例代码创建的命令栏。 它在其关闭、精简状态下显示。

![显示已关闭命令栏的屏幕截图。](images/command-bar-compact.png)

命令栏可在如下所示的关闭、最小状态下显示。 有关详细信息，请参阅[打开和关闭状态](#open-and-closed-states)部分。

![显示处于最小关闭状态的命令栏的屏幕截图。](images/command-bar-minimal.png)

下面是处于打开状态的相同命令栏。 标签标识控件的主要部分。

![显示处于打开状态的命令栏的屏幕截图。](images/commandbar_anatomy_open.png)

命令栏分为 4 个主要区域：
- 内容区域与栏的左侧对齐。 如果填充了 [Content](/uwp/api/windows.ui.xaml.controls.contentcontrol.content) 属性，将显示该区域。
- 主要命令区域与栏的右侧对齐。 如果填充了 [PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands) 属性，将显示该区域。  
- “查看更多”\[•••\] 按钮显示在该栏的右侧。 按“查看更多”\[•••\] 按钮会显示主要命令标签并在存在辅助命令的情况下打开溢出菜单。 当不存在任何主要命令标签或辅助标签时，该按钮将不可见。 要更改默认行为，请使用 [OverflowButtonVisibility](/uwp/api/windows.ui.xaml.controls.commandbar.overflowbuttonvisibility) 属性。
- 仅在命令栏处于打开状态并且填充了 [SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands) 属性时才显示溢出菜单。 当空间有限时，主要命令会移到 SecondaryCommands 区域中。 要更改默认行为，请使用 [IsDynamicOverflowEnabled](/uwp/api/windows.ui.xaml.controls.commandbar.isdynamicoverflowenabled) 属性。

当 [FlowDirection](/uwp/api/windows.ui.xaml.frameworkelement.flowdirection) 为 **RightToLeft** 时，布局会反转。

## <a name="create-a-command-bar"></a>创建命令栏
此示例创建以前显示的命令栏。

```xaml
<CommandBar>
    <AppBarToggleButton Icon="Shuffle" Label="Shuffle" Click="AppBarButton_Click" />
    <AppBarToggleButton Icon="RepeatAll" Label="Repeat" Click="AppBarButton_Click"/>
    <AppBarSeparator/>
    <AppBarButton Icon="Back" Label="Back" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Stop" Label="Stop" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Play" Label="Play" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Forward" Label="Forward" Click="AppBarButton_Click"/>

    <CommandBar.SecondaryCommands>
        <AppBarButton Label="Like" Click="AppBarButton_Click"/>
        <AppBarButton Label="Dislike" Click="AppBarButton_Click"/>
    </CommandBar.SecondaryCommands>

    <CommandBar.Content>
        <TextBlock Text="Now playing..." Margin="12,14"/>
    </CommandBar.Content>
</CommandBar>
```

## <a name="commands-and-content"></a>命令和内容
CommandBar 控件具有 3 个可用于添加命令和内容的属性：[PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbar.primarycommands)、[SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbar.secondarycommands) 和 [Content](/uwp/api/windows.ui.xaml.controls.contentcontrol.content)。


### <a name="commands"></a>命令

默认情况下，添加到命令栏中的项目也会添加到 PrimaryCommands 集合中  。 应按命令重要性的顺序添加命令，以便最重要的命令始终可见。 当命令栏宽度发生更改时，例如当用户调整其应用窗口大小时，在断点处，主要命令会在命令栏和溢出菜单间动态移动。 要更改此默认行为，请使用 [IsDynamicOverflowEnabled](/uwp/api/windows.ui.xaml.controls.commandbar.isdynamicoverflowenabled) 属性。 

在最小的屏幕（宽度为 320 epx）上，命令栏中最多可放入 4 个主要命令。 

你也可以将命令添加到 SecondaryCommands 集合中，该集合将显示在溢出菜单中  。

![带有“更多”区域和图标的命令栏示例](images/appbar_rs2_overflow_icons.png)

你可以根据需要以编程方式在 PrimaryCommands 和 SecondaryCommands 之间移动命令。

- 如果某个命令会一直显示在各个页面上，则最好将该命令放置在一个一致的位置上  。
- 我们建议将“接受”、“是”和“确定”命令放置在“拒绝”、“否”和“取消”的左侧。  一致性可以增加用户浏览系统的信心，并帮助他们在应用之间传递应用导航知识。

### <a name="app-bar-buttons"></a>应用栏按钮

PrimaryCommands 和 SecondaryCommands 都只能使用[AppBarButton](/uwp/api/Windows.UI.Xaml.Controls.AppBarButton)、[AppBarToggleButton](/uwp/api/Windows.UI.Xaml.Controls.AppBarToggleButton) 和 [AppBarSeparator](/uwp/api/Windows.UI.Xaml.Controls.AppBarSeparator) 命令元素填充。 

应用栏按钮控件以一个图标和文本标签为特征。 这些控件经过优化以便在命令栏中使用，它们的外观根据控件是用在命令栏中还是溢出菜单中进行更改。

#### <a name="icons"></a>图标

在主命令区域中显示时，图标大小为 20x20px；在溢出菜单中，图标按 16x16px 显示。 如果使用 [SymbolIcon](/uwp/api/windows.ui.xaml.controls.symbolicon)、[FontIcon](/uwp/api/windows.ui.xaml.controls.fonticon) 或 [PathIcon](/uwp/api/windows.ui.xaml.controls.pathicon)，则图标会在命令进入辅助命令区域时，自动缩放为正确大小而不会失真。

有关设置图标的详细信息和示例，请查看 [AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton) 类的文档。

#### <a name="labels"></a>标签

AppBarButton [IsCompact](/uwp/api/windows.ui.xaml.controls.appbarbutton.IsCompact) 属性确定是否显示标签。 在 CommandBar 控件中，命令栏随着命令栏的打开和关闭而自动覆盖按钮的 IsCompact 属性。

要放置应用栏按钮标签，请使用 CommandBar 的 [DefaultLabelPosition](/uwp/api/windows.ui.xaml.controls.commandbar.defaultlabelposition) 属性。

```xaml
<CommandBar DefaultLabelPosition="Right">
    <AppBarToggleButton Icon="Shuffle" Label="Shuffle"/>
    <AppBarToggleButton Icon="RepeatAll" Label="Repeat"/>
</CommandBar>
```

![标签位于右侧的命令栏](images/app-bar-labels-on-right.png)

在较大的窗口上，请考虑将标签移动到应用栏按钮图标的右侧，以改善可读性。 底部的标签需要用户打开命令栏才会显示，而右侧的标签在命令栏关闭的情况下也可见。

在溢出菜单中，标签默认放在图标右侧，并且忽略 LabelPosition  。 可通过将 [CommandBarOverflowPresenterStyle](/uwp/api/Windows.UI.Xaml.Controls.CommandBar.CommandBarOverflowPresenterStyle) 属性设置为面向 [CommandBarOverflowPresenter](/uwp/api/windows.ui.xaml.controls.commandbaroverflowpresenter) 的样式来调整样式设置。 

按钮标签应短，最好是一个单词。 图标下方较长的标签会换行为多行，从而增加打开的命令栏的总体高度。 你可以在文本中包含软连字符 (0x00AD)，以使标签提示应发生换行的字符边界。 在 XAML 中，你使用转义序列表示它，如下所示：

```xaml
<AppBarButton Icon="Back" Label="Areally&#x00AD;longlabel"/>
```

当标签在提示的位置换行时，它如下所示。

![带有换行标签的应用栏按钮](images/app-bar-button-label-wrap.png)

### <a name="command-bar-flyouts"></a>命令栏浮出控件

考虑对这些命令进行逻辑分组，例如将“答复”、“全部答复”和“转发”置于“响应”菜单中。 虽然一个应用栏按钮通常激活单个按钮，但应用栏按钮可用于显示有自定义内容的 [MenuFlyout](/uwp/api/windows.ui.xaml.controls.menuflyout) 或 [Flyout](/uwp/api/windows.ui.xaml.controls.flyout)。

![命令栏浮出控件示例](images/AppbarGuidelines_Flyouts.png)

### <a name="other-content"></a>其他内容

你可以通过设置 **Content** 属性来将任何 XAML 元素添加到内容区域。 如果要添加多个元素，需要将它们放置在面板容器中，并使该面板成为 Content 属性的单个子对象。

启用动态溢出时不会裁剪内容，这是因为主要命令可移动到溢出菜单中。 否则，主要命令具有优先权，可能会导致内容裁剪。

当 [ClosedDisplayMode](/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode) 为 Compact 时，如果内容大于命令栏的精简大小，则可能会剪裁内容  。 你应处理 [Opening](/uwp/api/windows.ui.xaml.controls.appbar.opening) 和 [Closed](/uwp/api/windows.ui.xaml.controls.appbar.closed) 事件以在内容区域中显示或隐藏部分 UI，以使它们不会进行剪裁。 有关详细信息，请参阅[打开和关闭状态](#open-and-closed-states)部分。


## <a name="open-and-closed-states"></a>打开和关闭状态

可以打开或关闭命令栏。 打开它时，它显示带有文本标签的主命令按钮，并打开溢出菜单（如果有辅助命令）。
命令栏向上（主命令上方）或向下（主命令下方）展开溢出菜单。 默认方向为向上，但如果没有足够空间向上展开溢出菜单，则命令栏向下展开。 

用户可通过按“查看更多”\[•••\] 按钮在这些状态之间切换。 你可以通过设置 [IsOpen](/uwp/api/windows.ui.xaml.controls.appbar.isopen) 属性来以编程方式在它们之间切换。 

你可以使用 [Opening](/uwp/api/windows.ui.xaml.controls.appbar.opening)、[Opened](/uwp/api/windows.ui.xaml.controls.appbar.opened)、[Closing](/uwp/api/windows.ui.xaml.controls.appbar.closing) 和 [Closed](/uwp/api/windows.ui.xaml.controls.appbar.closed) 事件来响应要打开或关闭的命令栏。  
- Opening 和 Closing 事件在过渡动画开始前发生。
- Opened 和 Closed 事件在过渡完成后发生。

在此示例中，Opening 和 Closing 事件用于更改命令栏的不透明度。 命令栏处于关闭状态时，它是半透明的，以使应用背景隐约显示出来。 命令栏处于打开状态时，命令栏变为不透明，以使用户专注于命令。

```xaml
<CommandBar Opening="CommandBar_Opening"
            Closing="CommandBar_Closing">
    <AppBarButton Icon="Accept" Label="Accept"/>
    <AppBarButton Icon="Edit" Label="Edit"/>
    <AppBarButton Icon="Save" Label="Save"/>
    <AppBarButton Icon="Cancel" Label="Cancel"/>
</CommandBar>
```

```csharp
private void CommandBar_Opening(object sender, object e)
{
    CommandBar cb = sender as CommandBar;
    if (cb != null) cb.Background.Opacity = 1.0;
}

private void CommandBar_Closing(object sender, object e)
{
    CommandBar cb = sender as CommandBar;
    if (cb != null) cb.Background.Opacity = 0.5;
}

```

### <a name="issticky"></a>IsSticky

如果用户在命令栏处于打开状态时与应用的其他部分交互，命令栏将自动关闭。 这称为轻型消除  。 可以通过设置 [IsSticky](/uwp/api/windows.ui.xaml.controls.appbar.issticky) 属性来控制轻型消除行为。 如果 `IsSticky="true"`，在用户按“查看更多”\[•••\] 按钮或从溢出菜单中选择项目之前，该栏保持打开状态。 

建议避免使用粘滞命令栏，因为它们不符合用户对[轻型消除和键盘焦点行为](./menus.md#light-dismiss)的预期。

### <a name="display-mode"></a>显示模式

你可以通过设置 [ClosedDisplayMode](/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode) 属性来控制命令栏在关闭状态下的显示方式。 有三种可供选择的关闭显示模式：
- **精简**：默认模式。 显示内容、不带有标签的主要命令图标和“查看更多”\[•••\] 按钮。
- **最小**：仅显示充当“查看更多”\[•••\] 按钮的细栏。 用户可以按栏上的任意位置来将其打开。
- **隐藏**：命令栏在关闭时不显示。 这对于显示带有内联命令栏的上下文命令非常有用。 在此情况下，你必须通过设置 **IsOpen** 属性或将 ClosedDisplayMode 更改为 **最小** 或 **精简** 来以编程方式打开命令栏。

此处，命令栏用于为 [RichEditBox](/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) 承载简单格式化命令。 当编辑框没有焦点时，格式化命令可能令人分心，因此隐藏它们。 当使用编辑框时，命令栏的 ClosedDisplayMode 更改为精简，以便使格式化命令可见。

```xaml
<StackPanel Width="300"
            GotFocus="EditStackPanel_GotFocus"
            LostFocus="EditStackPanel_LostFocus">
    <CommandBar x:Name="FormattingCommandBar" ClosedDisplayMode="Hidden">
        <AppBarButton Icon="Bold" Label="Bold" ToolTipService.ToolTip="Bold"/>
        <AppBarButton Icon="Italic" Label="Italic" ToolTipService.ToolTip="Italic"/>
        <AppBarButton Icon="Underline" Label="Underline" ToolTipService.ToolTip="Underline"/>
    </CommandBar>
    <RichEditBox Height="200"/>
</StackPanel>
```

```csharp
private void EditStackPanel_GotFocus(object sender, RoutedEventArgs e)
{
    FormattingCommandBar.ClosedDisplayMode = AppBarClosedDisplayMode.Compact;
}

private void EditStackPanel_LostFocus(object sender, RoutedEventArgs e)
{
    FormattingCommandBar.ClosedDisplayMode = AppBarClosedDisplayMode.Hidden;
}
```

>注意&nbsp;&nbsp;编辑命令的实现不在本示例范围内  。 有关详细信息，请参阅 [RichEditBox](rich-edit-box.md) 文章。

尽管最小和隐藏模式在某些情况下很有用，但请记住，隐藏所有操作可能会使用户困惑。

更改 ClosedDisplayMode 以向用户提供或多或少的提示会影响周围元素的布局。 相比之下，当 CommandBar 在关闭和打开之间转换时，它不会影响其他元素的布局。

## <a name="placement"></a>放置
命令栏可以放置在应用窗口顶部、底部和内联放置，方法是将其嵌入到布局控件（例如 ```Grid.row```）中。

![应用栏放置示例 1](images/AppbarGuidelines_Placement1.png)

-   对于较小的手持设备，我们建议将命令栏定位在屏幕底部以易于操作。
-   对于具有较大屏幕的设备，将命令栏靠近窗口顶部放置会使它们更为醒目，并且更易于发现。

使用 [DiagonalSizeInInches](/uwp/api/windows.graphics.display.displayinformation.diagonalsizeininches) API 确定物理屏幕大小。

命令栏可以放置在单视图屏幕（左边的示例）和多视图屏幕（右边的示例）上的以下屏幕区域中。 内联命令栏可以放置在操作空间中的任意位置。

![应用栏放置示例 2](images/AppbarGuidelines_Placement2.png)

>**触摸设备**：如果命令栏必须在触摸键盘或软输入面板 (SIP) 出现时对用户保持可见，则可以将命令栏分配到某个页面的 [BottomAppBar](/uwp/api/windows.ui.xaml.controls.page.bottomappbar) 属性，它将转换为在 SIP 出现时保持可见。 否则，你应内联放置命令栏，并根据应用内容定位。

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。
- [XAML 命令示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlCommanding)

## <a name="related-articles"></a>相关文章

* [Windows 应用的命令设计基础知识](../basics/commanding-basics.md)
* [CommandBar 类](/uwp/api/Windows.UI.Xaml.Controls.CommandBar)
