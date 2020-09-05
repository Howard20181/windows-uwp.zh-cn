---
description: Windows 推送通知服务 (WNS) 使第三方开发人员可从自己的云服务发送 Toast、磁贴、锁屏提醒和原始更新。 这提供了一种高效而可靠地向用户提供新更新的机制。
title: Windows 推送通知服务 (WNS) 概述
ms.assetid: 2125B09F-DB90-4515-9AA6-516C7E9ACCCD
template: detail.hbs
ms.date: 03/06/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: bd910c42743577a83491386f5c667dd09722ba9b
ms.sourcegitcommit: 8171695ade04a762f19723f0b88e46e407375800
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2020
ms.locfileid: "89494373"
---
# <a name="windows-push-notification-services-wns-overview"></a>Windows 推送通知服务 (WNS) 概述 

Windows 推送 Notification Services (WNS) 使第三方开发人员能够从他们自己的云服务发送 toast、磁贴、徽章和原始更新。 这提供了一种高效而可靠地向用户提供新更新的机制。

## <a name="how-it-works"></a>工作原理

下图显示了用于发送推送通知的完整数据流。 它涉及到以下步骤：

1.  您的应用程序从 WNS 请求推送通知通道。
2.  Windows 要求 WNS 创建通知通道。 此通道以统一资源标识符 (URI) 的形式返回到调用设备。
3.  WNS 向应用返回通知通道 URI。
4.  你的应用将 URI 发送到你自己的云服务。 然后你将 URI 存储在自己的云服务上，以便在发生通知时访问该 URI。 URI 是你自己的应用与自己的服务之间的接口；它负责通过安全可靠的 Web 标准来实现此接口。
5.  当你的云服务有要发送的更新时，它使用通道 URI 通知 WNS。 通过安全套接字层 (SSL) 发送 TTP POST 请求（包括通知负载）来执行此操作。 此步骤需要身份验证。
6.  WNS 接收请求，并将通知路由到相应的设备。

![推送通知的 WNS 数据流关系图](images/wns-diagram-01.jpg)

## <a name="registering-your-app-and-receiving-the-credentials-for-your-cloud-service"></a>注册你的应用，并为你的云服务接收凭据

在使用 WNS 发送通知之前，应用必须先向应用商店仪表板进行注册。 

每个应用都有其各自的一组云服务凭据。 这些凭据无法用于向其他任何应用发送通知。

### <a name="step-1-register-your-app-with-the-dashboard"></a>步骤1：将应用注册到仪表板

