---
Description: 通过在 Visual Studio 中使用向导，你可以从使用 Azure 移动服务创建的移动服务生成推送通知。
title: 由推送通知向导生成的代码
ms.assetid: 340F55C1-0DDF-4233-A8E4-C15EF9030785
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 6a3429607ecd9245dd10722fbb47d06d7aa6ef75
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234836"
---
# <a name="code-generated-by-the-push-notification-wizard"></a>由推送通知向导生成的代码
 

通过在 Visual Studio 中使用向导，你可以从使用 Azure 移动服务创建的移动服务生成推送通知。 Visual Studio 向导生成代码，以帮助你开始操作。 本主题说明向导如何修改你的项目、生成的代码有何作用、如何使用此代码，以及为了发挥推送通知的最大作用，你接下来可以如何操作。 请参阅 [Windows 推送通知服务 (WNS) 概述](windows-push-notification-services--wns--overview.md)。

## <a name="how-the-wizard-modifies-your-project"></a>向导如何修改你的项目


推送通知向导通过以下几种方式修改你的项目:

-   向移动服务托管的客户端添加引用 (MobileServicesManagedClient.dll)。 不适用于 JavaScript 项目。
-   在服务的子文件夹中添加一个文件，将该文件命名为 push.register.cs、push.register.vb、push.register.cpp 或 push.register.js。
-   在移动服务的数据库服务器上创建一个通道表。 该表中包含向应用实例发送推送通知所需的信息。
-   创建四个功能的脚本：删除、插入、读取和更新。
-   创建带有自定义 API 的脚本 notifyallusers.js，它将向所有客户端发送推送通知。
-   向你的 App.xaml.cs、App.xaml.vb 或 App.xaml.cpp 文件添加声明，或向 JavaScript 项目的新文件 service.js 添加声明。 该声明可声明一个 MobileServiceClient 对象，此对象中包含连接到移动服务所需的信息。 你可以通过使用名称 App.*MyServiceName*Client 从应用中的任何页面访问此 MobileServiceClient 对象，它的名称为 *MyServiceName*Client。

services.js 文件包含以下代码：

```js
var <mobile-service-name>Client = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                "https://<mobile-service-name>.azure-mobile.net/",
                "<your client secret>");
```

## <a name="registration-for-push-notifications"></a>推送通知注册


