---
description: 了解如何使用通知侦听器访问用户的所有通知。
title: 通知侦听器
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Chaseable tiles
template: detail.hbs
ms.date: 06/13/2017
ms.topic: article
keywords: windows 10, uwp, 通知侦听器, usernotificationlistener, 文档, 访问通知
ms.localizationpriority: medium
ms.openlocfilehash: 90d1d0757bad5a62a987144e6a81d0a6550db384
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93033660"
---
# <a name="notification-listener-access-all-notifications"></a>通知侦听器：访问所有通知

通知侦听器提供对用户通知的访问。 Smartwatch 和其他可穿戴设备可以使用通知侦听器将手机通知发送到可穿戴设备。 在收到通知时，Home 自动化应用程序可以使用通知侦听器来执行特定操作，例如，当你收到呼叫时使灯闪烁。 

> [!IMPORTANT]
> **需要周年更新** ：目标必须为 SDK 14393，并且必须运行版本 14393 或更高版本才能使用通知侦听器。


> **重要 API** ： [UserNotificationListener 类](/uwp/api/Windows.UI.Notifications.Management.UserNotificationListener)、 [UserNotificationChangedTrigger 类](/uwp/api/Windows.ApplicationModel.Background.UserNotificationChangedTrigger)


## <a name="enable-the-listener-by-adding-the-user-notification-capability"></a>通过添加用户通知功能支持侦听器 

要使用通知侦听器，必须在应用清单中添加用户通知侦听器功能。

1. 在 Visual Studio 中的解决方案资源管理器中，双击 `Package.appxmanifest` 文件，以打开清单设计器。
2. 打开“功能”选项卡。
3. 检查 **用户通知侦听器** 功能。


## <a name="check-whether-the-listener-is-supported"></a>检查侦听器是否受支持

如果应用支持较旧版本的 Windows 10，则需要使用 [ApiInformation class](/uwp/api/Windows.Foundation.Metadata.ApiInformation) 检查侦听器是否受支持。  如果侦听器不受支持，请避免对侦听器 API 执行任何调用。

```csharp
if (ApiInformation.IsTypePresent("Windows.UI.Notifications.Management.UserNotificationListener"))
{
    // Listener supported!
}
 
else
{
    // Older version of Windows, no Listener
}
```


## <a name="requesting-access-to-the-listener"></a>请求访问侦听器

由于侦听器允许访问用户通知，用户必须为引用提供访问其通知的权限。 在应用首次运行期间，应该请求使用通知侦听器的访问权限。 如果需要，可以在调用 [RequestAccessAsync](/uwp/api/windows.ui.notifications.management.usernotificationlistener.RequestAccessAsync) 之前显示一个比较初步的 UI，来说明为何应用需要能够访问用户通知，以便用户了解为什么他们应该允许应用访问。

```csharp
// Get the listener
UserNotificationListener listener = UserNotificationListener.Current;
 
// And request access to the user's notifications (must be called from UI thread)
UserNotificationListenerAccessStatus accessStatus = await listener.RequestAccessAsync();
 
switch (accessStatus)
{
    // This means the user has granted access.
    case UserNotificationListenerAccessStatus.Allowed:
 
        // Yay! Proceed as normal
        break;
 
    // This means the user has denied access.
    // Any further calls to RequestAccessAsync will instantly
    // return Denied. The user must go to the Windows settings
    // and manually allow access.
    case UserNotificationListenerAccessStatus.Denied:
 
        // Show UI explaining that listener features will not
        // work until user allows access.
        break;
 
    // This means the user closed the prompt without
    // selecting either allow or deny. Further calls to
    // RequestAccessAsync will show the dialog again.
    case UserNotificationListenerAccessStatus.Unspecified:
 
        // Show UI that allows the user to bring up the prompt again
        break;
}
```

用户随时可以通过 Windows“设置”撤销访问权限。 因此，应用程序应始终在执行使用通知侦听器的代码之前通过 [GetAccessStatus](/uwp/api/windows.ui.notifications.management.usernotificationlistener.GetAccessStatus) 方法检查访问状态。 如果用户撤销访问权限，API 将在不发出提示的情况下失败，而不引发异常（例如，用于获取所有通知的 API 将只返回空列表）。


## <a name="access-the-users-notifications"></a>访问用户通知

使用通知侦听器，可以获得用户当前通知的列表。 只需调用 [GetNotificationsAsync](/uwp/api/windows.ui.notifications.management.usernotificationlistener.GetNotificationsAsync) 方法，并指定你想获取的通知类型（目前受支持的通知类型只有 toast 通知）。

