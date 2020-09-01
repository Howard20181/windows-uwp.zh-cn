---
ms.assetid: 40B97E0C-EB1B-40C2-A022-1AB95DFB085E
description: 本文向你演示了如何将媒体从通用 Windows 应用转换到远程设备。
title: 媒体转换
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e1f9eae3934116df8c667291c2d98ba6bd851a97
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89163911"
---
# <a name="media-casting"></a>媒体转换



本文向你演示了如何将媒体从通用 Windows 应用转换到远程设备。

## <a name="built-in-media-casting-with-mediaplayerelement"></a>通过 MediaPlayerElement 的内置媒体转换

从通用 Windows 应用转换媒体的最简单方法是使用 [**MediaPlayerElement**](/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement) 控件的内置转换功能。

若要允许用户打开一个要在 **MediaPlayerElement** 控件中播放的视频文件，请将以下命名空间添加到你的项目。

[!code-cs[BuiltInCastingUsing](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetBuiltInCastingUsing)]

在应用的 XAML 文件中，添加 **MediaPlayerElement** 并将 [**AreTransportControlsEnabled**](/uwp/api/windows.ui.xaml.controls.mediaelement.aretransportcontrolsenabled) 设置为 true。

[!code-xml[MediaElement](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetMediaElement)]

添加按钮以让用户启动选取文件操作。

[!code-xml[OpenButton](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetOpenButton)]

在按钮的 [**Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) 事件处理程序中，创建 [**FileOpenPicker**](/uwp/api/Windows.Storage.Pickers.FileOpenPicker) 的新实例、将视频文件类型添加到 [**FileTypeFilter**](/uwp/api/windows.storage.pickers.fileopenpicker.filetypefilter) 集合，并将起始位置设置为用户的视频库。

调用 [**PickSingleFileAsync**](/uwp/api/windows.storage.pickers.fileopenpicker.picksinglefileasync) 以启动“文件选取器”对话框。 当此方法返回时，结果是表示视频文件的 [**StorageFile**](/uwp/api/Windows.Storage.StorageFile) 对象。 检查以确保文件不是 null，如果用户取消选取操作，该文件将是 null。 调用文件的 [**OpenAsync**](/uwp/api/windows.storage.storagefile.openasync) 方法来为该文件获取一个 [**IRandomAccessStream**](/uwp/api/Windows.Storage.Streams.IRandomAccessStream)。 最后，通过调用 [**CreateFromStorageFile**](/uwp/api/windows.media.core.mediasource.createfromstoragefile) 从选定文件创建新的 **MediaSource** 对象，并将其分配给 **MediaPlayerElement** 对象的 [**Source**](/uwp/api/windows.ui.xaml.controls.mediaplayerelement.source) 属性以使视频文件成为控件的视频源。

[!code-cs[OpenButtonClick](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetOpenButtonClick)]

在 **MediaPlayerElement** 中加载视频后，用户只需按传输控件上的转换按钮即可启动内置对话框，他们可以在该对话框中选择已加载媒体将转换到的设备。

![MediaElement 转换按钮](images/media-element-casting-button.png)

> [!NOTE] 
> 自 Windows 10 版本 1607 起，建议使用 **MediaPlayer** 类播放媒体项。 **MediaPlayerElement** 是一种轻型 XAML 控件，用于在 XAML 页面中呈现 **MediaPlayer** 的内容。 为了实现向后兼容性，继续支持 **MediaElement** 控件。 有关使用 **MediaPlayer** 和 **MediaPlayerElement** 播放媒体内容的详细信息，请参阅[使用 MediaPlayer 播放音频和视频](play-audio-and-video-with-mediaplayer.md)。 有关使用 **MediaSource** 和相关 API 处理媒体内容的信息，请参阅[媒体项、播放列表和轨](media-playback-with-mediasource.md)。

## <a name="media-casting-with-the-castingdevicepicker"></a>通过 CastingDevicePicker 的媒体转换

将媒体转换到设备的第二个方法是使用 [**CastingDevicePicker**](/uwp/api/Windows.Media.Casting.CastingDevicePicker)。 若要使用此类型，请在项目中包括 [**Windows.Media.Casting**](/uwp/api/Windows.Media.Casting) 命名空间。

[!code-cs[CastingNamespace](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetCastingNamespace)]

声明 **CastingDevicePicker** 对象的成员变量。

[!code-cs[DeclareCastingPicker](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetDeclareCastingPicker)]

初始化你的页面时，创建转换选取器的新实例并将 [**Filter**](/uwp/api/windows.media.casting.castingdevicepicker.filter) 设置为 [**SupportsVideo**](/uwp/api/Windows.Media.Casting.CastingDevicePickerFilter) 属性，以指示由选取器列出的转换设备应支持视频。 为 [**CastingDeviceSelected**](/uwp/api/windows.media.casting.castingdevicepicker.castingdeviceselected) 事件注册处理程序，它将在用户选取某台设备进行转换时引发。