必须先将应用注册到合作伙伴中心仪表板，然后才能通过 WNS 发送通知。 这将为应用提供凭据，云服务在向 WNS 进行验证的过程中将使用该凭据。 这些凭据由程序包安全标识符 (SID) 和密钥组成。 若要执行此注册，请登录到 [合作伙伴中心](https://partner.microsoft.com/dashboard)。 创建应用后，请参阅 [产品管理-WNS/MPNS](https://apps.dev.microsoft.com/) for instrunctions 了解如何检索凭据 (如果你想要使用 live services 解决方案，请按照本页) 上的 " **live 服务站点** " 链接进行操作。

若要注册，请执行以下操作：
1.    请参阅合作伙伴中心的 Windows 应用商店应用页，并以个人 Microsoft 帐户登录 (例如： johndoe@outlook.com ， janedoe@xboxlive.com) 。
2.    登录后，单击 "仪表板" 链接。
3.    在仪表板上，选择 "创建新应用"。

![wns 应用注册](../images/wns-create-new-app.png)

4.    通过保留应用名称创建应用。 为应用提供唯一的名称。 输入名称，并单击 "保留产品名称" 按钮。 如果名称可用，则将其保留给你的应用程序。 成功保留应用的名称后，应在此时选择要修改的其他详细信息。

![wns 保留产品名称](../images/wns-reserve-poduct-name.png)
 
### <a name="step-2-obtain-the-identity-values-and-credentials-for-your-app"></a>步骤2：获取应用的标识值和凭据

为应用保留名称后，Windows 应用商店创建了关联的凭据。 它还分配了关联的标识值，这些值必须存在于应用的清单文件中 (appxmanifest.xml) 。 如果已将应用上传到 Windows 应用商店，则这些值将自动添加到清单中。 如果尚未上传应用，需要手动将标识值添加到清单中。

1.    选择 "产品管理" 下拉箭头

![wns 产品管理](../images/wns-product-management.png)

2.    在 "产品管理" 下拉列表中，选择 "WNS/MPNS" 链接。

![wns 产品管理 continuted](../images/wns-product-management2.png)
 
3.    在 "WNS/MPNS" 页上，单击 "Windows 推送 Notification Services (WNS) 和 Microsoft Azure 移动服务" 部分下的 "Live 服务站点" 链接。

![wns live 服务](../images/wns-live-services-page.png)
 
4.     (之前，应用程序注册门户) 页面提供了一个要包含在应用程序清单中的 identity 元素。 这包括应用程序机密 (s) 、包安全标识符和应用程序标识。 在文本编辑器中打开清单，并在页面指示时添加该元素。    

> [!NOTE]
> 如果你使用 AAD 帐户登录，则需要联系注册应用的 Microsoft 帐户所有者以获取关联的应用机密。 如果需要查找此联系人的帮助，请单击屏幕右上角的齿轮，然后单击 "开发人员设置"，将在其中显示 Microsoft 帐户应用程序的创建者的电子邮件地址。
 
5.    将 SID 和客户端机密上传到你的云服务器。

> [!Important]
> 你的云服务应安全地存储和访问 SID 和客户端机密。 泄露或盗窃此信息后，攻击者便可以向用户发送通知，而无需你的许可或知识。

## <a name="requesting-a-notification-channel"></a>请求通知通道

当能够接收推送通知的应用运行时，它必须首先通过 [**CreatePushNotificationChannelForApplicationAsync**](/uwp/api/Windows.Networking.PushNotifications.PushNotificationChannelManager#Windows_Networking_PushNotifications_PushNotificationChannelManager_CreatePushNotificationChannelForApplicationAsync_System_String_) 请求通知通道。 若要查看全面介绍和示例代码，请参阅[如何请求、创建和保存通知通道](/previous-versions/windows/apps/hh465412(v=win.10))。 此 API 会返回一个唯一链接到进行调用的应用程序及其磁贴的通道 URI，所有通知类型均可通过此 URI 发送。

应用成功创建了通道 URI 之后，会将其与任何应该与该 URI 关联的特定于应用的元数据一起发送到它的云服务。

### <a name="important-notes"></a>重要事项

-   我们不保证应用的通知通道 URI 将始终保持相同。 我们建议应用在每次运行时均请求一个新的通道，并在 URI 更改时更新其服务。 开发人员绝不能修改该通道 URI，而应将其视作一段黑盒字符串。 此时，通道 URI 于 30 天后过期。 如果 Windows 10 应用会在后台定期更新其通道，则可以下载针对 Windows 8.1 的[推送和定期通知示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/Push%20and%20periodic%20notifications%20client-side%20sample%20(Windows%208))、重新使用其源代码和/或其展示的模式。
-   云服务和客户端应用之间的接口由你这个开发人员来实现。 我们建议应用使用其自身的服务完成身份验证过程，并通过安全的协议（如 HTTPS）来传输数据。
-   云服务必须始终确保通道 URI 使用域“notify.windows.com”。 该服务永远不应向任何其他域中的通道推送通知。 如果应用的回调发生了泄露，恶意攻击者可能会将该通道 URI 提交给假冒 WNS。 如果不检查域，云服务可能会在不知情的情况下向此攻击者泄露信息。
-   如果你的云服务尝试将通知传递到过期通道，WNS 将返回[响应代码 410](/previous-versions/windows/apps/hh465435(v=win.10))。 为响应此代码，你的服务不应再尝试将通知发送到该 URI。

## <a name="authenticating-your-cloud-service"></a>验证你的云服务

若要发送通知，云服务必须通过 WNS 进行验证。 此过程的第一步出现在使用 Microsoft Store 仪表板注册应用之时。 在注册过程中，应用会获得一个程序包安全标识符 (SID) 和一个密钥。 该信息由你的云服务用于向 WNS 进行验证。

WNS 身份验证方案通过来自 [OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-v2-23) 协议的客户端凭据配置文件来实现。 云服务通过提供其凭据（程序包 SID 和密钥）来向 WNS 进行验证。 反过来，云服务会获得一个访问令牌。 该访问令牌允许云服务发送通知。 每次向 WNS 发送通知请求时都必须使用该令牌。

该信息链简述如下：

1.  云服务遵循 OAuth 2.0 协议通过 HTTPS 将其凭据发送给 WNS。 这会向 WNS 验证该服务。
2.  如果身份验证成功，则 WNS 会返回一个访问令牌。 此访问令牌在所有后续通知请求中使用，直到令牌过期。

![云服务身份验证的 WNS 关系图](images/wns-diagram-02.jpg)

在对 WNS 进行身份验证的过程中，云服务会通过安全套接字层 (SSL) 提交一个 HTTP 请求。 参数以“application/x-www-for-urlencoded”格式提供。 在 "客户端 id" 字段中提供包 SID \_ ，在 "客户端密码" 字段中提供机密密钥， \_ 如以下示例中所示。 有关语法的详细信息，请参阅[访问令牌请求](/previous-versions/windows/apps/hh465435(v=win.10))参考。

> [!NOTE]
> 这只是一个示例，而不是可以在自己的代码中成功使用的剪切和粘贴代码。 

``` http
 POST /accesstoken.srf HTTP/1.1
 Content-Type: application/x-www-form-urlencoded
 Host: https://login.live.com
 Content-Length: 211
 
 grant_type=client_credentials&client_id=ms-app%3a%2f%2fS-1-15-2-2972962901-2322836549-3722629029-1345238579-3987825745-2155616079-650196962&client_secret=Vex8L9WOFZuj95euaLrvSH7XyoDhLJc7&scope=notify.windows.com
```

WNS 对云服务进行身份验证，如果成功，则发送“200 OK”响应。 访问令牌使用“application/json”媒体类型在该 HTTP 响应正文所含的参数中返回。 在你的服务收到该访问令牌后，可随时开始发送通知。

以下示例将显示一个包含访问令牌身份的成功验证响应。 有关语法的详细信息，请参阅[推送通知服务请求和响应头](/previous-versions/windows/apps/hh465435(v=win.10))。

``` http
 HTTP/1.1 200 OK   
 Cache-Control: no-store
 Content-Length: 422
 Content-Type: application/json
 
 {
     "access_token":"EgAcAQMAAAAALYAAY/c+Huwi3Fv4Ck10UrKNmtxRO6Njk2MgA=", 
     "token_type":"bearer"
 }
```

### <a name="important-notes"></a>重要事项

-   以上过程支持的 OAuth 2.0 协议遵循草案版本 V16。
-   OAuth 征求意见文档 (RFC) 使用术语“客户端”指示云服务。
-   当该 OAuth 草案定稿时，以上过程可能有所更改。
-   访问令牌可用于多个通知请求。 这样，云服务只需验证一次即可发送多个通知。 然而，当访问令牌过期时，云服务必须再次验证，以获得一个新的访问令牌。

## <a name="sending-a-notification"></a>发送通知


通过使用通道 URI，无论何时有适用于客户的更新，云服务都可以发送通知。

上述访问令牌可用于多个通知请求；云服务不必为每个通知都请求一个新的访问令牌。 如果访问令牌过期，通知请求将返回一个错误。 如果访问令牌被拒绝，我们建议你不要尝试多次重新发送通知。 如果遇到该错误，你将需要请求一个新的访问令牌并重新发送通知。 有关准确的错误代码，请参阅[推送通知响应代码](/previous-versions/windows/apps/hh465435(v=win.10))。

1.  云服务会向通道 URI 发出一个 HTTP POST。 该请求必须通过 SSL 发出并包含必要的标头和通知负载。 授权头必须包含授权所必需的访问令牌。

    示例请求如下。 有关语法的详细信息，请参阅[推送通知响应代码](/previous-versions/windows/apps/hh465435(v=win.10))。

    有关编写通知负载的详细信息，请参阅[快速入门：发送推送通知](/previous-versions/windows/apps/hh868252(v=win.10))。 磁贴、Toast 或锁屏提醒通知的负载作为 XML 内容提供，并依附于其分别定义的[适应磁贴架构](adaptive-tiles-schema.md)或[传统磁贴架构](/uwp/schemas/tiles/tiles-xml-schema-portal)。 原始通知的负载没有指定的结构。 严格来讲它是由应用定义的。

    ``` http
     POST https://cloud.notify.windows.com/?token=AQE%bU%2fSjZOCvRjjpILow%3d%3d HTTP/1.1
     Content-Type: text/xml
     X-WNS-Type: wns/tile
     Authorization: Bearer EgAcAQMAAAAALYAAY/c+Huwi3Fv4Ck10UrKNmtxRO6Njk2MgA=
     Host: cloud.notify.windows.com
     Content-Length: 24

     <body>
     ....
    ```

2.  WNS 发出响应，表明通知已收到，并将在下次可能时传递。 但是，WNS 不会提供设备或应用程序已收到通知的端到端确认。

此关系图演示数据流：

![发送通知的 WNS 关系图](images/wns-diagram-03.jpg)

### <a name="important-notes"></a>重要事项

-   WNS 不保证通知的可靠性或延迟。
-   通知绝不应包含机密或敏感数据。
-   若要发送通知，云服务必须先对 WNS 进行身份验证并获得访问令牌。
-   访问令牌仅允许云服务将通知发送到为其创建令牌的单个应用。 单个访问令牌无法用于在多个应用中发送通知。 因此，如果你的云服务支持多个应用，则在向每个通道 URI 推送通知时都必须提供相应应用的正确访问令牌。
-   当设备脱机时，WNS 将默认为每个通道 URI 存储至多 5 个磁贴通知（如果启用了队列；否则只能存储 1 个磁贴通知）和 1 个锁屏提醒通知，不存储原始通知。 可以通过 [X-WNS-Cache-Policy 标头](/previous-versions/windows/apps/hh465435(v=win.10))更改这种默认缓存行为。 请注意，当设备离线时，永远不会存储 Toast 通知。
-   在对用户个性化通知内容的方案中，WNS 建议云服务在收到这些更新时立即发送这些更新。 此方案的示例包括社交媒体源更新、即时通信邀请、新消息通知或警报。 作为备用方法，你可以使用向大部分用户频繁提供相同的通用更新的方案；例如，天气、股票和新闻更新。 WNS 指南中指定这些更新的频率最高为每 30 分钟一个。 最终用户或 WNS 可以将超过该频率的例常更新确定为滥发更新。
-   Windows 通知平台使用 WNS 维护定期数据连接，使套接字保持活动状态和正常运行。 如果没有应用程序请求或使用通知通道，则不会创建套接字。

## <a name="expiration-of-tile-and-badge-notifications"></a>磁贴和锁屏提醒通知过期时间

默认情况下，磁贴和徽标通知在下载完成时的三天后过期。 通知过期时，此内容将从磁贴或队列中删除，且不再向用户显示。 最佳做法是在所有磁贴和锁屏提醒通知上设置过期时间（使用对你的应用有意义的时间），以便使磁贴的内容不会在它不相关时继续保留。 对于具有已定义的使用寿命的内容来说，显式过期时间是必需的。 这还确保在你的云服务停止发送通知或用户在长时间内与网络断开连接时删除过时的内容。

你的云服务可以为每个通知设置一个过期时间，方法是设置 X-WNS-TTL HTTP 标头以指定通知在发送后保持有效的时间（以秒为单位）。 有关详细信息，请参阅 [推送通知服务请求和响应标头](/previous-versions/windows/apps/hh465435(v=win.10))。

例如，股票市场活跃交易日期间，你可将股票价格更新到期时间设置为发送间隔的两倍（例如，如果是每半小时发送一次通知，则将股票价格更新到期时间设置为一小时）。 另一个示例是，新闻应用可确定每日新闻磁贴更新的适当到期时间为一天。

## <a name="push-notifications-and-battery-saver"></a>推送通知和节电模式

节电模式可通过限制设备上的后台活动，延长电池使用时间。 Windows 10 允许用户设置在电量降低至特定阈值时，自动打开节电模式。 在节电模式处于打开状态时，将禁用推送消息接收，以节省电量。 但是也有几种例外情况。 以下 Windows 10 节电模式设置（见**设置**应用）允许应用即使在节电模式打开时，也可以接收推送通知。

-   **允许在节电模式下接收任何应用的推送通知**：此设置允许所有应用在节电模式处于打开状态时接收推送通知。 请注意，此设置仅适用于 Windows 10 桌面版（家庭版、专业版、企业版和教育版）。
-   **始终允许**：此设置允许在节电模式处于打开状态时在后台运行特定应用，包括接收推送通知。 此列表由用户手动维护。

此两种设置的状态无法检查，但可以检查节电模式的状态。 在 Windows 10 中，使用 [**EnergySaverStatus**](/uwp/api/Windows.System.Power.PowerManager.EnergySaverStatus) 属性检查节电模式状态。 应用也可以使用 [**EnergySaverStatusChanged**](/uwp/api/Windows.System.Power.PowerManager.EnergySaverStatusChanged) 事件侦听对节电模式的更改。

如果应用严重依赖推送通知，我们建议通知用户，在节电模式打开时，他们可能无法接收通知，并让他们可以轻松地调整**节电模式设置**。 通过在 Windows 10 `ms-settings:batterysaver-settings` 中使用节电模式设置 URI 架构，可以提供指向“设置”应用的简便链接。

> [!TIP]
> 当向用户通知节电的设置时，建议提供一种方法来在将来禁止显示该消息。 例如，以下示例中的 `dontAskMeAgainBox` 复选框保留用户在 [**LocalSettings**](/uwp/api/Windows.Storage.ApplicationData.LocalSettings) 中的首选项。

下面是一个示例，说明如何在 Windows 10 中检查节电功能是否已打开。 此示例将通知用户，并将“设置”应用启动到**节电模式设置**。 `dontAskAgainSetting` 允许用户在不希望再次收到通知时阻止消息。

```csharp
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;
using Windows.System;
using Windows.System.Power;
...
...
async public void CheckForEnergySaving()
{
   //Get reminder preference from LocalSettings
   bool dontAskAgain;
   var localSettings = Windows.Storage.ApplicationData.Current.LocalSettings;
   object dontAskSetting = localSettings.Values["dontAskAgainSetting"];
   if (dontAskSetting == null)
   {  // Setting does not exist
      dontAskAgain = false;
   }
   else
   {  // Retrieve setting value
      dontAskAgain = Convert.ToBoolean(dontAskSetting);
   }
   
   // Check if battery saver is on and that it's okay to raise dialog
   if ((PowerManager.EnergySaverStatus == EnergySaverStatus.On)
         && (dontAskAgain == false))
   {
      // Check dialog results
      ContentDialogResult dialogResult = await saveEnergyDialog.ShowAsync();
      if (dialogResult == ContentDialogResult.Primary)
      {
         // Launch battery saver settings (settings are available only when a battery is present)
         await Launcher.LaunchUriAsync(new Uri("ms-settings:batterysaver-settings"));
      }

      // Save reminder preference
      if (dontAskAgainBox.IsChecked == true)
      {  // Don't raise dialog again
         localSettings.Values["dontAskAgainSetting"] = "true";
      }
   }
}
```

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Storage.h>
#include <winrt/Windows.System.h>
#include <winrt/Windows.System.Power.h>
#include <winrt/Windows.UI.Xaml.h>
#include <winrt/Windows.UI.Xaml.Controls.h>
#include <winrt/Windows.UI.Xaml.Navigation.h>
using namespace winrt;
using namespace winrt::Windows::Foundation;
using namespace winrt::Windows::Storage;
using namespace winrt::Windows::System;
using namespace winrt::Windows::System::Power;
using namespace winrt::Windows::UI::Xaml;
using namespace winrt::Windows::UI::Xaml::Controls;
using namespace winrt::Windows::UI::Xaml::Navigation;
...
winrt::fire_and_forget CheckForEnergySaving()
{
    // Get reminder preference from LocalSettings.
    bool dontAskAgain{ false };
    auto localSettings = ApplicationData::Current().LocalSettings();
    IInspectable dontAskSetting = localSettings.Values().Lookup(L"dontAskAgainSetting");
    if (!dontAskSetting)
    {
        // Setting doesn't exist.
        dontAskAgain = false;
    }
    else
    {
        // Retrieve setting value
        dontAskAgain = winrt::unbox_value<bool>(dontAskSetting);
    }

    // Check whether battery saver is on, and whether it's okay to raise dialog.
    if ((PowerManager::EnergySaverStatus() == EnergySaverStatus::On) && (!dontAskAgain))
    {
        // Check dialog results.
        ContentDialogResult dialogResult = co_await saveEnergyDialog().ShowAsync();
        if (dialogResult == ContentDialogResult::Primary)
        {
            // Launch battery saver settings
            // (settings are available only when a battery is present).
            co_await Launcher::LaunchUriAsync(Uri(L"ms-settings:batterysaver-settings"));
        }

        // Save reminder preference.
        if (dontAskAgainBox().IsChecked())
        {
            // Don't raise the dialog again.
            localSettings.Values().Insert(L"dontAskAgainSetting", winrt::box_value(true));
        }
    }
}
```

这是适用于此示例中重点介绍的 [**ContentDialog**](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog) 的 XAML。

```xaml
<ContentDialog x:Name="saveEnergyDialog"
               PrimaryButtonText="Open battery saver settings"
               SecondaryButtonText="Ignore"
               Title="Battery saver is on."> 
   <StackPanel>
      <TextBlock TextWrapping="WrapWholeWords">
         <LineBreak/><Run>Battery saver is on and you may 
          not receive push notifications.</Run><LineBreak/>
         <LineBreak/><Run>You can choose to allow this app to work normally
         while in battery saver, including receiving push notifications.</Run>
         <LineBreak/>
      </TextBlock>
      <CheckBox x:Name="dontAskAgainBox" Content="OK, got it."/>
   </StackPanel>
</ContentDialog>
```

## <a name="related-topics"></a>相关主题

* [发送本地磁贴通知](sending-a-local-tile-notification.md)
* [快速入门：发送推送通知](/previous-versions/windows/apps/hh868252(v=win.10))
* [如何通过推送通知更新锁屏提醒](/previous-versions/windows/apps/hh465450(v=win.10))
* [如何请求、创建和保存通知通道](/previous-versions/windows/apps/hh465412(v=win.10))
* [如何为正在运行的应用程序截获通知](/previous-versions/windows/apps/jj709907(v=win.10))
* [如何使用 Windows 推送通知服务 (WNS) 进行验证](/previous-versions/windows/apps/hh465407(v=win.10))
* [推送通知服务请求和响应头](/previous-versions/windows/apps/hh465435(v=win.10))
* [推送通知指南和清单]()
* [原始通知](/previous-versions/windows/apps/hh761488(v=win.10))