---
title: 托管相机条形码扫描仪的预览
description: 了解如何托管应用程序中相机条形码扫描仪的预览
ms.date: 05/02/2018
ms.topic: article
keywords: windows 10, uwp, 服务点, pos
ms.localizationpriority: medium
ms.openlocfilehash: b3391a7640e49fb43ac0f7ea33a0fa992c4a7495
ms.sourcegitcommit: 0dec04de501a3db6b22dfd4a320fc09b5c4a21b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243284"
---
# <a name="hosting-a-camera-barcode-scanner-preview-in-your-application"></a>托管应用程序中相机条形码扫描仪的预览
## <a name="step-1-setup-your-camera-preview"></a>步骤 1：设置相机预览
向应用程序添加相机条形码扫描仪预览的第一步可以按照[显示相机预览](../audio-video-camera/simple-camera-preview-access.md)主题中的说明完成。  完成此步骤后，返回到此主题了解相机条形码扫描仪的特定修改。

## <a name="step-2-update-capability-declarations"></a>步骤 2：更新功能声明
若要防止用户收到麦克风同意提示，可以将此功能从应用清单中列出的功能中排除。

1. 在 Microsoft Visual Studio 的**解决方案资源管理器**中，通过双击 **package.appxmanifest** 项，打开应用程序清单的设计器。
2. 选择**功能**选项卡。
3. 取消选中**麦克风**复选框

 ## <a name="step-3-add-additional-using-directive-for-media-capture"></a>步骤 3：为媒体捕获添加其他 using 指令

```Csharp
using Windows.Media.Capture;
```

## <a name="step-4-set-up-your-mediacapture-initialization-settings"></a>步骤 4：设置 MediaCapture 初始化设置
下面的示例初始化 [**MediaCaptureInitializationSettings**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings)。 

```Csharp
 private void InitCaptureSettings()
{
    _captureInitSettings = new MediaCaptureInitializationSettings();
    _captureInitSettings.VideoDeviceId = BarcodeScanner.VideoDeviceId;
    _captureInitSettings.StreamingCaptureMode = StreamingCaptureMode.Video;
    _captureInitSettings.PhotoCaptureSource = PhotoCaptureSource.VideoPreview;
}
```
## <a name="step-5-associate-your-mediacapture-object-with-the-camera-barcode-scanner"></a>步骤 5：将 MediaCapture 对象与相机条形码扫描器相关联
将 *StartPreviewAsync()* 中的现有 mediaCapture.InitializeAsync() 替换为以下项：

```Csharp
try
    {

        mediaCapture = new MediaCapture();
        await mediaCapture.InitializeAsync(InitCaptureSettings());

        displayRequest.RequestActive();
        DisplayInformation.AutoRotationPreferences = DisplayOrientations.Landscape;
    }
```

> [!TIP]
> 请参阅[显示相机预览](https://docs.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access#add-capability-declarations-to-the-app-manifest)了解有关在应用程序中托管相机预览的更多高级主题。

## <a name="see-also"></a>请参阅

### <a name="samples"></a>示例

- [条形码扫描器示例](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