[!code-cs[InitCastingPicker](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetInitCastingPicker)]

在你的 XAML 文件中，添加按钮以允许用户启动选取器。

[!code-xml[CastPickerButton](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetCastPickerButton)]

在按钮的 **Click** 事件处理程序中，调用 [**TransformToVisual**](/uwp/api/windows.ui.xaml.uielement.transformtovisual) 以获取相对于另一个元素的 UI 元素转换。 在此示例中，转换是相对于应用程序窗口的可视根的转换选取器按钮的位置。 调用 [**CastingDevicePicker**](/uwp/api/Windows.Media.Casting.CastingDevicePicker) 对象的 [**Show**](/uwp/api/windows.media.casting.castingdevicepicker.show) 方法，以启动“转换选取器”对话框。 指定转换选取器按钮的位置和尺寸，以便系统可以使对话框在用户按下该按钮时弹出。

[!code-cs[CastPickerButtonClick](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetCastPickerButtonClick)]

在 **CastingDeviceSelected** 事件处理程序中，调用事件参数的 [**SelectedCastingDevice**](/uwp/api/windows.media.casting.castingdeviceselectedeventargs.selectedcastingdevice) 属性的 [**CreateCastingConnection**](/uwp/api/windows.media.casting.castingdevice.createcastingconnection) 方法，该方法表示用户所选的转换设备。 为 [**ErrorOccurred**](/uwp/api/windows.media.casting.castingconnection.erroroccurred) 和 [**StateChanged**](/uwp/api/windows.media.casting.castingconnection.statechanged) 事件注册处理程序。 最后，调用 [**RequestStartCastingAsync**](/uwp/api/windows.media.casting.castingconnection.requeststartcastingasync) 以开始转换，从而将结果传入 **MediaPlayerElement** 控件的 **MediaPlayer** 对象的 [**GetAsCastingSource**](/uwp/api/windows.ui.xaml.controls.mediaelement.getascastingsource) 方法，以便指定要转换的媒体是与 **MediaPlayerElement** 关联的 **MediaPlayer** 的内容。

> [!NOTE] 
> 必须在 UI 线程上启动转换连接。 由于未在 UI 线程上调用 **CastingDeviceSelected**，因此你必须将这些调用放置在对 [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync)（可使这些调用在 UI 线程上调用）的调用内。

[!code-cs[CastingDeviceSelected](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetCastingDeviceSelected)]

在 **ErrorOccurred** 和 **StateChanged** 事件处理程序中，应更新 UI 以通知用户当前转换状态。 在有关创建自定义转换设备选取器的下一节中详细讨论了这些事件。

[!code-cs[EmptyStateHandlers](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetEmptyStateHandlers)]

## <a name="media-casting-with-a-custom-device-picker"></a>通过自定义设备选取器的媒体转换

下一节介绍了如何通过从你的代码枚举转换设备和启动连接，创建你自己的转换设备选取器 UI。

若要枚举可用的转换设备，请在你的项目中包括 [**Windows.Devices.Enumeration**](/uwp/api/Windows.Devices.Enumeration) 命名空间。

[!code-cs[EnumerationNamespace](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetEnumerationNamespace)]

将以下控件添加到 XAML 页面，以实现此示例的基本 UI：

-   用于启动查找可用转换设备的设备观察程序的按钮。
-   用于向正在运行转换枚举的用户提供反馈的 [**ProgressRing**](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) 控件。
-   用于列出发现的转换设备的 [**ListBox**](/uwp/api/Windows.UI.Xaml.Controls.ListBox)。 为控件定义 [**ItemTemplate**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate)，以便我们可以将转换设备对象直接分配给该控件，并仍然显示 [**FriendlyName**](/uwp/api/windows.media.casting.castingdevice.friendlyname) 属性。
-   允许用户断开转换设备连接的按钮。

