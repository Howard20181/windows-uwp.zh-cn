---
title: 使用后台任务支持应用
description: 此部分中的主题介绍了如何使在后台运行的轻型代码响应触发器。
ms.assetid: EFF7CBFB-D309-4ACB-A2A5-28E19D447E32
ms.date: 08/21/2017
ms.topic: article
keywords: windows 10，uwp，后台任务
ms.localizationpriority: medium
ms.openlocfilehash: 7ca567d34c98deb75d7ebfa5ec9f70688ad18fdb
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259421"
---
# <a name="support-your-app-with-background-tasks"></a>使用后台任务支持应用


此部分中的主题介绍了如何使在后台运行的轻型代码响应触发器。 你可以使用后台任务在应用暂停或未运行时提供功能。 也可以将后台任务用于实时通信应用，例如 VOIP、邮件和 IM。

## <a name="playing-media-in-the-background"></a>在后台播放媒体

从 Windows 10 版本 1607 开始，在后台播放音频变得更简单。 有关详细信息，请参阅[在后台播放媒体](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio)。

## <a name="in-process-and-out-of-process-background-tasks"></a>进程内后台任务和进程外后台任务

有两种实现后台任务的方法：

* 进程内：应用与其后台进程在同一进程内运行
* 进程外：应用与后台进程在不同进程中运行。

进程内后台支持在 Windows 10 版本 1607 中引入，目的是简化编写后台任务。 但仍可以编写进程外后台任务。 有关在何时编写进程内和进程外后台任务的建议，请参阅[后台任务指南](guidelines-for-background-tasks.md)。

进程外后台任务更容易复原，因为在出现错误时，后台进程不会使应用崩溃。 但复原的代价是应用与后台任务之间的跨进程通信管理更为复杂。

进程外后台任务实现为轻型类，该类实现操作系统在独立进程 (backgroundtaskhost.exe) 中运行的 [**IBackgroundTask**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask) 接口。 使用 [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) 类注册后台任务。 注册后台任务时，类名称将用于指定入口点。

在 Windows 10 版本 1607 中，可以在无需创建后台任务的情况下启用后台活动。 可以直接在前台应用程序的进程中运行后台代码。

若要快速开始使用进程内后台任务，请参阅[创建和注册进程内后台任务](create-and-register-an-inproc-background-task.md)。

若要快速开始使用进程外后台任务，请参阅[创建和注册进程外后台任务](create-and-register-a-background-task.md)。

> [!TIP]
>从 Windows 10 开始，你不再需要将应用放置在锁定屏幕上，作为为其注册后台任务的先决条件。  

## <a name="background-tasks-for-system-events"></a>系统事件的后台任务

应用可以通过使用 [**SystemTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTrigger) 类注册后台任务来响应系统生成的事件。 应用可以使用以下任意系统事件触发器（在 [**SystemTriggerType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTriggerType) 中定义）

| 触发器名称                     | 说明                                                                                                    |
|----------------------------------|----------------------------------------------------------------------------------------------------------------|
| **InternetAvailable**            | Internet 变为可用。                                                                                |
| **NetworkStateChange**           | 网络更改，如开销或连接性发生更改。                                              |
| **OnlineIdConnectedStateChange** | 与帐户更改关联的联机 ID。                                                                 |
| **SmsReceived**                  | 安装的移动宽带设备收到一条新短信。                                         |
| **TimeZoneChange**               | 设备上的时区发生更改（例如，当系统为夏令时调整时钟时）。 |

有关详细信息，请参阅[使用后台任务响应系统事件](respond-to-system-events-with-background-tasks.md)。

## <a name="conditions-for-background-tasks"></a>后台任务的条件

你可以控制后台任务何时运行，通过添加条件，甚至可以在任务触发后进行控制。 在触发后，后台任务将不再运行，直至所有条件均符合为止。 可以使用以下条件（由 [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType) 枚举表示）。

| 条件名称           | 说明                       |
|--------------------------|-----------------------------------|
| **InternetAvailable**    | Internet 必须可用。   |
| **InternetNotAvailable** | Internet 必须不可用。 |
| **SessionConnected**     | Internet 必须连接。    |
| **SessionDisconnected**  | 该会话必须断开连接。 |
| **UserNotPresent**       | 用户必须离开。            |
| **UserPresent**          | 用户必须存在。         |

将 **InternetAvailable** 条件添加到你的后台任务 [BackgroundTaskBuilder.AddCondition](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder)，以将后台任务触发时间延迟到网络堆栈运行后。 此条件可以省电，因为必须有可用网络才会执行后台任务。 此条件不提供实时激活。

如果后台任务需要网络连接，请设置 [IsNetworkRequested](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder)，以确保在后台任务运行时网络保持连接。 这将告知后台任务基础结构在执行任务时保持网络运行，即使设备已进入连接待机模式也是如此。 如果后台任务未设置**IsNetworkRequested**，则在处于连接备用模式（例如，当手机的屏幕关闭时），后台任务将无法访问网络。  
有关后台任务条件的详细信息，请参阅[设置后台任务的运行条件](set-conditions-for-running-a-background-task.md)。

