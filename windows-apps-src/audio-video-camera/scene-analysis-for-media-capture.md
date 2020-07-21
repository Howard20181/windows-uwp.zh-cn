---
ms.assetid: B5D915E4-4280-422C-BA0E-D574C534410B
description: 本文介绍了如何使用 SceneAnalysisEffect 和 FaceDetectionEffect 分析媒体捕获预览流的内容。
title: 分析相机帧的效果
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 406af54cfaae8710cea2d989278a16f28c8dd619
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74256209"
---
# <a name="effects-for-analyzing-camera-frames"></a>分析相机帧的效果



本文介绍了如何使用 [**SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect) 和 [**FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect) 分析媒体捕获预览流的内容。

## <a name="scene-analysis-effect"></a>场景分析效果

[  **SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect) 将分析媒体捕获预览流中的视频帧，并建议处理选项以改进捕获结果。 当前，效果支持检测是否通过使用高动态范围 (HDR) 处理改进捕获。

如果该效果建议使用 HDR，可以采用以下方式执行此操作：

-   使用 [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture) 类通过 Windows 内置的 HDR 处理算法捕获照片。 有关详细信息，请参阅[高动态范围 (HDR) 照片捕获](high-dynamic-range-hdr-photo-capture.md)。

-   使用 [**HdrVideoControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.HdrVideoControl) 通过 Windows 内置的 HDR 处理算法捕获视频。 有关详细信息，请参阅[用于视频捕获的捕获设备控件](capture-device-controls-for-video-capture.md)。

-   使用 [**VariablePhotoSequenceControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.VariablePhotoSequenceController) 捕获帧的序列，稍后可使用自定义 HDR 实现对其进行组合。 有关详细信息，请参阅[可变照片序列](variable-photo-sequence.md)。

### <a name="scene-analysis-namespaces"></a>场景分析命名空间

若要使用场景分析，你的应用必须包含以下命名空间以及基本媒体捕获所需的命名空间。

[!code-cs[SceneAnalysisUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSceneAnalysisUsing)]

### <a name="initialize-the-scene-analysis-effect-and-add-it-to-the-preview-stream"></a>初始化场景分析效果并将其添加到预览流

使用两个 API、一个效果定义（可提供捕获设备初始化效果所需的设置）和一个效果实例（可用于控制效果）实现视频效果。 因为你可能想要从代码内的多个位置访问效果实例，因此你通常应声明成员变量以保留对象。

[!code-cs[DeclareSceneAnalysisEffect](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareSceneAnalysisEffect)]

在你的应用中，在初始化 **MediaCapture** 对象后，创建 [**SceneAnalysisEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffectDefinition) 的新实例。

通过在你的 [MediaCapture**对象上调用**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.addvideoeffectasync)AddVideoEffectAsync、提供 **SceneAnalysisEffectDefinition** 并指定 [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType)，指示该效果应应用于视频预览流（而不是捕获流），从而使用捕获设备注册效果。 **AddVideoEffectAsync** 返回已添加效果的实例。 因为此方法可以与多个效果类型结合使用，因此你必须将返回的实例转换为 [**SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect) 对象。

