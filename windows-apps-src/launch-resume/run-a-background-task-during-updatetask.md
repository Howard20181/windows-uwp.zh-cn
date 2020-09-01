---
title: 在 UWP 应用更新时运行后台任务
description: 了解如何创建在通用 Windows 平台 (UWP) 应用商店应用更新时运行的后台任务。
ms.date: 04/21/2017
ms.topic: article
keywords: windows 10，uwp，更新，后台任务，updatetask，后台任务
ms.localizationpriority: medium
ms.openlocfilehash: ff4dd5487357728b6a79f4c4d31a437075fcd006
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89164801"
---
# <a name="run-a-background-task-when-your-uwp-app-is-updated"></a>在 UWP 应用更新时运行后台任务

了解如何编写在通用 Windows 平台 (UWP) 应用商店应用更新后运行的后台任务。

在用户安装设备上安装的应用的更新后，操作系统调用 Update Task 后台任务。 这允许你的应用执行初始化任务，如初始化新的推送通知通道、更新数据库架构等，然后用户再启动你更新过的应用。

Update Task 与使用 [ServicingComplete](/uwp/api/Windows.ApplicationModel.Background.SystemTriggerType) 触发器启动后台任务不同，因为在后者中，你的应用在更新前必须运行至少一次来注册将用 **ServicingComplete** 触发器激活的后台任务。  Update Task 未注册，因此从未运行但已升级的应用仍将触发其更新任务。

## <a name="step-1-create-the-background-task-class"></a>步骤 1：创建后台任务类

与其他类型的后台任务相同，你以 Windows 运行时组件的形式实现 Update Task 后台任务。 要创建此组件，请按照[创建和注册进程外后台任务](./create-and-register-a-background-task.md)的**创建后台任务类**部分中的步骤进行操作。 步骤包括：

- 将 Windows 运行时组件项目添加到解决方案中。
- 创建从你的应用到该组件的引用。
- 在实现 [**IBackgroundTask**](/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask) 的组件中创建一个公共的密封类。
- 实现 [**Run**](/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) 方法，该方法是 Update Task 运行时调用的必需的入口点。 如果你要从后台任务进行异步调用，[创建和注册进程外后台任务](./create-and-register-a-background-task.md)介绍了如何通过 **Run** 方法使用延迟。

你无需注册此后台任务（**创建和注册进程外后台任务**主题的“注册要运行的后台任务”部分）即可使用 Update Task。 这是使用 Update Task 的主要原因，因为你无需在你的应用中添加任何代码来注册任务，应用也无需在更新前运行至少一次来注册后台任务。

下面的示例代码显示了 C# 中 Update Task 后台任务的基本起始点。 后台任务类本身以及后台任务项目中的所有其他类都必须为**公共**并且**密封**的。 你的后台任务类必须派生自 **IBackgroundTask** 并且有一个具有如下签名的公共 **Run()** 方法：

```cs
using Windows.ApplicationModel.Background;

namespace BackgroundTasks
{
    public sealed class UpdateTask : IBackgroundTask
    {
        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // your app migration/update code here
        }
    }
}
```

## <a name="step-2-declare-your-background-task-in-the-package-manifest"></a>步骤 2：在程序包清单中声明你的后台任务

在 Visual Studio 解决方案资源管理器中，右键单击 **Package.appxmanifest** 并单击**查看代码**查看程序包清单。 添加以下 `<Extensions>` XML 声明你的更新任务：

```XML
<Package ...>
    ...
  <Applications>  
    <Application ...>  
        ...
      <Extensions>  
        <Extension Category="windows.updateTask"  EntryPoint="BackgroundTasks.UpdateTask">  
        </Extension>  
      </Extensions>

    </Application>  
  </Applications>  
</Package>
```

在上面的 XML 中，确保 `EntryPoint` 属性设置为你的更新任务类的 namespace.class 名称。 该名称区分大小写。

## <a name="step-3-debugtest-your-update-task"></a>步骤 3：调试/测试你的更新任务

确保你已将应用部署到你的计算机以便进行更新。

在你的后台任务的 Run() 方法中设置一个断点。

![设置断点](images/run-func-breakpoint.png)

接下来，在解决方案资源管理器中右键单击你的应用的项目（不是后台任务项目），然后单击**属性**。 在应用程序“属性”窗口中，单击左侧的**调试**，然后选择**不启动，但在启动时调试代码**：

![设置调试设置](images/do-not-launch-but-debug.png)

接下来，为确保 UpdateTask 触发，请增加程序包的版本号。 在解决方案资源管理器中，双击应用的 **Package.appxmanifest** 文件以打开程序包设计器，然后更新**版本**号：

![更新版本](images/bump-version.png)

现在，在 Visual Studio 2019 中按 F5 时，将更新应用程序，系统将在后台激活你的 UpdateTask 组件。 调试此程序将自动连接到后台进程。 将命中你的断点，你可以单步执行更新代码逻辑。

后台任务完成时，你可以在同一调试会话中从 Windows“开始”菜单启动前台应用。 调试程序将再次自动连接，这此连接到你的前台进程，你可以单步执行应用的逻辑。

> [!NOTE]
> Visual Studio 2015 用户：以上步骤适用于 Visual Studio 2017 或 Visual Studio 2019。 如果你使用 Visual Studio 2015，则可以使用相同的技术触发和测试 UpdateTask，除非 Visual Studio 无法连接到它。 VS 2015 中的替代过程是设置 [ApplicationTrigger](./trigger-background-task-from-app.md)，它将 UpdateTask 设置为其入口点，并从前台应用直接触发执行。

## <a name="see-also"></a>另请参阅

[创建和注册进程外后台任务](./create-and-register-a-background-task.md)