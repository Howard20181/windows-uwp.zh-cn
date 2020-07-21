---
ms.assetid: 82ab5fc9-3a7f-4d9e-9882-077ccfdd0ec9
title: 为设备门户编写自定义插件
description: 了解如何编写使用 Windows 设备门户承载网页并提供诊断信息的 UWP 应用。
ms.date: 03/24/2017
ms.topic: article
keywords: windows 10, uwp, 设备门户
ms.localizationpriority: medium
ms.openlocfilehash: 4881fe961979243849728d3f835c449e0f71f4b4
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75683840"
---
# <a name="write-a-custom-plugin-for-device-portal"></a>为设备门户编写自定义插件

了解如何编写使用 Windows 设备门户承载网页并提供诊断信息的 UWP 应用。

从创意者更新开始，可以使用设备门户承载应用的诊断接口。 本文介绍为应用创建 DevicePortalProvider 的三个步骤 - 更改 appxmanifest，设置应用到设备门户服务的连接，以及处理传入请求。 本文还提供了一个用于入门的示例应用（即将推出）。 

## <a name="create-a-new-uwp-app-project"></a>创建新的 UWP 应用项目
在本指南中，为简单起见，我们将在一个解决方案中创建所有内容。

在 Microsoft Visual Studio 2019 中，创建新的 UWP 应用项目。 转到“文件”>“新建”>“项目”，选择“C# 空白应用 (Windows 通用)”，然后单击“下一步”。 在“配置新项目”对话框中， 将项目命名为“DevicePortalProvider”，然后单击“创建”。 这将是包含应用服务的应用。 确保选择“Windows 10 创意者更新(10.0; Build 15063)”以提供支持。  可能需要更新 Visual Studio 或安装新的 SDK - 详细信息见[此处](https://blogs.windows.com/buildingapps/2017/04/05/updating-tooling-windows-10-creators-update/)。 

## <a name="add-the-deviceportalprovider-extension-to-your-packageappxmanifest-file"></a>向 package.appxmanifest 文件添加 devicePortalProvider 扩展
需要向 package.appxmanifest  文件添加一些代码，以便使应用作为设备门户插件正常运行。 首先，在文件顶部添加以下命名空间定义。 还需将它们添加到 `IgnorableNamespaces` 属性。

```xml
<Package
    ... 
    xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
    xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
    IgnorableNamespaces="uap mp rescap uap4">
    ...
```

若要声明应用是设备门户提供程序，需要创建应用服务以及使用它的新设备门户提供程序扩展。 在 `Application` 下的 `Extensions` 元素中添加 windows.appService 和 windows.devicePortalProvider 扩展。 确保每个扩展中的 `AppServiceName` 属性匹配。 这会向设备门户服务表明，可以启动此应用服务以处理 handler 命名空间上的请求。 

```xml
...   
<Application 
    Id="App" 
    Executable="$targetnametoken$.exe"
    EntryPoint="DevicePortalProvider.App">
    ...
    <Extensions>
        <uap:Extension Category="windows.appService" EntryPoint="MySampleProvider.SampleProvider">
            <uap:AppService Name="com.sampleProvider.wdp" />
        </uap:Extension>
        <uap4:Extension Category="windows.devicePortalProvider">
            <uap4:DevicePortalProvider 
                DisplayName="My Device Portal Provider Sample App" 
                AppServiceName="com.sampleProvider.wdp" 
                HandlerRoute="/MyNamespace/api/" />
        </uap4:Extension>
    </Extensions>
</Application>
...
```

`HandlerRoute` 属性引用应用声明的 REST 命名空间。 设备门户服务收到的该命名空间上的任何 HTTP 请求（后跟通配符表示任意请求）都会发送到应用以待处理。 在此情况下，对 `<ip_address>/MyNamespace/api/*` 进行的任何已成功通过身份验证的 HTTP 请求都会发送到应用。 将用“长者优先”的办法来解决处理程序路线之间的冲突，即选择与请求匹配部分较长的路线，这意味着对“/MyNamespace/api/foo”的请求将优先匹配具有“/MyNamespace/api”的提供程序，而不是“/MyNamespace”的。  

此功能需要两个新功能。 它们也必须添加到 package.appxmanifest  文件。

```xml
...
<Capabilities>
    ...
    <Capability Name="privateNetworkClientServer" />
    <rescap:Capability Name="devicePortalProvider" />
</Capabilities>
...
```

> [!NOTE]
> 功能“devicePortalProvider”是受限的（“rescap”），这意味着必须先从 Microsoft Store 获取事先批准，然后才能在其中发布应用。 但是，这不影响你通过旁加载在本地测试应用。 有关受限功能的详细信息，请参阅[应用功能声明](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)。

