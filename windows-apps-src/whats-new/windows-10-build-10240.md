---
description: Windows 10 内部版本 10240 和开发人员工具更新继续提供受通用 Windows 平台支持的工具、功能和体验。
title: Windows 10 版本 10240 中的新增功能 - 2015 年 7 月
keywords: Windows 10, 1507, 10240
ms.date: 11/02/2017
ms.topic: article
ms.assetid: 4853e55b-2232-4589-903a-ccb60e07aeb8
ms.localizationpriority: medium
ms.openlocfilehash: 282026436355c1bcaab504d2818c2a30ff09e20d
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93029910"
---
# <a name="whats-new-in-windows-10-for-developers-build-10240"></a>面向开发人员的 Windows 10 版本 10240 中的新增功能

Windows 10 内部版本 10240 和更新的 SDK 提供用于打造出色的通用 Windows 平台应用的工具、功能和体验。 在 Windows 10 上[安装这些工具和 SDK](https://developer.microsoft.com/windows/downloads#_blank) 后，你可以随时[创建新的通用 Windows 应用](../get-started/create-uwp-apps.md)或了解如何使用 [Windows 上的现有应用代码](../porting/index.md)。

下面按功能逐项查看 Windows 10 版本 10240（也称为 Windows 10 版本 1507）的新增功能。

## <a name="adaptive-layouts"></a>自适应布局

功能 | 说明
 :---- | ----:
定制内容的多个视图 | XAML 为定义共享同一代码文件的定制视图（.xaml 文件）提供了新的支持。 这使你能够更加轻松地创建和维护针对特定设备系列或方案定制的不同视图。 如果你的应用具有不同的 UI 内容、布局或导航模型（因针对不同的方案而具有明显差异），请生成多个视图。 例如，对于针对移动版应用的单手使用模式进行优化的导航菜单，你可以使用 [Pivot](/uwp/api/Windows.UI.Xaml.Controls.Pivot)；而对于针对桌面版应用的鼠标输入进行优化的导航菜单，你可以使用 [SplitView](/uwp/api/Windows.UI.Xaml.Controls.SplitView)。
StateTriggers | 使用新的 [VisualState.StateTriggers](/uwp/api/windows.ui.xaml.visualstate.statetriggers) 功能，你可以基于窗口高度/宽度或者基于自定义触发器有条件地设置相关属性。 之前，你必须在代码中处理 Window [SizeChanged](/uwp/api/windows.ui.xaml.window.sizechanged) 事件并调用 [VisualStateManager.GotoState](/uwp/api/windows.ui.xaml.visualstatemanager.gotostate)。
Setters | 使用新的 [VisualState.Setters](/uwp/api/windows.ui.xaml.visualstate.setters) 语法，你可以使用简化的标记定义 [VisualStateManager](/uwp/api/windows.ui.xaml.visualstatemanager) 中的属性更改。 之前，你必须使用情节提要并创建动画来应用属性更改，例如将 [StackPanel](/uwp/api/windows.ui.xaml.controls.stackpanel) 的方向从水平更改为垂直。 在通用 Windows 应用中，可以使用以下简单的 Setter 语法：<setter target="stackPanel1.Orientation" value="Vertical" />


## <a name="xaml-features"></a>XAML 功能

功能 | 说明
 :---- | :----
已编译的数据绑定 (x:Bind) | 在通用 Windows 应用中，你可以使用基于编译器的新绑定机制，该机制可通过 x:Bind 属性进行启用。 基于编译器的绑定属于强类型，并且会在编译时进行处理，这样速度既快又可以在绑定类型不匹配时提供编译时错误。 而且由于绑定已转换为经过编译的应用代码，因此你现在可以通过在 Visual Studio 中单步调试代码诊断具体的绑定问题来调试绑定。 你也可以使用 x:Bind 绑定到某个方法，如下所示：<textblock text="{x:Bind Customer.Address.ToString()}" /> 对于典型的绑定方案，可以使用 x:Bind 代替 Binding，并获取改进的性能和可维护性。
列表的声明性增量呈现 (x:Phase) | 在通用 Windows 应用中，新的 x:Phase 属性允许你使用 XAML（而非代码）执行列表的增量呈现或阶段性呈现。 当平移项目比较复杂的长列表时，你的应用可能无法以足够跟上平移速度的速度来呈现项目，从而为你的用户带来不良的体验。 分阶段呈现使你可以指定列表项目中各个元素的呈现优先级，所以只有列表项目的最重要部分才会在快速平移时呈现。 这将为你的用户带来更为顺畅的平移体验。 <br /><br /> 在 Windows 8.1 中，你可以处理 [ContainerContentChanging](/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging) 事件，并编写用于分阶段呈现列表项目的代码。 在 UWP 应用中，你可以利用 x:Phase 属性以声明的方式完成分阶段呈现。 将 x:Phase 与已编译的绑定 x:Bind 结合使用使你可以轻松地为数据模板中的每个绑定元素指定呈现优先级。 平移时，呈现项目的工作会根据阶段分成时间片，从而支持增量项目呈现。
UI 元素的延迟加载 (x:deferLoadstrategy) | 在通用 Windows 应用中，新的 x:deferLoadstrategy 指令允许你指定要延迟加载的用户界面部分，从而改进启动性能并减少应用的内存使用量。 例如，如果你的应用 UI 具有用于数据验证的元素，并且仅在输入不正确的数据时显示，则可以延迟加载该元素（除非需要显示）。 然后，元素对象不会在加载页面时创建；只有出现数据错误时或者需要添加到页面的可视化树时才会创建它们。
SplitView | 借助新的 [SplitView](/uwp/api/windows.ui.xaml.controls.splitview) 控件，你可以轻松显示和隐藏瞬态内容。 该控件通常用于“汉堡包菜单”之类的顶级导航方案，其中导航内容处于隐藏状态，可按需滑入作为用户操作的结果。
RelativePanel | [RelativePanel](/uwp/api/windows.ui.xaml.controls.relativepanel) 是一种新型的布局面板，该面板允许你放置子对象并使其相互对齐或与父面板对齐。 例如，你可以指定某些文本应始终放置在该面板的左侧，某个按钮应始终在文本下方对齐。 当创建没有明确的线性模式的用户模式但却要求使用 [StackPanel](/uwp/api/windows.ui.xaml.controls.stackpanel) 或 [Grid](/uwp/api/windows.ui.xaml.controls.grid) 时，可使用 ReleativePanel。
CalendarView | 借助 [CalendarView](/uwp/api/windows.ui.xaml.controls.calendarview) 控件，可使用基于月份的自定义视图轻松查看和选择日期以及日期范围。 CalendarView 支持相关功能，例如可将最小日期、最大日期和截止日期限制在可选的日期范围内。 你还可以设置自定义密度栏，这些密度栏可用于显示某一天的计划的一般“细节”。
CalendarDatePicker | [CalendarDatePicker](/uwp/api/windows.ui.xaml.controls.calendardatepicker) 是一个下拉式控件，该控件已针对从 CalendarView 选取某个日期进行了优化，尤其是能够显示诸如星期几或丰富的日历信息等上下文信息。 它类似于 [DatePicker](/uwp/api/windows.ui.xaml.controls.datepicker) 控件，不过 DatePicker 是针对选取一个已知日期（例如出生日期）进行优化的。
MediaTransportControls | 借助新的 [MediaTransportControls](/uwp/api/windows.ui.xaml.controls.mediatransportcontrols) 类，可更轻松地自定义 [MediaElement](/uwp/api/windows.ui.xaml.controls.mediaelement) 的传输控件。 在 Windows 8.1 中，你可以启用 MediaElement 的内置传输控件，或通过调用 MediaElement 方法创建你自己的传输控件。 现在，你可以使用 MediaTransportControls 的内置功能，并且仍然可以轻松地自定义其外观，以适合你的应用。
属性更改通知 | 在通用 Windows 应用中，你可以侦听 DependencyObjects 上的属性更改，即便是属性没有对应的更改事件也是如此。 该通知会像事件一样运行，但实际上作为回调公开。 该回调会像事件处理程序一样采用发送者参数，但不会采用事件参数。 仅传递属性标识符以指示何种属性。 借助此信息，你的应用可以为多个属性通知定义单个处理程序。 有关详细信息，请参阅 [RegisterPropertyChangedCallback](/uwp/api/windows.ui.xaml.dependencyobject.registerpropertychangedcallback) 和 [UnregisterPropertyChangedCallback](/uwp/api/windows.ui.xaml.dependencyobject.unregisterpropertychangedcallback)。
地图 | 已更新为提供 3D 鸟瞰图和街景视图的 [MapControl](/uwp/api/windows.ui.xaml.controls.maps.mapcontrol) 类。 这些新功能和早期的映射功能现在可用于通用 Windows 应用。 使用以下 API 向应用添加地图功能：[Windows.UI.Xaml.Controls.Maps](/uwp/api/windows.ui.xaml.controls.maps) 和 [Windows.Services.Maps](/uwp/api/windows.services.maps)。 若要立即开始在通用 Windows 应用中使用这些 API，请从[必应地图开发人员中心](https://www.bingmapsportal.com/)请求一个密钥。 有关详细信息，请参阅[如何验证地图应用](/previous-versions/windows/apps/dn741528(v=win.10))。 Windows 10 还有一个新增功能，即电脑和手机用户可以从“设置”应用下载离线地图。 当 Internet 访问不可用时，[MapControl](/uwp/api/windows.ui.xaml.controls.maps.mapcontrol) 可使用脱机地图（如果可用）来显示地图。
输入按钮映射 | [Windows.UI.Xaml.Input.KeyEventArgs](/uwp/api/windows.ui.xaml.input.keyroutedeventargs) 类具有新的 [OriginalKey](/uwp/api/windows.ui.xaml.input.keyroutedeventargs.originalkey) 属性以及对 [Windows.System.VirtualKey](/uwp/api/windows.system.virtualkey) 所做的相应更新，从而让你可以获取与键盘输入事件关联的原始且未映射的输入按钮。
墨迹书写 | 借助 [InkCanvas](/uwp/api/windows.ui.xaml.controls.inkcanvas) 控件和基础 [InkPresenter](/uwp/api/windows.ui.input.inking.inkpresenter) 类，现在可以更容易地使用采用 C++、C# 或 Visual Basic 的 Windows 运行时应用中功能强大的墨迹功能。 [InkCanvas](/uwp/api/windows.ui.xaml.controls.inkcanvas) 控件定义了一个用于绘制和呈现墨迹笔划的覆盖区域。 此控件的功能（输入、处理和呈现）源自 [InkPresenter](/uwp/api/windows.ui.input.inking.inkpresenter)、[InkStroke](/uwp/api/windows.ui.input.inking.inkstroke)、[InkRecognizers](/uwp/api/windows.ui.input.inking.inkrecognizer) 和 [InkSynchronizer](/uwp/api/windows.ui.input.inking.inksynchronizer) 类。 **重要说明：** 这些类在使用 JavaScript 的 Windows 应用中不受支持。


## <a name="updated-xaml-features"></a>已更新的 XAML 功能

功能 | 说明
 :---- | :----
CommandBar 和 AppBar 更新 | [CommandBar](/uwp/api/windows.ui.xaml.controls.commandbar) 和 [AppBar](/uwp/api/windows.ui.xaml.controls.appbar) 控件已针对跨设备系列的 UWP 应用更新为具有一致的 API、行为和用户体验。 <br /><br />适用于通用 Windows 应用的 CommandBar 控件已得到了改进，从而可提供 AppBar 功能的超集，并且针对如何将其用于你的应用提供了更大的灵活性。 对于 Windows 10 上所有新的通用 Windows 应用，应使用 CommandBar。 在 Windows 8.1 的 CommandBar 中，你只能使用已实现 [ICommandBarElement](/uwp/api/windows.ui.xaml.controls.icommandbarelement) 的控件，如 [AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton)。 在通用 Windows 应用中，除了 AppBarButtons 外，你现在还可以将自定义内容放入 CommandBar 中。 <br /><br />AppBar 控件已得到了更新，以便你可以更加轻松地将使用 AppBar 的 Windows 8.1 应用移动到通用 Windows 平台。 AppBar 设计为可与全屏应用一起使用并且可通过边缘手势进行调用。 控件更新考虑到了 Windows 10 中窗口化应用和缺少边缘手势等问题。 <br /><br />之前仅在 Windows Phone 上提供的[隐藏 AppBar.ClosedDisplayMode](/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode) 现在在所有设备系列上均受支持，从而让你可以在不同的命令提示级别之间选择。 AppBar 默认情况下显示最少内容的提示，以便在将你的 Windows 8.1 应用升级到通用 Windows 应用时能为你提供一致性，不过你将无法再依赖平台中的边缘手势支持。
GridView 更新 | 在 Windows 10 之前，默认的 GridView 布局方向在 Windows 上为水平方向，而在 Windows Phone 上为垂直方向。 在 UWP 应用中，GridView 默认情况下针对所有设备系列使用垂直布局，以确保你能获得一致的默认体验。
AreStickyGroupHeadersEnabled 属性 | 当你在 [ListView](/uwp/api/windows.ui.xaml.controls.listview) 或 [GridView](/uwp/api/windows.ui.xaml.controls.gridview) 中显示分组数据时，组标头将在列表滚动时保持可见状态。 这在大型数据集中很重要，因为标头会提供用户正在查看的数据的上下文。 但是，如果每组中只有几个元素，你可能需要将这些标头滚动到具有这些项目的屏幕之外。 你可以通过设置 [ItemsStackPanel](/uwp/api/windows.ui.xaml.controls.itemsstackpanel) 和 [ItemsWrapGrid](/uwp/api/windows.ui.xaml.controls.itemswrapgrid) 上的 AreStickyGroupHeadersEnabled 属性控制此行为。
GroupHeaderContainerFromItemContainer 方法 | 当你在 [ItemsControl](/uwp/api/windows.ui.xaml.controls.itemscontrol) 中显示分组数据时，你可以通过调用 [GroupHeaderContainerFromItemContainer](/uwp/api/windows.ui.xaml.controls.itemscontrol.groupheadercontainerfromitemcontainer) 方法，获取对该组父标头的引用。 例如，如果用户删除组中的最后一项，你可以获取对组标头的引用，并且可以同时删除该项和组标头。
ChoosingGroupHeaderContainer 事件 | 借助 [ListViewBase](/uwp/api/windows.ui.xaml.controls.listviewbase) 上的新 [ChoosingGroupHeaderContainer](/uwp/api/windows.ui.xaml.controls.listviewbase.choosinggroupheadercontainer) 事件，你可以在 ListView 或 GridView 中设置组标头的状态。 例如，你可以通过处理此事件，将组标头上的 [AutomationProperties.Nameproperty](/uwp/api/windows.ui.xaml.automation.automationproperties.nameproperty) 设置为借助辅助技术来标识该组。
ChoosingItemContainer 事件 | 借助 ListViewBase 上的新 [ChoosingItemContainer](/uwp/api/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) 事件，你可以更好地控制 [ListView](/uwp/api/windows.ui.xaml.controls.listview) 或 [GridView](/uwp/api/windows.ui.xaml.controls.gridview) 中的 UI 虚拟化。 将此事件与 [ContainerContentChanging](/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging) 事件结合使用，以保持你自己的要根据需要利用的回收容器的队列。 例如，如果由于筛选而重置了数据源，你可以快速地将一组已创建好的视觉效果 (ItemContainers) 与其数据匹配，以便实现最佳性能。
列表滚动虚拟化 |XAML [ListView](/uwp/api/windows.ui.xaml.controls.listview) 和 [GridView](/uwp/api/windows.ui.xaml.controls.gridview) 控件中有一个新 [ListViewBase.ChooseingItemContainer](/uwp/api/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) 事件，该事件可在数据集合中发生更改时改进控件的性能。 现在，系统将维护当前视图中的项目以及焦点和选择状态，而不是完全重置列表，以免重播进入动画；视口中新建和已删除的项目会流畅地以动画方式进入和离开。 一旦未破坏的容器中的数据集合发生变化，应用即可快速将所有“旧”项与其之前的容器匹配，并跳过容器生命周期替代方法的进一步处理。 仅“新”项得到处理并与回收的容器或新容器相关联。
SelectRange 方法和 SelectedRanges 属性 | 在通用 Windows 应用中，借助 [ListView](/uwp/api/windows.ui.xaml.controls.listview) 和 [GridView](/uwp/api/windows.ui.xaml.controls.gridview) 控件，你现在可以根据项目索引的范围（而非项目对象引用）选择相应项目。 使用此方法描述项目选择更有效率，因为不需要为每个所选的项目创建项目对象。 有关详细信息，请参阅 [ListViewBase.SelectedRanges](/uwp/api/windows.ui.xaml.controls.listviewbase.selectedranges)、[ListViewBase.SelectRange](/uwp/api/windows.ui.xaml.controls.listviewbase.selectrange) 和 [ListViewBase.DeselectRange](/uwp/api/windows.ui.xaml.controls.listviewbase.deselectrange)。
新的 ListViewItemPresenter API | [ListView](/uwp/api/windows.ui.xaml.controls.listview) 和 [GridView](/uwp/api/windows.ui.xaml.controls.gridview) 使用项目表示器来提供默认的视觉对象，以供选择和着重显示。 在 UWP 应用中，[ListViewItemPresenter](/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter) 和[GridViewItemPresenter](/uwp/api/windows.ui.xaml.controls.primitives.gridviewitempresenter) 具有新的属性，以便你可以针对列表项目进一步自定义视觉对象。 新增的属性为 heckBoxBrush、CheckMode、FocusSecondaryBorderBrush、PointerOverForeground、PressedBackground 和 SelectedPressedBackground。
SemanticZoom 更新 | 对于跨所有设备系列的 UWP 应用，[SemanticZoom](/uwp/api/windows.ui.xaml.controls.semanticzoom) 控件现在具有一致的行为。 在放大视图和缩小视图之间进行切换的默认操作是点击放大视图中的组标头。 这与 Windows Phone 8.1 中的行为相同，但与 Windows 8.1 中的行为不同，对于后者，可使用收缩手势进行缩放。 若要使用收缩手势更改视图，应在 SemanticZoom 的内部 ScrollViewer 上设置 [ScrollViewer.ZoomMode](/uwp/api/windows.ui.xaml.controls.scrollviewer.zoommode)="Enabled"。 <br /><br />对于通用 Windows 应用，缩小视图会替换放大视图，并且大小与其替换的视图相同。 这与 Windows 8.1 中的行为相同，但与 Windows Phone 8.1 中的行为不同，对于后者，缩小视图的大小占据了整个屏幕，并且在所有其他内容之上呈现。
DatePicker 和 TimePicker 更新 | 对于跨所有设备系列的通用 Windows 应用，[DatePicker](/uwp/api/windows.ui.xaml.controls.datepicker) 和 [TimePicker](/uwp/api/windows.ui.xaml.controls.timepicker) 控件现在具有一个一致的实现。 它们还具有适用于 Windows 10 的新外观。 控件的弹出部分现在可在所有设备上使用 [DatePickerFlyout](/uwp/api/windows.ui.xaml.controls.datepickerflyout) 和 [TimePickerFlyout](/uwp/api/windows.ui.xaml.controls.timepickerflyout) 控件。 这与 Windows Phone 8.1 上的此类行为相同，不过在 Windows 8.1 上有所变动，因为其中使用了 [ComboBox](/uwp/api/windows.ui.xaml.controls.combobox) 控件。 使用浮出控件可让你更轻松地创建自定义的日期和时间选取器。
新的 ScrollViewer API | [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer) 具有新的 [DirectManipulationStarted](/uwp/api/windows.ui.xaml.controls.scrollviewer.directmanipulationstarted) 和 [DirectManipulationCompleted](/uwp/api/windows.ui.xaml.controls.scrollviewer.directmanipulationcompleted) 事件，可在触摸平移开始和停止时通知应用。 你可以处理这些事件以使你的 UI 与这些用户操作相协调。
MenuFlyout 更新 | 在通用 Windows 应用中，有一些新的 API 使你可以更轻松地生成更好的上下文菜单。 新 [MenuFlyout.ShowAt](/uwp/api/windows.ui.xaml.controls.menuflyout.showat) 方法使你可以指定你希望浮出控件相对于其他元素的出现位置。 （MenuFlyout 甚至可与应用窗口的边界重叠。）使用新的 [MenuFlyoutSubItem](/uwp/api/windows.ui.xaml.controls.menuflyoutsubitem) 类创建级联菜单。
ContentPresenter、Grid 和 StackPanel 的新 Border 属性 | 常见容器控件具有新的边框属性，可用于在其周围绘制边框，而无需向你的 XAML 添加其他 Border 元素。 [ContentPresenter](/uwp/api/windows.ui.xaml.controls.contentpresenter)、[Grid](/uwp/api/windows.ui.xaml.controls.grid) 和 [StackPanel](/uwp/api/windows.ui.xaml.controls.stackpanel) 包含以下新属性：BorderBrush、BorderThickness、CornerRadius 和 Padding。
ContentPresenter 上的新文本 API | [ContentPresenter](/uwp/api/windows.ui.xaml.controls.contentpresenter) 包含的新 API 可让你更好地控制文本显示：LineHeight、LineStackingStrategy、MaxLines 和 TextWrapping。
系统焦点视觉效果 | XAML 控件的焦点视觉效果现在由系统创建，而不是作为 XAML 元素在控件模板中声明。 在移动设备上，通常不需要焦点视觉效果，让系统根据需要创建并管理它们可提升性能。 如果你需要更好地控制焦点视觉效果，可以替代系统行为并提供定义焦点视觉效果的自定义控件模板。 有关详细信息，请参阅 [UseSystemFocusVisuals](/uwp/api/windows.ui.xaml.controls.control.usesystemfocusvisuals) 和 [IsTemplateFocusTarget](/uwp/api/windows.ui.xaml.controls.control.istemplatefocustargetproperty)。
PasswordBox.PasswordRevealMode | 在通用 Windows 应用中，[PasswordRevealMode](/uwp/api/windows.ui.xaml.controls.passwordbox.passwordrevealmode) 属性将替换 IsPasswordRevealButtonEnabled 属性，以跨设备系列提供一致的行为。 **警告：** 在 Windows 10 之前，密码显示按钮默认情况下不显示；在通用 Windows 应用中，将在默认情况下显示。 如果应用安全要求始终掩盖密码，请确保将 PasswordRevealMode 设置为 Hidden。
Control.IsTextScaleFactorEnabled | 以前可在 Windows Phone 8.1 上使用的 [IsTextScaleFactorEnabled](/uwp/api/windows.ui.xaml.controls.control.istextscalefactorenabledproperty) 属性现在可用于所有设备系列上的通用 Windows 应用。
AutoSuggestBox | Windows Phone 8.1 中的 [AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.autosuggestbox) 控件现在可用于所有设备系列上的通用 Windows 应用，你应使用它而不是 [SearchBox](/uwp/api/windows.ui.xaml.controls.searchbox)。 AutoSuggestBox 在用户键入时即提供建议，与各种输入类型相辅相成，如触摸、键盘和输入法编辑器。 它还具有一些可使之更好地发挥搜索框作用的新成员：[QueryIcon](/uwp/api/windows.ui.xaml.controls.autosuggestbox.queryicon) 属性和 [QuerySubmitted](/uwp/api/windows.ui.xaml.controls.autosuggestbox.querysubmitted) 事件。
ContentDialog | Windows Phone 8.1 中的 [ContentDialog](/uwp/api/windows.ui.xaml.controls.contentdialog) 控件现在可用于所有设备系列上的通用 Windows 应用。 ContentDialog 可用于显示可在所有系列的设备上完美运行的自定义模式对话框。
Pivot | Windows Phone 8.1 中的 [Pivot](/uwp/api/windows.ui.xaml.controls.pivot) 控件现在可用于所有设备系列上的通用 Windows 应用。 现在，你可以将相同的 Pivot 控件用于你的移动和桌面设备应用。 Pivot 基于屏幕大小和输入类型提供自适应行为。 你可以设置 Pivot 控件的样式，以提供类似选项卡的行为，每个透视项中包含不同的信息视图。

## <a name="text"></a>文本

功能 | 说明
 :---- | :----
Windows 核心文本 API | 新的 [Windows.UI.Text.Core](/uwp/api/windows.ui.text.core) 命名空间具有一个客户端-服务器系统功能，该系统可将键盘输入处理集中到单个服务器。 你可以使用它来操作自定义文本输入控件的编辑缓冲区。 文本输入服务器通过应用和服务器之间的异步信道确保文本输入控件的内容及其编辑缓冲区的内容始终处于同步状态。
矢量图标 | [Glyphs](/uwp/api/windows.ui.xaml.documents.glyphs) 元素具有新的 [IsColorFontEnabled](/uwp/api/windows.ui.xaml.documents.glyphs.iscolorfontenabled) 和 [ColorFontPalleteIndex](/uwp/api/windows.ui.xaml.documents.glyphs.colorfontpaletteindex) 属性，可支持彩色字体；现在，你可以使用字体文件呈现基于字体的图标。 在将 ColorFontPalleteIndex 用于调色板切换时，可使用不同的颜色组合呈现一个图标；例如，显示该图标的启用和禁用版本。
输入法编辑器窗口事件 | 用户有时会通过在文本输入框正下方的窗口中显示的输入法编辑器输入文字（通常用于东亚语言）。 若要使你的应用 UI 与 IME 窗口相得益彰，你可以对 [TextBox](/uwp/api/windows.ui.xaml.controls.textbox) 和 [RichEditBox](/uwp/api/windows.ui.xaml.controls.richeditbox) 使用 CandidateWindowBoundsChanged 事件和 DesiredCandidateWindowAlignment 属性。
文本排版事件 | [TextBox](/uwp/api/windows.ui.xaml.controls.textbox) 和 [RichEditBox](/uwp/api/windows.ui.xaml.controls.richeditbox) 包含新的事件，以便在使用输入法编辑器撰写文本时通知应用：TextCompositionStarted、TextCompositionEnded 和 TextCompositionChanged。 你可以处理这些事件以使你的应用代码与 IME 文本排版过程相协调。 例如，你可以针对东亚语言实现内联式自动完成功能。
改进对双向文本的处理 | XAML 文本控件的新 API 可改进对双向文本的处理，从而在各种输入语言中更好地实现文本对齐和段落方向性。 TextReadingOrder 属性的默认值已更改为 DetectFromContent，因此对检测读取顺序的支持在默认情况下处于启用状态。 TextReadingOrder 属性也已添加到 PasswordBox、RichEditBox 和 TextBox。 若要选择从内容自动检测对齐方式，可以将文本控件的 TextAlignment 属性设置为新的 DetectFromContent 值。
文本呈现 | 在 Windows 10 中，XAML 应用中的文本呈现速度现在大多比 Windows 8.1 将近快两倍。 在大多数情况下，你的应用将受益于这一改进，而无需进行任何更改。 除了更快地呈现之外，这些改进还降低了 5% 的 XAML 应用典型内存消耗。

## <a name="application-model"></a>应用程序模型

功能 | 说明
 :---- | :----
Cortana | 通过语音命令扩展 Cortana 的基本功能，这些命令用于在外部应用程序中启动并执行一个单独操作。 通过集成应用的基本功能，并通过为用户提供中心入口点以在无需直接打开应用的情况下完成大多数任务，Cortana 可以充当应用和用户之间的联络人。 在大多数情况下，这可以为用户节省大量时间和精力。 了解如何[将应用集成到 Cortana 画布](/previous-versions/windows/apps/dn974230(v=win.10))。 如果你需要创意，可以参考[通用 Windows 应用设计基础知识](../design/layout/index.md)中特定于 Cortana 的设计建议和 UX 指南。
文件资源浏览器 | 新的 [Windows.System.Launcher.LaunchFolderAsync](/uwp/api/windows.system.launcher.launchfolderasync) 方法允许你启动文件资源管理器并显示所指定的文件夹的内容。
共享存储 | 新的 [Windows.ApplicationModel.DataTransfer.SharedStorageAccessManager](/uwp/api/windows.applicationmodel.datatransfer.sharedstorageaccessmanager) 类及其方法允许你与另一个应用共享文件，方法是当通过使用 URI 激活启动另一个应用时，传递一个共享标记。 目标应用会兑换令牌以获取源应用共享的文件。
Settings | 通过将 ms-settings 协议与 [LaunchUriAsync](/uwp/api/windows.system.launcher.launchuriasync) 方法结合使用，显示内置的设置页面。 例如，以下代码显示 WLAN 设置页面： **bool result = await Launcher.LaunchUriAsync(new Uri("ms-settings://network/wifi"));** <br /><br />有关可以显示的设置页面列表，请参阅[如何使用 ms-settings 协议显示内置设置页面](/previous-versions/windows/apps/jj207014(v=vs.105))。
应用到应用的通信 | 通过 Windows 10 中新增的[应用到应用的通信](/previous-versions/windows/apps/dn997827(v=win.10)) API，Windows 应用程序（以及 Windows Web 应用程序）可以相互启动并交换数据和文件。 利用这些新 API，使得原本需要用户使用多个应用程序才能完成的复杂任务现在可以无缝地进行处理。 例如，你的应用可启动社交网络应用来选择联系人，或启动结算应用程序来完成支付流程。
应用服务 | 应用服务是应用在 Windows 10 中向其他应用提供服务的方法。 应用服务的表现形式为后台任务。 前台应用可以调用其他应用中的应用服务以在后台执行任务。 有关应用服务 API 的参考信息，请参阅 [Windows.ApplicationModel.AppService](/uwp/api/Windows.ApplicationModel.AppService)。
应用包清单 | 对 Windows 10 的[程序包清单架构](/uwp/schemas/appxpackage/appx-package-manifest)参考的更新包括已添加、已删除和已更改的元素。 有关该架构中所有元素、属性和类型的参考信息，请参阅[元素层次结构](/uwp/schemas/appxpackage/uapmanifestschema/root-elements)。

## <a name="devices"></a>设备

功能 | 说明
 :---- | :----
Microsoft Surface Hub | Microsoft Surface Hub 是一款强大的团队协作设备和大屏幕平台，适用于在 Surface Hub 或连接的设备中以本机方式运行的通用 Windows 应用。 针对自己的业务生成可以利用大屏幕、触摸和墨迹输入以及各种板载硬件（如相机和传感器）的应用。<br /><br />请参阅[通用 Windows 应用设计基础知识](../design/layout/index.md)中特定于 Surface Hub 的设计建议和 UX 指南。 这些文档介绍了通用 Windows 应用的响应式设计技术。 <br /><br />有关支持社区共享应用的详细信息，请参阅 [SharedModeSettings](/uwp/api/windows.system.profile.sharedmodesettings)。 有关墨迹输入以及在 [InkCanvas](/uwp/api/windows.ui.xaml.controls.inkcanvas) 控件上支持多点墨迹的详细信息，请参阅 [Windows.UI.Input.Inking](/uwp/api/windows.ui.input.inking) 和 [Windows.UI.Input.Inking.Core](/uwp/api/windows.ui.input.inking.core)。 有关处理传感器输入的信息，请参阅[集成设备、打印机和传感器](/previous-versions/windows/apps/br229563(v=win.10))。
位置 | Windows 10 引入了一个新方法 [RequestAccessAsync](/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) 来提示用户提供其位置的访问权限。 用户可通过 **“设置”** 应用中的 **“位置隐私设置”** 来设置其位置数据隐私。 仅当出现以下情况时，你的应用才可以访问用户的位置：“此设备的位置”已打开（不适用于 Windows 10 手机版）、位置服务设置“位置”已打开，并且在“选择可以使用你的位置信息的应用”下，你的应用已设置为打开 。 <br /><br />在访问用户的位置之前，务必调用 **RequestAccessAsync** 。 此时，你的应用必须位于前台，并且 **RequestAccessAsync** 必须从 UI 线程中进行调用。 除非用户向你的应用授予访问其位置的权限，否则你的应用将无法访问位置数据。
AllJoyn | [Windows.Devices.AllJoyn](/uwp/api/windows.devices.alljoyn) Windows 运行时命名空间引入了 Microsoft 实现的 AllJoyn 开放源代码软件框架和服务。 这些 API 使通用 Windows 设备应用可以在物联网 (IoT) 应用场景中参与到 AllJoyn 驱动的其他设备中。 有关 AllJoyn C API 的详细信息，请在 [AllSeen Alliance](https://allseenalliance.org/) 处下载相关文档。 使用该版本中所包含的 [AllJoynCodeGen](/previous-versions/windows/apps/dn913809(v=win.10)) 工具，生成可用于在设备应用中启用 AllJoyn 方案的 Windows 组件。 <br /><br />**注意：** Windows 10 IoT 核心版现在可用于新类型的小型设备，从而允许你使用 Windows 和 Visual Studio 创建“物联网”(IoT) 设备。 在 [Windows 开发人员中心](https://developer.microsoft.com/windows/iot)中了解关于 Windows IoT 的详细信息。
在移动设备 (XAML) 上打印 API | 有一组单独且统一的 API 使你可以在各设备系列（包括移动设备）中基于 XAML 的 UWP 应用内进行打印。 现在，你可以从 Windows.Graphics.Printing 和 Windows.UI.Xaml.Printing 命名空间使用熟悉的与打印相关的 API，将打印添加到你的移动应用。
电池 | 通过 [Windows.Devices.Power](/uwp/api/windows.devices.power) 命名空间中的电池 API，你的应用可了解连接到正在运行该应用的设备的所有电池的详细信息。 通过创建 [Battery](/uwp/api/windows.devices.power.battery) 对象来表示单个电池控制器或聚合的所有电池控制器（在它们分别由 [FromIdAsync](/uwp/api/windows.devices.power.battery.fromidasync) 或 [AggregateBattery](/uwp/api/windows.devices.power.battery.aggregatebattery) 创建后）。 使用 [GetReport](/uwp/api/windows.devices.power.battery.getreport) 方法返回 [BatteryReport](/uwp/api/windows.devices.power.batteryreport) 对象，该对象可指示相应电池的充电、容量和状态。
MIDI 设备 | 新的 [Windows.Devices.Midi](/uwp/api/windows.devices.midi) 命名空间可以让你创建能够与外部 MIDI 设备通信的 A=apps、直接与 Microsoft GS MIDI 软件合成器通信的应用和外部设备，以及多个客户端同时访问单个 MIDI 端口的方案。
自定义传感器支持 | [Windows.Devices.Sensors.Custom](/uwp/api/windows.devices.sensors.custom) 命名空间允许硬件开发人员定义新的自定义传感器类型，例如 CO2 传感器。
基于主机的卡仿真 (HCE) | 主机卡仿真使你能够实现操作系统中托管的 NFC 卡仿真服务，并且仍可以通过 NFC 射频与外部读取器终端进行通信。 若要触发后台任务以通过 NFC 模拟智能卡，请使用 [SmartCardTrigger](/uwp/api/Windows.ApplicationModel.Background.SmartCardTrigger) 类。 [SmartCardTriggerType](/uwp/api/Windows.Devices.SmartCards.SmartCardTriggerType) 枚举中的 EmulatorHostApplicationActivated 值会让你的应用知道 HCE 事件已发生。

## <a name="graphics"></a>显卡

功能 | 说明
 :---- | :----
DirectX | Windows 10 中的 DirectX 12 引入了下一版本的 Microsoft Direct3D，它是 DirectX 的核心 3D 图形 API。 [Direct3D 12 图形](/windows/desktop/direct3d12/direct3d-12-graphics)可实现类似控制台的低级别 API 的效率和性能。 Direct3D 12 比以往更快且更有效率。 它可以实现更丰富的场景、更多的对象、更复杂的效果，并且可以更好地利用现代图形硬件。
SoftwareBitmapSource | 在通用 Windows 应用中，你可以使用新的 [SoftwareBitmapSource](/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource) 类型作为 XAML 图像源。 这样，你可以将未编码图像传递到 XAML 框架，以便立即在屏幕上显示，从而通过 XAML 框架绕过图像解码。 你可以实现更快的图像呈现，如直接从相机呈现低延迟的照片、使用自定义图像解码器、从 DirectX 表面捕获帧或从头开始创建内存中的图像，并通过低延迟和低内存开销的方式直接在 XAML 中将其全部呈现。
透视相机 | 在通用 Windows 应用中，XAML 具有全新 Transform3D API，它可用于将透视转换应用到 XAML 树（或场景），后者再根据该单一场景范围的转换（或相机）来转换所有 XAML 子元素。 你之前可以通过 MatrixTransform 和复杂的数学运算执行此操作，但 Transform3D 极大地简化了此效果，并且还支持对此效果进行动画处理。 有关详细信息，请参阅 [UIElement.Transform3D](/uwp/api/windows.ui.xaml.uielement.transform3d) 属性、[Transform3D](/uwp/api/windows.ui.xaml.media.media3d.transform3d)、[CompositeTransform3D](/uwp/api/windows.ui.xaml.media.media3d.compositetransform3d) 和 [PerspectiveTransform3D](/uwp/api/windows.ui.xaml.media.media3d.perspectivetransform3d)。

## <a name="media"></a>媒体

功能 | 说明
 :---- | :----
HTTP 实时传送视频流 | 你可以使用新的 [AdaptiveMediaSource](/uwp/api/windows.media.streaming.adaptive.adaptivemediasource) 类将自适应视频流功能添加到你的应用。 通过将对象指向流清单文件可初始化该对象。 支持的清单格式包括 Http 实时传送视频流 (HLS) 和 HTTP 动态自适应流式处理 (DASH)。 对象绑定到 XAML 媒体元素后，即开始自适应播放。 在适当的时候可以查询和设置流的属性，如可用的比特率、最小比特率和最大比特率。
对媒体基础转换 (MFTs) 的媒体基础转换代码视频处理器 (XVP) 支持 | 使用媒体基础转换 (MFT) 的 Windows 应用现在可以使用 **媒体基础转换代码视频处理器** (XVP) 来改变、缩放和转换原始视频数据：新 [MF_XVP_CALLER_ALLOCATES_OUTPUT](/windows/desktop/medfound/mf-xvp-caller-allocates-output) 属性支持输出到调用方分配的纹理，即使在 Microsoft DirectX 视频加速 (DXVA) 模式下也是如此。 新 [IMFVideoProcessorControl2](/windows/desktop/api/mfidl/nn-mfidl-imfvideoprocessorcontrol2) 接口允许你的应用启用硬件效果、查询支持的硬件效果以及替代由视频处理器执行的旋转操作。
转码 | 新 [MediaProcessingTrigger](/uwp/api/Windows.ApplicationModel.Background.MediaProcessingTrigger) API 让你的应用在后台任务中执行媒体转码，这样即使当前台应用已终止，也可以继续进行转码操作。
MediaElement 媒体故障事件 | 在通用 Windows 应用中，[MediaElement](/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 将播放包含多个流的内容，即使其中一个流具有解码错误，只要媒体内容包含至少一个有效流即可。 例如包含了一个音频流和一个视频流的内容，如果其中的视频流无法播放，[MediaElement](/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 仍会播放音频流。 [PartialMediaFailureDetected](/uwp/api/windows.ui.xaml.controls.mediaelement.partialmediafailuredetected) 会通知你流内的其中一个流无法解码。 它还会告知你哪些类型的流无法播放，以便你可以在 UI 中反映该信息。 如果在媒体流内的所有流失败，将引发 [MediaFailed](/uwp/api/windows.ui.xaml.controls.mediaelement.mediafailed) 事件。
对使用 MediaElement 的自适应视频流的支持 | [MediaElement](/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 具有新的 [SetPlaybackSource](/uwp/api/windows.ui.xaml.controls.mediaelement.setplaybacksource) 方法，可支持自适应视频流。 若要将你的媒体源设置为 AdaptiveMediaSource，请使用此方法。
通过 MediaElement 和图像转换 | “[MediaElement](/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 和图像”控件具有新的 [GetAsCastingSource](/uwp/api/windows.ui.xaml.controls.mediaelement.getascastingsource) 方法。 你可以使用此方法以编程方式将内容从任何媒体或图像元素发送到更多类型的远程设备，如 Miracast、蓝牙和 DLNA。 将 MediaElement 的 [AreTransportControlsEnabled](/uwp/api/windows.ui.xaml.controls.mediaelement.aretransportcontrolsenabled) 设置为 true 时，此功能将自动启用。
适用于桌面应用的媒体传输控件 | [ISystemMediaTransportControls](/previous-versions/windows/desktop/mediatransport/isystemmediatransportcontrols) 接口和相关的 API 允许桌面应用与内置的系统媒体传输控件交互。 其中包括：响应用户与传输控件按钮的交互，以及更新传输控件显示内容以显示有关当前正在播放的媒体内容的元数据。
随机访问 JPEG 编码和解码 | 新的 WIC 方法 [IWICJpegFrameEncode](/windows/desktop/api/wincodec/nn-wincodec-iwicjpegframeencode) 和 [IWICJpegFrameDecode](/windows/desktop/api/wincodec/nn-wincodec-iwicjpegframedecode) 支持 JPEG 图像的编码和解码。 你现在还可以启用为图像数据编制的索引，从而可以有效地随机访问大型图像，但代价是会产生较大的内存占用量。
用于媒体合成的覆盖 | 新 [MediaOverlay](/uwp/api/Windows.Media.Editing.MediaOverlay) 和 [MediaOverlayLayer](/uwp/api/Windows.Media.Editing.MediaOverlayLayer) API 更便于向媒体组合添加静态或动态媒体内容的多个层。 可以针对每个图层调整不透明度、位置和计时，并且甚至可以为输入层实现你自己的自定义合成器。
新的特效框架 | [Windows.Media.Effects](/uwp/api/windows.media.effects) 命名空间提供了简单、直观的框架，以便将效果添加到音频和视频流。 该框架包含你可以实现的基本接口，用于创建自定义音频和视频特效，并将它们插入媒体管道中。

## <a name="networking"></a>网络

功能 | 说明
 :---- | :----
套接字 | 套接字更新包括： <br /><br />**套接字代理：** 套接字代理可以在应用生命周期的任何状态下，代表应用建立和关闭套接字连接。 这使得应用及其提供的服务更容易被发现。 例如，通过套接字代理，Win32 服务即使不在运行中，也仍然可以接受传入的套接字连接。 <br /><br />**吞吐量改进：** 套接字吞吐量已针对使用 Windows.Networking.Sockets 命名空间的应用进行了优化。
后台传输后处理任务 | 你可以利用 [Windows.Networking.BackgroundTransfer](/uwp/api/windows.networking.backgroundtransfer) 命名空间中的新 API 注册后处理任务组。 这样你的应用便可以立即根据后台传输的成功或失败执行操作，而不是等待下次用户恢复它，即使该应用不在前台运行也是如此。
对广告的蓝牙支持 | 借助 [Windows.Devices.Bluetooth.Advertisement](/uwp/api/windows.devices.bluetooth.advertisement) 命名空间，你的应用可以发送、接收并筛选蓝牙 LE 广告。
WLAN Direct API 更新 | 设备代理已更新，目的是在不离开应用的情况下启用与设备配对。 除此之外，[Windows.Devices.WiFiDirect](/uwp/api/windows.devices.wifidirect) 命名空间还可以让某个设备能够检测到其他设备，并让该设备侦听传入的连接通知。<br /><br />**注意：** 此版本中的 Wi-Fi Direct 功能改进并未内置于用户体验中，并且它们仅支持一键配对。 另外，此版本仅支持一个活动连接。
JSON 支持改进 | 在调试会话期间，转换 JSON 对象时，[Windows.Data.Json](/uwp/api/windows.data.json) 命名空间现在能更好地支持已有的标准定义和开发人员体验。

## <a name="security"></a>安全

功能 | 说明
 :---- | :----
ECC 加密 | [Windows.Security.Cryptography](/uwp/api/windows.security.cryptography) 命名空间中的新 API 提供对椭圆曲线加密法的支持，该加密法是一种基于有限字段上的椭圆曲线的公钥加密实现。 ECC 在算术方面比 RSA 更为复杂，但提供了较小的密钥大小、降低了内存消耗，从而提升了性能。 它为 Microsoft 服务和客户提供了 RSA 密钥和 NIST 批准曲线参数的替代方法。
Microsoft Passport | Microsoft Passport 是身份验证的一种替代方法，使用非对称加密和手势来替换密码。 “凭据”命名空间中的类（如 [KeyCredentialManger](/uwp/api/windows.security.credentials.keycredentialmanager)）让开发人员可以轻松通过 Microsoft Passport 来创建应用程序，而无需使用复杂的加密或生物识别。
Microsoft Passport for Work | Microsoft Passport for Work 是使用 Azure Active Directory 帐户登录 Windows 的替代方法，不需要使用密码、智能卡和虚拟智能卡。 你可以选择是禁用还是启用此策略设置。
令牌代理 | 令牌代理是一种新的身份验证框架，使应用更易于连接到联机标识提供者（如 Facebook）。 帐户用户名和密码管理等功能和简化的 UI 为用户极大改善了身份验证体验。

## <a name="system-services"></a>系统服务

功能 | 说明
 :---- | :----
强力 | 当启用或未启用节点模式时，你的 Windows 桌面应用程序现在都可以收到通知。 通过响应电源条件更改，你的应用程序可以帮助延长电池使用时间。 <br /><br />[GUID_POWER_SAVING_STATUS](/windows/desktop/Power/power-setting-guids)：将此新的 GUID 与 [PowerSettingRegisterNotification](/windows/desktop/api/powersetting/nf-powersetting-powersettingregisternotification) 函数结合使用，以便在节电模式处于启用或未启用状态时收到通知。 <br /><br />[SYSTEM_POWER_STATUS](/windows/desktop/api/winbase/ns-winbase-_system_power_status)：已将此结构更新为支持节电模式。 第四个成员 *SystemStatusFlag* （以前名为 Reserved1），现在用于指示节电模式是否已启用。 使用 [GetSystemPowerStatus](/windows/desktop/api/winbase/nf-winbase-getsystempowerstatus) 函数检索指向此结构的指针。
版本 | 你可以使用 [Version 帮助程序函数](/windows/desktop/SysInfo/version-helper-apis)确定操作系统的版本。 在 Windows 10 中，这些帮助程序函数包括一个新函数 [IsWindows10OrGreater](/windows/desktop/api/versionhelpers/nf-versionhelpers-iswindows10orgreater)。 如果你想要确定系统版本，则应该使用帮助程序函数，而不是使用已弃用的 [GetVersionEx](/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getversionexa) 和 [GetVersion](/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getversion) 函数。 有关如何获取系统版本的详细信息，请参阅[获取系统版本](/windows/desktop/SysInfo/getting-the-system-version)。 <br /><br />如果你使用已弃用的 [GetVersionEx](/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getversionexa) 或 [GetVersion](/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getversion) 函数在 [OSVERSIONINFOEX](/windows/desktop/api/winnt/ns-winnt-_osversioninfoexa) 或 [OSVERSIONINFO](/windows/desktop/api/winnt/ns-winnt-_osversioninfoa) 结构中获取版本信息，请注意，这些结构包含的版本号将从适用于 Windows 8.1 和 Windows Server 2012 R2 的 6.3 版增加到适用于 Windows 10 的 10.0 版。 有关操作系统版本号的详细信息，请参阅[操作系统版本](/windows/desktop/SysInfo/operating-system-version)。 <br /><br />你还需要在你的应用程序中明确定向到 Windows 8.1 或 Windows 10，以使用 [GetVersionEx](/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getversionexa) 或 [GetVersion](/windows/desktop/api/sysinfoapi/nf-sysinfoapi-getversion) 函数获取有关这些版本的正确版本信息。 有关如何针对这些版本的 Windows 定向你的应用程序的信息，请参阅[针对 Windows 定向你的应用程序](/windows/desktop/SysInfo/targeting-your-application-at-windows-8-1)。
用户信息 | [Windows.System](/uwp/api/windows.system) 命名空间中的新 API 使你可以轻松访问用户相关信息，例如其用户名和头像。 它还提供了响应用户事件（如登录和注销）的功能。
内存管理和分析 | 对 [Windows.System](/uwp/api/windows.system) 中内存分析 API 的支持已扩展至所有平台，并且其整体功能已通过新的类和函数进行增强。

## <a name="storage"></a>存储

功能 | 说明
 :---- | :----
适用于 Windows Phone 的文件搜索 API | 作为应用发布者，你可以通过向应用清单添加扩展来注册应用以便与你发布的其他应用共享存储文件夹。 然后，调用 [Windows.Storage.ApplicationData.GetPublisherCacheFolder](/uwp/api/windows.storage.applicationdata.getpublishercachefolder) 方法来获取共享的存储位置。 Windows 运行时应用强大的安全模型通常会防止应用在自身之间共享数据。 但是，它可以帮助来自同一发布者的应用共享基于每个用户的文件和设置。

## <a name="tools"></a>工具

功能 | 说明
 :---- | :----
Visual Studio 中的动态可视化树 | Visual Studio 具有新的动态可视化树功能。 你可以在调试时使用该功能来快速了解应用的可视化树状态，并了解元素属性的设置方式。 该功能还允许你在应用运行时更改属性值，这样你无需重新启动即可进行调整和实验。
跟踪日志记录 | [跟踪日志记录](/windows/desktop/tracelogging/trace-logging-portal)是适用于用户模式应用和内核模式驱动程序的全新事件跟踪 API；它基于 [Windows 事件跟踪](/windows/desktop/ETW/event-tracing-portal) (ETW) 生成。 此 API 提供了一种简化方式来检测代码和在结构数据中包括事件，而无需要求单独的检测清单 XML 文件。 WinRT、.NET 和 C/C++ TraceLogging API 都可服务于不同的开发人员受众。

## <a name="user-experience"></a>用户体验

功能 | 说明
 :---- | :----
语音识别 | 通用 Windows 平台现在支持针对长篇听写方案的连续语音识别。 了解如何在语音交互文档中启用连续听写。
不同应用程序平台之间的拖放功能 | 新 [Windows.ApplicationModel.DataTransfer.DragDrop](/uwp/api/windows.applicationmodel.datatransfer.dragdrop) 命名空间为通用 Windows 应用提供拖放功能。 以前，桌面程序中的常见拖放方案（例如将文档从文件夹拖动到需要附加它的 Outlook 电子邮件中）不适用于通用 Windows 应用。 使用这些新的 API，你的应用可以让用户轻松地在不同的通用 Windows 应用和桌面之间移动数据。 <br /><br />为了支持应用之间的拖放，这些新的 API 已添加到XAML：[ListViewBase.DragItemsCompleted](/uwp/api/windows.ui.xaml.controls.listviewbase.dragitemscompleted)； <br />UIElement：[CanDrag](/uwp/api/windows.ui.xaml.uielement.candrag)、[DragStarting](/uwp/api/windows.ui.xaml.uielement.dragstarting)、[StartDragAsync](/uwp/api/windows.ui.xaml.uielement.startdragasync)、[DropCompleted](/uwp/api/windows.ui.xaml.uielement.dropcompleted)；  <br />[DragOperationDeferral](/uwp/api/windows.ui.xaml.dragoperationdeferral)、[DragUI](/uwp/api/windows.ui.xaml.dragui)、[DragUIOverride](/uwp/api/windows.ui.xaml.draguioverride)； <br />DragEventArgs：[AcceptedOperation](/uwp/api/windows.ui.xaml.drageventargs.acceptedoperation)、[DataView](/uwp/api/windows.ui.xaml.drageventargs.dataview)、[DragUIOverride](/uwp/api/windows.ui.xaml.drageventargs.draguioverride)、[GetDeferral](/uwp/api/windows.ui.xaml.drageventargs.getdeferral)、[Modifiers](/uwp/api/windows.ui.xaml.drageventargs.modifiers)； <br />[DragItemsCompletedEventArgs](/uwp/api/windows.ui.xaml.controls.dragitemscompletedeventargs)、[DropCompletedEventArgs](/uwp/api/windows.ui.xaml.dropcompletedeventargs)、[DragStartingEventArgs](/uwp/api/windows.ui.xaml.dragstartingeventargs)
自定义窗口标题栏 | 对于适用于桌面设备系列的 UWP 应用，现在可以将 [ApplicationViewTitleBar](/uwp/api/windows.ui.viewmanagement.applicationviewtitlebar) 类与 [ApplicationView.TitleBar](/uwp/api/windows.ui.viewmanagement.applicationview.titlebar) 属性和 [Window.SetTitleBar](/uwp/api/windows.ui.viewmanagement.applicationview.titlebar) 方法结合使用，以便将默认 Windows 标题栏内容替换为你自己的自定义 XAML 内容。 你的 XAML 将被视为“系统镶边”，因此 Windows（而不是你的应用）将处理输入事件。 这意味着用户甚至可以在单击你的自定义标题栏内容时拖动窗口和调整窗口的大小。

## <a name="web"></a>Web

功能 | 说明
 :---- | :----
Microsoft Edge | Microsoft Edge 是为 Windows 10 生成的全新默认浏览器。 有关 Microsoft Edge 中包括的开发人员功能和标准的详细信息和概述（包括最新的 JavaScript 功能），请参阅 [Microsoft Edge 开发人员指南](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide)。
WebView 浏览 | [WebView](/uwp/api/windows.ui.xaml.controls.webview) 控件与新的 Microsoft Edge 浏览器使用相同的呈现引擎。 这将提供最准确、最符合标准的 HTML 呈现模式。
线程外 WebView | 若要允许处理和显示单独后台线程上的 Web 内容，可以指定 [WebView.ExecutionMode](/uwp/api/windows.ui.xaml.controls.webview.executionmode)。 在某些特定情况下，这可以提高性能。
WebView.UnsupportedUriSchemeIdentified 事件 | 新 [WebView.UnsupportedUriSchemeIdentified](/uwp/api/windows.ui.xaml.controls.webview.unsupportedurischemeidentified) 事件可用于确定你的应用处理不受支持的 URI 方案的方式。 你可以处理此事件以使你的应用能够针对不受支持的 URI 方案提供自定义处理。 有关 HTML WebView 控件，请参阅 [MSWebViewUnsupportedUriSchemeIdentified](/previous-versions/windows/apps/dn803906(v=win.10)) 事件。
WebView.NewWindowRequested 事件 | 新 [WebView.NewWindowRequested](/uwp/api/windows.ui.xaml.controls.webview.newwindowrequested) 事件使你可以在 WebView 中的脚本请求新浏览器窗口时做出响应。 有关 HTML WebView 控件，请参阅 [MSWebViewNewWindowRequested](/microsoft-edge/webview) 事件。
WebView.PermissionRequested 事件 | 新 [WebView.PermissionRequested](/uwp/api/windows.ui.xaml.controls.webview.permissionrequested) 事件允许 WebView 内容使用丰富的全新 HTML5 API，它们需要获得特殊的用户权限，如地理位置。 有关 HTML WebView 控件，请参阅 [MSWebViewPermissionRequested](/previous-versions/windows/apps/dn806030(v=win.10)) 事件。
WebView.UnviewableContentIdentified 事件 | 新 [WebView.UnviewableContentIdentified](/uwp/api/windows.ui.xaml.controls.webview.unviewablecontentidentified) 事件使你可以在 WebView 导航到 PDF 文件或 Office 文档等非 Web 内容时做出响应。 有关 HTML WebView 控件，请参阅 [MSWebViewUnviewableContentIdentified](/microsoft-edge/webview) 事件。
WebView.AddWebAllowedObject 方法 | 你可以通过调用新 [WebView.AddWebAllowedObject](/uwp/api/windows.ui.xaml.controls.webview.addweballowedobject) 方法，将 WinRT 对象注入 XAML WebView 中，然后通过托管在该 WebView 中的可信 JavaScript 来调用其函数。 例如，Web 内容可以通过请求其父应用调用 [ToastNotificationManager](/uwp/api/windows.ui.notifications.toastnotificationmanager) WinRT API，显示系统通知。 有关 HTML WebView 控件，请参阅 [addWebAllowedObject](/microsoft-edge/webview) 方法。
WebView.ClearTemporaryWebDataAsync 方法 | 当用户与 XAML WebView 中的 Web 内容交互时，WebView 控件将根据该用户的会话缓存数据。 若要清除此缓存，可以调用新的 [ClearTemporaryWebDataAsync ](/uwp/api/windows.ui.xaml.controls.webview.cleartemporarywebdataasync) 方法。 例如，你可以在一位用户退出应用后清除缓存，这样其他用户就无法访问前一会话的任何数据。
