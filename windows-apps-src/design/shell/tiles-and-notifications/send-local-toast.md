---
description: '了解如何从 c # 应用程序发送本地 toast 通知，并处理用户单击 toast 的操作。'
title: '从 c # 应用发送本地 toast 通知'
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification from C# apps
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: 'windows 10，uwp，发送 toast 通知，通知，发送通知，toast 通知，如何，快速入门，入门，代码示例，演练，c #，csharp，win32，桌面'
ms.localizationpriority: medium
ms.openlocfilehash: 23896b65bf2e4e0a9fc2edf5744b647d6d9c26ed
ms.sourcegitcommit: 6cd970686d1ea7176b7e6651f349a14551709820
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559402"
---
# <a name="send-a-local-toast-notification-from-c-apps"></a>从 c # 应用发送本地 toast 通知

[!INCLUDE [intro](includes/send-toast-intro.md)]

> [!IMPORTANT]
> 如果要编写 c + + 应用程序，请参阅 [c + + UWP](send-local-toast-cpp-uwp.md) 或 [c + + WRL](send-local-toast-desktop-cpp-wrl.md) 文档。


## <a name="step-1-install-nuget-package"></a>步骤1：安装 NuGet 包

[!INCLUDE [nuget package](includes/nuget-package.md)]

[!INCLUDE [nuget package .NET warnings](includes/nuget-package-dotnet-warnings.md)]

我们的代码示例将使用此包。 使用此包可以创建 toast 通知，而无需使用 XML，还允许桌面应用发送 toast。


## <a name="step-2-send-a-toast"></a>步骤2：发送 toast

[!INCLUDE [basic toast intro](includes/send-toast-basic-toast-intro.md)]

```csharp
// Requires Microsoft.Toolkit.Uwp.Notifications NuGet package version 7.0 or greater
new ToastContentBuilder()
    .AddArgument("action", "viewConversation")
    .AddArgument("conversationId", 9813)
    .AddText("Andrew sent you a picture")
    .AddText("Check this out, The Enchantments in Washington!")
    .Show(); // Not seeing the Show() method? Make sure you have version 7.0, and if you're using .NET 5, your TFM must be net5.0-windows10.0.17763.0 or greater
```

尝试运行此代码，你应看到通知已出现！


## <a name="step-3-handling-activation"></a>步骤3：处理激活

显示通知后，你可能需要处理用户单击通知 (这意味着在用户单击后显示特定内容、在一般情况下打开你的应用，或在用户单击通知) 时执行操作。

用于处理激活的步骤不同于 UWP、桌面 (.MSIX) 和桌面 (未打包) 应用。


#### <a name="uwp"></a>[UWP](#tab/uwp)

当用户单击你的通知 (或使用前台激活) 通知上的按钮时，将调用应用 **的** **OnActivated** ，并将返回你添加的参数。

**App.xaml.cs**

```csharp
protected override void OnActivated(IActivatedEventArgs e)
{
    // Handle notification activation
    if (e is ToastNotificationActivatedEventArgs toastActivationArgs)
    {
        // Obtain the arguments from the notification
        ToastArguments args = ToastArguments.Parse(toastActivationArgs.Argument);

        // Obtain any user input (text boxes, menu selections) from the notification
        ValueSet userInput = toastActivationArgs.UserInput;
 
        // TODO: Show the corresponding content
    }
}
```

[!INCLUDE [OnLaunched warning](includes/onlaunched-warning.md)]

#### <a name="desktop-msix"></a>[桌面 (.MSIX) ](#tab/desktop-msix)

首先，在 **appxmanifest.xml** 中添加：