## <a name="application-manifest-requirements"></a>应用程序清单要求

在应用成功注册可以在进程外运行的后台任务前，必须在应用程序清单中声明该任务。 在与主机应用运行的相同进程中运行的后台任务无需在应用程序清单中声明。 有关详细信息，请参阅[在应用程序清单中声明后台任务](declare-background-tasks-in-the-application-manifest.md)。

## <a name="background-tasks"></a>后台任务

以下实时触发器可用于在后台运行轻型自定义代码：

| 实时触发器  | 说明 |
|--------------------|-------------|
| **控制通道** | 后台任务通过使用 [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) 可以使连接保持活动状态，在控制通道上接收消息。 如果你的应用正在侦听套接字，可以使用套接字代理而不是 **ControlChannelTrigger**。 有关使用套接字代理的详细信息，请参阅 [SocketActivityTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SocketActivityTrigger)。 **ControlChannelTrigger** 在 Windows Phone 上不受支持。 |
| **记** | 后台任务运行的频率可以为每 15 分钟一次，并且可以通过使用 [**TimeTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.TimeTrigger) 将它们设置在特定时间运行。 有关详细信息，请参阅[通过计时器运行后台任务](run-a-background-task-on-a-timer-.md)。 |
| **推送通知** | 后台任务响应 [**PushNotificationTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.PushNotificationTrigger) 以接收原始推送通知。 |

**注意**  

通用 Windows 应用必须在注册任何后台触发器类型之前调用 [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync)。

若要确保通用 Windows 应用在你发布更新后继续正常运行，请在启动已经过更新的应用时调用 [**RemoveAccess**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.removeaccess)，然后调用 [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync)。 有关详细信息，请参阅[后台任务指南](guidelines-for-background-tasks.md)。

**限制触发器实例数：** 应用可注册的某些触发器的实例数存在限制。 对于应用的每个实例，应用只能注册一次 [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger)、[MediaProcessingTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.mediaprocessingtrigger) 和 [DeviceUseTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.deviceusetrigger?f=255&MSPPError=-2147217396)。 如果应用超出此限制，注册会引发异常。

## <a name="system-event-triggers"></a>系统事件触发器

[  **SystemTriggerType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTriggerType) 枚举表示以下系统事件触发器：

| 触发器名称            | 说明                                                       |
|-------------------------|-------------------------------------------------------------------|
| **UserPresent**         | 后台任务在用户出现后触发。   |
| **UserAway**            | 后台任务在用户离开后触发。    |
| **ControlChannelReset** | 后台任务在控件通道初始化后触发。 |
| **SessionConnected**    | 后台任务在会话连接后触发。   |

   
以下系统事件触发器将指示用户何时将应用移到或移出锁屏界面。

| 触发器名称                     | 说明                                  |
|----------------------------------|----------------------------------------------|
| **LockScreenApplicationAdded**   | 向锁屏中添加应用磁贴。     |
| **LockScreenApplicationRemoved** | 从锁屏中删除应用磁贴。 |

 
## <a name="background-task-resource-constraints"></a>后台任务资源限制

后台任务时轻型的。 使后台执行保持在最低程度可确保前台应用和电池使用时间的最佳用户体验。 通过对后台任务应用资源限制来实施该操作。

后台任务限制为在 30 秒的时钟时间内使用。

### <a name="memory-constraints"></a>内存约束

由于对于内存较低的设备的资源约束，后台任务可能具有内存限制，该限制决定了后台任务可以使用的内存上限。 如果后台任务尝试执行超过此限制的操作，则该操作将失败，并可能生成内存不足的异常情况，但该任务可以处理此情况。 如果该任务无法处理内存不足的异常情况，或者由于尝试的操作的性质导致无法生成内存不足的异常情况，任务将立即终止。  

