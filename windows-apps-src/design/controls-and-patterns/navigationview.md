---
description: NavigationView 是为应用实现顶级导航模式的自适应控件。
title: NavigationView
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: ''
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: 959fc9f77c94ee05bf4191bd3ce01049a8163bde
ms.sourcegitcommit: b89d3bc42713fbe4c0ada99d6f514f1304821221
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107466447"
---
# <a name="navigationview"></a>NavigationView

NavigationView 控件可为应用提供顶级导航。 它适应各种屏幕大小，支持顶部和左侧导航样式。

![顶部导航](images/nav-view-header.png)<br/>
NavigationView 支持顶部和左侧导航窗格或菜单

**获取 Windows UI 库**

:::row:::
   :::column:::
      ![WinUI 徽标](images/winui-logo-64x64.png)
   :::column-end:::
   :::column span="3":::
      NavigationView 控件作为 Windows UI 库的一部分提供，该库是一个 NuGet 包，其中包含用于 Windows 应用的新控件和 UI 功能。 有关详细信息（包括安装说明），请参阅 [Windows UI 库概述](/uwp/toolkits/winui/)。
   :::column-end:::
   :::column:::

   :::column-end:::
:::row-end:::

> **平台 API**：[Windows.UI.Xaml.Controls.NavigationView 类](/uwp/api/windows.ui.xaml.controls.navigationview)
>
> Windows UI 库 API：[Microsoft.UI.Xaml.Controls.NavigationView 类](/uwp/api/microsoft.ui.xaml.controls.navigationview)
>
> NavigationView 的某些功能（如顶部和分层导航）需要 Windows 10 版本 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) 或更高版本，或是 [Windows UI 库](/uwp/toolkits/winui/)。

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

NavigationView 是非常适合于以下功能的自适应导航控件：

- 在整个应用中提供一致的导航体验。
- 在较小窗口上保留屏幕空间。
- 组织对许多导航类别的访问。

有关其他导航模式，请参阅[导航设计基础知识](../basics/navigation-basics.md)。

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/XAML-controls-gallery-app-icon-sm.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处<a href="xamlcontrolsgallery:/item/NavigationView">打开此应用，了解 NavigationView 的实际应用</a>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="display-modes"></a>显示模式

> PaneDisplayMode 属性需要 Windows 10 版本 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) 或更高版本，或是 [Windows UI 库](/uwp/toolkits/winui/)。

可以使用 PaneDisplayMode 属性配置不同的导航样式或显示模式（对于 NavigationView）。

:::row:::
    :::column:::
    ### <a name="top"></a>顶部
    窗格位于内容上方。</br>
    `PaneDisplayMode="Top"`
    :::column-end:::
    :::column span="2":::
    ![顶部导航的示例](images/displaymode-top.png)
    :::column-end:::
:::row-end:::

在以下情况下建议使用顶部导航：

- 具有 5 个或更少的同等重要顶级导航类别，最终采用下拉溢出菜单中的任何其他顶级导航类别都被视为不太重要。
- 需要在屏幕上显示所有导航选项。
- 要为应用内容提供更多空间。
- 图标无法清楚地描述应用的导航类别。

:::row:::
    :::column:::
    ### <a name="left"></a>左
    窗格会展开并位于内容左侧。</br>
    `PaneDisplayMode="Left"`
    :::column-end:::
    :::column span="2":::
    ![展开的左侧导航窗格示例](images/displaymode-left.png)
    :::column-end:::
:::row-end:::

在以下情况下建议使用左侧导航：

- 具有 5-10 个同等重要的顶级导航类别。
- 希望导航类别非常突出，而其他应用内容的空间较少。

:::row:::
    :::column:::
    ### <a name="leftcompact"></a>LeftCompact
    窗格在打开之前仅显示图标，位于内容左侧。</br>
    `PaneDisplayMode="LeftCompact"`
    :::column-end:::
    :::column span="2":::
    ![精简左侧导航窗格示例](images/displaymode-leftcompact.png)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
    ### <a name="leftminimal"></a>LeftMinimal
    窗格打开之前仅显示菜单按钮。 打开时，它位于内容左侧。</br>
    `PaneDisplayMode="LeftMinimal"`
    :::column-end:::
    :::column span="2":::
    ![最小左侧导航窗格示例](images/displaymode-leftminimal.png)
    :::column-end:::
:::row-end:::

### <a name="auto"></a>Auto