## <a name="set-up-your-background-task-and-winrt-component"></a>设置后台任务和 WinRT 组件
若要设置设备门户连接，应用必须将来自设备门户服务的应用服务连接与在应用中运行的设备门户实例关联。 若要执行此操作，请使用实现 [IBackgroundTask  ](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask) 的类向应用程序添加新的 WinRT 组件。

```csharp
namespace MySampleProvider {
    // Implementing a DevicePortalConnection in a background task
    public sealed class SampleProvider : IBackgroundTask {
        //...
    }
```

确保其名称与 AppService 入口点（“MySampleProvider.SampleProvider”）设置的命名空间和类名匹配。 对设备门户提供程序进行第一个请求时，设备门户会存储该请求，启动应用的后台任务，调用其 Run  方法，然后传入 [IBackgroundTaskInstance  ](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtaskinstance)。 应用随后使用它设置 [DevicePortalConnection  ](https://docs.microsoft.com/uwp/api/windows.system.diagnostics.deviceportal.deviceportalconnection) 实例。

```csharp
// Implement background task handler with a DevicePortalConnection
public void Run(IBackgroundTaskInstance taskInstance) {
    // Take a deferral to allow the background task to continue executing 
    this.taskDeferral = taskInstance.GetDeferral();
    taskInstance.Canceled += TaskInstance_Canceled;

    // Create a DevicePortal client from an AppServiceConnection 
    var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
    var appServiceConnection = details.AppServiceConnection;
    this.devicePortalConnection = DevicePortalConnection.GetForAppServiceConnection(appServiceConnection);

    // Add Closed, RequestReceived handlers 
    devicePortalConnection.Closed += DevicePortalConnection_Closed;
    devicePortalConnection.RequestReceived += DevicePortalConnection_RequestReceived;
}
```

应用必须处理以下两个事件才能完成请求处理循环：Closed  ，用于设备门户服务关闭时；[RequestReceived  ](https://docs.microsoft.com/uwp/api/windows.system.diagnostics.deviceportal.deviceportalconnectionrequestreceivedeventargs)用于显示传入的 HTTP 请求并提供设备门户提供程序的主要功能。 

## <a name="handle-the-requestreceived-event"></a>处理 RequestReceived 事件
对于在插件的指定处理程序路线上进行的每个 HTTP 请求，会引发一次 RequestReceived  事件。 设备门户提供程序的请求处理循环与 NodeJS Express 的类似：请求和响应对象会随事件一起提供，处理程序会通过填充响应对象进行响应。 在设备门户提供程序中，RequestReceived  事件及其处理程序使用 [Windows.Web.Http.HttpRequestMessage  ](https://docs.microsoft.com/uwp/api/windows.web.http.httprequestmessage) 和 [HttpResponseMessage  ](https://docs.microsoft.com/uwp/api/windows.web.http.httpresponsemessage) 对象。   

```csharp
// Sample RequestReceived echo handler: respond with an HTML page including the query and some additional process information. 
private void DevicePortalConnection_RequestReceived(DevicePortalConnection sender, DevicePortalConnectionRequestReceivedEventArgs args)
{
    var req = args.RequestMessage;
    var res = args.ResponseMessage;

    if (req.RequestUri.AbsolutePath.EndsWith("/echo"))
    {
        // construct an html response message
        string con = "<h1>" + req.RequestUri.AbsoluteUri + "</h1><br/>";
        var proc = Windows.System.Diagnostics.ProcessDiagnosticInfo.GetForCurrentProcess();
        con += String.Format("This process is consuming {0} bytes (Working Set)<br/>", proc.MemoryUsage.GetReport().WorkingSetSizeInBytes);
        con += String.Format("The process PID is {0}<br/>", proc.ProcessId);
        con += String.Format("The executable filename is {0}", proc.ExecutableFileName);
        res.Content = new HttpStringContent(con);
        res.Content.Headers.ContentType = new HttpMediaTypeHeaderValue("text/html");
        res.StatusCode = HttpStatusCode.Ok;            
    }
    //...
}
```

在此示例请求处理程序中，我们首先从 args  参数中拉取出请求和响应对象，然后创建包含请求 URL 和一些其他 HTML 格式设置的字符串。 这会作为 [HttpStringContent  ](https://docs.microsoft.com/uwp/api/windows.web.http.httpstringcontent) 实例添加到响应对象。 还允许使用其他 [IHttpContent  ](https://docs.microsoft.com/uwp/api/windows.web.http.ihttpcontent) 类（如用于“字符串”和“缓冲区”的类）。

随后将响应设置为 HTTP 响应，并向它提供 200（正常）状态代码。 它应按预期方式在进行原始调用的浏览器中呈现。 请注意，当 RequestReceived  事件处理程序返回时，响应消息会自动返回到用户代理：无需其他“send”方法。

![设备门户响应消息](images/device-portal/plugin-response-message.png)

## <a name="providing-static-content"></a>提供静态内容
可以直接从包中的文件夹提供静态内容，因此可以很轻松地向提供程序添加 UI。  提供静态内容的最简单方法是在项目中创建可以映射到 URL 的内容文件夹。

![设备门户静态内容文件夹](images/device-portal/plugin-static-content.png)
 
然后在 RequestReceived  事件处理程序中添加检测静态内容路线并相应地映射请求的路线处理程序。  

```csharp
if (req.RequestUri.LocalPath.ToLower().Contains("/www/")) {
    var filePath = req.RequestUri.AbsolutePath.Replace('/', '\\').ToLower();
    filePath = filePath.Replace("\\backgroundprovider", "")
    try {
        var fileStream = Windows.ApplicationModel.Package.Current.InstalledLocation.OpenStreamForReadAsync(filePath).GetAwaiter().GetResult();
        res.StatusCode = HttpStatusCode.Ok;
        res.Content = new HttpStreamContent(fileStream.AsInputStream());
        res.Content.Headers.ContentType = new HttpMediaTypeHeaderValue("text/html");
    } catch(FileNotFoundException e) {
        string con = String.Format("<h1>{0} - not found</h1>\r\n", filePath);
        con += "Exception: " + e.ToString();
        res.Content = new HttpStringContent(con);
        res.StatusCode = HttpStatusCode.NotFound;
        res.Content.Headers.ContentType = new HttpMediaTypeHeaderValue("text/html");
    }
}
```
确保内容文件夹内的所有文件在 Visual Studio 的“属性”菜单中都都标记为“内容”并设置为“如果较新则复制”或“始终复制”。  这会确保在部署 AppX 程序包时文件始终处于其中。  

![配置静态内容文件复制](images/device-portal/plugin-file-copying.png)

## <a name="using-existing-device-portal-resources-and-apis"></a>使用现有设备门户资源和 API
设备门户提供程序提供的静态内容会在与核心设备门户服务相同的端口上进行提供。  这意味着可以在 HTML 中使用简单的 `<link>` 和 `<script>` 标记引用设备门户附带的现有 JS 和 CSS。 一般情况下，建议使用 rest.js  （在一个方便的 webbRest 对象中包装所有核心设备门户 REST API）和 common.css  文件（可用于设置内容样式以适应设备门户 UI 的其余部分）。 可以在示例中包含的 index.html  页面上看到这样一个示例。 它使用 rest.js  从设备门户检索设备名称和正在运行的进程。 

![设备门户插件输出](images/device-portal/plugin-output.png)
 
重要的是，对 webbRest 使用 HttpPost/DeleteExpect200 方法会自动执行 [CSRF 处理](https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks)，让网页可以调用状态不断变化的 REST API。  

> [!NOTE] 
> 设备门户附带的静态内容不带有针对重大更改的保证。 虽然 API 预计不会经常更改，不过它们可能会更改，特别是在 common.js  和 controls.js  文件（提供程序不应使用它们）。 

## <a name="debugging-the-device-portal-connection"></a>调试设备门户连接
若要调试后台任务，必须更改 Visual Studio 运行代码的方式。 请按照以下用于调试应用服务连接的步骤以检查提供程序如何处理 HTTP 请求：

1.  从“调试”菜单中，选择 DevicePortalProvider 属性。 
2.  在“调试”选项卡下的“启动操作”部分中，选择“不启动，但在启动时调试代码”。  
![将插件置于调试模式](images/device-portal/plugin-debug-mode.png)
3.  在 RequestReceived 处理程序函数中设置断点。
![requestreceived 处理程序中的断点](images/device-portal/plugin-requestreceived-breakpoint.png)
> [!NOTE] 
> 请确保该版本体系结构完全匹配目标的体系结构。 如果你使用的是 64 位电脑，则必须使用 AMD64 版本进行部署。 
4.  按 F5 部署应用
5.  关闭设备门户，然后重新打开它，以便它可找到你的应用（仅当更改应用清单时才需要 - 其余时候重新部署即可，可跳过此步骤）。 
6.  在浏览器中，访问提供程序的命名空间，应命中断点。

## <a name="related-topics"></a>相关主题
* [Windows 设备门户概述](device-portal.md)
* [创建和使用应用服务](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)