你可以使用 [**MemoryManager**](https://docs.microsoft.com/uwp/api/Windows.System.MemoryManager) PAI 查询当前内存使用量和限制，以发现上限（若有），并监视正在进行的后台任务内存使用量。

### <a name="per-device-limit-for-apps-with-background-tasks-for-low-memory-devices"></a>内存较低的设备上带有后台任务的应用在每台设备上的限制

在内存受限的设备上，对于可以安装在一台设备上并在任何给定时间使用后台任务的应用数量有所限制。 如果超出该数量，将无法调用注册所有后台任务时所需的 [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync)。

### <a name="battery-saver"></a>节电模式

除非你豁免你的应用，以便它可以在节电模式打开时仍可以运行后台任务和接收推送通知，否则当节电模式功能启用时，如果设备未连接到外部电源且电量低于指定剩余电量，它将阻止后台任务运行。 这不会阻止你注册后台任务。

但是，对于企业应用程序和将不在 Microsoft Store 中发布的应用程序，请参阅[无限期在后台运行](run-in-the-background-indefinetly.md)，了解如何使用功能在后台无限期运行后台任务或扩展执行会话。

## <a name="background-task-resource-guarantees-for-real-time-communication"></a>后台任务资源保证实时通信

为了防止资源配额干涉实时通信功能，后台任务使用 [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) 和 [**PushNotificationTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.PushNotificationTrigger) 接收用于每次运行任务所保证的 CPU 资源配额。 资源配额如上所述，并为这些后台任务保持不变。

你的应用不必采用任何不同的方式即可获取 [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) 和 [**PushNotificationTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.PushNotificationTrigger) 后台任务的保证资源配额。 系统始终将这些后台任务视为关键后台任务。

## <a name="maintenance-trigger"></a>维护触发

维护任务仅在设备插入到交流电源时才会运行。 有关详细信息，请参阅[使用维护触发器](use-a-maintenance-trigger.md)。

## <a name="background-tasks-for-sensors-and-devices"></a>传感器和设备的后台任务

你的应用可以使用 [**DeviceUseTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceUseTrigger) 类从后台任务访问传感器和外围设备。 你可以针对运行时间较长的操作（例如数据同步或监视）使用此触发器。 与用于系统事件的任务不同，**DeviceUseTrigger** 任务仅在你的应用在前台运行且在其上无法设置任何条件时触发。

> [!IMPORTANT]
> **DeviceUseTrigger** 和 **DeviceServicingTrigger** 不能用于进程内后台任务。

某些关键设备操作（如长时间运行的固件更新）无法通过 [**DeviceUseTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceUseTrigger) 执行。 此类操作仅可以在电脑上通过使用 [**DeviceServicingTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.DeviceServicingTrigger) 的特权应用执行。 *特权应用*是指由设备制造商授权执行这些操作的应用。 设备元数据用于指定已指派哪个应用（如果有）作为设备的特权应用。 有关详细信息，请参阅[Microsoft Store 设备应用的设备同步和更新](https://msdn.microsoft.com/library/windows/hardware/dn265139(v=vs.85).aspx)

## <a name="managing-background-tasks"></a>管理后台任务

后台任务可以使用事件和本地存储向你的应用报告进度、完成和取消。 你的应用还可以捕捉由后台任务引发的异常，并在应用更新过程中管理后台任务注册。 有关详细信息，请参阅：

[处理取消的后台任务](handle-a-cancelled-background-task.md)  
[监视后台任务进度和完成](monitor-background-task-progress-and-completion.md)

在应用启动期间检查你的后台任务注册。 确保你的应用的未分组后台任务显示在 BackgroundTaskBuilder.AllTasks 中。 重新注册未显示的后台任务。 注销任何不再需要的任务。 这可确保每次应用启动时所有后台任务注册都是最新的。

## <a name="related-topics"></a>相关主题

**Windows 10 中的多任务的概念指南**

* [启动、恢复和多任务](index.md)

**相关的后台任务指南**

* [后台任务指南](guidelines-for-background-tasks.md)
* [从后台任务访问传感器和设备](access-sensors-and-devices-from-a-background-task.md)
* [创建和注册进程内后台任务](create-and-register-an-inproc-background-task.md)
* [创建和注册进程外后台任务](create-and-register-a-background-task.md)
* [将进程外后台任务转换为进程内后台任务](convert-out-of-process-background-task.md)
* [调试后台任务](debug-a-background-task.md)
* [在应用程序清单中声明后台任务](declare-background-tasks-in-the-application-manifest.md)
* [分组后台任务注册](group-background-tasks.md)
* [处理取消的后台任务](handle-a-cancelled-background-task.md)
* [如何在 UWP 应用中触发挂起、继续和后台事件（调试时）](https://docs.microsoft.com/visualstudio/debugger/how-to-trigger-suspend-resume-and-background-events-for-windows-store-apps-in-visual-studio)
* [监视后台任务进度和完成](monitor-background-task-progress-and-completion.md)
* [在后台播放媒体](https://docs.microsoft.com/windows/uwp/audio-video-camera/background-audio)
* [注册后台任务](register-a-background-task.md)
* [使用后台任务响应系统事件](respond-to-system-events-with-background-tasks.md)
* [在计时器上运行后台任务](run-a-background-task-on-a-timer-.md)
* [更新 UWP 应用时运行后台任务](run-a-background-task-during-updatetask.md)
* [在后台无限期运行](run-in-the-background-indefinetly.md)
* [设置后台任务的运行条件](set-conditions-for-running-a-background-task.md)
* [从应用触发后台任务](trigger-background-task-from-app.md)
* [使用后台任务更新动态磁贴](update-a-live-tile-from-a-background-task.md)
* [使用维护触发器](use-a-maintenance-trigger.md)
