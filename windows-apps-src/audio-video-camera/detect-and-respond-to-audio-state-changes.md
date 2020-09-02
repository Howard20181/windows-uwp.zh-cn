---
ms.assetid: EE0C1B28-EF9C-4BD9-A3C0-BDF11E75C752
description: 本文介绍了 UWP 应用如何检测并响应音频流级别中的系统初始化变化
title: 检测和响应音频的状态变化
ms.date: 04/03/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a997a3c4d46bbd71c6ac4ae0d78b60edd8bd3ca1
ms.sourcegitcommit: c3ca68e87eb06971826087af59adb33e490ce7da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362720"
---
# <a name="detect-and-respond-to-audio-state-changes"></a>检测和响应音频的状态变化
从 Windows 10 版本 1803 开始，应用可检测到系统何时降低或静音应用使用的音频流的音频级别。 可以接收捕获和呈现流、特定音频设备和音频类别或应用用于媒体播放的 [**MediaPlayer**](/uwp/api/Windows.Media.Playback.MediaPlayer) 对象的通知。 例如，当警报响起时，系统可能降低（或者“闪避”）音频播放级别。 如果应用没有在应用清单中声明 *backgroundMediaPlayback* 功能，系统将在应用进入后台时将其静音。 

对于所有受支持的音频流，处理音频状态更改的模式是相同的。 首先，创建 [**AudioStateMonitor**](/uwp/api/windows.media.audio.audiostatemonitor) 类的实例。 在以下示例中，应用使用 [**MediaCapture**](/uwp/api/Windows.Media.Capture.MediaCapture) 类捕获游戏聊天的音频。 调用工厂方法以获取与默认通信设备的游戏聊天音频捕获流相关联的音频状态监视器。  接下来，为 [**SoundLevelChanged**](/uwp/api/windows.media.audio.audiostatemonitor.soundlevelchanged) 事件注册处理程序，这将在系统更改关联流的音频级别时引发。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs" id="SnippetDeviceIdCategoryVars":::

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs" id="SnippetSoundLevelDeviceIdCategory":::

在**SoundLevelChanged**事件处理程序中，检查传递到处理程序的**AudioStateMonitor**发送程序的[**SoundLevel**](/uwp/api/windows.media.audio.audiostatemonitor.soundlevel)属性，以确定该流的新音频级别。 在此示例中，声音级别为静音时应用停止捕获音频，音频级别返回到完整音量时恢复捕获。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs" id="SnippetGameChatSoundLevelChanged":::

有关使用 **MediaCapture** 捕获音频的详细信息，请参阅[使用 MediaCapture 捕获基本的照片、视频和音频](basic-photo-video-and-audio-capture-with-MediaCapture.md)。

[**MediaPlayer**](/uwp/api/Windows.Media.Playback.MediaPlayer) 类的每个实例都具有与它相关联的 **AudioStateMonitor**，可用于检测系统何时更改当前播放的内容的音量级别。 根据正在播放内容的类型，可以决定以不同的方式处理音频状态更改。 例如，可以决定在音频降低时暂停播客的播放，但如果内容是音乐，则继续播放。 

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaPlayer_RS1/cs/MainPage.xaml.cs" id="SnippetAudioStateVars":::

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaPlayer_RS1/cs/MainPage.xaml.cs" id="SnippetSoundLevelChanged":::

有关使用 **MediaPlayer** 的详细信息，请查阅[使用 MediaPlayer 播放音频和视频](play-audio-and-video-with-mediaplayer.md)。 

## <a name="related-topics"></a>相关主题
