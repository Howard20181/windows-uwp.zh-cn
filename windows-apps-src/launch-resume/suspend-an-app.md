---
title: 处理应用暂停
description: 了解当系统挂起你的应用时如何保存重要的应用程序数据。
ms.assetid: F84F1512-24B9-45EC-BF23-A09E0AC985B0
ms.date: 07/06/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: f912e6212346a4019d8421c542a81eb2318dc5d9
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260410"
---
# <a name="handle-app-suspend"></a>处理应用暂停

**重要的 API**

- [**封存**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.suspending)

了解当系统挂起你的应用时如何保存重要的应用程序数据。 以下示例向事件处理程序注册 [**Suspending**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.suspending) 事件并将字符串保存到文件中。

## <a name="register-the-suspending-event-handler"></a>注册暂停的事件处理程序

注册以处理 [**Suspending**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.suspending) 事件，该事件指示在系统暂停你的应用之前，应用应该保存其应用程序数据。

```csharp
using System;
using Windows.ApplicationModel;
using Windows.ApplicationModel.Activation;
using Windows.UI.Xaml;

partial class MainPage
{
   public MainPage()
   {
      InitializeComponent();
      Application.Current.Suspending += new SuspendingEventHandler(App_Suspending);
   }
}
```

```vb
Public NotInheritable Class MainPage

   Public Sub New()
      InitializeComponent()
      AddHandler Application.Current.Suspending, AddressOf App_Suspending
   End Sub
   
End Class
```

```cppwinrt
MainPage::MainPage()
{
    InitializeComponent();
    Windows::UI::Xaml::Application::Current().Suspending({ this, &MainPage::App_Suspending });
}
```

```cpp
using namespace Windows::ApplicationModel;
using namespace Windows::ApplicationModel::Activation;
using namespace Windows::Foundation;
using namespace Windows::UI::Xaml;
using namespace AppName;

MainPage::MainPage()
{
   InitializeComponent();
   Application::Current->Suspending +=
       ref new SuspendingEventHandler(this, &MainPage::App_Suspending);
}
```

## <a name="save-application-data-before-suspension"></a>在挂起之前保存应用程序数据

当你的应用处理 [**Suspending**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.suspending) 事件时，它将有机会将其重要的应用程序数据保存到处理程序函数中。 应用应该使用 [**LocalSettings**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localsettings) 存储 API 来同步保存简单的应用程序数据。

```csharp
partial class MainPage
{
    async void App_Suspending(
        Object sender,
        Windows.ApplicationModel.SuspendingEventArgs e)
    {
        // TODO: This is the time to save app data in case the process is terminated.
    }
}
```

```vb
Public NonInheritable Class MainPage

    Private Sub App_Suspending(
        sender As Object,
        e As Windows.ApplicationModel.SuspendingEventArgs) Handles OnSuspendEvent.Suspending

        ' TODO: This is the time to save app data in case the process is terminated.
    End Sub

End Class
```

```cppwinrt
void MainPage::App_Suspending(
    Windows::Foundation::IInspectable const& /* sender */,
    Windows::ApplicationModel::SuspendingEventArgs const& /* e */)
{
    // TODO: This is the time to save app data in case the process is terminated.
}
```

```cpp
void MainPage::App_Suspending(Object^ sender, SuspendingEventArgs^ e)
{
    // TODO: This is the time to save app data in case the process is terminated.
}
```

## <a name="release-resources"></a>释放资源

你应释放独占资源和文件句柄，以便其他应用可以在你的应用暂停时访问它们。 独占资源的示例包括相机、I/O 设备、外部设备以及网络资源。 显式释放独占资源和文件句柄有助于确保其他应用可以在你的应用暂停时访问它们。 当恢复应用时，它应该重新获取其独占资源和文件句柄。

## <a name="remarks"></a>备注

每当用户切换到其他应用、桌面或“开始”屏幕时，系统都会暂停你的应用。 每当用户切回到你的应用时，系统就会恢复你的应用。 当系统恢复你的应用时，你的变量和数据结构的内容与系统将你的应用暂停之前的内容相同。 系统会将你的应用完全恢复到你离开时的状态，使用户感觉你的应用好像一直在后台运行一样。

当你的应用暂停时，系统会尝试将你的应用及其数据保留在内存中。 但是，如果系统没有资源将你的应用保存在内存里，则将终止你的应用。 当用户切换回已终止的暂停应用时，该应用会发送 [**Activated**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.activated) 事件，且应该在其 [**OnLaunched**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched) 方法中还原其应用程序数据。

当终止应用时系统不会通知应用，因此当暂停应用时，你的应用必须保存其应用程序数据并释放独占资源和文件句柄，并且在终止后又激活应用时还原这些内容。

如果在处理程序中执行异步调用，控件将立即从该异步调用中返回。 这意味着，执行之后会从事件处理程序中返回，并且应用会转变为下一个状态，即使异步调用尚未完成。 使用传递给事件处理程序的 [**EnteredBackgroundEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel?redirectedfrom=MSDN) 对象上的 [**GetDeferral**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel?redirectedfrom=MSDN) 方法以延迟暂停，直到调用返回的 [**Windows.Foundation.Deferral**](https://docs.microsoft.com/uwp/api/windows.foundation.deferral.complete) 对象上的 [**Complete**](https://docs.microsoft.com/uwp/api/windows.foundation.deferral) 方法。

延迟并不会增加应用终止之前需要运行的代码量。 它只延迟终止，直到调用延迟的 *Complete* 方法，或者达到截止时间，*以先发生者为准*。 要延长处于“暂停中”状态的事件，请使用 [**ExtendedExecutionSession**](run-minimized-with-extended-execution.md)

> [!NOTE]
> 若要提高 Windows 8.1 中的系统响应能力，在挂起应用程序后，将对其授予低优先级访问资源的权限。 为了支持新的优先级，延长了暂停操作超时，以便应用具有与普通优先级相当的 5 秒（在 Windows 上）或者 1 到 10 秒超时（在 Windows Phone 上）。 你无法扩展或改变此超时窗口。

**有关使用 Visual Studio 进行调试的注释：** Visual Studio 阻止 Windows 暂停连接到调试程序的应用。 这是为了允许用户在应用正在运行时查看 Visual Studio 调试 UI。 调试应用时，可以使用 Visual Studio 将一个暂停事件发送给该应用。 请确保 **“调试位置”** 工具栏正在显示，然后单击 **“暂停”** 图标。

## <a name="related-topics"></a>相关主题

* [应用生命周期](app-lifecycle.md)
* [处理应用激活](activate-an-app.md)
* [处理应用恢复](resume-an-app.md)
* [启动、挂起和恢复的 UX 指导原则](https://docs.microsoft.com/windows/uwp/launch-resume/index)
* [扩展执行](run-minimized-with-extended-execution.md)

 

 