在 \* UploadChannel 方法中，注册设备以接收推送通知。 应用商店将跟踪应用的已安装实例，并提供推送通知通道。 请参阅 [**PushNotificationChannelManager**](https://docs.microsoft.com/uwp/api/Windows.Networking.PushNotifications.PushNotificationChannelManager)。

对于 JavaScript 后端和 .NET 后端，客户端代码是类似的。 默认情况下，当你为 JavaScript 后端服务添加推送通知时，对 notifyAllUsers 自定义 API 的示例调用将插入到 UploadChannel 方法中。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MobileServices;
using Newtonsoft.Json.Linq;

namespace App2
{
    internal class mymobileservice1234Push
    {
        public async static void UploadChannel()
        {
            var channel = await Windows.Networking.PushNotifications.PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            try
            {
                await App.mymobileservice1234Client.GetPush().RegisterNativeAsync(channel.Uri);
                await App.mymobileservice1234Client.InvokeApiAsync("notifyAllUsers");
            }
            catch (Exception exception)
            {
                HandleRegisterException(exception);
            }
        }

        private static void HandleRegisterException(Exception exception)
        {
            
        }
    }
}
```

```vb
Imports Microsoft.WindowsAzure.MobileServices
Imports Newtonsoft.Json.Linq

Friend Class mymobileservice1234Push
    Public Shared Async Sub UploadChannel()
        Dim channel = Await Windows.Networking.PushNotifications.PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync()

        Try
            Await App.mymobileservice1234Client.GetPush().RegisterNativeAsync(channel.Uri)
            Await App.mymobileservice1234Client.GetPush().RegisterNativeAsync(channel.Uri, New String() {"tag1", "tag2"})
            Await App.mymobileservice1234Client.InvokeApiAsync("notifyAllUsers")
        Catch exception As Exception
            HandleRegisterException(exception)
        End Try
    End Sub

    Private Shared Sub HandleRegisterException(exception As Exception)

    End Sub
End Class
```

```c++
#include "pch.h"
#include "services\mobile services\mymobileservice1234\mymobileservice1234Push.h"

using namespace AzureMobileHelper;

using namespace web;
using namespace concurrency;

using namespace Windows::Networking::PushNotifications;

void mymobileservice1234Push::UploadChannel()
{
    create_task(PushNotificationChannelManager::CreatePushNotificationChannelForApplicationAsync()).
    then([] (PushNotificationChannel^ newChannel) 
    {
        return mymobileservice1234MobileService::GetClient().get_push().register_native(newChannel->Uri->Data());
    }).then([]()
    {
        return mymobileservice1234MobileService::GetClient().invoke_api(L"notifyAllUsers");
    }).then([](task<json::value> result)
    {
        try
        {
            result.wait();
        }
        catch(...)
        {
            HandleExceptionsComingFromTheServer();
        }
    });
}

void mymobileservice1234Push::HandleExceptionsComingFromTheServer()
{
}
```

```js
(function () {
    "use strict";

    var app = WinJS.Application;
    var activation = Windows.ApplicationModel.Activation;

    app.addEventListener("activated", function (args) {
        if (args.detail.kind == activation.ActivationKind.launch) {
            Windows.Networking.PushNotifications.PushNotificationChannelManager.createPushNotificationChannelForApplicationAsync()
                .then(function (channel) {
                    mymobileserviceclient1234Client.push.registerNative(channel.Uri, new Array("tag1", "tag2"))
                    return mymobileservice1234Client.push.registerNative(channel.uri);
                })
                .done(function (registration) {
                    return mymobileservice1234Client.invokeApi("notifyAllUsers");
                }, function (error) {
                    // Error

                });
        }
    });
})();
```

推送通知标记提供了一种将通知限制到客户端子集中的方法。 你可以使用 registerNative 方法（或 RegisterNativeAsync）方法为所有推送通知注册而不指定标记，或者可以通过提供第二个参数，即一个标记数组来向标记注册。 如果你向一个或多个标记注册，则只接收匹配这些标记的通知。

## <a name="server-side-scripts-javascript-backend-only"></a>服务器端脚本（仅 JavaScript 后端）


对于使用 JavaScript 后端的移动服务，服务器端脚本在发生删除、插入、读取或更新操作时运行。 服务器端脚本并不实现这些操作，但是当从客户端调用 Windows Mobile REST API，从而触发这些事件时，这些脚本便会运行。 然后这些脚本自身通过调用 request.execute 或 request.respond 向调用上下文发出响应，从而向这些操作传递控制。 请参阅 [Azure 移动服务 REST API 参考](https://msdn.microsoft.com/library/azure/jj710108.aspx)。

服务器端脚本中有多种函数可用。 请参阅 [Azure 移动服务中的注册表操作](https://msdn.microsoft.com/library/azure/dn167708.aspx)。 有关所有可用函数的参考，请参阅[移动服务服务器脚本参考](https://msdn.microsoft.com/library/windowsazure/jj554226)。

还将在 Notifyallusers.js 中创建以下自定义 API 代码：

```js
exports.post = function(request, response) {
    response.send(statusCodes.OK,{ message : 'Hello World!' })
    
    // The following call is for illustration purpose only
    // The call and function body should be moved to a script in your app
    // where you want to send a notification
    sendNotifications(request);
};

// The following code should be moved to appropriate script in your app where notification is sent
function sendNotifications(request) {
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual><binding template="ToastText01">' +
        '<text id="1">Sample Toast</text></binding></visual></toast>';
    var push = request.service.push; 
    push.wns.send(null,
        payload,
        'wns/toast', {
            success: function (pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        });
}
```

sendNotifications 函数发送 toast 通知形式的单一通知。 你还可以使用其他类型的推送通知。

**提示**   有关如何在编辑脚本时获取帮助的信息，请参阅[为服务器端 JavaScript 启用 IntelliSense](https://blogs.msdn.com/b/visualstudio/archive/2013/07/26/enabling-intellisense-for-mobile-services-javascript-in-visual-studio.aspx)。

 

## <a name="push-notification-types"></a>推送通知类型


Windows 还支持推送通知以外的通知。 有关通知的一般信息，请参阅[选择通知传递方法](choosing-a-notification-delivery-method.md)。

Toast 通知易于使用，你可以在为你生成的通道表的 Insert.js 代码中查看示例。 如果你计划使用磁贴通知或锁屏提醒通知，那么你必须为磁贴或锁屏提醒创建 XML 模板，并且必须在模板中指定打包信息的编码。 请参阅[使用磁贴、锁屏提醒和 toast 通知](https://docs.microsoft.com/previous-versions/windows/apps/hh868259(v=win.10))。

由于 Windows 响应推送通知，因此在应用未运行时，Windows 也可以处理大部分此类通知。 例如，即便本地邮件应用不处于运行状态，当收到新邮件时，推送通知也可以提醒客户。 Windows 对 toast 通知的处理方式是显示信息，例如显示短信的第一行内容。 Windows 对磁贴通知或锁屏提醒通知的处理方式是更新应用的动态磁贴，使其反映新邮件的数量。 通过这种方式，你可以提示应用的用户到应用中查看新信息。 应用在运行时可以接收原始通知，你可以使用原始通知向你的应用发送数据。 如果应用未运行，可以设置后台任务，用于监视推送通知。

应根据 Windows 应用的准则使用推送通知，因为这些通知会占用用户的资源，如果使用过度，可能会分散注意力。 请参阅[推送通知指南和清单](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)。

如果你使用推送通知更新动态磁贴，那么你还应该遵循[磁贴和锁屏提醒指南和清单](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-creating-tiles)中的指南。

## <a name="next-steps"></a>后续步骤


### <a name="using-the-windows-push-notification-services-wns"></a>使用 Windows 推送通知服务 (WNS)

在以下情况下，你可以直接调用 Windows 推送通知服务 (WNS)：移动服务缺乏足够的灵活性、希望使用 C# 或 Visual Basic 编写服务器代码、或者你已经有云服务并且希望从云服务发送推送通知。 通过直接调用 WNS，你可以从自己的云服务发送推送通知，例如使用通过数据库或其他 Web 服务监视数据的辅助角色。 你的云服务必须通过 WNS 验证，才能向你的应用发送推送通知。 请参阅[如何执行 Windows 推送通知服务 (JavaScript)](https://docs.microsoft.com/previous-versions/windows/apps/hh465407(v=win.10)) 或 [(C#/C++/VB) 的验证](https://docs.microsoft.com/previous-versions/windows/apps/hh868206(v=win.10))。

你还可以通过在移动服务中运行计划的任务来发送推送通知。 请参阅[在移动服务中计划定期作业](https://azure.microsoft.com/documentation/articles/mobile-services-schedule-recurring-tasks/)。

**警告**   一旦你运行了推送通知向导一次，就不要再次运行向导以添加其他移动服务的注册代码。 针对每个项目多次运行该向导会生成导致对 [**CreatePushNotificationChannelForApplicationAsync**](https://docs.microsoft.com/uwp/api/Windows.Networking.PushNotifications.PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync) 方法的重叠调用的代码，从而导致运行时异常。 如果你要为多个移动服务注册推送通知，请运行该向导一次，然后重新编写该注册代码以确保对 **CreatePushNotificationChannelForApplicationAsync** 的调用不会同时运行。 例如，你可以通过将向导生成的代码移到 register \* 来实现此目的。（包括对**CreatePushNotificationChannelForApplicationAsync**的调用），但具体情况取决于应用程序的体系结构。

 

## <a name="related-topics"></a>相关主题


* [Windows 推送通知服务 (WNS) 概述](windows-push-notification-services--wns--overview.md)
* [原始通知概述](raw-notification-overview.md)
* [连接到 Windows Azure 移动服务 (JavaScript)](https://docs.microsoft.com/previous-versions/windows/apps/dn263160(v=win.10))
* [连接到 Windows Azure 移动服务 (C#/C++/VB)](https://docs.microsoft.com/previous-versions/windows/apps/dn263175(v=win.10))
* [快速入门：为移动服务添加推送通知 (JavaScript)](https://docs.microsoft.com/previous-versions/windows/apps/dn263163(v=win.10))
 

 