```csharp
// Get the toast notifications
IReadOnlyList<UserNotification> notifs = await listener.GetNotificationsAsync(NotificationKinds.Toast);
```


## <a name="displaying-the-notifications"></a>显示通知

每个通知都以 [UserNotification](/uwp/api/windows.ui.notifications.usernotification) 形式表示，它提供有关发出通知的应用的信息、创建通知的时间、通知 ID 以及通知本身。

```csharp
public sealed class UserNotification
{
    public AppInfo AppInfo { get; }
    public DateTimeOffset CreationTime { get; }
    public uint Id { get; }
    public Notification Notification { get; }
}
```

[AppInfo](/uwp/api/windows.ui.notifications.usernotification.AppInfo) 属性提供显示通知所需要的信息。

> [!NOTE]
> 我们建议将所有处理单个通知的代码放入 try/catch，以免在捕获单个通知时发生意外异常。 切不可只因为一个特定通知出现问题就不能显示其他通知。

```csharp
// Select the first notification
UserNotification notif = notifs[0];
 
// Get the app's display name
string appDisplayName = notif.AppInfo.DisplayInfo.DisplayName;
 
// Get the app's logo
BitmapImage appLogo = new BitmapImage();
RandomAccessStreamReference appLogoStream = notif.AppInfo.DisplayInfo.GetLogo(new Size(16, 16));
await appLogo.SetSourceAsync(await appLogoStream.OpenReadAsync());
```

通知本身的内容（如通知文本）包含在 [Notification](/uwp/api/windows.ui.notifications.usernotification.Notification) 属性中。 此属性包含通知的可视部分。 （如果对在 Windows 上发送通知很熟悉，可以注意到 [Notification](/uwp/api/windows.ui.notifications.notification) 对象中的 [Visual](/uwp/api/windows.ui.notifications.notification.Visual) 和 [Visual.Bindings](/uwp/api/windows.ui.notifications.notificationvisual.Bindings) 属性对应于开发人员在弹出通知时发送的内容。）

我们需要寻找 toast 绑定（对于错误证明代码，应检查绑定是否为 null）。 你可以从绑定中获取文本元素。 可以选择显示任意多的文本元素。 （理想情况下应全部显示。）可以选择对文本元素区别处理；例如，将第一个文本视为标题文本，将后续元素视为正文文本。

```csharp
// Get the toast binding, if present
NotificationBinding toastBinding = notif.Notification.Visual.GetBinding(KnownNotificationBindings.ToastGeneric);
 
if (toastBinding != null)
{
    // And then get the text elements from the toast binding
    IReadOnlyList<AdaptiveNotificationText> textElements = toastBinding.GetTextElements();
 
    // Treat the first text element as the title text
    string titleText = textElements.FirstOrDefault()?.Text;
 
    // We'll treat all subsequent text elements as body text,
    // joining them together via newlines.
    string bodyText = string.Join("\n", textElements.Skip(1).Select(t => t.Text));
}
```


## <a name="remove-a-specific-notification"></a>删除特定的通知

如果可穿戴设备或服务允许用户消除通知，则可以删除实际的通知，这样用户以后就不会在其手机或电脑上看到它。 只需提供需要删除的通知的通知 ID（从 [UserNotification](/uwp/api/windows.ui.notifications.usernotification) 对象获得）： 

```csharp
// Remove the notification
listener.RemoveNotification(notifId);
```


## <a name="clear-all-notifications"></a>清除所有通知

[UserNotificationListener.ClearNotifications](/uwp/api/windows.ui.notifications.management.usernotificationlistener.ClearNotifications)方法可清除所有的用户通知。 请谨慎使用此方法。 仅当可穿戴设备或服务显示所有通知时才应清除所有通知。 如果可穿戴设备或服务仅显示某些通知，则当用户单击“清除通知”按钮时，该用户只希望删除这些特定通知；但是，调用 [ClearNotifications](/uwp/api/windows.ui.notifications.management.usernotificationlistener.ClearNotifications) 方法实际上会导致所有通知（包括可穿戴设备或服务未显示的通知）被删除。

```csharp
// Clear all notifications. Use with caution.
listener.ClearNotifications();
```


## <a name="background-task-for-notification-addeddismissed"></a>已添加/已消除的通知的后台任务

支持应用侦听通知的一种常用方法是设置后台任务，这样，无论应用当前是否在运行，你都知道何时添加或消除了通知。