若要接收场景分析的结果，你必须为 [**SceneAnalyzed**](https://docs.microsoft.com/uwp/api/windows.media.core.sceneanalysiseffect.sceneanalyzed) 事件注册处理程序。

当前，场景分析效果仅包含高动态范围分析器。 通过将效果的 [**HighDynamicRangeControl.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangecontrol.enabled) 设置为 true 启用 HDR 分析。

[!code-cs[CreateSceneAnalysisEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateSceneAnalysisEffectAsync)]

### <a name="implement-the-sceneanalyzed-event-handler"></a>实现 SceneAnalyzed 事件处理程序

场景分析的结果在 **SceneAnalyzed** 事件处理程序中返回。 传入处理程序中的 [**SceneAnalyzedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalyzedEventArgs) 对象具有包含 [**HighDynamicRangeOutput**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffectFrame) 对象的 [**SceneAnalysisEffectFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.HighDynamicRangeOutput) 对象。 高动态范围输出的 [**Certainty**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangeoutput.certainty) 属性提供了一个介于 0 到 1.0 的值，其中 0 指示 HDR 处理不会帮助改进捕获结果，1.0 指示 HDR 处理会帮助改进捕获结果。 你可以确定要使用 HDR 的阈值点，或向用户显示结果并让用户作出决定。

[!code-cs[SceneAnalyzed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSceneAnalyzed)]

传入处理程序中的 [**HighDynamicRangeOutput**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.HighDynamicRangeOutput) 对象还具有 [**FrameControllers**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangeoutput.framecontrollers) 属性，它包含用于捕获可变照片序列进行 HDR 处理的建议帧控制器。 有关详细信息，请参阅[可变照片序列](variable-photo-sequence.md)。

### <a name="clean-up-the-scene-analysis-effect"></a>清理场景分析效果

当你的应用完成捕获时，在处理 **MediaCapture** 对象之前，你应该通过将效果的 [**HighDynamicRangeAnalyzer.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangecontrol.enabled) 属性设置为 false 来禁用场景分析效果，并取消注册 [**SceneAnalyzed**](https://docs.microsoft.com/uwp/api/windows.media.core.sceneanalysiseffect.sceneanalyzed) 事件处理程序。 请调用 [**MediaCapture.ClearEffectsAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.cleareffectsasync)，从而指定视频预览流，因为该流是已添加效果的流。 最后，将你的成员变量设置为 null。

[!code-cs[CleanUpSceneAnalysisEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpSceneAnalysisEffectAsync)]

## <a name="face-detection-effect"></a>人脸检测效果

[  **FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect) 可在媒体捕获预览流内标识人脸的位置。 该效果允许你在预览流中检测到人脸时接收通知，并为预览帧内每个检测到的人脸提供边界框。 在受支持的设备上，人脸检测效果还提供增强的曝光，并聚焦于场景中最重要的人脸。

### <a name="face-detection-namespaces"></a>人脸检测命名空间

若要使用人脸检测，你的应用必须包含以下命名空间，以及基本媒体捕获所需的命名空间。

[!code-cs[FaceDetectionUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFaceDetectionUsing)]

### <a name="initialize-the-face-detection-effect-and-add-it-to-the-preview-stream"></a>初始化人脸检测效果并将其添加到预览流

使用两个 API、一个效果定义（可提供捕获设备初始化效果所需的设置）和一个效果实例（可用于控制效果）实现视频效果。 因为你可能想要从代码内的多个位置访问效果实例，因此你通常应声明成员变量以保留对象。

[!code-cs[DeclareFaceDetectionEffect](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareFaceDetectionEffect)]

在你的应用中，在初始化 **MediaCapture** 对象后，创建 [**FaceDetectionEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffectDefinition) 的新实例。 设置 [**DetectionMode**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffectdefinition.detectionmode) 属性以设置优先级：更快的人脸检测优先或更精确的人脸检测优先。 设置 [**SynchronousDetectionEnabled**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffectdefinition.synchronousdetectionenabled) 以指定传入的帧不会延迟等待人脸检测完成，因为这可能导致不连贯的预览体验。

通过在你的 [MediaCapture**对象上调用**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.addvideoeffectasync)AddVideoEffectAsync、提供 **FaceDetectionEffectDefinition** 并指定 [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType)，指示该效果应应用于视频预览流（而不是捕获流），从而使用捕获设备注册效果。 **AddVideoEffectAsync** 返回已添加效果的实例。 因为此方法可以与多个效果类型结合使用，因此你必须将返回的实例转换为 [**FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect) 对象。

通过设置 [**FaceDetectionEffect.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.enabled) 属性，启用或禁用效果。 通过设置 [**FaceDetectionEffect.DesiredDetectionInterval**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.desireddetectioninterval) 属性调整效果分析帧的频率。 在正在运行媒体捕获时，可同时调整这两个属性。

[!code-cs[CreateFaceDetectionEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateFaceDetectionEffectAsync)]

### <a name="receive-notifications-when-faces-are-detected"></a>在检测到人脸时接收通知

如果你想要在检测到人脸时执行某些操作（例如，在视频预览中检测到的人脸周围绘制一个框），你可以注册 [**FaceDetected**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.facedetected) 事件。

[!code-cs[RegisterFaceDetectionHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterFaceDetectionHandler)]

在该事件的处理程序中，你可以通过访问 [**FaceDetectedEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffectframe.detectedfaces) 的 [**FaceDetectionEffectFrame.DetectedFaces**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectedEventArgs) 属性，获取帧中所有检测到的人脸列表。 [  **FaceBox**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.detectedface.facebox) 属性是 [**BitmapBounds**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapBounds) 结构，用于描述包含检测到的人脸的矩形，其单位相对于预览流尺寸。 若要查看将预览流坐标转换到屏幕坐标的示例代码，请参阅[人脸检测 UWP 示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraFaceDetection)。

[!code-cs[FaceDetected](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFaceDetected)]

### <a name="clean-up-the-face-detection-effect"></a>清理人脸检测效果

当你的应用完成捕获时，在处理 **MediaCapture** 对象之前，应该使用 [**FaceDetectionEffect.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.enabled) 禁用人脸检测效果并取消注册 [**FaceDetected**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.facedetected) 事件处理程序（如果你之前已注册一个处理程序）。 请调用 [**MediaCapture.ClearEffectsAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.cleareffectsasync)，从而指定视频预览流，因为该流是已添加效果的流。 最后，将你的成员变量设置为 null。

[!code-cs[CleanUpFaceDetectionEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpFaceDetectionEffectAsync)]

### <a name="check-for-focus-and-exposure-support-for-detected-faces"></a>查看对检测到的人脸的焦点和曝光支持

并非所有设备都具有可根据检测到的人脸调整其焦点和曝光的捕获设备。 因为人脸检测会消耗设备资源，因此你可能只希望在可使用该功能增强捕获的设备上启用人脸检测。 若要查看基于人脸的捕获优化是否可用，请为初始化的 [MediaCapture**获取**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.VideoDeviceController)VideoDeviceController[](capture-photos-and-video-with-mediacapture.md)，然后获取视频设备控制器的 [**RegionsOfInterestControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.RegionsOfInterestControl)。 查看 [**MaxRegions**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.maxregions) 是否支持至少一个区域。 然后，查看 [**AutoExposureSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.autoexposuresupported) 或 [**AutoFocusSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.autofocussupported) 是否为 true。 如果满足这些条件，则设备可以充分利用人脸检测来增强捕获。

[!code-cs[AreFaceFocusAndExposureSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAreFaceFocusAndExposureSupported)]

## <a name="related-topics"></a>相关主题

* [摄像头](camera.md)
* [带有 MediaCapture 的基本照片、视频和音频捕获](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 




