---
description: 了解如何使用扩展执行让你的应用在最小化时保持运行
title: 使用扩展执行来推迟应用挂起
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 扩展执行, 最小化, ExtendedExecutionSession, 后台任务, 应用程序生命周期, 锁屏界面
ms.assetid: e6a6a433-5550-4a19-83be-bbc6168fe03a
ms.localizationpriority: medium
ms.openlocfilehash: fdb47a7c57ff8ef719b819253ab768c0d836be14
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684555"
---
# <a name="postpone-app-suspension-with-extended-execution"></a>使用扩展执行来推迟应用挂起

本文将为你展示当应用挂起时如何借助扩展执行进行推迟，从而让你的应用在最小化时或在锁屏界面中也可以运行。

当用户最小化或者离开应用时，应用会进入挂起状态。  其内存会保留，但是其代码不运行。 对于具有可视用户界面的所有操作系统版本均是如此。 有关应用何时挂起的更多详细信息，请参阅[应用程序生命周期](app-lifecycle.md)。 

某些情况下，应用在用户离开后或最小化时可能需要保持运行，而不是挂起。 例如，即使用户离开去使用其他应用，计步应用也需要保持运行并跟踪步数。 

如果应用需要保持运行，操作系统可以让它保持运行，或者它也可以请求保持运行。 例如，在后台播放音频时，如果按照[后台媒体播放](../audio-video-camera/background-audio.md)的以下步骤进行操作，操作系统可以保持应用运行更长时间。 否则，你必须多次手动请求。 你用来执行后台执行的时间可能只有几分钟，但你必须随时准备处理被吊销的会话。 当应用在调试程序下运行时，这些应用程序生命周期时间限制会被禁用。 出于这一原因，在测试推迟应用挂起的扩展执行和其他工具时，一定不要使用调试程序运行；或者，也可以借助 Visual Studio 中提供的生命周期事件进行测试。 
 
