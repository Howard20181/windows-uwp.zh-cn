---
title: 托管相机条形码扫描仪的预览
description: 了解如何托管应用程序中相机条形码扫描仪的预览
ms.date: 05/02/2018
ms.topic: article
keywords: windows 10, uwp, 服务点, pos
ms.localizationpriority: medium
ms.openlocfilehash: 6657fa52a5e3cac0821def7102b4bc93089b2ffe
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89159631"
---
# <a name="hosting-a-camera-barcode-scanner-preview-in-your-application"></a>托管应用程序中相机条形码扫描仪的预览
## <a name="step-1-setup-your-camera-preview"></a>步骤 1：设置相机预览
向应用程序添加相机条形码扫描仪预览的第一步可以按照[显示相机预览](../audio-video-camera/simple-camera-preview-access.md)主题中的说明完成。  完成此步骤后，返回到此主题了解相机条形码扫描仪的特定修改。

## <a name="step-2-update-capability-declarations"></a>步骤 2：更新功能声明
若要防止用户收到麦克风同意提示，可以将此功能从应用清单中列出的功能中排除。

1. 在 Microsoft Visual Studio 的 **解决方案资源管理器**中，双击 " **appxmanifest.xml** " 项，打开应用程序清单的设计器。
2. 选择“功能”选项卡。
3. 取消选中**麦克风**复选框

 ## <a name="step-3-add-additional-using-directive-for-media-capture"></a>步骤 3：使用媒体捕获指令添加其他功能

```Csharp
using Windows.Media.Capture;
```

## <a name="step-4-set-up-your-mediacapture-initialization-settings"></a>步骤 4：设置 MediaCapture 初始化设置
下面的示例初始化 [**MediaCaptureInitializationSettings**](/uwp/api/windows.media.capture.mediacaptureinitializationsettings)。 

```Csharp
 private void InitCaptureSettings()
{
    _captureInitSettings = new MediaCaptureInitializationSettings();
    _captureInitSettings.VideoDeviceId = BarcodeScanner.VideoDeviceId;
    _captureInitSettings.StreamingCaptureMode = StreamingCaptureMode.Video;
    _captureInitSettings.PhotoCaptureSource = PhotoCaptureSource.VideoPreview;
}
```
## <a name="step-5-associate-your-mediacapture-object-with-the-camera-barcode-scanner"></a>步骤 5：将 MediaCapture 对象与相机条形码扫描仪关联
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
> 请参阅[显示相机预览](../audio-video-camera/simple-camera-preview-access.md#add-capability-declarations-to-the-app-manifest)了解有关在应用程序中托管相机预览的更多高级主题。

## <a name="see-also"></a>另请参阅

### <a name="samples"></a>示例

- [条形码扫描仪示例](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)