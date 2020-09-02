---
ms.assetid: 0309c7a1-8e4c-4326-813a-cbd9f8b8300d
description: 本文介绍了如何创建、计划和管理媒体播放应用的媒体中断。
title: 创建、计划和管理媒体中断
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4c3d9743b3c82b935029eeb0ee4c5b9347251fc2
ms.sourcegitcommit: c3ca68e87eb06971826087af59adb33e490ce7da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89363970"
---
# <a name="create-schedule-and-manage-media-breaks"></a>创建、计划和管理媒体中断

本文介绍了如何创建、计划和管理媒体播放应用的媒体中断。 媒体中断通常用于在媒体内容中插入音频或视频广告。 从 Windows 10 版本 1607 开始，可使用 [**MediaBreakManager**](/uwp/api/Windows.Media.Playback.MediaBreakManager) 类快速并简单地将媒体中断添加到使用 [**MediaPlayer**](/uwp/api/Windows.Media.Playback.MediaPlayer) 播放的任何 [**MediaPlaybackItem**](/uwp/api/Windows.Media.Playback.MediaPlaybackItem)。


计划一个或多个媒体中断后，系统将在播放期间的指定时间自动播放你的媒体内容。 **MediaBreakManager** 提供事件，以便应用可在媒体中断开始、结束或用户跳过时做出反应。 你还可以访问媒体中断的 [**MediaPlaybackSession**](/uwp/api/Windows.Media.Playback.MediaPlaybackSession)，以监视诸如下载和缓冲进度更新等事件。

## <a name="schedule-media-breaks"></a>计划媒体中断
每个 **MediaPlaybackItem** 对象均有其自己的 [**MediaBreakSchedule**](/uwp/api/Windows.Media.Playback.MediaBreakSchedule)，可用于配置在项目播放时将播放的媒体中断。 在应用中使用媒体中断的第一步是为主播放内容创建 [**MediaPlaybackItem**](/uwp/api/Windows.Media.Playback.MediaPlaybackItem)。 

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetMoviePlaybackItem":::

有关使用 **MediaPlaybackItem**、[**MediaPlaybackList**](/uwp/api/Windows.Media.Playback.MediaPlaybackList) 和其他基本媒体播放 API 的详细信息，请参阅[媒体项、播放列表和曲目](media-playback-with-mediasource.md)。

以下示例展示了如何将预播放中断添加到 **MediaPlaybackItem**，这意味着系统在播放包含媒体中断的播放项目前会播放该中断。 首先，将实例化新 [**MediaBreak**](/uwp/api/Windows.Media.Playback.MediaBreak) 对象。 在此示例中，使用 [**MediaBreakInsertionMethod.Interrupt**](/uwp/api/Windows.Media.Playback.MediaBreakInsertionMethod) 调用构造函数，这意味着在播放中断内容的同时，主内容将暂停。 

接下来，为在中断时要播放的内容（例如广告）创建 **MediaPlaybackItem**。 此播放项目的 [**CanSkip**](/uwp/api/windows.media.playback.mediaplaybackitem.canskip) 属性设置为 false。 这意味着用户将无法使用内置的媒体控件跳过该项目。 但应用仍可选择以编程方式调用 [**SkipCurrentBreak**](/uwp/api/windows.media.playback.mediabreakmanager.skipcurrentbreak) 来跳过广告。 

媒体中断的 [**PlaybackList**](/uwp/api/windows.media.playback.mediabreak.playbacklist) 属性是允许你以播放列表的形式播放多个媒体项目的 [**MediaPlaybackList**](/uwp/api/Windows.Media.Playback.MediaPlaybackList)。 从列表的**项目**集合中添加一个或多个 **MediaPlaybackItem** 对象，以将它们包含在媒体中断的播放列表中。

最后，使用主内容播放项目的 [**BreakSchedule**](/uwp/api/windows.media.playback.mediaplaybackitem.breakschedule) 属性计划媒体中断。 通过将该中断分配到计划对象的 [**PrerollBreak**](/uwp/api/windows.media.playback.mediabreakschedule.prerollbreak) 属性，指定该中断为预播放中断。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetPreRollBreak":::

