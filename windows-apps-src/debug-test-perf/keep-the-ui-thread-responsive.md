---
ms.assetid: FA25562A-FE62-4DFC-9084-6BD6EAD73636
title: 保持 UI 线程有响应
description: 用户期望应用在执行计算时保持响应，无论计算机的类型如何都是如此。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b9a129e8b780e85df2c38c50ab712641d3849a34
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "71339859"
---
# <a name="keep-the-ui-thread-responsive"></a>保持 UI 线程有响应


用户期望应用在执行计算时保持响应，无论计算机的类型如何都是如此。 这对于不同的应用具有不同含义。 对于某些应用，这意味着要提供更加真实的物理结构、从磁盘或 Web 更快地加载数据、快速显示复杂的场景和在页面之间导航、瞬间查找方向，或者快速处理数据。 无论计算属于何种类型，用户都希望其应用对输入做出反应，并消除在应用在“思考”时看起来毫无响应的情形。

应用由事件驱动，这意味着代码将在响应事件时执行工作，然后在出现下一事件之前保持空闲状态。 用于 UI（布局、输入、引发事件等）的平台代码以及用于 UI 的应用代码都在同一 UI 线程上执行。 该线程上执行一次只能执行一个指令，因此如果应用代码处理事件的时间过长，则框架将无法运行布局或引发表示用户交互的新事件。 应用的响应能力与处理工作的 UI 线程的可用性相关。

你需要使用 UI 线程进行几乎所有对 UI 线程的更改，包括创建 UI 类型以及访问其成员。 无法从后台线程更新 UI，但可以使用 [**CoreDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) 向其发布消息以使代码在该处运行。

> **注意**：唯一的例外是，有一个单独的呈现线程，它能够应用不会影响输入处理方式或基本布局的 UI 更改。   例如，许多不会影响布局的动画和过渡都可以在此呈现线程上运行。

## <a name="delay-element-instantiation"></a>延迟元素实例化

应用中的某些最慢阶段可能包含启动以及切换视图。 请勿执行不必要的工作从而避免调用用户会首先看到的 UI。 例如，不要创建逐步显示 UI 和弹出窗口内容的 UI。

-   使用 [x:Load attribute](../xaml-platform/x-load-attribute.md) 或 [x:DeferLoadStrategy](https://docs.microsoft.com/windows/uwp/xaml-platform/x-deferloadstrategy-attribute) 延迟实例化元素。
-   以编程方式根据需要将元素插入树中。

[CoreDispatcher.RunIdleAsync  ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runidleasync) 队列适用于要在不忙时处理的 UI 线程。

## <a name="use-asynchronous-apis"></a>使用异步 API

为了帮助应用保持响应状态，平台提供乐许多 API 的异步版本。 异步 API 确保你的活动执行线程永远不会占用大量时间。 从 UI 线程调用 API 时，请使用异步版本（如果可用）。 有关使用 **async** 模式编程的详细信息，请参阅[异步编程](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps)或[使用 C# 或 Visual Basic 调用异步 API](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic)。

## <a name="offload-work-to-background-threads"></a>将工作卸载至后台线程

编写事件处理程序以便快速返回。 在需要执行大量工作的情况下，将工作安排在后台线程并返回。

你可以使用 C# 中的 **await** 运算符、Visual Basic 中的 **Await** 运算符或 C++ 中的委托异步安排工作。 但这并不保证你安排的工作将在后台线程上运行。 许多通用 Windows 平台 (UWP) API 将为你在后台线程中安排工作，但如果你仅使用 **await** 或某个委托调用你的应用代码，请在 UI 线程上运行该委托或方法。 你必须明确指出希望何时在后台线程上运行你的应用代码。 在 C# 和 Visual Basic 中，可通过将代码传递给 [Task.Run  ](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) 来实现此操作。

请记住，可能只能从 UI 线程访问 UI 元素。 先使用 UI 线程访问 UI 元素，再启动后台工作，并且/或者在后台线程上使用 [**CoreDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) 或 [**CoreDispatcher.RunIdleAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runidleasync)。

可以在后台线程上执行的工作的一个示例是计算游戏中的计算机 AI。 执行对计算机的下一次移动进行计算的代码需要花费很长时间。

```csharp
public class AsyncExample
{
    private async void NextMove_Click(object sender, RoutedEventArgs e)
    {
        // The await causes the handler to return immediately.
        await System.Threading.Tasks.Task.Run(() => ComputeNextMove());
        // Now update the UI with the results.
        // ...
    }

    private async System.Threading.Tasks.Task ComputeNextMove()
    {
        // Perform background work here.
        // Don't directly access UI elements from this method.
    }
}
```

> [!div class="tabbedCodeSnippets"]
> ```csharp
> public class Example
> {
>     // ...
>     private async void NextMove_Click(object sender, RoutedEventArgs e)
>     {
>         await Task.Run(() => ComputeNextMove());
>         // Update the UI with results
>     }
> 
>     private async Task ComputeNextMove()
>     {
>         // ...
>     }
>     // ...
> }
> ```
> ```vb
> Public Class Example
>     ' ...
>     Private Async Sub NextMove_Click(ByVal sender As Object, ByVal e As RoutedEventArgs)
>         Await Task.Run(Function() ComputeNextMove())
>         ' update the UI with results
>     End Sub
> 
>     Private Async Function ComputeNextMove() As Task
>         ' ...
>     End Function
>     ' ...
> End Class
> ```

在此示例中，`NextMove_Click` 处理程序将在 **await** 返回，以便保持 UI 线程有响应。 但在 `ComputeNextMove`（在后台线程上执行）完成后，执行过程将再次调用该处理程序。 该处理程序中的其余代码将使用结果更新 UI。

> **注意**：还存在适用于 UWP 的   ThreadPool[  和 ](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPool)ThreadPoolTimer[  API，可用于类似场景。](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer) 有关详细信息，请参阅[线程和异步编程](https://docs.microsoft.com/windows/uwp/threading-async/index)。

## <a name="related-topics"></a>相关主题

* [自定义用户交互](https://docs.microsoft.com/windows/uwp/design/layout/index)
