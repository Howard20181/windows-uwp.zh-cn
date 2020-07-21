---
ms.assetid: ''
description: 本文介绍了如何通过 MediaFrameReader 类使用开放源计算机视觉库 (OpenCV)。
title: 通过 MediaFrameReader 使用 OpenCV
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, openCV
ms.localizationpriority: medium
ms.openlocfilehash: a6594898dff1bf5f2262034b10e262082335f1b2
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74255948"
---
# <a name="use-the-open-source-computer-vision-library-opencv-with-mediaframereader"></a>通过 MediaFrameReader 使用开放源计算机视觉库 (OpenCV)

本文介绍了如何通过 [**MediaFrameReader**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) 类（可同时读取多个源中的媒体帧）使用开放源计算机视觉库 (OpenCV)，后者是一个本机代码库，可提供各类图像处理算法。 本文中的示例代码向你展示了一款简单的应用，该应用可通过颜色传感器获取帧，使用 OpenCV 库模糊每帧，然后在 XAML **Image** 控件中显示经过处理的图像。 

>[!NOTE]
>OpenCV 和 OpenCV 不会定期更新，也不会通过应用商店合规性检查，因此这些包仅适用于试验。

本文基于其他两篇文章的内容：

* [使用 MediaFrameReader 处理媒体帧](process-media-frames-with-mediaframereader.md) - 此文提供了有关使用 **MediaFrameReader** 获取一个或多个媒体帧源的详细信息，并详细介绍了大部分示例代码。 **使用 MediaFrameReader 处理媒体帧**中专门提供了帮助程序类 **FrameRenderer** 的代码列表，该代码可以处理 XAML **Image** 元素中媒体帧展示。 本文中的示例代码也使用此帮助程序类。

* [使用 OpenCV 处理软件位图](process-software-bitmaps-with-opencv.md)-本文逐步讲解如何创建 Windows 运行时 Component， **OpenCVBridge**的本机代码，这有助于在**MediaFrameReader**使用的**SoftwareBitmap**对象和 OpenCV 库使用的**材料**类型之间进行转换。 此文中的示例代码假定你已按步骤将 **OpenCVBridge** 组件添加到 UWP 应用解决方案中。

若要查看和下载本文中所述方案的完整、端到端工作示例，除了这些文章以外，请参阅 Windows 通用示例 GitHub 存储库中的[相机帧 + OpenCV 示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraOpenCV)。

若要快速开始开发，可以使用 NuGet 包在 UWP 应用项目中包含 OpenCV 库，但当你将应用提交到应用商店时，这些包可能不会传递应用 certficication 进程，因此建议你下载 OpenCV库源代码，并在提交应用之前自行生成二进制文件。 使用 OpenCV 进行开发的信息可以在 [https://opencv.org](https://opencv.org) 中找到


## <a name="implement-the-opencvhelper-native-windows-runtime-component"></a>实现 OpenCVHelper 本机 Windows 运行时组件
按照[使用 OpenCV 处理软件位图](process-software-bitmaps-with-opencv.md)中的步骤创建 OpenCV helper Windows 运行时组件，并向 UWP 应用解决方案添加对组件项目的引用。

## <a name="find-available-frame-source-groups"></a>查找可用的帧源组
首先，你需要从将要获取的媒体帧中查找媒体帧源组。 通过调用 **[MediaFrameSourceGroup.FindAllAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.FindAllAsync)** 获取当前设备上的可用源组列表。 然后选择为你的应用方案提供所需传感器类型的源组。 对于此示例，我们只需选择一个可提供 RGB 相机帧的源组即可。

[!code-cs[OpenCVFrameSourceGroups](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameSourceGroups)]

## <a name="initialize-the-mediacapture-object"></a>初始化 MediaCapture 对象
接着，你需要初始化 **MediaCapture** 对象以使用在上一步骤中选择的帧源组，方法是设置 **MediaCaptureInitializationSettings[ 的 ](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.SourceGroup)** SourceGroup 属性。

> [!NOTE] 
> [使用 OpenCV 处理软件位图](process-software-bitmaps-with-opencv.md)中详述的 OpenCVHelper 组件采用的技术要求要处理的图像数据驻留在 CPU 内存中，而不是 GPU 内存中。 因此，应将 **MediaCaptureInitializationSettings** 的 **[MemoryPreference](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.MemoryPreference)** 字段指定为 **MemoryPreference.CPU**。

初始化 **MediaCapture** 对象后，通过访问 **[MediaCapture.FrameSources](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.FrameSources)** 属性获取 RGB 帧源的引用。

[!code-cs[OpenCVInitMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVInitMediaCapture)]

## <a name="initialize-the-mediaframereader"></a>初始化 MediaFrameReader
接着，为上一步中检索到的 RGB 帧源创建一个 [**MediaFrameReader**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader)。 为了维持良好的帧率，你可能需要处理分辨率低于传感器分辨率的帧。 本示例提供了 **[MediaCapture.CreateFrameReaderAsync](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapsize)** 方法的可选 **[BitmapSize](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createframereaderasync)** 参数，以请求将帧阅读器提供的帧调整为 640 x 480 像素。

创建帧阅读器后，为 **[FrameArrived](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.FrameArrived)** 事件注册一个处理程序。 然后，创建一个新的 **[SoftwareBitmapSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource)** 对象，**FrameRenderer** 帮助程序类可以使用该对象来展示处理的图像。 然后调用 **FrameRenderer** 的构造函数。 初始化在 OpenCVBridge Windows 运行时组件中定义的**OpenCVHelper**类的实例。 **FrameArrived** 处理程序将使用此帮助程序类来处理每个帧。 最后，通过调用 **[StartAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.StartAsync)** 启动帧阅读器。

[!code-cs[OpenCVFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameReader)]


## <a name="handle-the-framearrived-event"></a>处理 FrameArrived 事件
帧阅读器中提供新帧时将会引发 **FrameArrived** 事件。 调用 **[TryAcquireLatestFrame](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.TryAcquireLatestFrame)** 以获取帧（如存在）。 从MediaFrameReference **[ 中获取 ](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereference)SoftwareBitmap**。 请注意，本示例中使用的 **CVHelper** 类需要图像使用带预乘 alpha 的 BRGA8 像素格式。 如果传递至事件的帧具有不同的格式，请将 **SoftwareBitmap** 转换为正确的格式。 接着，创建一个 **SoftwareBitmap**，以用作模糊操作的目标。 源图像属性用作构造函数的参数，以创建具有匹配格式的位图。 调用帮助程序类**模糊**方法来处理帧。 最后，将模糊操作的输出图像传递至 **PresentSoftwareBitmap**，它是 **FrameRenderer** 帮助程序类的方法，用于在初始化使用的 XAML **Image** 控件中显示图像。

[!code-cs[OpenCVFrameArrived](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameArrived)]

## <a name="related-topics"></a>相关主题

* [摄像头](camera.md)
* [带有 MediaCapture 的基本照片、视频和音频捕获](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [用 MediaFrameReader 处理媒体帧](process-media-frames-with-mediaframereader.md)
* [用 OpenCV 处理软件位图](process-software-bitmaps-with-opencv.md)
* [照相机框架示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraFrames)
* [照相机相框 + OpenCV 示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraOpenCV)
 

 




