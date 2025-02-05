---
description: 了解如何在 toast 通知上使用自定义音频，使你的应用能够快速了解你的品牌独特的声音效果。
title: toast 上的自定义音频
label: Custom audio on toasts
template: detail.hbs
ms.date: 12/15/2017
ms.topic: article
keywords: windows 10, uwp, toast, 自定义音频, 通知, 音频, 声音
ms.localizationpriority: medium
ms.openlocfilehash: 905292155dfc43a82c464edb651b2d176aeab960
ms.sourcegitcommit: 5e718720d1032a7089dea46a7c5aefa6cda3385f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103226121"
---
# <a name="custom-audio-on-toasts"></a>toast 上的自定义音频

Toast 通知可使用自定义音频，从而让你的应用展现品牌的独特音效。 例如，消息应用可在其 Toast 通知上使用自己的消息传递声音，而不是使用通用的通知声音，以便用户立即知道接收的通知来自该应用。

## <a name="install-uwp-community-toolkit-nuget-package"></a>安装 UWP 社区工具包 NuGet 程序包

若要通过代码创建通知，我们强烈建议使用 UWP 社区工具包通知库，它提供通知 XML 内容的对象模型。 可手动构造通知 XML，但此操作比较杂乱且容易出错。 UWP 社区工具包内部的通知库由 Microsoft 拥有通知的团队生成和维护。

从 NuGet 安装 [Microsoft 工具包](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) 。


## <a name="add-namespace-declarations"></a>添加命名空间声明

```csharp
using Microsoft.Toolkit.Uwp.Notifications;
```


## <a name="add-the-custom-audio"></a>添加自定义音频

Windows 移动版始终支持 toast 通知中的自定义音频。 但是，只有版本 1511（内部版本 10586）的桌面设备添加了对自定义音频的支持。 如果将包含自定义音频的 toast 发送到版本 1511 之前的桌面设备，该 toast 将处于静音模式。 因此，对于版本 1511 之前的桌面设备，toast 通知中不应包含自定义音频，这样通知至少会使用默认通知声音。

**已知问题**：如果使用版本 1511 的桌面设备，只有在通过 Microsoft Store 安装应用时，自定义 toast 音频才能正常工作。 这意味着在提交到 Microsoft Store 之前无法在本地测试桌面设备上的自定义音频，但只要从 Microsoft Store 安装应用，音频便可正常工作。 我们已在周年更新中修复了此问题，因此本地部署的应用中的自定义音频将正常工作。

```csharp
var contentBuilder = new ToastContentBuilder()
    .AddText("New message");

    
bool supportsCustomAudio = true;
 
// If we're running on Desktop before Version 1511, do NOT include custom audio
// since it was not supported until Version 1511, and would result in a silent toast.
if (AnalyticsInfo.VersionInfo.DeviceFamily.Equals("Windows.Desktop")
    && !ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 2))
{
    supportsCustomAudio = false;
}
 
if (supportsCustomAudio)
{
    contentBuilder.AddAudio(new Uri("ms-appx:///Assets/Audio/CustomToastAudio.m4a"));
}

// Send the toast
contentBuilder.Show();
```

支持的音频文件类型包括...

- .aac
- .flac
- .m4a
- .mp3
- .wav
- .wma


## <a name="send-the-notification"></a>发送通知

发送带有音频的通知与发送定期通知 (只需调用 Show 方法) 相同。 请参阅 [发送本地 toast](send-local-toast.md) 以了解详细信息。


## <a name="related-topics"></a>相关主题

- [GitHub 上的完整代码示例](https://github.com/WindowsNotifications/quickstart-toast-with-custom-audio)
- [发送本地 toast](send-local-toast.md)
- [toast 内容文档](adaptive-interactive-toasts.md)
