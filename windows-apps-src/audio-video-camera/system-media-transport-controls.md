---
ms.assetid: EFCF84D0-2F4C-454D-97DA-249E9EAA806C
description: 通过 SystemMediaTransportControls 类，你的应用可以使用内置于 Windows 的系统媒体传输控件，并更新控件显示的有关你的应用当前播放的媒体的元数据。
title: 手动控制系统媒体传输控件
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0a4163105b934f5c1e2970fab9f51b76d69d1bd8
ms.sourcegitcommit: c95915f8a13736705eab74951a12b2cf528ea612
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876222"
---
# <a name="manual-control-of-the-system-media-transport-controls"></a>手动控制系统媒体传输控件


从 Windows 10 版本 1607 开始，使用 [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer) 类播放媒体的 UWP 应用默认自动与系统媒体传输控件 (SMTC) 集成。 对于大多数方案，均推荐使用此方法与 SMTC 交互。 有关自定义 SMTC 与 **MediaPlayer** 的默认集成的详细信息，请参阅[与系统媒体传输控件集成](integrate-with-systemmediatransportcontrols.md)。

在以下几个方案中，可能需要实现 SMTC 的手动控制。 这些方案包括使用 [**MediaTimelineController**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaTimelineController) 控制一个或多个媒体播放器的播放的情形。 或者使用多个媒体播放器，但仅希望应用拥有一个 SMTC 实例的情形。 如果使用 [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 播放媒体，则必须手动控制 SMTC。

## <a name="set-up-transport-controls"></a>设置传输控件
如果使用 **MediaPlayer** 播放媒体，则通过访问 [**MediaPlayer.SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.systemmediatransportcontrols) 属性可以获取 [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) 类的实例。 如果要手动控制 SMTC，应该通过将 [**CommandManager.IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackcommandmanager.isenabled) 属性设置为 false，禁用 **MediaPlayer** 提供的自动集成。

> [!NOTE] 
> 如果你通过将 [**IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackcommandmanager.isenabled) 设置为 false 禁用 [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer) 的 [**MediaPlaybackCommandManager**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackCommandManager)，它将中断 **MediaPlayer** 和 **MediaPlayerElement** 提供的 [**TransportControls**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement.transportcontrols) 之间的链接，以致内置传输控件不再自动控制播放器的播放。 作为替代方法，你必须实现自己的控件才能控制 **MediaPlayer**。

[!code-cs[InitSMTCMediaPlayer](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetInitSMTCMediaPlayer)]

还可以通过调用 [**GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.getforcurrentview) 获取 [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) 的实例。 如果使用 **MediaElement** 播放媒体，必须使用此方法获取该对象。

[!code-cs[InitSMTCMediaElement](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetInitSMTCMediaElement)]

通过设置 **SystemMediaTransportControls** 对象的相应“已启用”属性（例如 [**IsPlayEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.isplayenabled)、[**IsPauseEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.ispauseenabled)、[**IsNextEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.isnextenabled) 和 [**IsPreviousEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.ispreviousenabled)），启用应用将使用的按钮。 有关可用控件的完整列表，请参阅 **SystemMediaTransportControls** 参考文档。

[!code-cs[EnableContols](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetEnableContols)]

为 [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed) 事件注册处理程序，以在用户按下按钮时接收通知。

[!code-cs[RegisterButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterButtonPressed)]

## <a name="handle-system-media-transport-controls-button-presses"></a>处理系统媒体传输控件按钮按下操作

当按下了所启用的某个按钮时，系统传输控件会引发 [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed) 事件。 传入到事件处理程序中的 [**SystemMediaTransportControlsButtonPressedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsButtonPressedEventArgs) 的 [**Button**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsbuttonpressedeventargs.button) 属性是 [**SystemMediaTransportControlsButton**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsButton) 枚举的成员，用于指示按下了哪个已启用的按钮。

要从 [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed) 事件处理程序更新 UI 线程上的对象（例如 [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 对象），你必须通过 [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) 来封送调用。 这是因为 **ButtonPressed** 事件处理程序不会从 UI 线程中调用，因此如果你尝试直接修改 UI，将会引发异常。

[!code-cs[SystemMediaTransportControlsButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsButtonPressed)]

## <a name="update-the-system-media-transport-controls-with-the-current-media-status"></a>使用当前的媒体状态更新系统媒体传输控件

当媒体的状态发生更改时，应该通知 [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls)，以便系统可以更新控件来反映当前状态。 若要执行此操作，请将 [**PlaybackStatus**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackstatus) 属性设置为 [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 的 [**CurrentStateChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaelement.currentstatechanged) 事件内的适当 [**MediaPlaybackStatus**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaPlaybackStatus) 值，该事件将在媒体状态更改时引发。

[!code-cs[SystemMediaTransportControlsStateChange](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsStateChange)]

## <a name="update-the-system-media-transport-controls-with-media-info-and-thumbnails"></a>使用媒体信息和缩略图更新系统媒体传输控件

使用 [**SystemMediaTransportControlsDisplayUpdater**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsDisplayUpdater) 类来更新传输控件显示的媒体信息，例如当前播放的媒体项的歌曲标题或唱片集画面。 使用 [**SystemMediaTransportControls.DisplayUpdater**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.displayupdater) 属性获取此类的实例。 对于典型方案，传递元数据的建议方法是调用 [**CopyFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.copyfromfileasync)，从而传入当前播放的媒体文件。 显示更新程序将自动从文件中提取元数据和缩略图图像。

调用 [**Update**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.update) 会导致系统媒体传输控件使用新的元数据和缩略图更新其 UI。

[!code-cs[SystemMediaTransportControlsUpdater](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsUpdater)]

如果你的方案需要它，你可以通过设置 [**DisplayUpdater**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.displayupdater) 类公开的 [**MusicProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.musicproperties)、[**ImageProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.imageproperties) 或 [**VideoProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.videoproperties) 对象的值，手动更新由系统媒体传输控件显示的元数据。

[!code-cs[SystemMediaTransportControlsUpdaterManual](./code/SMTCWin10/cs/MainPage.xaml.cs#SystemMediaTransportControlsUpdaterManual)]

> [!Note]
> 即使应用程序未提供由系统[媒体传输控件](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.type#Windows_Media_SystemMediaTransportControlsDisplayUpdater_Type
)显示的其他媒体元数据，也应为 SystemMediaTransportControlsDisplayUpdater 属性设置一个值。 此值有助于系统正确处理媒体内容，包括阻止屏幕保护程序在播放期间激活。


## <a name="update-the-system-media-transport-controls-timeline-properties"></a>更新系统媒体传输控件时间线属性

系统传输控件显示有关当前播放的媒体项时间线的信息，包括媒体项的当前播放位置、开始时间和结束时间。 若要更新系统传输控件时间线属性，请创建一个新的 [**SystemMediaTransportControlsTimelineProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsTimelineProperties) 对象。 设置对象的属性以反映正在播放的媒体项的当前状态。 调用 [**SystemMediaTransportControls.UpdateTimelineProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.updatetimelineproperties) 会导致控件更新时间线。

[!code-cs[UpdateTimelineProperties](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetUpdateTimelineProperties)]

-   必须提供 [**StartTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.starttime)、[**EndTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.endtime) 和 [**Position**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackpositionchangerequested) 的值，才能使系统控件显示正在播放的项的时间线。

-   [**MinSeekTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.minseektime)和[**MaxSeekTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.maxseektime)允许指定时间线内用户可查找的范围。 此操作的典型方案是允许内容提供商在其媒体中包含广告中断。

    你必须设置 [**MinSeekTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.minseektime) 和 [**MaxSeekTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.maxseektime)，才能引发 [**PositionChangeRequest**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackpositionchangerequested)。

-   建议你通过在播放期间每隔大约 5 秒更新这些属性，然后在播放状态发生更改（例如暂停或查找一个新位置）时再次执行此操作，使系统控件和媒体播放保持同步。

## <a name="respond-to-player-property-changes"></a>响应播放器属性更改

还有一组与媒体播放器本身（而不是正在播放的媒体项的状态）相关的系统传输控件属性。 其中每个属性都与用户调整关联的控件时引发的事件相匹配。 这些属性和事件包括：

| 属性                                                                  | Event                                                                                                   |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [**AutoRepeatMode**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.autorepeatmode) | [**AutoRepeatModeChangeRequested**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.autorepeatmodechangerequested) |
| [**PlaybackRate**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackrate)     | [**PlaybackRateChangeRequested**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackratechangerequested)     |
| [**ShuffleEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.shuffleenabled) | [**ShuffleEnabledChangeRequested**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.shuffleenabledchangerequested) |

 
若要处理用户与其中一个控件的交互，请先为关联的事件注册一个处理程序。

[!code-cs[RegisterPlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterPlaybackChangedHandler)]

在该事件的处理程序中，首先确保请求的值在有效的预期范围内。 如果在此范围内，则在 [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 上设置相应的属性，然后在 [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) 对象上设置相应的属性。

[!code-cs[PlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetPlaybackChangedHandler)]

-   为了引发其中一个播放器属性事件，必须为该属性设置初始值。 例如，直到你为 [**PlaybackRate**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackrate) 属性设置了某个值至少一次后，才会引发 [**PlaybackRateChangeRequested**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackratechangerequested)。

## <a name="use-the-system-media-transport-controls-for-background-audio"></a>将系统媒体传输控件用于后台音频

如果没有使用 **MediaPlayer** 提供的自动 SMTC 集成，必须手动与 SMTC 集成才能启用后台音频。 应用至少必须能通过将 [**IsPlayEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.isplayenabled) 和 [**IsPauseEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.ispauseenabled) 设置为 true 来启用播放和暂停按钮。 你的应用还必须处理 [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed) 事件。 如果应用不符合这些要求，当应用移至后台时，音频播放将停止。

对于将新的单进程模型用于后台音频的应用，应通过调用 [**GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.getforcurrentview) 获取 [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) 的实例。 对于将传统的双进程模型用于后台音频的应用，必须使用 [**BackgroundMediaPlayer.Current.SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.systemmediatransportcontrols) 从后台进程访问 SMTC。

有关在后台播放音频的详细信息，请参阅[在后台播放媒体](background-audio.md)。

## <a name="related-topics"></a>相关主题
* [媒体播放](media-playback.md)
* [与系统媒体传输控件集成](integrate-with-systemmediatransportcontrols.md) 
* [系统 Media Tranport 示例](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/SystemMediaTransportControls) 

 