1. **xmlns:com** 声明
1. **xmlns:desktop** 声明
1. 在 **IgnorableNamespaces** 属性中，添加 **com** 和 **desktop**
1. **desktop：** **toastNotificationActivation** 的扩展使用所选) 的新 GUID 声明 toast 激活器 CLSID (。
1. 仅限 .MSIX：使用步骤 #4 中的 GUID 对 COM 激活器使用 **com： Extension** 。 请确保包含， `Arguments="-ToastActivated"` 以便了解你的启动来自通知

**Package.appxmanifest**

```xml
<!--Add these namespaces-->
<Package
  ...
  xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="... com desktop">
  ...
  <Applications>
    <Application>
      ...
      <Extensions>

        <!--Specify which CLSID to activate when toast clicked-->
        <desktop:Extension Category="windows.toastNotificationActivation">
          <desktop:ToastNotificationActivation ToastActivatorCLSID="replaced-with-your-guid-C173E6ADF0C3" /> 
        </desktop:Extension>

        <!--Register COM CLSID LocalServer32 registry key-->
        <com:Extension Category="windows.comServer">
          <com:ComServer>
            <com:ExeServer Executable="YourProject\YourProject.exe" Arguments="-ToastActivated" DisplayName="Toast activator">
              <com:Class Id="replaced-with-your-guid-C173E6ADF0C3" DisplayName="Toast activator"/>
            </com:ExeServer>
          </com:ComServer>
        </com:Extension>

      </Extensions>
    </Application>
  </Applications>
 </Package>
```

然后， **在应用程序的启动代码** (for WPF) 中，订阅 OnActivated 事件。

[!INCLUDE [desktop toast activation sequence](includes/desktop-toast-activation-code.md)]


[!INCLUDE [desktop toast activation sequence](includes/desktop-toast-activation-sequence.md)]


#### <a name="desktop-unpackaged"></a>[桌面 (未打包) ](#tab/desktop)

[!INCLUDE [desktop toast activation sequence](includes/desktop-toast-activation-sequence.md)]

**在应用程序的启动代码** (WPF) 中，订阅 OnActivated 事件。

[!INCLUDE [desktop toast activation sequence](includes/desktop-toast-activation-code.md)]

---


## <a name="step-4-handling-uninstallation"></a>步骤4：处理卸载

#### <a name="uwp"></a>[UWP](#tab/uwp)

无需执行任何操作！ 卸载 UWP 应用时，将自动清除所有通知和任何其他相关资源。

#### <a name="desktop-msix"></a>[桌面 (.MSIX) ](#tab/desktop-msix)

无需执行任何操作！ 卸载 .MSIX 应用时，会自动清除所有通知和任何其他相关资源。

#### <a name="desktop-unpackaged"></a>[桌面 (未打包) ](#tab/desktop)

如果你的应用程序具有卸载程序，你应在卸载程序中调用 `ToastNotificationManagerCompat.Uninstall();` 。 如果你的应用程序是无需安装程序的 "可移植应用程序"，请考虑在应用程序退出时调用此方法，除非你有要在应用程序关闭后保存的通知。

Uninstall 方法将清除任何计划的和当前通知、删除任何关联的注册表值并删除库创建的任何关联的临时文件。

---


## <a name="adding-images"></a>添加图像

可以向通知添加丰富的内容。 我们会将一个内联图像和一个配置文件 (应用徽标覆盖) 映像。

[!INCLUDE [images note](includes/images-note.md)]

> [!IMPORTANT]
> Http 映像仅在其清单中具有 internet 功能的 UWP/.MSIX/稀疏应用中受支持。 桌面非 .MSIX/稀疏应用不支持 http 映像;必须将映像下载到本地应用数据，并在本地引用它。

<img alt="Toast with images" src="images/send-toast-02.png" width="364"/>

```csharp
// Construct the content and show the toast!
new ToastContentBuilder()
    ...

    // Inline image
    .AddInlineImage(new Uri("https://picsum.photos/360/202?image=883"))

    // Profile (app logo override) image
    .AddAppLogoOverride(new Uri("ms-appdata:///local/Andrew.jpg"), ToastGenericAppLogoCrop.Circle)
    
    .Show();
```



## <a name="adding-buttons-and-inputs"></a>添加按钮和输入

你可以添加按钮和输入以使你的通知交互。 按钮可以启动前景应用、协议或后台任务。 我们将添加一个 "答复" 文本框、一个 "赞" 按钮和一个打开该图像的 "视图" 按钮。

<img src="images/toast-notification.png" width="628" alt="Screenshot of a toast notification with inputs and buttons"/>

```csharp
int conversationId = 384928;

// Construct the content
new ToastContentBuilder()
    .AddArgument("conversationId", conversationId)
    ...

    // Text box for replying
    .AddInputTextBox("tbReply", placeHolderContent: "Type a response")

    // Buttons
    .AddButton(new ToastButton()
        .SetContent("Reply")
        .AddArgument("action", "reply")
        .SetBackgroundActivation())

    .AddButton(new ToastButton()
        .SetContent("Like")
        .AddArgument("action", "like")
        .SetBackgroundActivation())

    .AddButton(new ToastButton()
        .SetContent("View")
        .AddArgument("action", "viewImage")
        .AddArgument("imageUrl", image.ToString()))
    
    .Show();
```

对前台按钮的激活方式的处理方式与应用程序 (主 toast 正文相同。 OnActivated 将) 调用。

请注意，在单击按钮时，还会返回添加到顶层 toast (（例如会话 ID) ）的参数，只要按钮使用 AddArgument API （如上所示） (如果你在按钮上自定义了赋值参数，则不会将顶级参数包括在) 中。



## <a name="handling-background-activation"></a>处理后台激活

#### <a name="uwp"></a>[UWP](#tab/uwp)

当你对你的 toast（或 toast 内的按钮）指定后台激活时，将执行后台任务而不是激活前台应用。

有关后台任务的详细信息，请参阅[使用后台任务支持应用](../../../launch-resume/support-your-app-with-background-tasks.md)。

如果你的目标版本是 14393 或更高版本，则可以使用进程内后台任务，这样可大大简化操作。 请注意，无法在较旧版本的 Windows 上运行进程内后台任务。 在此代码示例中，将使用进程内后台任务。

```csharp
const string taskName = "ToastBackgroundTask";

// If background task is already registered, do nothing
if (BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name.Equals(taskName)))
    return;

// Otherwise request access
BackgroundAccessStatus status = await BackgroundExecutionManager.RequestAccessAsync();

// Create the background task
BackgroundTaskBuilder builder = new BackgroundTaskBuilder()
{
    Name = taskName
};

// Assign the toast action trigger
builder.SetTrigger(new ToastNotificationActionTrigger());

// And register the task
BackgroundTaskRegistration registration = builder.Register();
```


然后在应用程序中，重写 OnBackgroundActivated 方法。 然后，你可以检索预定义参数和用户输入，类似于前台激活。

**App.xaml.cs**

```csharp
protected override async void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    var deferral = args.TaskInstance.GetDeferral();
 
    switch (args.TaskInstance.Task.Name)
    {
        case "ToastBackgroundTask":
            var details = args.TaskInstance.TriggerDetails as ToastNotificationActionTriggerDetail;
            if (details != null)
            {
                ToastArguments arguments = ToastArguments.Parse(details.Argument);
                var userInput = details.UserInput;

                // Perform tasks
            }
            break;
    }
 
    deferral.Complete();
}
```

#### <a name="desktop"></a>[桌面](#tab/desktop-msix+desktop)

对于桌面应用程序，后台激活的处理方式与前台激活相同 (**OnActivated** 事件处理程序将) 触发。 您可以选择不显示任何 UI，并在处理激活后关闭应用程序。

---


## <a name="set-an-expiration-time"></a>设置过期时间

在 Windows 10 中，所有 toast 通知被用户消除或忽略后将转到操作中心，以便在弹出窗口消失后，用户仍可查看通知。

但是，如果你的通知中的消息仅在一段时间内相关，则应对 toast 通知设置过期时间，让用户不至于看到来自应用的过时信息。 例如，如果升级的有效时间仅为 12 个小时，则将过期时间设置为 12 个小时。 下面的代码中将过期时间设置为 2 天。

> [!NOTE]
> 本地 toast 通知的默认和最长过期时间为 3 天。

```csharp
// Create toast content and show the toast!
new ToastContentBuilder()
    .AddText("Expires in 2 days...")
    .Show(toast =>
    {
        toast.ExpirationTime = DateTime.Now.AddDays(2);
    });
```


## <a name="provide-a-primary-key-for-your-toast"></a>为 toast 提供主键

如要以编程方式删除或替换发送的通知，需使用 Tag 属性（还可选择使用 Group 属性）来为通知提供主键。 然后，你可以在以后使用此主键来删除或替换该通知。

要查看有关替换/删除已发送的 toast 通知的更多详细信息，请参阅[快速入门：在操作中心 (XAML) 中管理 toast 通知](/previous-versions/windows/apps/dn631260(v=win.10))。

Tag 和 Group 组合充当复合主键。 Group 是两者中较为通用的标识符，你可以用它来分配如“wallPosts”、“messages”、“friendRequests”等组。而 Tag 应该唯一标识组中的通知本身。 使用通用组时，可以使用 [RemoveGroup API](/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_) 删除该组中的所有通知。

```csharp
// Create toast content and show the toast!
new ToastContentBuilder()
    .AddText("New post on your wall!")
    .Show(toast =>
    {
        toast.Tag = "18365";
        toast.Group = "wallPosts";
    });
```



## <a name="clear-your-notifications"></a>清除你的通知

应用负责删除和清除自己的通知。 当你的应用启动时，我们不会自动清除你的通知。

仅当用户显式单击通知时，Windows 才会自动删除该通知。

下面是消息传递应用应执行的操作的示例...

1. 用户收到关于对话中新消息的多个 toast
2. 用户点击其中一个 toast 以打开该对话
3. 应用打开该对话，然后清除该对话的所有 toast（方法是对该对话的应用提供的组使用 [RemoveGroup](/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_)）
4. 用户的操作中心现在能正确反映通知状态，因为操作中心未留有该对话的过期通知。

若要了解有关清除所有通知或删除特定通知的信息，请参阅[快速入门：在操作中心 (XAML) 中管理 toast 通知](/previous-versions/windows/apps/dn631260(v=win.10))。

```csharp
ToastNotificationManagerCompat.History.Clear();
```



## <a name="resources"></a>资源

* [GitHub 上的完整代码示例](https://github.com/WindowsNotifications/quickstart-sending-local-toast-win10)
* [toast 内容文档](adaptive-interactive-toasts.md)
* [ToastNotification 类](/uwp/api/Windows.UI.Notifications.ToastNotification)
* [ToastNotificationActivatedEventArgs 类](/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)