创建 [ExtendedExecutionSession](https://docs.microsoft.com/uwp/api/windows.applicationmodel.extendedexecution.extendedexecutionsession) 来多次请求在后台完成操作。 你创建的 **ExtendedExecutionSession** 种类由你在创建它时提供的 [ExtendedExecutionReason](https://docs.microsoft.com/uwp/api/windows.applicationmodel.extendedexecution.extendedexecutionreason) 来决定。 有三个 **ExtendedExecutionReason** 枚举值：**Unspecified、LocationTracking** 和 **SavingData**。 任何时候都只能请求一个 **ExtendedExecutionSession**；在已批准的会话请求当前处于活动状态时，尝试创建另一个会话将导致 **ExtendedExecutionSession** 构造函数引发异常 0x8007139F，指示组或资源不处于执行请求的操作的正确状态。 不要使用 [ExtendedExecutionForegroundSession](https://docs.microsoft.com/uwp/api/windows.applicationmodel.extendedexecution.foreground.extendedexecutionforegroundsession) 和 [ExtendedExecutionForegroundReason](https://docs.microsoft.com/uwp/api/windows.applicationmodel.extendedexecution.foreground.extendedexecutionforegroundreason)；它们需要受限功能，并且不能在 Microsoft Store 应用程序中使用。

## <a name="run-while-minimized"></a>最小化时运行

有两种情况可以使用扩展执行：
- 在常规前台执行期间并且应用程序处于运行状态时。
- 应用程序的正在挂起事件处理程序收到正在挂起事件之后（操作系统即将把应用转入挂起状态）。

这两种情况下的代码是相同的，但应用程序在不同情况下的行为稍有不同。 在第一种情况下，即使发生通常会触发挂起的事件（例如，用户离开应用程序），应用程序仍保持运行状态。 执行扩展生效期间，应用程序永远不会收到正在挂起事件。 释放扩展后，应用程序才重新获得挂起资格。

在第二种情况下，如果应用程序正在转换到挂起状态，它将在扩展期间保持正在挂起状态。 扩展到期后，应用程序进入挂起状态，无需进一步通知。

针对媒体处理、项目编译或者保持网络连接处于活动状态的情况，创建 **ExtendedExecutionSession** 以请求应用在进入后台之前运行更长时间时请使用 **ExtendedExecutionReason.Unspecified**。 在运行适用于桌面版 Windows 10（Home、Pro、Enterprise 和 Education）的桌面设备上，如果需要在最小化应用时避免应用挂起，则可以使用此方法。

在启动长时间运行的操作时请求扩展，以便延迟**挂起**状态转换，否则，应用进入后台时就会挂起。 在桌面设备上，使用 **ExtendedExecutionReason.Unspecified** 创建的扩展执行会话都有一个电池感知时间限制。 如果设备连接到墙上的电源，对扩展执行的时段没有限制。 如果设备使用电池供电，则扩展执行时段最多可以在后台运行 10 分钟。

在**按应用查看电池使用情况**设置中选择**允许应用运行后台任务**选项时，平板电脑或笔记本用户可以达到同等时长的运行时间，但以牺牲电池使用时间为代价。 （若要在笔记本电脑上查找此选项，请转到**设置** > **系统** > **电池** > **按应用查看电池使用情况**（剩余电量百分比下的链接）>选择应用 > 关闭**由 Windows 管理** > 选择**运行应用运行后台任务**。  

对于所有操作系统版本，当设备进入“连接待机”模式时，这种扩展执行会话将停止。 在运行 Windows 10 移动版的移动设备上，只要屏幕亮着，这种扩展执行会话将一直运行。 当屏幕关闭时，设备会立即尝试进入低电量“连接待机”模式。 在桌面设备上，如果出现锁定屏幕，会话将继续运行。 屏幕关闭后，设备会在一段时间后进入“连接待机”模式。 在 Xbox OS Edition 上，设备会在 1 小时后进入“连接待机”模式，除非用户更改默认值。

## <a name="track-the-users-location"></a>跟踪用户位置

如果应用需要定期记录 [GeoLocator](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator) 中的位置，请在创建 **ExtendedExecutionSession** 时指定 **ExtendedExecutionReason.LocationTracking**。 用于健身跟踪和导航的应用，此应用需要定期监控用户的位置并使用该原因。

位置跟踪扩展执行会话可以根据需要尽可能长时间运行，在移动设备上锁定屏幕时也是如此。 不过，每个设备上只能运行一个此类会话。 位置跟踪扩展执行会话只能在前台请求，并且应用必须处于**正在运行**状态。 这确保用户了解应用已启动扩展位置跟踪会话。 当应用于后台运行时，仍可以通过后台任务或者应用服务使用 GeoLocator，而无需请求位置跟踪扩展执行会话。

## <a name="save-critical-data-locally"></a>本地保存关键数据

请在创建 **ExtendedExecutionSession** 时指定 **ExtendedExecutionReason.SavingData** 来保存用户数据，以免因应用终止前未保存数据而导致数据丢失以及负面用户体验。

请勿使用这种会话延长应用生命周期来上载或下载数据。 如果需要上载数据，请在有可用的交流电源时请求 [background transfer](https://docs.microsoft.com/windows/uwp/networking/background-transfers) 或注册 **MaintenanceTrigger** 来处理传输。 **ExtendedExecutionReason.SavingData** 扩展执行会话可以在应用位于前台并且处于**正在运行**状态时请求，也可以在应用位于后台并且处于**挂起**状态时请求。

应用终止之前，**挂起**状态是应用在生命周期内可以执行任务的最后机会。 **ExtendedExecutionReason.SavingData** 是唯一一种可在**挂起**状态下请求的 **ExtendedExecutionSession**。 当应用处于**挂起**状态时请求 **ExtendedExecutionReason.SavingData** 扩展执行会话会引发潜在问题，你应当引起注意。 如果在应用处于**挂起**状态时请求扩展执行会话，并且用户请求再次启动应用，可能要花很长时间启动。 这是因为必须完成扩展执行会话时段才能关闭应用的旧实例并启动应用的新实例。 牺牲启动性能时间以保证用户状态不丢失。

## <a name="request-disposal-and-revocation"></a>请求、处置和吊销

与扩展执行会话有 3 个基本的交互：请求、处置和吊销。  以下代码段中对发出请求进行了建模。

### <a name="request"></a>请求

```csharp
var newSession = new ExtendedExecutionSession();
newSession.Reason = ExtendedExecutionReason.Unspecified;
newSession.Revoked += SessionRevoked;
ExtendedExecutionResult result = await newSession.RequestExtensionAsync();

switch (result)
{
    case ExtendedExecutionResult.Allowed:
        DoLongRunningWork();
        break;

    default:
    case ExtendedExecutionResult.Denied:
        DoShortRunningWork();
        break;
}
```
[请参阅代码示例](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario1_UnspecifiedReason.xaml.cs#L81-L110)  

调用 **RequestExtensionAsync** 会向操作系统核实来了解用户是否已批准应用的后台活动以及系统是否有可用资源来启用后台执行。 任何时候系统只为一个应用批准一个会话，对 **RequestExtensionAsync** 的后续调用将导致会话被拒绝。

你可以事先检查 [BackgroundExecutionManager](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager) 来确定 [BackgroundAccessStatus](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundaccessstatus?f=255&MSPPError=-2147217396)，后者是指示你的应用是否能在后台运行的用户设置。 若要了解这些用户设置的详细信息，请参阅[后台活动和能耗感知](https://blogs.windows.com/buildingapps/2016/08/01/battery-awareness-and-background-activity/#XWK8mEgWD7JHvC10.97)。

**ExtendedExecutionReason** 指示了应用正在后台执行的操作。 **Description** 字符串是用户可读的字符串，可解释应用需要执行操作的原因。 该字符串不呈现给用户，但可能会在日后的 Windows 版本中提供。 **Revoked** 事件处理程序为必需项，以便在用户或系统决定应用不可以再在后台运行时正常终止扩展执行会话。

### <a name="revoked"></a>吊销

如果应用有处于活动状态的扩展执行会话，并且系统要求后台活动终止（因为前台应用程序需要资源），则吊销会话。 如果事先未引发 **Revoked** 事件处理程序，则扩展执行会话时段永远不会终止。

针对 **ExtendedExecutionReason.SavingData** 扩展执行会话引发 **Revoked** 事件时，应用有一秒钟的时间来完成其正在执行的操作并结束**挂起**。

吊销可以出于多种原因：达到执行时间限制、达到后台能耗配额或者需要回收内存以便用户在前台打开新应用。

以下是 Revoked 事件处理程序示例：

```cs
private async void SessionRevoked(object sender, ExtendedExecutionRevokedEventArgs args)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        switch (args.Reason)
        {
            case ExtendedExecutionRevokedReason.Resumed:
                rootPage.NotifyUser("Extended execution revoked due to returning to foreground.", NotifyType.StatusMessage);
                break;

            case ExtendedExecutionRevokedReason.SystemPolicy:
                rootPage.NotifyUser("Extended execution revoked due to system policy.", NotifyType.StatusMessage);
                break;
        }

        EndExtendedExecution();
    });
}
```
[请参阅代码示例](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario1_UnspecifiedReason.xaml.cs#L124-L141)

### <a name="dispose"></a>处置

最后一步是处理扩展执行会话。 你需要处理会话以及任何其他内存密集型资源，否则，应用在等待会话关闭时所使用的能耗将计入应用的能耗配额。 要为应用保留尽可能多的能耗配额，务必在完成你的会话工作时处理会话，以便应用可以更快速地进入**挂起**状态。

自行处理会话（而不是等待吊销事件）可降低应用的能耗配额使用情况。 这意味着在未来的会话中将允许你的应用在后台运行更长时间，因为你有更多的可用能耗配额来执行此操作。 你必须保留对 **ExtendedExecutionSession** 对象的引用直到操作结束，这样你便可以调用其 **Dispose** 方法。

处理扩展执行会话的代码段如下所示：

```cs
void ClearExtendedExecution(ExtendedExecutionSession session)
{
    if (session != null)
    {
        session.Revoked -= SessionRevoked;
        session.Dispose();
        session = null;
    }
}
```
[请参阅代码示例](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario1_UnspecifiedReason.xaml.cs#L49-L63)

一个应用一次只能有一个 **ExtendedExecutionSession** 处于活动状态。 很多应用使用异步任务以完成需要访问存储、网络或网络服务等资源的复杂操作。 如果需要完成多个异步任务完成操作，则在处理 **ExtendedExecutionSession** 并允许应用挂起之前必须考虑每个任务的状态。 这需要引用计算仍在运行的任务数量，直到值达到零时再处理会话。

下面是扩展执行会话期间管理多个任务的一些示例代码。 有关如何在应用中使用该功能的详细信息，请参阅下面链接的代码示例：

```cs
static class ExtendedExecutionHelper
{
    private static ExtendedExecutionSession session = null;
    private static int taskCount = 0;

    public static bool IsRunning
    {
        get
        {
            if (session != null)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

    public static async Task<ExtendedExecutionResult> RequestSessionAsync(ExtendedExecutionReason reason, TypedEventHandler<object, ExtendedExecutionRevokedEventArgs> revoked, String description)
    {
        // The previous Extended Execution must be closed before a new one can be requested.       
        ClearSession();

        var newSession = new ExtendedExecutionSession();
        newSession.Reason = reason;
        newSession.Description = description;
        newSession.Revoked += SessionRevoked;

        // Add a revoked handler provided by the app in order to clean up an operation that had to be halted prematurely
        if(revoked != null)
        {
            newSession.Revoked += revoked;
        }

        ExtendedExecutionResult result = await newSession.RequestExtensionAsync();

        switch (result)
        {
            case ExtendedExecutionResult.Allowed:
                session = newSession;
                break;
            default:
            case ExtendedExecutionResult.Denied:
                newSession.Dispose();
                break;
        }
        return result;
    }

    public static void ClearSession()
    {
        if (session != null)
        {
            session.Dispose();
            session = null;
        }

        taskCount = 0;
    }

    public static Deferral GetExecutionDeferral()
    {
        if (session == null)
        {
            throw new InvalidOperationException("No extended execution session is active");
        }

        taskCount++;
        return new Deferral(OnTaskCompleted);
    }

    private static void OnTaskCompleted()
    {
        if (taskCount > 0)
        {
            taskCount--;
        }
        
        //If there are no more running tasks than end the extended lifetime by clearing the session
        if (taskCount == 0 && session != null)
        {
            ClearSession();
        }
    }

    private static void SessionRevoked(object sender, ExtendedExecutionRevokedEventArgs args)
    {
        //The session has been prematurely revoked due to system constraints, ensure the session is disposed
        if (session != null)
        {
            session.Dispose();
            session = null;
        }
        
        taskCount = 0;
    }
}
```
[请参阅代码示例](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario4_MultipleTasks.xaml.cs)

## <a name="ensure-that-your-app-uses-resources-well"></a>确保应用正常使用资源

当应用不再是前台应用时，调整应用内存和能耗使用情况是确保操作系统系统允许应用继续运行的关键。 使用[内存管理 API](https://docs.microsoft.com/uwp/api/windows.system.memorymanager) 查看应用当前使用的内存量。 当另一个应用在前台运行时，你的应用占用的内存越多，操作系统就越难保持你的应用继续运行。 用户最终控制你的应用可以执行的所有后台活动，并且可以看到你的应用对电池使用情况的影响。

使用 [BackgroundExecutionManager.RequestAccessAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager) 确定用户是否已决定应限制你应用的后台活动。 注意电池使用情况，并且仅当有必要完成用户想要执行的操作时再在后台运行应用。

## <a name="see-also"></a>另请参阅

[扩展执行示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ExtendedExecution)  
[应用程序生命周期](https://docs.microsoft.com/windows/uwp/launch-resume/app-lifecycle)  
[应用生命周期 - 借助后台任务和扩展执行使应用保持活动状态](https://msdn.microsoft.com/magazine/mt590969.aspx)
[后台内存管理](https://docs.microsoft.com/windows/uwp/launch-resume/reduce-memory-usage)  
[后台传输](https://docs.microsoft.com/windows/uwp/networking/background-transfers)  
[电池感知和后台活动](https://blogs.windows.com/buildingapps/2016/08/01/battery-awareness-and-background-activity/#I2bkQ6861TRpbRjr.97)  
[MemoryManager 类](https://docs.microsoft.com/uwp/api/windows.system.memorymanager)  
[在后台播放媒体](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio)  