默认情况下，PaneDisplayMode 设置为 Auto。在 Auto 模式下，NavigationView 会进行自适应，在窗口狭窄时为 LeftMinimal，接下来为 LeftCompact，随后在窗口变宽时为 Left。 有关更多信息，请参阅[自适应行为](#adaptive-behavior)部分。

![左侧导航默认自适应行为](images/displaymode-auto.png)<br/>
NavigationView 默认自适应行为

## <a name="anatomy"></a>结构

这些图像显示为顶部或左侧导航进行配置时，控件窗格、标题和内容区域的布局。

![顶部 NavigationView 布局](images/topnav-anatomy.png)<br/>
顶部导航布局

![左侧 NavigationView 布局](images/leftnav-anatomy.png)<br/>
左侧导航布局

### <a name="pane"></a>窗格

可以使用 PaneDisplayMode 属性将窗格置于内容上方或内容左侧。

NavigationView 窗格可以包含：

- [NavigationViewItem](/uwp/api/windows.ui.xaml.controls.navigationviewitem) 对象。 用于导航到特定页面的导航项。
- [NavigationViewItemSeparator](/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator) 对象。 用于对导航项进行分组的分隔器。 将 [Opacity](/uwp/api/windows.ui.xaml.uielement.opacity) 属性设置为 0 可将分隔器呈现为空间。
- [NavigationViewItemHeader](/uwp/api/windows.ui.xaml.controls.navigationviewitemheader) 对象。 用于标记项组的标题。
- 允许应用级别搜索的可选 [AutoSuggestBox](auto-suggest-box.md) 控件。 可将该控件分配给 [NavigationView.AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.navigationview.autosuggestbox) 属性。
- [应用设置](../app-settings/guidelines-for-app-settings.md)的可选入口点。 若要隐藏设置项，请将 [IsSettingsVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible) 属性设置为 false。

左窗格还包含：

- 用于切换窗格打开和关闭状态的菜单按钮。 打开窗格后在较大的应用窗口中，你可以选择使用 [IsPaneToggleButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible) 属性隐藏此按钮。

NavigationView 有一个后退按钮，它位于窗格的左上角。 但是，它不会自动处理向后导航以及将内容添加到 Backstack。 若要启用向后导航，请参阅[向后导航](#backwards-navigation)部分。

下面是顶部和左侧窗格位置的详细窗格结构。

#### <a name="top-navigation-pane"></a>顶部导航窗格

![NavigationView 顶部窗格剖析](images/navview-pane-anatomy-horizontal.png)

1. 标头
1. 导航项
1. 分隔器
1. AutoSuggestBox（可选）
1. 设置按钮（可选）

#### <a name="left-navigation-pane"></a>左侧导航窗格

![NavigationView 左侧窗格剖析](images/navview-pane-anatomy-vertical.png)

1. “菜单”按钮
1. 导航项
1. 分隔器
1. 标头
1. AutoSuggestBox（可选）
1. 设置按钮（可选）

#### <a name="footer-menu-items"></a>页脚菜单项
你可以使用 [FooterMenuItems](/uwp/api/microsoft.ui.xaml.controls.navigationview.FooterMenuItems) 将导航项放置在导航窗格的末尾，与将项目放置在窗格的开头处的 [MenuItems](/uwp/api/microsoft.ui.xaml.controls.navigationview.MenuItems) 属性形成对比。

默认情况下，FooterMenuItems 将显示在“设置”项之前。 “设置”项仍然可以使用 [`IsSettingsVisible`](/uwp/api/microsoft.ui.xaml.controls.navigationview.IsSettingsVisible) 属性进行切换。

只有“导航”项应该放在 FooterMenuItems 中 - 任何其他需要与窗格页脚对齐的内容都应该放在 [PaneFooter](/uwp/api/microsoft.ui.xaml.controls.navigationview.PaneFooter) 中。

有关如何向 NavigationView 添加 FooterMenuItems 的示例，请参阅 [FooterMenuItems 类](/uwp/api/microsoft.ui.xaml.controls.navigationview.FooterMenuItems)。 

下图显示了页脚菜单中包含“帐户”、“购物车”和“帮助”导航项的 NavigationView。 

![带有 FooterMenuItems 的 NavigationView](images/footermenu-leftmode.png)

#### <a name="pane-footer"></a>窗格页脚

可以通过将自由格式内容添加到 [PaneFooter](/uwp/api/microsoft.ui.xaml.controls.navigationview.PaneFooter) 属性，将该内容置于窗格页脚中。

:::row:::
    :::column:::
    ![窗格页脚顶部导航](images/navview-freeform-footer-top.png)<br>
     顶部窗格页脚<br>
    :::column-end:::
    :::column:::
    ![窗格页脚左侧导航](images/navview-freeform-footer-left.png)<br>
    左侧窗格页脚<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-title-and-header"></a>窗格标题和页眉

可以通过设置 [PaneTitle](/uwp/api/windows.ui.xaml.controls.navigationview.PaneTitle) 属性，在窗格标题区域中放置文本内容。 它采用一个字符串，在菜单按钮旁显示文本。

若要添加非文本内容（如图像或徽标），可以通过将任何元素添加到 [PaneHeader](/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader) 属性来置于窗格标题中。

如果同时设置了 PaneTitle 和 PaneHeader，则内容会在菜单按钮旁水平堆叠（PaneTitle 最接近菜单按钮）。

:::row:::
    :::column:::
    ![窗格标题顶部导航](images/navview-freeform-header-top.png)<br>
     顶部窗格标题<br>
    :::column-end:::
    :::column:::
    ![窗格标题左侧导航](images/navview-freeform-header-left.png)<br>
    左侧窗格标题<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-content"></a>窗格内容

可以通过将自由格式内容添加到 [PaneCustomContent](/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent) 属性，将该内容置于窗格中。

:::row:::
    :::column:::
    ![窗格自定义内容顶部导航](images/navview-freeform-pane-top.png)<br>
     顶部窗格自定义内容<br>
    :::column-end:::
    :::column:::
    ![窗格自定义内容左侧导航](images/navview-freeform-pane-left.png)<br>
    左侧窗格自定义内容<br>
    :::column-end:::
:::row-end:::

### <a name="header"></a>Header

可以通过设置 [Header](/uwp/api/windows.ui.xaml.controls.navigationview.header) 属性来添加页面标题。

![NavigationView 标题区域示例](images/nav-header.png)<br/>
NavigationView 标题

标题区域在左侧窗格位置中与导航按钮垂直对齐，在顶部窗格位置中位于窗格下方。 它具有固定高度 52 px。 它的用途是保存所选导航类别的页面标题。 标题停靠在页面顶部，并作为内容区域的滚动剪辑点。

只要 NavigationView 处于最小显示模式下，标题便可见。 你可以选择在其他模式下隐藏标题，这样可以增大窗口宽度。 若要隐藏标题，请将 [AlwaysShowHeader](/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) 属性设置为 False。

### <a name="content"></a>内容

![NavigationView 内容区域示例](images/nav-content.png)<br/>
NavigationView 内容

内容区域是显示所选导航类别的大部分信息的位置。

建议在 NavigationView 处于最小模式时对内容区域使用 12px 边距，其他情况使用 24px 边距。

## <a name="adaptive-behavior"></a>自适应行为

默认情况下，NavigationView 会根据可用的屏幕空间大小自动更改其显示模式。 [CompactModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.compactmodethresholdwidth) 和 [ExpandedModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.expandedmodethresholdwidth) 属性指定显示模式更改的断点。 可以修改这些值以自定义自适应显示模式行为。

### <a name="default"></a>默认值

当 PaneDisplayMode 设置为其默认值 Auto 时，自适应行为是：

- 在较大窗口宽度（1008px 或更大）上显示展开的左窗格。
- 在中等窗格宽度（641px 到 1007px）上显示仅限图标的左侧导航窗格 (LeftCompact)。
- 在较小窗口宽度（640px 或更小）上仅显示菜单按钮 (LeftMinimal)。

有关自适应行为的窗口大小的更多信息，请参阅[屏幕大小和断点](../layout/screen-sizes-and-breakpoints-for-responsive-design.md)。

![左侧导航默认自适应行为](images/displaymode-auto.png)<br/>
NavigationView 默认自适应行为

### <a name="minimal"></a>最小

第二种常见自适应模式是在较大窗口宽度上使用展开的左侧窗格，在中等和较小窗口宽度上仅使用菜单按钮。

在以下情况下建议使用此模式：

- 在较小窗口宽度上需要将更多空间用于应用内容。
- 无法使用图标清楚地表示导航类别。

![左侧导航最小自适应行为](images/adaptive-behavior-minimal.png)<br/>
NavigationView“最小”自适应行为

若要配置此行为，请将 CompactModeThresholdWidth 设为要折叠窗格时的宽度。 在这里，它从默认值 640 更改为 1007。 还应设置 ExpandedModeThresholdWidth 以确保值不会发生冲突。

```xaml
<NavigationView CompactModeThresholdWidth="1007" ExpandedModeThresholdWidth="1007"/>
```

### <a name="compact"></a>精简

第三种常见自适应模式是在较大窗口宽度上使用展开的左侧窗格，在中等和较小窗口宽度上使用仅限图标的 LeftCompact 导航窗格。

在以下情况下建议使用此模式：

- 始终在屏幕上显示所有导航选项非常重要。
- 可以使用图标清楚地表示导航类别。

![左侧导航精简自适应行为](images/adaptive-behavior-compact.png)<br/>
NavigationView“精简”自适应行为

若要配置此行为，请将 CompactModeThresholdWidth 设置为 0。

```xaml
<NavigationView CompactModeThresholdWidth="0"/>
```

### <a name="no-adaptive-behavior"></a>无自适应行为

若要禁用自动自适应行为，请将 PaneDisplayMode 设置为 Auto 以外的值。在这里，它设置为 LeftMinimal，因此仅显示菜单按钮，无论窗口宽度如何。

![左侧导航无自适应行为](images/adaptive-behavior-none.png)<br/>
PaneDisplayMode 设置为 LeftMinimal 的 NavigationView

```xaml
<NavigationView PaneDisplayMode="LeftMinimal" />
```

如前面在“显示模式”部分中所述，可以将窗格设置为始终位于顶部、始终展开、始终精简或始终最小。 还可以在应用代码中自己管理显示模式。 下一部分中演示了这一示例。

### <a name="top-to-left-navigation"></a>顶部到左侧导航

在应用中使用顶部导航时，导航项会随着窗口宽度减少而折叠到溢出菜单中。 当应用窗口较窄时，将 PaneDisplayMode 从 Top 切换为 LeftMinimal 导航可以提供更好的用户体验，而不是让所有项都折叠到溢出菜单中。

在以下情况下，建议在较大窗口大小上使用顶部导航，在较小窗口大小上使用左侧导航：

- 要一起显示一组同等重要的顶级导航类别，以便在此组中的一个类别不适合屏幕时，会折叠为左侧导航栏以便使它们具有同等重要性。
- 希望在较小窗口大小中保留尽可能多的内容空间。

此示例演示如何使用 [VisualStateManager](/uwp/api/Windows.UI.Xaml.VisualStateManager) 和 [AdaptiveTrigger.MinWindowWidth](/uwp/api/windows.ui.xaml.adaptivetrigger.minwindowwidth) 属性在 Top 与 LeftMinimal 导航之间进行切换。

![顶部或左侧自适应行为 1 的示例](images/navigation-top-to-left.png)

```xaml
<Grid>
    <NavigationView x:Name="NavigationViewControl" >
        <NavigationView.MenuItems>
            <NavigationViewItem Content="A" x:Name="A" />
            <NavigationViewItem Content="B" x:Name="B" />
            <NavigationViewItem Content="C" x:Name="C" />
        </NavigationView.MenuItems>
    </NavigationView>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState>
                <VisualState.StateTriggers>
                    <AdaptiveTrigger
                        MinWindowWidth="{x:Bind NavigationViewControl.CompactModeThresholdWidth}" />
                </VisualState.StateTriggers>

                <VisualState.Setters>
                    <Setter Target="NavigationViewControl.PaneDisplayMode" Value="Top"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>

```

> [!TIP]
> 如果使用 AdaptiveTrigger.MinWindowWidth，则当窗口宽于指定最小宽度时，会触发可视状态。 这意味着默认 XAML 定义狭窄窗口，而 VisualState 定义在窗口变得更宽时应用的修改。 NavigationView 的默认 PaneDisplayMode 为 Auto，所以当窗口宽度小于或等于 CompactModeThresholdWidth 时，就会使用 LeftMinimal 导航。 当窗口变得更宽时，VisualState 会替代默认值，并使用 Top 导航。

## <a name="navigation"></a>导航

NavigationView 不会自动执行任何导航任务。 当用户点击某个导航项时，NavigationView 会将此项显示为已选中，并触发 [ItemInvoked](/uwp/api/windows.ui.xaml.controls.navigationview.ItemInvoked) 事件。 如果点击导致新项被选中，则还会引发 [SelectionChanged](/uwp/api/windows.ui.xaml.controls.navigationview.SelectionChanged) 事件。

可以处理任一事件来执行与请求的导航相关的任务。 应处理哪个事件取决于希望应用具有的行为。 通常情况下，你会导航到请求的页面，并更新 NavigationView 标题来响应这些事件。

每当用户点击导航项时都会引发 ItemInvoked，即使该项已被选中。 （还可以使用鼠标、键盘或其他输入通过等效操作调用该项。 有关更多信息，请参阅[输入和交互](../input/index.md)。）如果在 ItemInvoked 处理程序中导航，则默认情况下，会重新加载页面，并将重复条目添加到导航堆栈。 如果在调用某个项时进行导航，则应禁止重新加载页面，或确保在重新加载页面时不会在导航 Backstack 中创建重复条目。 （请参阅代码示例。）

用户可以通过调用当前未选中的项，或是以编程方式更改所选项来引发 SelectionChanged。 如果因为用户调用某个项而发生选择更改，则首先发生 ItemInvoked 事件。 如果以编程方式进行选择更改，则不会引发 ItemInvoked。

所有导航项都是同一选择模型的一部分，无论它们是 [MenuItems](/uwp/api/microsoft.ui.xaml.controls.navigationview.MenuItems) 还是 [FooterMenuItems](/uwp/api/microsoft.ui.xaml.controls.navigationview.FooterMenuItems) 的一部分。 一次只能选择一个导航项。

### <a name="backwards-navigation"></a>向后导航

NavigationView 具有内置后退按钮；但是与向前导航一样，它不会自动执行向后导航。 用户点击后退按钮时，会引发 [BackRequested](/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested) 事件。 可处理此事件以执行向后导航。 有关更多信息和代码示例，请参阅[导航历史记录和向后导航](../basics/navigation-history-and-backwards-navigation.md)。

在 Minimal 或 Compact 模式下，NavigationView 窗格会作为浮出控件打开。 在这种情况下，单击后退按钮会关闭窗格并改为引发 PaneClosing 事件。

可以通过设置以下这些属性来隐藏或禁用后退按钮：

- [IsBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible)：用于显示和隐藏后退按钮。 此属性采用 [NavigationViewBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationviewbackbuttonvisible) 枚举值，默认情况下设置为 Auto。 当按钮处于折叠状态时，不会在布局中为它保留空间。
- [IsBackEnabled](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled)：用于启用或禁用后退按钮。 可以将此属性数据绑定到导航框架的 [CanGoBack](/uwp/api/windows.ui.xaml.controls.frame.cangoback) 属性。 如果 IsBackEnabled 为 false，则不会引发 BackRequested。

:::row:::
    :::column:::
        ![左侧导航窗格中的 NavigationView 后退按钮](images/leftnav-back.png)<br/>
        左侧导航窗格中的后退按钮
    :::column-end:::
    :::column:::
        ![顶部导航窗格中的 NavigationView 后退按钮](images/topnav-back.png)<br/>
        顶部导航窗格中的后退按钮
    :::column-end:::
:::row-end:::

## <a name="code-example"></a>代码示例

> [!IMPORTANT]
> 对于任何利用 Windows UI (WinUI) 库工具包的项目，都需要执行相同的初步设置步骤。 如需更多的背景、设置和支持信息，请参阅 [Windows UI 库入门](/uwp/toolkits/winui/getting-started)。

此示例演示如何将 **NavigationView** 用于顶部导航窗格（在窗口大小较大时）和左侧导航窗格（在窗口大小较小时）。 它可以调整为适合仅限左侧的导航，具体方法是在 VisualStateManager 中删除顶部导航设置。

该示例演示一种设置导航数据的推荐方法，它适用于许多常见方案。 该示例还演示如何使用 NavigationView 的后退按钮和键盘导航实现向后导航。

此代码假设应用包含的要导航到的页面具有以下名称：HomePage、AppsPage、GamesPage、MusicPage、MyContentPage 和 SettingsPage。 未显示这些页面的代码。

> [!IMPORTANT]
> 有关应用页面的信息存储在 [ValueTuple](/dotnet/api/system.valuetuple) 中。 此结构要求应用项目的最低版本必须是 SDK 17763 或更高版本。 如果使用 WinUI 版本的 NavigationView 针对早期版本的 Windows 10，则可以改为使用 [System.ValueTuple NuGet 包](https://www.nuget.org/packages/System.ValueTuple/)。

> [!IMPORTANT]
> 此代码演示如何使用 [Windows UI 库](/uwp/toolkits/winui/)版本的 NavigationView。 如果改为使用平台版本的 NavigationView，则应用项目的最低版本必须是 SDK 17763 或更高版本。 若要使用平台版本，请删除对 `muxc:` 的所有引用。

```xaml
<Page ... xmlns:muxc="using:Microsoft.UI.Xaml.Controls" ... >
<Grid>
    <muxc:NavigationView x:Name="NavView"
                         Loaded="NavView_Loaded"
                         ItemInvoked="NavView_ItemInvoked"
                         BackRequested="NavView_BackRequested">
        <muxc:NavigationView.MenuItems>
            <muxc:NavigationViewItem Tag="home" Icon="Home" Content="Home"/>
            <muxc:NavigationViewItemSeparator/>
            <muxc:NavigationViewItemHeader x:Name="MainPagesHeader"
                                           Content="Main pages"/>
            <muxc:NavigationViewItem Tag="apps" Content="Apps">
                <muxc:NavigationViewItem.Icon>
                    <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xEB3C;"/>
                </muxc:NavigationViewItem.Icon>
            </muxc:NavigationViewItem>
            <muxc:NavigationViewItem Tag="games" Content="Games">
                <muxc:NavigationViewItem.Icon>
                    <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE7FC;"/>
                </muxc:NavigationViewItem.Icon>
            </muxc:NavigationViewItem>
            <muxc:NavigationViewItem Tag="music" Icon="Audio" Content="Music"/>
        </muxc:NavigationView.MenuItems>

        <muxc:NavigationView.AutoSuggestBox>
            <!-- See AutoSuggestBox documentation for
                 more info about how to implement search. -->
            <AutoSuggestBox x:Name="NavViewSearchBox" QueryIcon="Find"/>
        </muxc:NavigationView.AutoSuggestBox>

        <ScrollViewer>
            <Frame x:Name="ContentFrame" Padding="12,0,12,24" IsTabStop="True"
                   NavigationFailed="ContentFrame_NavigationFailed"/>
        </ScrollViewer>
    </muxc:NavigationView>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState>
                <VisualState.StateTriggers>
                    <AdaptiveTrigger
                        MinWindowWidth="{x:Bind NavViewCompactModeThresholdWidth}"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <!-- Remove the next 3 lines for left-only navigation. -->
                    <Setter Target="NavView.PaneDisplayMode" Value="Top"/>
                    <Setter Target="NavViewSearchBox.Width" Value="200"/>
                    <Setter Target="MainPagesHeader.Visibility" Value="Collapsed"/>
                    <!-- Leave the next line for left-only navigation. -->
                    <Setter Target="ContentFrame.Padding" Value="24,0,24,24"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
</Page>
```

> [!IMPORTANT]
> 此代码演示如何使用 [Windows UI 库](/uwp/toolkits/winui/)版本的 NavigationView。 如果改为使用平台版本的 NavigationView，则应用项目的最低版本必须是 SDK 17763 或更高版本。 若要使用平台版本，请删除对 `muxc` 的所有引用。

```csharp
// Add "using" for WinUI controls.
// using muxc = Microsoft.UI.Xaml.Controls;

private double NavViewCompactModeThresholdWidth { get { return NavView.CompactModeThresholdWidth; } }

private void ContentFrame_NavigationFailed(object sender, NavigationFailedEventArgs e)
{
    throw new Exception("Failed to load Page " + e.SourcePageType.FullName);
}

// List of ValueTuple holding the Navigation Tag and the relative Navigation Page
private readonly List<(string Tag, Type Page)> _pages = new List<(string Tag, Type Page)>
{
    ("home", typeof(HomePage)),
    ("apps", typeof(AppsPage)),
    ("games", typeof(GamesPage)),
    ("music", typeof(MusicPage)),
};

private void NavView_Loaded(object sender, RoutedEventArgs e)
{
    // You can also add items in code.
    NavView.MenuItems.Add(new muxc.NavigationViewItemSeparator());
    NavView.MenuItems.Add(new muxc.NavigationViewItem
    {
        Content = "My content",
        Icon = new SymbolIcon((Symbol)0xF1AD),
        Tag = "content"
    });
    _pages.Add(("content", typeof(MyContentPage)));

    // Add handler for ContentFrame navigation.
    ContentFrame.Navigated += On_Navigated;

    // NavView doesn't load any page by default, so load home page.
    NavView.SelectedItem = NavView.MenuItems[0];
    // If navigation occurs on SelectionChanged, this isn't needed.
    // Because we use ItemInvoked to navigate, we need to call Navigate
    // here to load the home page.
    NavView_Navigate("home", new Windows.UI.Xaml.Media.Animation.EntranceNavigationTransitionInfo());

    // Listen to the window directly so the app responds
    // to accelerator keys regardless of which element has focus.
    Window.Current.CoreWindow.Dispatcher.AcceleratorKeyActivated +=
        CoreDispatcher_AcceleratorKeyActivated;

    Window.Current.CoreWindow.PointerPressed += CoreWindow_PointerPressed;

    SystemNavigationManager.GetForCurrentView().BackRequested += System_BackRequested;
}

private void NavView_ItemInvoked(muxc.NavigationView sender,
                                 muxc.NavigationViewItemInvokedEventArgs args)
{
    if (args.IsSettingsInvoked == true)
    {
        NavView_Navigate("settings", args.RecommendedNavigationTransitionInfo);
    }
    else if (args.InvokedItemContainer != null)
    {
        var navItemTag = args.InvokedItemContainer.Tag.ToString();
        NavView_Navigate(navItemTag, args.RecommendedNavigationTransitionInfo);
    }
}

// NavView_SelectionChanged is not used in this example, but is shown for completeness.
// You will typically handle either ItemInvoked or SelectionChanged to perform navigation,
// but not both.
private void NavView_SelectionChanged(muxc.NavigationView sender,
                                      muxc.NavigationViewSelectionChangedEventArgs args)
{
    if (args.IsSettingsSelected == true)
    {
        NavView_Navigate("settings", args.RecommendedNavigationTransitionInfo);
    }
    else if (args.SelectedItemContainer != null)
    {
        var navItemTag = args.SelectedItemContainer.Tag.ToString();
        NavView_Navigate(navItemTag, args.RecommendedNavigationTransitionInfo);
    }
}

private void NavView_Navigate(
    string navItemTag,
    Windows.UI.Xaml.Media.Animation.NavigationTransitionInfo transitionInfo)
{
    Type _page = null;
    if (navItemTag == "settings")
    {
        _page = typeof(SettingsPage);
    }
    else
    {
        var item = _pages.FirstOrDefault(p => p.Tag.Equals(navItemTag));
        _page = item.Page;
    }
    // Get the page type before navigation so you can prevent duplicate
    // entries in the backstack.
    var preNavPageType = ContentFrame.CurrentSourcePageType;

    // Only navigate if the selected page isn't currently loaded.
    if (!(_page is null) && !Type.Equals(preNavPageType, _page))
    {
        ContentFrame.Navigate(_page, null, transitionInfo);
    }
}

private void NavView_BackRequested(muxc.NavigationView sender,
                                   muxc.NavigationViewBackRequestedEventArgs args)
{
    TryGoBack();
}

private void CoreDispatcher_AcceleratorKeyActivated(CoreDispatcher sender, AcceleratorKeyEventArgs e)
{
    // When Alt+Left are pressed navigate back
    if (e.EventType == CoreAcceleratorKeyEventType.SystemKeyDown
        && e.VirtualKey == VirtualKey.Left
        && e.KeyStatus.IsMenuKeyDown == true
        && !e.Handled)
    {
        e.Handled = TryGoBack();
    }
}

private void System_BackRequested(object sender, BackRequestedEventArgs e)
{
    if (!e.Handled)
    {
        e.Handled = TryGoBack();
    }
}

private void CoreWindow_PointerPressed(CoreWindow sender, PointerEventArgs e)
{
    // Handle mouse back button.
    if (e.CurrentPoint.Properties.IsXButton1Pressed)
    {
        e.Handled = TryGoBack();
    }
}

private bool TryGoBack()
{
    if (!ContentFrame.CanGoBack)
        return false;

    // Don't go back if the nav pane is overlayed.
    if (NavView.IsPaneOpen &&
        (NavView.DisplayMode == muxc.NavigationViewDisplayMode.Compact ||
         NavView.DisplayMode == muxc.NavigationViewDisplayMode.Minimal))
        return false;

    ContentFrame.GoBack();
    return true;
}

private void On_Navigated(object sender, NavigationEventArgs e)
{
    NavView.IsBackEnabled = ContentFrame.CanGoBack;

    if (ContentFrame.SourcePageType == typeof(SettingsPage))
    {
        // SettingsItem is not part of NavView.MenuItems, and doesn't have a Tag.
        NavView.SelectedItem = (muxc.NavigationViewItem)NavView.SettingsItem;
        NavView.Header = "Settings";
    }
    else if (ContentFrame.SourcePageType != null)
    {
        var item = _pages.FirstOrDefault(p => p.Page == e.SourcePageType);

        NavView.SelectedItem = NavView.MenuItems
            .OfType<muxc.NavigationViewItem>()
            .First(n => n.Tag.Equals(item.Tag));

        NavView.Header =
            ((muxc.NavigationViewItem)NavView.SelectedItem)?.Content?.ToString();
    }
}
```

> [!NOTE]
> 对于此代码示例的 [C++ /WinRT](../../cpp-and-winrt-apis/index.md) 版本，请首先基于“空白应用(C++/WinRT)”项目模板创建一个新项目，然后将该列表中的代码添加到指示的源代码文件中。 若要完全按照列表中所示使用源代码，请将新项目命名为 NavigationViewCppWinRT

```cppwinrt
// MainPage.idl
runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
{
    ...
    Double NavViewCompactModeThresholdWidth{ get; };
}

// pch.h
...
#include "winrt/Windows.UI.Xaml.Input.h"
#include "winrt/Windows.UI.Xaml.Media.Animation.h"
#include "winrt/Microsoft.UI.Xaml.Controls.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"
#include <winrt/Windows.UI.Core.h>
#include "winrt/Windows.UI.Input.h"


// MainPage.h
#pragma once

#include "MainPage.g.h"

namespace muxc
{
    using namespace winrt::Microsoft::UI::Xaml::Controls;
};

namespace wuxc
{
    using namespace winrt::Windows::UI::Xaml::Controls;
};

namespace winrt::NavigationViewCppWinRT::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage();

        double NavViewCompactModeThresholdWidth();
        void ContentFrame_NavigationFailed(
            Windows::Foundation::IInspectable const& /* sender */,
            Windows::UI::Xaml::Navigation::NavigationFailedEventArgs const& args);
        void NavView_Loaded(
            Windows::Foundation::IInspectable const& /* sender */,
            Windows::UI::Xaml::RoutedEventArgs const& /* args */);
        void NavView_ItemInvoked(
            Windows::Foundation::IInspectable const& /* sender */,
            muxc::NavigationViewItemInvokedEventArgs const& args);

        // NavView_SelectionChanged is not used in this example, but is shown for completeness.
        // You'll typically handle either ItemInvoked or SelectionChanged to perform navigation,
        // but not both.
        void NavView_SelectionChanged(
            muxc::NavigationView const& /* sender */,
            muxc::NavigationViewSelectionChangedEventArgs const& args);
        void NavView_Navigate(
            std::wstring navItemTag,
            Windows::UI::Xaml::Media::Animation::NavigationTransitionInfo const& transitionInfo);
        void NavView_BackRequested(
            muxc::NavigationView const& /* sender */,
            muxc::NavigationViewBackRequestedEventArgs const& /* args */);
        void On_Navigated(
            Windows::Foundation::IInspectable const& /* sender */,
            Windows::UI::Xaml::Navigation::NavigationEventArgs const& args);
        void CoreDispatcher_AcceleratorKeyActivated(
            Windows::UI::Core::CoreDispatcher const& /* sender */,
            Windows::UI::Core::AcceleratorKeyEventArgs const& args);
        void CoreWindow_PointerPressed(
            Windows::UI::Core::CoreWindow const& /* sender */,
            Windows::UI::Core::PointerEventArgs const& args);
        void System_BackRequested(
            Windows::Foundation::IInspectable const& /* sender */,
            Windows::UI::Core::BackRequestedEventArgs const& args);
        bool TryGoBack();

    private:
        // Vector of std::pair holding the Navigation Tag and the relative Navigation Page.
        std::vector<std::pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>> m_pages;
    };
}

namespace winrt::NavigationViewCppWinRT::factory_implementation
{
    struct MainPage : MainPageT<MainPage, implementation::MainPage>
    {
    };
}

// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "MainPage.g.cpp"

namespace winrt::NavigationViewCppWinRT::implementation
{
    MainPage::MainPage()
    {
        InitializeComponent();
        m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>
            (L"home", winrt::xaml_typename<NavigationViewCppWinRT::HomePage>()));
        m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>
            (L"apps", winrt::xaml_typename<NavigationViewCppWinRT::AppsPage>()));
        m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>
            (L"games", winrt::xaml_typename<NavigationViewCppWinRT::GamesPage>()));
        m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>
            (L"music", winrt::xaml_typename<NavigationViewCppWinRT::MusicPage>()));
    }

    double MainPage::NavViewCompactModeThresholdWidth()
    {
        return NavView().CompactModeThresholdWidth();
    }

    void MainPage::ContentFrame_NavigationFailed(
        Windows::Foundation::IInspectable const& /* sender */,
        Windows::UI::Xaml::Navigation::NavigationFailedEventArgs const& args)
    {
        throw winrt::hresult_error(
            E_FAIL, winrt::hstring(L"Failed to load Page ") + args.SourcePageType().Name);
    }

    // List of ValueTuple holding the Navigation Tag and the relative Navigation Page
    std::vector<std::pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>> m_pages;

    void MainPage::NavView_Loaded(
        Windows::Foundation::IInspectable const& /* sender */,
        Windows::UI::Xaml::RoutedEventArgs const& /* args */)
    {
        // You can also add items in code.
        NavView().MenuItems().Append(muxc::NavigationViewItemSeparator());
        muxc::NavigationViewItem navigationViewItem;
        navigationViewItem.Content(winrt::box_value(L"My content"));
        navigationViewItem.Icon(wuxc::SymbolIcon(static_cast<wuxc::Symbol>(0xF1AD)));
        navigationViewItem.Tag(winrt::box_value(L"content"));
        NavView().MenuItems().Append(navigationViewItem);
        m_pages.push_back(
            std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>(
                L"content", winrt::xaml_typename<NavigationViewCppWinRT::MyContentPage>()));

        // Add handler for ContentFrame navigation.
        ContentFrame().Navigated({ this, &MainPage::On_Navigated });

        // NavView doesn't load any page by default, so load home page.
        NavView().SelectedItem(NavView().MenuItems().GetAt(0));
        // If navigation occurs on SelectionChanged, then this isn't needed.
        // Because we use ItemInvoked to navigate, we need to call Navigate
        // here to load the home page.
        NavView_Navigate(L"home",
            Windows::UI::Xaml::Media::Animation::EntranceNavigationTransitionInfo());

        // Listen to the window directly so the app responds
        // to accelerator keys regardless of which element has focus.
        winrt::Windows::UI::Xaml::Window::Current().CoreWindow().Dispatcher().
            AcceleratorKeyActivated({ this, &MainPage::CoreDispatcher_AcceleratorKeyActivated });
 
        winrt::Windows::UI::Xaml::Window::Current().CoreWindow().
            PointerPressed({ this, &MainPage::CoreWindow_PointerPressed });
 
        Windows::UI::Core::SystemNavigationManager::GetForCurrentView().
            BackRequested({ this, &MainPage::System_BackRequested });

    }

    void MainPage::NavView_ItemInvoked(
        Windows::Foundation::IInspectable const& /* sender */,
        muxc::NavigationViewItemInvokedEventArgs const& args)
    {
        if (args.IsSettingsInvoked())
        {
            NavView_Navigate(L"settings", args.RecommendedNavigationTransitionInfo());
        }
        else if (args.InvokedItemContainer())
        {
            NavView_Navigate(
                winrt::unbox_value_or<winrt::hstring>(
                    args.InvokedItemContainer().Tag(), L"").c_str(),
                args.RecommendedNavigationTransitionInfo());
        }
    }

    // NavView_SelectionChanged is not used in this example, but is shown for completeness.
    // You will typically handle either ItemInvoked or SelectionChanged to perform navigation,
    // but not both.
    void MainPage::NavView_SelectionChanged(
        muxc::NavigationView const& /* sender */,
        muxc::NavigationViewSelectionChangedEventArgs const& args)
    {
        if (args.IsSettingsSelected())
        {
            NavView_Navigate(L"settings", args.RecommendedNavigationTransitionInfo());
        }
        else if (args.SelectedItemContainer())
        {
            NavView_Navigate(
                winrt::unbox_value_or<winrt::hstring>(
                    args.SelectedItemContainer().Tag(), L"").c_str(),
                args.RecommendedNavigationTransitionInfo());
        }
    }

    void MainPage::NavView_Navigate(
        std::wstring navItemTag,
        Windows::UI::Xaml::Media::Animation::NavigationTransitionInfo const& transitionInfo)
    {
        Windows::UI::Xaml::Interop::TypeName pageTypeName;
        if (navItemTag == L"settings")
        {
            pageTypeName = winrt::xaml_typename<NavigationViewCppWinRT::SettingsPage>();
        }
        else
        {
            for (auto&& eachPage : m_pages)
            {
                if (eachPage.first == navItemTag)
                {
                    pageTypeName = eachPage.second;
                    break;
                }
            }
        }
        // Get the page type before navigation so you can prevent duplicate
        // entries in the backstack.
        Windows::UI::Xaml::Interop::TypeName preNavPageType =
            ContentFrame().CurrentSourcePageType();

        // Navigate only if the selected page isn't currently loaded.
        if (pageTypeName.Name != L"" && preNavPageType.Name != pageTypeName.Name)
        {
            ContentFrame().Navigate(pageTypeName, nullptr, transitionInfo);
        }
    }

    void MainPage::NavView_BackRequested(
        muxc::NavigationView const& /* sender */,
        muxc::NavigationViewBackRequestedEventArgs const& /* args */)
    {
        TryGoBack();
    }

    void MainPage::CoreDispatcher_AcceleratorKeyActivated(
        Windows::UI::Core::CoreDispatcher const& /* sender */,
        Windows::UI::Core::AcceleratorKeyEventArgs const& args)
    {
        // When Alt+Left are pressed navigate back
        if (args.EventType() == Windows::UI::Core::CoreAcceleratorKeyEventType::SystemKeyDown
            && args.VirtualKey() == Windows::System::VirtualKey::Left
            && args.KeyStatus().IsMenuKeyDown
            && !args.Handled())
        {
            args.Handled(TryGoBack());
        }
    }
 
    void MainPage::CoreWindow_PointerPressed(
        Windows::UI::Core::CoreWindow const& /* sender */,
        Windows::UI::Core::PointerEventArgs const& args)
    {
        // Handle mouse back button.
        if (args.CurrentPoint().Properties().IsXButton1Pressed())
        {
            args.Handled(TryGoBack());
        }
    }
 
    void MainPage::System_BackRequested(
        Windows::Foundation::IInspectable const& /* sender */,
        Windows::UI::Core::BackRequestedEventArgs const& args)
    {
        if (!args.Handled())
        {
            args.Handled(TryGoBack());
        }
    }
 
    bool MainPage::TryGoBack()
    {
        if (!ContentFrame().CanGoBack())
            return false;
        // Don't go back if the nav pane is overlayed.
        if (NavView().IsPaneOpen() &&
            (NavView().DisplayMode() == muxc::NavigationViewDisplayMode::Compact ||
                NavView().DisplayMode() == muxc::NavigationViewDisplayMode::Minimal))
            return false;
        ContentFrame().GoBack();
        return true;
    }  

    void MainPage::On_Navigated(
        Windows::Foundation::IInspectable const& /* sender */,
        Windows::UI::Xaml::Navigation::NavigationEventArgs const& args)
    {
        NavView().IsBackEnabled(ContentFrame().CanGoBack());

        if (ContentFrame().SourcePageType().Name ==
            winrt::xaml_typename<NavigationViewCppWinRT::SettingsPage>().Name)
        {
            // SettingsItem is not part of NavView.MenuItems, and doesn't have a Tag.
            NavView().SelectedItem(NavView().SettingsItem().as<muxc::NavigationViewItem>());
            NavView().Header(winrt::box_value(L"Settings"));
        }
        else if (ContentFrame().SourcePageType().Name != L"")
        {
            for (auto&& eachPage : m_pages)
            {
                if (eachPage.second.Name == args.SourcePageType().Name)
                {
                    for (auto&& eachMenuItem : NavView().MenuItems())
                    {
                        auto navigationViewItem =
                            eachMenuItem.try_as<muxc::NavigationViewItem>();
                        {
                            if (navigationViewItem)
                            {
                                winrt::hstring hstringValue =
                                    winrt::unbox_value_or<winrt::hstring>(
                                        navigationViewItem.Tag(), L"");
                                if (hstringValue == eachPage.first)
                                {
                                    NavView().SelectedItem(navigationViewItem);
                                    NavView().Header(navigationViewItem.Content());
                                }
                            }
                        }
                    }
                    break;
                }
            }
        }
    }
}
```

### <a name="alternative-cwinrt-implementation"></a>替代 C++ /WinRT 实现

上面所示的 C# 和 C++ /WinRT 代码经过专门设计，让你能够对这两个版本使用相同的 XAML 标记。 但是，你可能更希望通过另一种方法来实现此部分中所述的 C++/WinRT 版本。

下面是 **NavView_ItemInvoked** 处理程序的替代版本。 此版本的处理程序中的技术涉及首先（在 [NavigationViewItem](/uwp/api/windows.ui.xaml.controls.navigationviewitem) 的标记中）存储要导航到的页面的完整类型名称。 在处理程序中，对值取消装箱，将它转换 [Windows::UI::Xaml::Interop::TypeName](/uwp/api/windows.ui.xaml.interop.typename) 对象，并使用该对象导航到目标页。 不需要在上面的示例中看到的名为 `_pages` 的映射变量；你将能够创建单元测试，从而确认标记中的值是否属于有效类型。 另请参阅[通过 C++/WinRT 将值装箱到 IInspectable 和对其取消装箱](../../cpp-and-winrt-apis/boxing.md)。

```cppwinrt
void MainPage::NavView_ItemInvoked(
    Windows::Foundation::IInspectable const & /* sender */,
    Windows::UI::Xaml::Controls::NavigationViewItemInvokedEventArgs const & args)
{
    if (args.IsSettingsInvoked())
    {
        // Navigate to Settings.
    }
    else if (args.InvokedItemContainer())
    {
        Windows::UI::Xaml::Interop::TypeName pageTypeName;
        pageTypeName.Name = unbox_value<hstring>(args.InvokedItemContainer().Tag());
        pageTypeName.Kind = Windows::UI::Xaml::Interop::TypeKind::Primitive;
        ContentFrame().Navigate(pageTypeName, nullptr);
    }
}
```

## <a name="hierarchical-navigation"></a>分层导航
某些应用可能具有更复杂的层次结构，该结构不仅仅需要导航项的简单列表。 你可能想要使用顶级导航项来显示页面类别，其中子项显示特定页。 如果你具有仅链接到其他页面的中心样式页面，则此方法也非常有用。 在这些情况下，应创建分层 NavigationView。

要在窗格中显示嵌套导航项的层次结构列表，请使用 [MenuItems](/uwp/api/microsoft.ui.xaml.controls.navigationviewitem.menuitems) 属性或 NavigationViewItem 的 [MenuItemsSource](/uwp/api/microsoft.ui.xaml.controls.navigationviewitem.menuitemssource) 属性。
每个 NavigationViewItem 可以包含其他 NavigationViewItem 和组织元素，如项标题和分隔符。 若要在使用 `MenuItemsSource` 时显示层次结构列表，请将 `ItemTemplate` 设置为 NavigationViewItem，并将其 `MenuItemsSource` 属性绑定到层次结构的下一级。

尽管 NavigationViewItem 可以包含任意数量的嵌套级别，但建议为应用使用较浅的导航层次结构。 我们认为两个级别是兼顾使用和理解的理想选择。

NavigationView 在顶部、左侧和 LeftCompact 窗格显示模式下显示层次结构。 下面是展开的子树在每个窗格显示模式下的外观：

![具有层次结构的 NavigationView](images/navigation-view-hierarchy-labeled.png)

### <a name="adding-a-hierarchy-of-items-in-markup"></a>在标记中添加项的层次结构
在标记中声明应用导航层次结构。

```Xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<muxc:NavigationView>
    <muxc:NavigationView.MenuItems>
        <muxc:NavigationViewItem Content="Home" Icon="Home" ToolTipService.ToolTip="Home"/>
        <muxc:NavigationViewItem Content="Collections" Icon="Keyboard" ToolTipService.ToolTip="Collections">
            <muxc:NavigationViewItem.MenuItems>
                <muxc:NavigationViewItem Content="Notes" Icon="Page" ToolTipService.ToolTip="Notes"/>
                <muxc:NavigationViewItem Content="Mail" Icon="Mail" ToolTipService.ToolTip="Mail"/>
            </muxc:NavigationViewItem.MenuItems>
        </muxc:NavigationViewItem>
    </muxc:NavigationView.MenuItems>
</muxc:NavigationView>
```

### <a name="adding-a-hierarchy-of-items-using-data-binding"></a>使用数据绑定添加项的层次结构

向 NavigationView 添加菜单项的层次结构 
* 将 MenuItemsSource 属性绑定到分层数据
* 将项模板定义为 NavigationViewMenuItem，将其内容设置为菜单项的标签，并将其 MenuItemsSource 属性绑定到层次结构的下一级

此示例还演示了[展开](/uwp/api/microsoft.ui.xaml.controls.navigationview.expanding)和[折叠](/uwp/api/microsoft.ui.xaml.controls.navigationview.collapsed)事件。 带有子项的菜单项会引发这些事件。

```xaml
<Page ... xmlns:muxc="using:Microsoft.UI.Xaml.Controls" ... >
    <Page.Resources>
        <DataTemplate x:Key="NavigationViewMenuItem" x:DataType="local:Category">
            <muxc:NavigationViewItem Content="{x:Bind Name}" MenuItemsSource="{x:Bind Children}"/>
        </DataTemplate>
    </Page.Resources>

    <Grid>
        <muxc:NavigationView x:Name="navview" 
    MenuItemsSource="{x:Bind Categories, Mode=OneWay}" 
    MenuItemTemplate="{StaticResource NavigationViewMenuItem}" 
    ItemInvoked="{x:Bind OnItemInvoked}" 
    Expanding="OnItemExpanding" 
    Collapsed="OnItemCollapsed" 
    PaneDisplayMode="Left">
            <StackPanel Margin="10,10,0,0">
                <TextBlock Margin="0,10,0,0" x:Name="ExpandingItemLabel" Text="Last Expanding: N/A"/>
                <TextBlock x:Name="CollapsedItemLabel" Text="Last Collapsed: N/A"/>
            </StackPanel>
        </muxc:NavigationView>
    </Grid>
</Page>
```

```csharp
public class Category
{
    public String Name { get; set; }
    public String CategoryIcon { get; set; }
    public ObservableCollection<Category> Children { get; set; }
}

public sealed partial class HierarchicalNavigationViewDataBinding : Page
{
    public HierarchicalNavigationViewDataBinding()
    {
        this.InitializeComponent();
    }  

    public ObservableCollection<Category> Categories = new ObservableCollection<Category>()
    {
        new Category(){
            Name = "Menu item 1",
            CategoryIcon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu item 2",
                    CategoryIcon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() {
                            Name  = "Menu item 3",
                            CategoryIcon = "Icon",
                            Children = new ObservableCollection<Category>() {
                                new Category() { Name  = "Menu item 4", CategoryIcon = "Icon" },
                                new Category() { Name  = "Menu item 5", CategoryIcon = "Icon" }
                            }
                        }
                    }
                }
            }
        },
        new Category(){
            Name = "Menu item 6",
            CategoryIcon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu item 7",
                    CategoryIcon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() { Name  = "Menu item 8", CategoryIcon = "Icon" },
                        new Category() { Name  = "Menu item 9", CategoryIcon = "Icon" }
                    }
                }
            }
        },
        new Category(){ Name = "Menu item 10", CategoryIcon = "Icon" }
    };

    private void OnItemInvoked(object sender, NavigationViewItemInvokedEventArgs e)
    {
        var clickedItem = e.InvokedItem;
        var clickedItemContainer = e.InvokedItemContainer;
    }
    private void OnItemExpanding(object sender, NavigationViewItemExpandingEventArgs e)
    {
        var nvib = e.ExpandingItemContainer;
        var name = "Last expanding: " + nvib.Content.ToString();
        ExpandingItemLabel.Text = name;
    }
    private void OnItemCollapsed(object sender, NavigationViewItemCollapsedEventArgs e)
    {
        var nvib = e.CollapsedItemContainer;
        var name = "Last collapsed: " + nvib.Content;
        CollapsedItemLabel.Text = name;
    }
}
```

```cppwinrt
// Category.idl
namespace HierarchicalNavigationViewDataBinding
{
    runtimeclass Category
    {
        String Name;
        String CategoryIcon;
        Windows.Foundation.Collections.IObservableVector<Category> Children;
    }
}

// Category.h
#pragma once
#include "Category.g.h"

namespace winrt::HierarchicalNavigationViewDataBinding::implementation
{
    struct Category : CategoryT<Category>
    {
        Category();
        Category(winrt::hstring name,
            winrt::hstring categoryIcon,
            Windows::Foundation::Collections::
                IObservableVector<HierarchicalNavigationViewDataBinding::Category> children);

        winrt::hstring Name();
        void Name(winrt::hstring const& value);
        winrt::hstring CategoryIcon();
        void CategoryIcon(winrt::hstring const& value);
        Windows::Foundation::Collections::
            IObservableVector<HierarchicalNavigationViewDataBinding::Category> Children();
        void Children(Windows::Foundation::Collections:
            IObservableVector<HierarchicalNavigationViewDataBinding::Category> const& value);

    private:
        winrt::hstring m_name;
        winrt::hstring m_categoryIcon;
        Windows::Foundation::Collections::
            IObservableVector<HierarchicalNavigationViewDataBinding::Category> m_children;
    };
}

// Category.cpp
#include "pch.h"
#include "Category.h"
#include "Category.g.cpp"

namespace winrt::HierarchicalNavigationViewDataBinding::implementation
{
    Category::Category()
    {
        m_children = winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
    }

    Category::Category(
        winrt::hstring name,
        winrt::hstring categoryIcon,
        Windows::Foundation::Collections::
            IObservableVector<HierarchicalNavigationViewDataBinding::Category> children)
    {
        m_name = name;
        m_categoryIcon = categoryIcon;
        m_children = children;
    }

    hstring Category::Name()
    {
        return m_name;
    }

    void Category::Name(hstring const& value)
    {
        m_name = value;
    }

    hstring Category::CategoryIcon()
    {
        return m_categoryIcon;
    }

    void Category::CategoryIcon(hstring const& value)
    {
        m_categoryIcon = value;
    }

    Windows::Foundation::Collections::IObservableVector<HierarchicalNavigationViewDataBinding::Category>
        Category::Children()
    {
        return m_children;
    }

    void Category::Children(
        Windows::Foundation::Collections::IObservableVector<HierarchicalNavigationViewDataBinding::Category>
            const& value)
    {
        m_children = value;
    }
}

// MainPage.idl
import "Category.idl";

namespace HierarchicalNavigationViewDataBinding
{
    [default_interface]
    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        Windows.Foundation.Collections.IObservableVector<Category> Categories{ get; };
    }
}

// MainPage.h
#pragma once

#include "MainPage.g.h"

namespace muxc
{
    using namespace winrt::Microsoft::UI::Xaml::Controls;
};

namespace winrt::HierarchicalNavigationViewDataBinding::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage();

        Windows::Foundation::Collections::IObservableVector<HierarchicalNavigationViewDataBinding::Category>
            Categories();

        void OnItemInvoked(muxc::NavigationView const& sender, muxc::NavigationViewItemInvokedEventArgs const& args);
        void OnItemExpanding(
            muxc::NavigationView const& sender,
            muxc::NavigationViewItemExpandingEventArgs const& args);
        void OnItemCollapsed(
            muxc::NavigationView const& sender,
            muxc::NavigationViewItemCollapsedEventArgs const& args);

    private:
        Windows::Foundation::Collections::
            IObservableVector<HierarchicalNavigationViewDataBinding::Category> m_categories;
    };
}

namespace winrt::HierarchicalNavigationViewDataBinding::factory_implementation
{
    struct MainPage : MainPageT<MainPage, implementation::MainPage>
    {
    };
}

// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "MainPage.g.cpp"

#include "Category.h"

namespace winrt::HierarchicalNavigationViewDataBinding::implementation
{
    MainPage::MainPage()
    {
        InitializeComponent();

        m_categories = 
            winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();

        auto menuItem10 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 10", L"Icon", nullptr);

        auto menuItem9 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 9", L"Icon", nullptr);
        auto menuItem8 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 8", L"Icon", nullptr);
        auto menuItem7Children = 
            winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
        menuItem7Children.Append(*menuItem9);
        menuItem7Children.Append(*menuItem8);

        auto menuItem7 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 7", L"Icon", menuItem7Children);
        auto menuItem6Children = 
            winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
        menuItem6Children.Append(*menuItem7);

        auto menuItem6 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 6", L"Icon", menuItem6Children);

        auto menuItem5 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 5", L"Icon", nullptr);
        auto menuItem4 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 4", L"Icon", nullptr);
        auto menuItem3Children =
            winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
        menuItem3Children.Append(*menuItem5);
        menuItem3Children.Append(*menuItem4);

        auto menuItem3 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 3", L"Icon", menuItem3Children);
        auto menuItem2Children = 
            winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
        menuItem2Children.Append(*menuItem3);

        auto menuItem2 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 2", L"Icon", menuItem2Children);
        auto menuItem1Children = 
            winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
        menuItem1Children.Append(*menuItem2);

        auto menuItem1 = winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
            (L"Menu item 1", L"Icon", menuItem1Children);

        m_categories.Append(*menuItem1);
        m_categories.Append(*menuItem6);
        m_categories.Append(*menuItem10);
    }

    Windows::Foundation::Collections::IObservableVector<HierarchicalNavigationViewDataBinding::Category>
        MainPage::Categories()
    {
        return m_categories;
    }

    void MainPage::OnItemInvoked(
        muxc::NavigationView const& /* sender */,
        muxc::NavigationViewItemInvokedEventArgs const& args)
    {
        auto clickedItem = args.InvokedItem();
        auto clickedItemContainer = args.InvokedItemContainer();
    }

    void MainPage::OnItemExpanding(
        muxc::NavigationView const& /* sender */,
        muxc::NavigationViewItemExpandingEventArgs const& args)
    {
        auto nvib = args.ExpandingItemContainer();
        auto name = L"Last expanding: " + winrt::unbox_value<winrt::hstring>(nvib.Content());
        ExpandingItemLabel().Text(name);
    }

    void MainPage::OnItemCollapsed(
        muxc::NavigationView const& /* sender */,
        muxc::NavigationViewItemCollapsedEventArgs const& args)
    {
        auto nvib = args.CollapsedItemContainer();
        auto name = L"Last collapsed: " + winrt::unbox_value<winrt::hstring>(nvib.Content());
        CollapsedItemLabel().Text(name);
    }
}
```

### <a name="selection"></a>选择

默认情况下，任何项都可以包含子项（已调用或已选择）。

当向用户提供导航选项的分层树时，可以使父项不可选择，例如，当你的应用没有与该父项关联的目标页时。 如果父项可以选择，则建议使用左展开或顶部窗格显示模式。 LeftCompact 模式将使用户导航到父项，以便在每次调用子树时将其打开。

当处于左侧模式时，选定项将沿其左边缘绘制选择指示器，当处于顶部模式时，会沿其下边缘进行绘制。 下面显示的是在左侧模式和顶部模式下选中了父项的 NavigationView。

![在左侧模式下选中了父项的 NavigationView](images/navigation-view-selection.png)

![在顶部模式下选中了父项的 NavigationView](images/navigation-view-selection-top.png)

选定项不一定总是可见。 如果选中了折叠/不可展开的子树中的子项，则其第一个可见上级将显示为选中状态。 如果子树展开，则选择指示器将移回至选定项。

例如，在上图中，用户可以选择日历项，然后折叠其子树。 在这种情况下，选择指示器将显示在帐户项下，因为帐户是日历的第一个可见上级。 当用户再次展开子树时，选择指示器将移回到日历项。 

整个 NavigationView 不会显示 1 个以上选择指示器。

在顶部和左侧模式下，单击 NavigationViewItem 上的箭头将展开或折叠子树。 单击或点击 NavigationViewItem 上的其他位置将触发 `ItemInvoked` 事件，同时还会折叠或展开子树。

若要防止项在被调用时显示选择指示器，请将其 [SelectsOnInvoked](/uwp/api/microsoft.ui.xaml.controls.navigationviewitem.selectsoninvoked) 属性设置为 False，如下所示：

```xaml
<Page ... xmlns:muxc="using:Microsoft.UI.Xaml.Controls" ... >
    <Page.Resources>
        <DataTemplate x:Key="NavigationViewMenuItem" x:DataType="local:Category">
            <muxc:NavigationViewItem Content="{x:Bind Name}"
            MenuItemsSource="{x:Bind Children}"
            SelectsOnInvoked="{x:Bind IsLeaf}"/>
        </DataTemplate>
    </Page.Resources>

    <Grid>
        <muxc:NavigationView x:Name="navview" 
    MenuItemsSource="{x:Bind Categories, Mode=OneWay}" 
    MenuItemTemplate="{StaticResource NavigationViewMenuItem}">
        </muxc:NavigationView>
    </Grid>
</Page>
```

```csharp
public class Category
{
    public String Name { get; set; }
    public String CategoryIcon { get; set; }
    public ObservableCollection<Category> Children { get; set; }
    public bool IsLeaf { get; set; }
}

public sealed partial class HierarchicalNavigationViewDataBinding : Page
{
    public HierarchicalNavigationViewDataBinding()
    {
        this.InitializeComponent();
    }  

    public ObservableCollection<Category> Categories = new ObservableCollection<Category>()
    {
        new Category(){
            Name = "Menu item 1",
            CategoryIcon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu item 2",
                    CategoryIcon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() {
                            Name  = "Menu item 3",
                            CategoryIcon = "Icon",
                            Children = new ObservableCollection<Category>() {
                                new Category() { Name  = "Menu item 4", CategoryIcon = "Icon", IsLeaf = true },
                                new Category() { Name  = "Menu item 5", CategoryIcon = "Icon", IsLeaf = true }
                            }
                        }
                    }
                }
            }
        },
        new Category(){
            Name = "Menu item 6",
            CategoryIcon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu item 7",
                    CategoryIcon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() { Name  = "Menu item 8", CategoryIcon = "Icon", IsLeaf = true },
                        new Category() { Name  = "Menu item 9", CategoryIcon = "Icon", IsLeaf = true }
                    }
                }
            }
        },
        new Category(){ Name = "Menu item 10", CategoryIcon = "Icon", IsLeaf = true }
    };
}
```

```cppwinrt
// Category.idl
namespace HierarchicalNavigationViewDataBinding
{
    runtimeclass Category
    {
        ...
        Boolean IsLeaf;
    }
}

// Category.h
...
struct Category : CategoryT<Category>
{
    ...
    Category(winrt::hstring name,
        winrt::hstring categoryIcon,
        Windows::Foundation::Collections::IObservableVector<HierarchicalNavigationViewDataBinding::Category> children,
        bool isleaf = false);
    ...
    bool IsLeaf();
    void IsLeaf(bool value);

private:
    ...
    bool m_isleaf;
};

// Category.cpp
...
Category::Category(winrt::hstring name,
    winrt::hstring categoryIcon,
    Windows::Foundation::Collections::IObservableVector<HierarchicalNavigationViewDataBinding::Category> children,
    bool isleaf) : m_name(name), m_categoryIcon(categoryIcon), m_children(children), m_isleaf(isleaf) {}
...
bool Category::IsLeaf()
{
    return m_isleaf;
}

void Category::IsLeaf(bool value)
{
    m_isleaf = value;
}

// MainPage.h and MainPage.cpp
// Delete OnItemInvoked, OnItemExpanding, and OnItemCollapsed.

// MainPage.cpp
...
MainPage::MainPage()
{
    InitializeComponent();

    m_categories = winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();

    auto menuItem10 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 10", L"Icon", nullptr, true);

    auto menuItem9 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 9", L"Icon", nullptr, true);
    auto menuItem8 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 8", L"Icon", nullptr, true);
    auto menuItem7Children = 
        winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
    menuItem7Children.Append(*menuItem9);
    menuItem7Children.Append(*menuItem8);

    auto menuItem7 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 7", L"Icon", menuItem7Children);
    auto menuItem6Children = 
        winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
    menuItem6Children.Append(*menuItem7);

    auto menuItem6 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 6", L"Icon", menuItem6Children);

    auto menuItem5 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 5", L"Icon", nullptr, true);
    auto menuItem4 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 4", L"Icon", nullptr, true);
    auto menuItem3Children = 
        winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
    menuItem3Children.Append(*menuItem5);
    menuItem3Children.Append(*menuItem4);

    auto menuItem3 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 3", L"Icon", menuItem3Children);
    auto menuItem2Children = 
        winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
    menuItem2Children.Append(*menuItem3);

    auto menuItem2 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 2", L"Icon", menuItem2Children);
    auto menuItem1Children = 
        winrt::single_threaded_observable_vector<HierarchicalNavigationViewDataBinding::Category>();
    menuItem1Children.Append(*menuItem2);

    auto menuItem1 = 
        winrt::make_self<HierarchicalNavigationViewDataBinding::implementation::Category>
        (L"Menu item 1", L"Icon", menuItem1Children);

    m_categories.Append(*menuItem1);
    m_categories.Append(*menuItem6);
    m_categories.Append(*menuItem10);
}
...
```

### <a name="keyboarding-within-hierarchical-navigationview"></a>分层 NavigationView 内的键盘操作

用户可以使用[键盘](../input/keyboard-interactions.md)在 NavigationView 周围移动焦点。 箭头键在窗格内公开“内部导航”，并按照[树视图](./tree-view.md)中提供的交互进行操作。 当在 NavigationView 或其浮出控件菜单（在 HierarchicalNavigationView 的顶部和左侧模式下显示）中导航时，键操作将发生变化。 下面是每个键可在分层 NavigationView 中执行的特定操作：

| 键      |      在左侧模式下      |  在顶部模式下 | 在浮出控件中  |
|----------|------------------------|--------------|------------|
| 向上 |将焦点移动到当前焦点项的正上方。 | 不执行任何操作。 |将焦点移动到当前焦点项的正上方。|
| 向下|将焦点移动到当前焦点项的正下方。* | 不执行任何操作。 | 将焦点移动到当前焦点项的正下方。* |
| 权限 |不执行任何操作。  |将焦点移动到当前焦点项的右侧。 |不执行任何操作。|
| 左 |不执行任何操作。 | 将焦点移动到当前焦点项的左侧。  |不执行任何操作。 |
| 空格键/Enter |如果某项具有子项，则展开/折叠项且不更改焦点。   | 如果某项具有子项，则将子项展开为浮出控件，并将焦点置于浮出控件中的第一项。 | 调用/选择项并关闭浮出控件。 |
| Esc | 不执行任何操作。 | 不执行任何操作。 | 关闭浮出控件。|

空格键或 Enter 键始终会调用/选择一个项。

*请注意，项不需要在视觉上相邻，焦点将从窗格列表的最后一项移动到设置项。 

## <a name="navigationview-customization"></a>NavigationView 自定义

### <a name="pane-backgrounds"></a>窗格背景

默认情况下，NavigationView 窗格根据显示模式使用不同的背景：

- 在左侧展开，与内容并排时（左侧模式），窗格是纯灰色。
- 在内容顶部以覆盖形式打开时（顶部、最小或精简模式），窗格使用应用内 Acrylic。

若要修改窗格背景，可以在每种模式中替代用于呈现背景的 XAML 主题资源。 （使用此方法而不是单个 PaneBackground 属性是为了针对不同显示模式支持不同背景。）

此表显示每种显示模式下使用的主题资源。

| 显示模式 | 主题资源 |
| ------------ | -------------- |
| 左 | NavigationViewExpandedPaneBackground |
| LeftCompact<br/>LeftMinimal | NavigationViewDefaultPaneBackground |
| 顶部 | NavigationViewTopPaneBackground |

此示例演示如何在 App.xaml 中替代主题资源。 替代主题资源时，应始终至少提供“Default”和“HighContrast”资源字典，并根据需要提供用于“Light”或“Dark”资源的字典。 有关更多信息，请参阅 [ResourceDictionary.ThemeDictionaries](/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries)。

> [!IMPORTANT]
> 此代码演示如何使用 [Windows UI 库](/uwp/toolkits/winui/)版本的 AcrylicBrush。 如果改为使用平台版本的 AcrylicBrush，则应用项目的最低版本必须是 SDK 16299 或更高版本。 若要使用平台版本，请删除对 `muxm:` 的所有引用。

```xaml
<Application ... xmlns:muxm="using:Microsoft.UI.Xaml.Media" ...>
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"/>
                <ResourceDictionary>
                    <ResourceDictionary.ThemeDictionaries>
                        <ResourceDictionary x:Key="Default">
                            <!-- The "Default" theme dictionary is used unless a specific
                                 light, dark, or high contrast dictionary is provided. These
                                 resources should be tested with both the light and dark themes,
                                 and specific light or dark resources provided as needed. -->
                            <muxm:AcrylicBrush x:Key="NavigationViewDefaultPaneBackground"
                                   BackgroundSource="Backdrop"
                                   TintColor="LightSlateGray"
                                   TintOpacity=".6"/>
                            <muxm:AcrylicBrush x:Key="NavigationViewTopPaneBackground"
                                   BackgroundSource="Backdrop"
                                   TintColor="{ThemeResource SystemAccentColor}"
                                   TintOpacity=".6"/>
                            <LinearGradientBrush x:Key="NavigationViewExpandedPaneBackground"
                                     StartPoint="0.5,0" EndPoint="0.5,1">
                                <GradientStop Color="LightSlateGray" Offset="0.0" />
                                <GradientStop Color="White" Offset="1.0" />
                            </LinearGradientBrush>
                        </ResourceDictionary>
                        <ResourceDictionary x:Key="HighContrast">
                            <!-- Always include a "HighContrast" dictionary when you override
                                 theme resources. This empty dictionary ensures that the 
                                 default high contrast resources are used when the user
                                 turns on high contrast mode. -->
                        </ResourceDictionary>
                    </ResourceDictionary.ThemeDictionaries>
                </ResourceDictionary>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

### <a name="top-whitespace"></a>顶部空白

> `IsTitleBarAutoPaddingEnabled` 属性需要 [Windows UI 库](/uwp/toolkits/winui/) 2.2 或更高版本。

某些应用选择[自定义其窗口的标题栏](../shell/title-bar.md)，可能会将其应用内容扩展到标题栏区域中。 当 NavigationView 是 **使用 [ExtendViewIntoTitleBar](/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.extendviewintotitlebar) API** 扩展到标题栏的应用中的根元素时，该控件会自动调整其交互式元素的位置，以防止与 [可拖动区域](../shell/title-bar.md#draggable-regions)重叠。

![扩展到标题栏中的应用](images/navigation-view-with-titlebar-padding.png)

如果应用通过调用 [Window.SetTitleBar](/uwp/api/windows.ui.xaml.window.settitlebar) 方法来指定可拖动区域，并且你希望使“后退”和菜单按钮拖动到距离应用窗口顶部更近的位置，请将 [IsTitleBarAutoPaddingEnabled](/uwp/api/microsoft.ui.xaml.controls.navigationview.istitlebarautopaddingenabled) 设置为 False。

![应用扩展到标题栏而无需额外填充](images/navigation-view-no-titlebar-padding.png)

```Xaml
<muxc:NavigationView x:Name="NavView" IsTitleBarAutoPaddingEnabled="False">
```

#### <a name="remarks"></a>备注
若要进一步调整 NavigationView 的标题区域位置，请重写 *NavigationViewHeaderMargin XAML* 主题资源，例如，在“页”资源中。

```Xaml
<Page.Resources>
    <Thickness x:Key="NavigationViewHeaderMargin">12,0</Thickness>
</Page.Resources>
```

此主题资源修改 [NavigationView.Header](/uwp/api/windows.ui.xaml.controls.navigationview.header) 周围的边距。

## <a name="related-topics"></a>相关主题

- [NavigationView 类](/uwp/api/windows.ui.xaml.controls.navigationview)
- [列表/细节](list-details.md)
- [导航基础知识](../basics/navigation-basics.md)
- [Fluent Design 概述](/windows/apps/fluent-design-system)