此时，你可以播放主媒体项目，所创建的媒体中断也将在主内容之前播放。 创建新 [**MediaPlayer**](/uwp/api/Windows.Media.Playback.MediaPlayer) 对象，并有选择性地将 [**AutoPlay**](/uwp/api/windows.media.playback.mediaplayer.autoplay) 属性设置为 true，以开始自动播放内容。 将 **MediaPlayer** 的 [**Source**](/uwp/api/windows.media.playback.mediaplayer.source) 属性设置为主媒体播放项目。 此操作不是必需的，但可以将 **MediaPlayer** 分配到 [**MediaPlayerElement**](/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement) 以在 XAML 页面中呈现媒体。 有关使用 **MediaPlayer** 的详细信息，请查阅[使用 MediaPlayer 播放音频和视频](play-audio-and-video-with-mediaplayer.md)。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetPlay":::

使用与预播放中断相同的技术（但无需将 **MediaBreak** 对象分配到 [**PostrollBreak**](/uwp/api/windows.media.playback.mediabreakschedule.postrollbreak) 属性），添加在包含主内容的 **MediaPlaybackItem** 完成播放后再添加播放后中断。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetPostRollBreak":::

你还可以计划一个或多个在播放主内容时的指定时间播放的播放间隙中断。 在以下示例中，使用接受 **TimeSpan** 对象的构造函数重载创建 [**MediaBreak**](/uwp/api/Windows.Media.Playback.MediaBreak)，这可指定播放主媒体项目时播放中断的时间。 同样，指定 [**MediaBreakInsertionMethod.Interrupt**](/uwp/api/Windows.Media.Playback.MediaBreakInsertionMethod) 以指示播放中断时暂停播放主内容。 通过调用 [**InsertMidrollBreak**](/uwp/api/windows.media.playback.mediabreakschedule.insertmidrollbreak) 将播放间隙中断添加到计划。 通过访问 [**MidrollBreaks**](/uwp/api/windows.media.playback.mediabreakschedule.midrollbreaks) 属性获取计划中当前播放间隙中断的只读列表。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetMidrollBreak":::

显示的下一个播放间隙中断示例使用 [**MediaBreakInsertionMethod.Replace**](/uwp/api/Windows.Media.Playback.MediaBreakInsertionMethod) 插入方法，这意味着系统将在播放中断时继续处理主内容。 此选项通常由动实时流媒体应用使用，在此应用中，你不希望在播放广告时暂停内容，也不希望其落后于实时流。 

此示例也使用可接受两个 [**TimeSpan**](/uwp/api/Windows.Foundation.TimeSpan) 参数的 [**MediaPlaybackItem**](/uwp/api/Windows.Media.Playback.MediaPlaybackItem) 构造函数的重载。 第一个参数指定媒体中断项目中的起始点，播放从此处开始。 第二个参数指定媒体中断项目播放的持续时间。 因此，在以下示例中，**MediaBreak** 将在主内容 20 分钟处开始播放。 播放它时，媒体项目将在中断媒体项目开始 30 秒后再开始，并在主媒体内容恢复播放前 15 秒播放。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetMidrollBreak2":::

## <a name="skip-media-breaks"></a>跳过媒体中断
如前文所述，可设置 **MediaPlaybackItem** 的 [**CanSkip**](/uwp/api/windows.media.playback.mediaplaybackitem.canskip) 属性以防止用户使用内置控件跳过内容。 但可以随时从代码中调用 [**SkipCurrentBreak**](/uwp/api/windows.media.playback.mediabreakmanager.skipcurrentbreak) 以跳过当前中断。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetSkipButtonClick":::

## <a name="handle-mediabreak-events"></a>处理 MediaBreak 事件

为了根据媒体中断的状态变化采取行动，可注册与媒体中断相关的几个事件。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetRegisterMediaBreakEvents":::

开始媒体中断时会引发 [**BreakStarted**](/uwp/api/windows.media.playback.mediabreakmanager.breakstarted)。 你可能希望更新 UI 以让用户知道正在播放媒体中断内容。 此示例使用传递到处理程序的 [**MediaBreakStartedEventArgs**](/uwp/api/Windows.Media.Playback.MediaBreakStartedEventArgs) 获取对已开始的媒体中断的引用。 然后使用 [**CurrentItemIndex**](/uwp/api/windows.media.playback.mediaplaybacklist.currentitemindex) 属性确定此时在播放媒体中断播放列表内的哪个媒体项。 然后更新 UI，向用户显示中断中剩余的当前广告索引和广告数。 请记住，必须在 UI 线程上更新 UI，因此应该在 [**RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) 调用中进行调用。 

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetBreakStarted":::

