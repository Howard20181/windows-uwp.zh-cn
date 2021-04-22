---
title: 即便跨设备，也继续用户活动
description: 本主题介绍如何帮助用户继续执行之前其在应用中，甚至是在多台设备中所执行的操作。
keywords: 用户活动, 时间线, cortana 从你离开的位置继续, cortana 从我离开的位置继续, project rome
ms.date: 04/27/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 45a1d1d319f67dfac5e7c44a4c65c3bf2e3c0335
ms.sourcegitcommit: 73ec979ce6b9701e7135fd0541bf932b0847908e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881690"
---
# <a name="continue-user-activity-even-across-devices"></a>即便跨设备，也继续用户活动

本主题介绍如何帮助用户继续执行他们之前在其电脑上及不同设备的应用中所执行的操作。

> [!NOTE]
> 从2021年6月开始，具有通过 Microsoft 帐户在其 Windows 设备上同步的活动历史记录的用户将无法再使用时间线上传新活动)  (。 它们仍将能够使用时间线，并查看其活动历史记录 (有关其本地 PC) 的最近应用、网站和文件的信息。 AAD 连接的帐户不会受到影响。 

## <a name="user-activities-and-timeline"></a>用户活动和时间线

我们每天很可能会使用多台设备。 我们可能会在公共汽车上使用手机，白天工作时会使用电脑，晚上休息娱乐时或许会使用手机或平板电脑。 从 Windows 10 1803 版本或更高版本开始，如果创建[用户活动](/uwp/api/windows.applicationmodel.useractivities.useractivity)，该活动会显示在 Windows 时间线和 Cortana 的”从我离开的位置继续”功能中。 时间线是一个丰富的任务视图，它利用“用户活动”显示你正在执行的操作的时间顺序视图。 它还包括你在各种不同设备上执行的操作。

![Windows 时间线映像](images/timeline.png)

同样，将手机链接到 Windows 电脑，可继续执行之前在 iOS 或 Android 设备上的操作。

可将 **UserActivity** 看成是用户在应用中正在处理的具体内容。 例如，如果正在使用 RSS 阅读器，则 **UserActivity** 可以是正在阅读的内容源。 如果正在玩游戏，则 **UserActivity** 可以是正在玩的级别。 如果正在听音乐，则 **UserActivity** 可以是正在收听的播放列表。 如果正在处理文档，则 **UserActivity** 可以是离开文档时的位置，以此类推。  简而言之，**UserActivity** 表示应用内的一个目标，通过它，用户能够继续执行之前执行的操作。

当通过调用 [UserActivity.CreateSession](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession) 参与某个 **UserActivity** 时，系统会创建历史记录，指示该 **UserActivity** 的开始和结束时间。 当随着时间推移不断重新参与该 **UserActivity** 时，系统会记录下多条与之关联的历史记录。

## <a name="add-user-activities-to-your-app"></a>将用户活动添加到应用中

一个 [UserActivity](/uwp/api/windows.applicationmodel.useractivities.useractivity) 是 Windows 中一个用户参与单位。 它包含三个部分：用于激活该活动所属应用的 URI、视觉对象和用于描述该活动的元数据。

1. [ActivationUri](/uwp/api/windows.applicationmodel.useractivities.useractivity.activationuri#Windows_ApplicationModel_UserActivities_UserActivity_ActivationUri) 用于通过特定上下文继续执行应用程序操作。 通常，此链接采用方案的协议处理程序的形式 (例如，"my app：//page2？ action = edit" ) 或 AppUriHandler (，例如 http://constoso.com/page2?action=edit) 。
2. [VisualElements](/uwp/api/windows.applicationmodel.useractivities.useractivity.visualelements) 公开一个类，允许用户使用标题、描述或自适应卡片元素来直观标识活动。
3. 最后，[内容](/uwp/api/windows.applicationmodel.useractivities.useractivityvisualelements.content#Windows_ApplicationModel_UserActivities_UserActivityVisualElements_Content)是可用于存储活动元数据的位置，通过这一位置，可按特定上下文来对活动进行归组和检索。 通常，这会采用数据形式 [https://schema.org](https://schema.org) 。

向应用添加 **UserActivity**：

1. 在应用（例如，页面导航、新游戏级别等）中用户的上下文更改时，生成 **UserActivity** 对象。
2. 使用最少的必填字段填充 **UserActivity** 对象：[ActivityId](/uwp/api/windows.applicationmodel.useractivities.useractivity.activityid#Windows_ApplicationModel_UserActivities_UserActivity_ActivityId)、[ActivationUri](/uwp/api/windows.applicationmodel.useractivities.useractivity.activationuri) 和 [UserActivity.VisualElements.DisplayText](/uwp/api/windows.applicationmodel.useractivities.useractivityvisualelements.displaytext#Windows_ApplicationModel_UserActivities_UserActivityVisualElements_DisplayText)。
3. 向应用添加自定义方案处理程序，以便 **UserActivity** 可以重新激活它。

只需几行代码，即可将 **UserActivity** 集成到应用。 例如，假设此代码在 MainPage 类内的 MainPage.xaml.cs 中（请注意：假设 `using Windows.ApplicationModel.UserActivities;`）：

```csharp
UserActivitySession _currentActivity;
private async Task GenerateActivityAsync()
{
    // Get the default UserActivityChannel and query it for our UserActivity. If the activity doesn't exist, one is created.
    UserActivityChannel channel = UserActivityChannel.GetDefault();
    UserActivity userActivity = await channel.GetOrCreateUserActivityAsync("MainPage");
 
    // Populate required properties
    userActivity.VisualElements.DisplayText = "Hello Activities";
    userActivity.ActivationUri = new Uri("my-app://page2?action=edit");
     
    //Save
    await userActivity.SaveAsync(); //save the new metadata
 
    // Dispose of any current UserActivitySession, and create a new one.
    _currentActivity?.Dispose();
    _currentActivity = userActivity.CreateSession();
}
```

上述 `GenerateActivityAsync()` 方法中的第一行会获取用户的 [UserActivityChannel](/uwp/api/windows.applicationmodel.useractivities.useractivitychannel)。 这是此应用的活动将发布到的源。 下一行查询名为 `MainPage` 的活动的渠道。

* 应用应以后列方式命名活动，即每次用户位于应用中的某个特定位置时都会生成相同的 ID。 例如，如果应用基于页面，则使用该页面的标识符；如果应用基于文档，则使用该文档的名称（或名称的哈希）。
* 如果源中有一个现有的活动具有相同的 ID，则系统将从渠道返回该活动，同时将 `UserActivity.State` 设置为[已发布](/uwp/api/windows.applicationmodel.useractivities.useractivitystate)）。 如果没有具有该名称的活动，则会返回新活动，同时将 `UserActivity.State` 设置为 **新建**。
* 活动的作用域为相应的应用。 不必担心活动 ID 与其他应用中的 ID 产生冲突。

获取或创建 **UserActivity** 后，指定其他两个必填字段：`UserActivity.VisualElements.DisplayText` 和 `UserActivity.ActivationUri`。

接下来，通过调用 [SaveAsync](/uwp/api/windows.applicationmodel.useractivities.useractivity.saveasync) 保存 **UserActivity** 元数据，最后调用会返回 [UserActivitySession](/uwp/api/windows.applicationmodel.useractivities.useractivitysession) 的 [CreateSession](/uwp/api/windows.applicationmodel.useractivities.useractivity.createsession)。 **UserActivitySession** 是一个对象，可用于管理用户的 **UserActivity** 的实际参与情况。 例如，我们应在用户离开页面时，调用 **UserActivitySession** 上的 `Dispose()`。 在以上示例中，我们还在调用 `CreateSession()` 之前调用 `_currentActivity` 上的 `Dispose()`。 这是因为我们使 `_currentActivity` 成为页面的成员字段，并希望先停止任何现有活动再开始新活动（请注意：`?`是在执行成员访问之前测试 null 的 [null 条件运算符](/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-)）。

由于在此情况下，`ActivationUri` 是自定义方案，所以我们还需要在应用程序清单中注册该协议。 这是在 Package.appmanifest XML 文件中，或使用设计器完成的。

若要使用设计器进行更改，可双击项目中的 Package.appmanifest 文件以启动设计器，选择 **声明** 选项卡，并添加 **协议** 定义。 目前仅需要填写 **名称** 属性。 它应该与我们上面指定的 URI `my-app` 相匹配。

现在，我们需要编写一些代码，以指示应用在被协议激活时应执行什么操作。 我们将重写 App.xaml.cs 中的 `OnActivated` 方法，以将 URI 传递到主页，如下所示：

```csharp
protected override void OnActivated(IActivatedEventArgs e)
{
    if (e.Kind == ActivationKind.Protocol)
    {
        var uriArgs = e as ProtocolActivatedEventArgs;
        if (uriArgs != null)
        {
            if (uriArgs.Uri.Host == "page2")
            {
                // Navigate to the 2nd page of the  app
            }
        }
    }
    Window.Current.Activate();
}
```

此代码用于检测应用是否是通过协议激活的。 如果是，它会设法确定应用应执行什么操作来继续之前的任务（应用因此任务被激活）。 作为一个简单的应用程序，此应用程序恢复的唯一活动就是在应用程序启动时将你置于辅助页上。

## <a name="use-adaptive-cards-to-improve-the-timeline-experience"></a>使用自适应卡片来改善时间线体验

用户活动显示在 Cortana 和时间线中。 当活动显示在时间线中时，系统将使用[自适应卡片](https://adaptivecards.io/)框架来显示它们。 如果不为每项活动提供一张自适应卡片，时间线会根据应用程序名称和图标、标题字段和可选说明字段自动创建简单的活动卡。 下面是自适应卡片有效负载和它生成的卡的示例。

![自适应卡片](images/adaptivecard.png)]

自适应卡片有效负载 JSON 字符串的示例：

```json
{ 
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json", 
  "type": "AdaptiveCard", 
  "version": "1.0",
  "backgroundImage": "https://winblogs.azureedge.net/win/2017/11/eb5d872c743f8f54b957ff3f5ef3066b.jpg", 
  "body": [ 
    { 
      "type": "Container", 
      "items": [ 
        { 
          "type": "TextBlock", 
          "text": "Windows Blog", 
          "weight": "bolder", 
          "size": "large", 
          "wrap": true, 
          "maxLines": 3 
        }, 
        { 
          "type": "TextBlock", 
          "text": "Training Haiti’s radiologists: St. Louis doctor takes her teaching global", 
          "size": "default", 
          "wrap": true, 
          "maxLines": 3 
        } 
      ] 
    } 
  ]
}
```

将自适应卡片有效负载作为 JSON 字符串添加到 **UserActivity**，如下所示：

```csharp
activity.VisualElements.Content = 
Windows.UI.Shell.AdaptiveCardBuilder.CreateAdaptiveCardFromJson(jsonCardText); // where jsonCardText is a JSON string that represents the card
```

## <a name="cross-platform-and-service-to-service-integration"></a>跨平台及服务到服务集成

如果应用跨平台（例如，在 Android 和 iOS 上）运行，或在云中维护用户状态，可通过 [Microsoft Graph](https://developer.microsoft.com/graph) 发布 UserActivities。
在使用 Microsoft 帐户对应用程序或服务进行了身份验证后，只需通过两个简单 REST 调用，使用上文所述的相同数据，生成[活动](/graph/api/resources/projectrome-activity)和[历史记录](/graph/api/resources/projectrome-historyitem)对象。

## <a name="summary"></a>摘要

可使用 [UserActivity](/uwp/api/windows.applicationmodel.useractivities) API 在时间线和 Cortana 中显示应用。
* 了解有关 [ **UserActivity** API 的详细信息](/uwp/api/windows.applicationmodel.useractivities)
* 查看[示例代码](https://github.com/Microsoft/project-rome)。
* 请参阅[更复杂的自适应卡片](https://adaptivecards.io/)。
* 通过 [Microsoft Graph](https://developer.microsoft.com/graph) 从 iOS、Android 或 web 服务发布 **UserActivity**。
* 了解有关 [GitHub 上的 Project Rome](https://github.com/Microsoft/project-rome)的详细信息。

## <a name="key-apis"></a>关键 API

* [UserActivities 命名空间](/uwp/api/windows.applicationmodel.useractivities)

## <a name="related-topics"></a>相关主题

* [用户活动 (Project 罗马文档) ](/windows/project-rome/user-activities/)
* [自适应卡](/adaptive-cards/)
* [自适应卡片可视化工具，示例](https://adaptivecards.io/)
* [处理 URI 激活](./handle-uri-activation.md)
* [使用 Microsoft Graph、活动源和自适应卡片在任何平台上与客户互动](https://channel9.msdn.com/Events/Connect/2017/B111)
* [Microsoft Graph](https://developer.microsoft.com/graph)
