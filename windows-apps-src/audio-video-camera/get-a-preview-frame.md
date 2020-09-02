---
ms.assetid: 05E418B4-5A62-42BD-BF66-A0762216D033
description: 本主题展示如何从媒体捕获预览流获取单个预览帧。
title: 获取预览帧
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1a688ade1e8907cb0de0683df0751d1eebef4ed7
ms.sourcegitcommit: c3ca68e87eb06971826087af59adb33e490ce7da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89362620"
---
# <a name="get-a-preview-frame"></a>获取预览帧


本主题展示如何从媒体捕获预览流获取单个预览帧。

> [!NOTE] 
> 本文以[使用 MediaCapture 捕获基本的照片、视频和音频](basic-photo-video-and-audio-capture-with-MediaCapture.md)中讨论的概念和代码为基础，该文章介绍了实现基本照片和视频捕获的步骤。 我们建议你先熟悉该文中的基本媒体捕获模式，然后再转到更高级的捕获方案。 本文中的代码假设你的应用已有一个正确完成初始化的 MediaCapture 的实例，并且你有一个带有活动视频预览流的 [**CaptureElement**](/uwp/api/Windows.UI.Xaml.Controls.CaptureElement)。

除了基本媒体捕获所需的命名空间，捕获预览帧还需要以下命名空间。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/BasicMediaCaptureWin10/cs/MainPage.xaml.cs" id="SnippetPreviewFrameUsing":::

请求预览帧时，你可以通过使用你想要的格式创建 [**VideoFrame**](/uwp/api/Windows.Media.VideoFrame) 对象来指定你希望用于接收该帧的格式。 此示例通过调用 [**VideoDeviceController.GetMediaStreamProperties**](/uwp/api/windows.media.devices.videodevicecontroller.getmediastreamproperties) 并指定 [**MediaStreamType.VideoPreview**](/uwp/api/Windows.Media.Capture.MediaStreamType) 以请求该预览流的属性来创建与该预览流相同分辨率的视频帧。 该预览流的宽度和高度用于创建新的视频帧。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/BasicMediaCaptureWin10/cs/MainPage.xaml.cs" id="SnippetCreateFormatFrame":::

如果已初始化 [**MediaCapture**](/uwp/api/Windows.Media.Capture.MediaCapture) 对象，并且有一个活动的预览流，请调用 [**GetPreviewFrameAsync**](/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync) 以获取预览流。 传入最后一步中创建的视频帧以指定返回帧的格式。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/BasicMediaCaptureWin10/cs/MainPage.xaml.cs" id="SnippetGetPreviewFrameAsync":::

通过访问 [**VideoFrame**](/uwp/api/Windows.Media.VideoFrame) 对象的 [**SoftwareBitmap**](/uwp/api/windows.media.videoframe.softwarebitmap) 属性，获取该预览帧的 [**SoftwareBitmap**](/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) 表示形式。 有关保存、加载和修改软件位图的信息，请参阅[图像处理](imaging.md)。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/BasicMediaCaptureWin10/cs/MainPage.xaml.cs" id="SnippetGetPreviewBitmap":::

如果需要通过 Direct3D API 使用图像，也可以获取该预览帧的 [**IDirect3DSurface**](/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface) 表示形式。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/BasicMediaCaptureWin10/cs/MainPage.xaml.cs" id="SnippetGetPreviewSurface":::

> [!IMPORTANT]
> 返回的 **VideoFrame** 的 [**SoftwareBitmap**](/uwp/api/windows.media.videoframe.softwarebitmap) 属性或 [**Direct3DSurface**](/uwp/api/windows.media.videoframe.direct3dsurface) 属性可能为 null，具体取决于你调用 **GetPreviewFrameAsync** 的方式，也取决于运行你的应用的设备。

> - 如果你调用接受 **VideoFrame** 参数的 [**GetPreviewFrameAsync**](/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync) 的重载，则返回的 **VideoFrame** 将含有非 null 的 **SoftwareBitmap**，并且 **Direct3DSurface** 属性将为 null。
> - 如果你在使用 Direct3D 图面来内部表示帧的设备上调用不带任何参数的 [**GetPreviewFrameAsync**](/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync) 的重载，则 **Direct3DSurface** 属性将为非 null，并且 **SoftwareBitmap** 属性将为 null。
> - 如果你在不使用 Direct3D 图面以内部表示帧的设备上调用不带任何参数的 [**GetPreviewFrameAsync**](/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync) 的重载，则 **SoftwareBitmap** 属性将为非 null 且 **Direct3DSurface** 属性将为 null。

你的应用在对由 **SoftwareBitmap** 或 **Direct3DSurface** 属性返回的对象尝试操作之前，应始终检查 null 值。

当你使用该预览帧完成操作时，务必调用其 [**Close**](/uwp/api/windows.media.videoframe.close) 方法（映射到 C# 中的释放）以释放该帧使用的资源。 或者，使用自动释放对象的 **using** 模式。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/BasicMediaCaptureWin10/cs/MainPage.xaml.cs" id="SnippetCleanUpPreviewFrame":::

## <a name="related-topics"></a>相关主题

* [摄像头](camera.md)
* [使用 MediaCapture 捕获基本的照片、视频和音频](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 