在中断中的所有媒体项完成播放或已跳过时，将引发 [**BreakEnded**](/uwp/api/windows.media.playback.mediabreakmanager.breakended)。 可以使用此事件的处理程序更新 UI，以指示不再播放媒体中断内容。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetBreakEnded":::

当用户在播放 [**CanSkip**](/uwp/api/windows.media.playback.mediaplaybackitem.canskip) 为 true 的项目时按内置 UI 中的“下一个”** 按钮，或通过调用 [**SkipCurrentBreak**](/uwp/api/windows.media.playback.mediabreakmanager.skipcurrentbreak) 跳过代码中的中断时，将引发 **BreakSkipped** 事件。

以下示例使用 **MediaPlayer** 的 [**Source**](/uwp/api/windows.media.playback.mediaplayer.source) 属性获取对主内容的媒体项的引用。 跳过的媒体中断属于此项目的中断计划。 接下来，代码会查看跳过的媒体中断是否与计划中设置为 [**PrerollBreak**](/uwp/api/windows.media.playback.mediabreakschedule.prerollbreak) 属性的媒体中断相同。 如果相同，这意味着预播放中断是已跳过的中断，而在此情况下，将创建新的播放间隙中断，并计划在主内容播放 10 分钟后再播放。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetBreakSkipped":::

当主媒体项的播放位置超过一个或多个媒体中断的计划时间时，将引发 [**BreaksSeekedOver**](/uwp/api/windows.media.playback.mediabreakmanager.breaksseekedover)。 以下示例查看是否找到多个媒体中断、播放位置是否前移，以及前移时间是否少于 10 分钟。 如果是，通过调用 **MediaPlayer.BreakManager** 的 [**PlayBreak**](/uwp/api/windows.media.playback.mediabreakmanager.playbreak) 方法，将立即播放找到的第一个中断（从由事件参数公开的 [**SeekedOverBreaks**](/uwp/api/windows.media.playback.mediabreakseekedovereventargs.seekedoverbreaks) 集合中获取）。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetBreakSeekedOver":::


## <a name="access-the-current-playback-session"></a>访问当前播放会话
[**MediaPlaybackSession**](/uwp/api/Windows.Media.Playback.MediaPlaybackSession) 对象使用 **MediaPlayer** 类提供与当前播放的媒体内容相关的数据和事件。 [**MediaBreakManager**](/uwp/api/Windows.Media.Playback.MediaBreakManager) 还具有 **MediaPlaybackSession**，可访问该类以获取仅与播放的媒体中断内容相关的数据和事件。 可从播放会话中获取的信息包括当前播放状态（正在播放或已暂停），以及内容当前播放到哪里。 如果媒体中断内容的纵横比与你的主内容的纵横比不同，可使用 [**NaturalVideoWidth**](/uwp/api/windows.media.playback.mediaplaybacksession.naturalvideowidth) 和 [**NaturalVideoHeight**](/uwp/api/windows.media.playback.mediaplaybacksession.naturalvideoheight) 属性以及 [**NaturalVideoSizeChanged**](/uwp/api/windows.media.playback.mediaplaybacksession.naturalvideosizechanged) 调整视频 UI。 还可以接收可提供关于应用性能的有价值遥测的事件，例如 [**BufferingStarted**](/uwp/api/windows.media.playback.mediaplaybacksession.bufferingstarted)、[**BufferingEnded**](/uwp/api/windows.media.playback.mediaplaybacksession.bufferingended) 和 [**DownloadProgressChanged**](/uwp/api/windows.media.playback.mediaplaybacksession.downloadprogresschanged)。

以下示例为 **BufferingProgressChanged 事件**注册处理程序；在事件处理程序中，它会更新 UI 以显示当前缓冲进度。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetRegisterBufferingProgressChanged":::

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaBreaks_RS1/cs/MainPage.xaml.cs" id="SnippetBufferingProgressChanged":::

## <a name="related-topics"></a>相关主题
* [媒体播放](media-playback.md)
* [使用 MediaPlayer 播放音频和视频](play-audio-and-video-with-mediaplayer.md)
* [手动控制系统媒体传输控件](system-media-transport-controls.md)

 

 