由于在周年更新中增加了[单进程模型](../../../launch-resume/create-and-register-an-inproc-background-task.md)，添加后台任务变得相当简单。 在主应用代码中，在获得对通知侦听器的用户访问权限和运行后台任务的权限后，只需注册一个新后台任务，并使用 [Toast 通知类型](/uwp/api/windows.ui.notifications.notificationkinds)设置  [UserNotificationChangedTrigger](/uwp/api/windows.applicationmodel.background.usernotificationchangedtrigger)。

```csharp
// TODO: Request/check Listener access via UserNotificationListener.Current.RequestAccessAsync
 
// TODO: Request/check background task access via BackgroundExecutionManager.RequestAccessAsync
 
// If background task isn't registered yet
if (!BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name.Equals("UserNotificationChanged")))
{
    // Specify the background task
    var builder = new BackgroundTaskBuilder()
    {
        Name = "UserNotificationChanged"
    };
 
    // Set the trigger for Listener, listening to Toast Notifications
    builder.SetTrigger(new UserNotificationChangedTrigger(NotificationKinds.Toast));
 
    // Register the task
    builder.Register();
}
```

然后，在 App.xaml.cs 中，覆盖 [OnBackgroundActivated](/uwp/api/windows.ui.xaml.application.OnBackgroundActivated) 方法（如果尚未覆盖），然后在任务名称中使用切换语句来确定调用了众多后台任务触发器中的哪一个。

```csharp
protected override async void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    var deferral = args.TaskInstance.GetDeferral();
 
    switch (args.TaskInstance.Task.Name)
    {
        case "UserNotificationChanged":
            // Call your own method to process the new/removed notifications
            // The next section of documentation discusses this code
            await MyWearableHelpers.SyncNotifications();
            break;
    }
 
    deferral.Complete();
}
```

后台任务只是“即时点击”：它不会提供有关添加或删除了哪个具体通知的任何信息。 触发后台任务时，应在同步可穿戴设备上的通知，以反映平台中的通知。 这可以确保在后台任务失败时，在后台任务下一次执行时仍然可以恢复可穿戴设备上的通知。

`SyncNotifications` 是您实现的方法;下一节将演示如何进行操作。 


## <a name="determining-which-notifications-were-added-and-removed"></a>确定添加和删除了哪些通知

在 `SyncNotifications` 方法中，要确定添加或删除了哪些通知（与可穿戴设备同步），必须计算当前通知集合与平台通知之间的差异。

```csharp
// Get all the current notifications from the platform
IReadOnlyList<UserNotification> userNotifications = await listener.GetNotificationsAsync(NotificationKinds.Toast);
 
// Obtain the notifications that our wearable currently has displayed
IList<uint> wearableNotificationIds = GetNotificationsOnWearable();
 
// Copy the currently displayed into a list of notification ID's to be removed
var toBeRemoved = new List<uint>(wearableNotificationIds);
 
// For each notification in the platform
foreach (UserNotification userNotification in userNotifications)
{
    // If we've already displayed this notification
    if (wearableNotificationIds.Contains(userNotification.Id))
    {
        // We want to KEEP it displayed, so take it out of the list
        // of notifications to remove.
        toBeRemoved.Remove(userNotification.Id);
    }
 
    // Otherwise it's a new notification
    else
    {
        // Display it on the Wearable
        SendNotificationToWearable(userNotification);
    }
}
 
// Now our toBeRemoved list only contains notification ID's that no longer exist in the platform.
// So we will remove all those notifications from the wearable.
foreach (uint id in toBeRemoved)
{
    RemoveNotificationFromWearable(id);
}
```


## <a name="foreground-event-for-notification-addeddismissed"></a>已添加/已消除的通知的前台事件

> [!IMPORTANT] 
> 已知问题：在生成 17763/10 月2018更新/版本1809之前的内部版本中，前台事件将导致 CPU 循环和/或工作不起作用。 如果需要更早版本的支持，请改用后台任务。

还可以从内存中事件处理程序侦听通知 .。。

```csharp
// Subscribe to foreground event
listener.NotificationChanged += Listener_NotificationChanged;
 
private void Listener_NotificationChanged(UserNotificationListener sender, UserNotificationChangedEventArgs args)
{
    // Your code for handling the notification
}
```


## <a name="how-to-fix-delays-in-the-background-task"></a>如何修复后台任务的延迟问题

测试应用时，你可能会注意到后台任务有时会延迟，并且不会触发几分钟时间。 若要解决此延迟，请提示用户中转到 "系统设置-> 系统"-> > 电池-按应用程序使用电池 "，在列表中找到你的应用程序，选择它，然后将其设置为" 在后台始终允许 "。 此后，后台任务应该始终在收到通知后一秒内触发。
