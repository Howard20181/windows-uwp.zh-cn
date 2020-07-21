---
Description: 了解如何在 toast 通知上使用通知镜像。
title: 通知镜像
label: Notification mirroring
template: detail.hbs
ms.date: 12/15/2017
ms.topic: article
keywords: windows 10, uwp, toast, 云中的操作中心, 通知镜像, 通知, 跨设备
ms.localizationpriority: medium
ms.openlocfilehash: b897c6574f6cbfe78406d1c624f2e3b7286ef582
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82971052"
---
# <a name="notification-mirroring"></a>通知镜像

通知镜像由云中的操作中心提供支持，借助通知镜像，用户可以在电脑上查看手机通知。

> [!IMPORTANT]
> **需要周年更新**：必须运行内部版本 14393 或更高版本才能查看通知镜像工作。 如果想要选择将应用退出通知镜像，则必须以 SDK 14393 为目标来访问镜像 API。

通过通知镜像和 Cortana，用户可以方便地从电脑上接收和操作手机的通知（Windows 移动版和 Android）。 开发人员无需执行任何操作即可启用通知镜像，镜像自动工作！ 点击镜像 Toast 上的按钮，就像快速回复消息一样，将会返回手机，调用后台任务或启动前台应用。

<img alt="Notification mirroring diagram" src="images/toast-mirroring.gif" width="350"/>

开发人员从通知镜像中获得了两大好处：镜像通知会导致更多的用户与你的服务合作，同时还有助于用户发现你的 Microsoft Store 桌面应用程序！ 你的用户可能甚至还不知道，你有一个适用于 Windows 10 桌面的出色的 Windows 应用程序。 当用户从他们的手机接收到镜像通知时，用户可以单击 toast 通知进入 Microsoft Store，他们可以在其中安装 Windows 应用。

镜像适用于 Windows Phone 和 Android。 用户需要同时在手机和桌面上登录 Cortana，通知镜像才能运行。


## <a name="what-if-the-app-is-installed-on-both-devices"></a>如果在两台设备上都安装了该应用呢？

如果用户已经在电脑上安装了应用，我们将自动禁用镜像手机通知，使他们不会看到重复的通知。 镜像通知将根据以下条件自动禁用...

1. 电脑上的应用有**相同的显示名称或相同 PFN**（包系列名称）
2. 电脑应用已发送一条 Toast 通知

如果电脑应用未发送 Toast，我们仍然会显示手机通知，因为用户很有可能尚未启动电脑应用）。


## <a name="how-to-opt-out-of-mirroring"></a>如何选择退出镜像

Windows 应用开发人员、企业和用户可以选择禁用通知镜像。

> [!NOTE]
> 禁用镜像还会禁用[通用消除](universal-dismiss.md)。


### <a name="as-a-developer-opt-out-an-individual-notification"></a>作为开发人员，选择退出单个通知

有时可能不希望将一个特定于设备的通知镜像到其他设备。 可以通过在 Toast 通知上设置 **Mirroring** 属性来防止镜像特定通知。 目前，只能在本地通知上设置此镜像属性（在发送 WNS 推送通知时不能指定该属性）。

**已知问题**：通过 `ToastNotificationHistory.GetHistory()` API 检索镜像属性将始终返回默认值（**允许**）而不是指定的选项。 不必担心，一切都正常运行 - 只是在检索已损坏的值。

```csharp
var toast = new ToastNotification(xml)
{
    // Disable mirroring of this notification
    Mirroring = NotificationMirroring.Disabled
};
  
ToastNotificationManager.CreateToastNotifier().Show(toast);
```


### <a name="as-a-developer-opt-out-completely"></a>作为开发人员，选择完全退出

某些开发人员可能会选择将应用完全退出通知镜像。 尽管我们认为镜像对所有的应用都有好处，但用户也可以轻松选择退出。只需调用以下方法一次，应用将会选择退出。例如，可以将此调用放置在 `App.xaml.cs` 内的应用的构造函数中...

```csharp
public App()
{
    this.InitializeComponent();
    this.Suspending += OnSuspending;
 
    // Disable notification mirroring for entire app
    ToastNotificationManager.ConfigureNotificationMirroring(NotificationMirroring.Disabled);
}
```


### <a name="as-an-enterprise-how-do-i-opt-out"></a>作为企业，如何选择退出？

企业可以选择完全禁用通知镜像。 若要执行此操作，只需编辑组策略，关闭通知镜像。


### <a name="as-a-user-how-do-i-opt-out"></a>作为用户，如何选择退出？

用户可以选择退出单个应用，或通过禁用该功能选择完全退出。 如果不希望某个特定应用的通知镜像到桌面，可以仅禁用该特定应用。 在手机和电脑上的 Cortana 设置中可以找到这些选项。