[!code-xml[CustomPickerXAML](./code/MediaCasting_RS1/cs/MainPage.xaml#SnippetCustomPickerXAML)]

在代码隐藏中，声明 [**DeviceWatcher**](/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) 和 [**CastingConnection**](/uwp/api/Windows.Media.Casting.CastingConnection) 的成员变量。

[!code-cs[DeclareDeviceWatcher](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetDeclareDeviceWatcher)]

在 *startWatcherButton* 的 **Click** 处理程序中，首先通过禁用该按钮，并在正在运行设备枚举时激活进度环来更新 UI。 清除转换设备的列表框。

接下来，通过调用 [**DeviceInformation.CreateWatcher**](/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher) 创建设备观察程序。 此方法可用于观察许多不同类型的设备。 指明你想要通过使用 [**CastingDevice.GetDeviceSelector**](/uwp/api/windows.media.casting.castingdevice.getdeviceselector) 返回的设备选择器字符串，观察支持视频转换的设备。

最后，为 [**Added**](/uwp/api/windows.devices.enumeration.devicewatcher.added)、[**Removed**](/uwp/api/windows.devices.enumeration.devicewatcher.removed)、[**EnumerationCompleted**](/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted) 和 [**Stopped**](/uwp/api/windows.devices.enumeration.devicewatcher.stopped) 事件注册事件处理程序。

[!code-cs[StartWatcherButtonClick](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetStartWatcherButtonClick)]

当观察程序发现新设备时，将引发 **Added** 事件。 在此事件的处理程序中，通过调用 [**CastingDevice.FromIdAsync**](/uwp/api/windows.media.casting.castingdevice.fromidasync) 并传入发现的转换设备的 ID 来创建新的 [**CastingDevice**](/uwp/api/Windows.Media.Casting.CastingDevice) 对象，它包含在已传入到处理程序中的 **DeviceInformation** 对象中。

将 **CastingDevice** 添加到转换设备 **ListBox**，以便用户可以选择它。 由于在 XAML 中定义的 [**ItemTemplate**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate)，[**FriendlyName**](/uwp/api/windows.media.casting.castingdevice.friendlyname) 属性将用作列表框中的项文本。 因为未在 UI 线程上调用此事件处理程序，因此你必须在对 [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) 的调用内更新 UI。

[!code-cs[WatcherAdded](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherAdded)]

当观察程序检测到转换设备不再存在时，将引发 **Removed** 事件。 比较已传入到处理程序中 **Added** 对象的 ID 属性与列表框的 [**Items**](/uwp/api/windows.ui.xaml.controls.itemscontrol.items) 集合中每个 **Added** 的 ID。 如果 ID 匹配，则从集合中删除该对象。 同样，因为 UI 已得到更新，因此必须从 **RunAsync** 调用内进行此调用。

[!code-cs[WatcherRemoved](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherRemoved)]

当观察程序完成设备检测时，将引发 **EnumerationCompleted** 事件。 在此事件的处理程序中，更新 UI 以让用户知道设备枚举已完成，并通过调用 [**Stop**](/uwp/api/windows.devices.enumeration.devicewatcher.stop) 停止设备观察程序。

[!code-cs[WatcherEnumerationCompleted](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherEnumerationCompleted)]

当设备观察程序已停止时，应引发 Stopped 事件。 在此事件的处理程序中，停止 [**ProgressRing**](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) 控件并重新启用 *startWatcherButton*，以便用户可以重新启动设备枚举过程。

[!code-cs[WatcherStopped](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetWatcherStopped)]

当用户从列表框中选择转换设备之一时，将引发 [**SelectionChanged**](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) 事件。 它位于将创建转换连接并且将启动转换的这一处理程序中。

首先，请确保设备观察程序已停止，以便设备枚举不会干扰媒体转换。 通过在用户所选的 **CastingDevice** 对象上调用 [**CreateCastingConnection**](/uwp/api/windows.media.casting.castingdevice.createcastingconnection) 来创建转换连接。 为 [**StateChanged**](/uwp/api/windows.media.casting.castingconnection.statechanged) 和 [**ErrorOccurred**](/uwp/api/windows.media.casting.castingconnection.erroroccurred) 事件添加事件处理程序。

通过调用 [**RequestStartCastingAsync**](/uwp/api/windows.media.casting.castingconnection.requeststartcastingasync) 启动媒体转换，从而传入通过调用 **MediaPlayer** 方法 [**GetAsCastingSource**](/uwp/api/windows.ui.xaml.controls.mediaelement.getascastingsource) 返回的转换源。 最后，使断开连接按钮可见，以允许用户停止媒体转换。

[!code-cs[SelectionChanged](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetSelectionChanged)]

在状态发生更改的处理程序中，要采取的操作取决于转换连接的新状态：

-   如果状态为 **Connected** 或 **Rendering**，确保 **ProgressRing** 控件处于非活动状态，并且断开连接按钮可见。
-   如果状态为 **Disconnected**，取消选择列表框中的当前转换设备、使 **ProgressRing** 控件处于非活动状态，并隐藏断开连接按钮。
-   如果状态为 **Connecting**，使 **ProgressRing** 控件处于活动状态，并隐藏断开连接按钮。
-   如果状态为 **Disconnecting**，使 **ProgressRing** 控件处于活动状态，并隐藏断开连接按钮。

[!code-cs[StateChanged](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetStateChanged)]

在 **ErrorOccurred** 事件的处理程序中，更新你的 UI 以让用户知道发生了转换错误，并取消选择列表框中的当前 **CastingDevice** 对象。

[!code-cs[ErrorOccurred](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetErrorOccurred)]

最后，实现断开连接按钮的处理程序。 通过调用 **CastingConnection** 对象的 [**DisconnectAsync**](/uwp/api/windows.media.casting.castingconnection.disconnectasync) 方法，停止媒体转换并从转换设备断开连接。 此调用必须已通过调用 [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) 调度到 UI 线程。

[!code-cs[DisconnectButton](./code/MediaCasting_RS1/cs/MainPage.xaml.cs#SnippetDisconnectButton)]

 

 