---
title: 创建多实例通用 Windows 应用
description: 本主题介绍如何编写支持多实例的 UWP 应用。
keywords: 多实例 UWP
ms.date: 09/21/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: cdb8d87a63eba14ecb2dc25e3cb5451dce6cae60
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684639"
---
# <a name="create-a-multi-instance-universal-windows-app"></a>创建多实例通用 Windows 应用

本主题介绍如何创建多实例通用 Windows 平台 (UWP) 应用。

从 Windows 10 版本1803（10.0;版本17134），UWP 应用可以选择支持多个实例。 如果多实例 UWP 应用的一个实例正在运行，并发生后续的激活请求，平台将不会激活现有实例。 然而，它会创建一个新实例，而且在单独的进程中运行。

> [!IMPORTANT]
> JavaScript 应用程序支持多实例，但不支持多实例重定向。 由于 JavaScript 应用程序不支持多实例重定向，因此对于此类应用程序， [**AppInstance**](/uwp/api/windows.applicationmodel.appinstance)类不起作用。

## <a name="opt-in-to-multi-instance-behavior"></a>选择启用多实例行为

如果要创建新的多实例应用程序，可以安装 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 中提供的[多实例应用项目模板.VSIX](https://marketplace.visualstudio.com/items?itemName=AndrewWhitechapelMSFT.MultiInstanceApps)。 一旦安装该模板，可通过 **Visual C# > Windows 通用**（或**其他语言 > Visual C++ > Windows 通用**）下的**新建项目**对话框获取。

已安装两个模板：**多实例 UWP 应用**模板，提供用于创建多实例应用的模板，以及**多实例重定向 UWP 应用**模板，它提供用于构建的其他逻辑，可基于这些逻辑启动新实例或选择性地激活已启动的实例。 例如，你可能只需要一次编辑相同文档的一个实例，因此你会将该文件的实例打开到前台，而不是启动一个新的实例。

这两个模板都将 `SupportsMultipleInstances` 添加到 `package.appxmanifest` 文件中。 请注意命名空间前缀 `desktop4` 和 `iot2`：只有面向桌面的项目或物联网（IoT）项目才支持多实例。

```xml
<Package
  ...
  xmlns:desktop4="http://schemas.microsoft.com/appx/manifest/desktop/windows10/4"
  xmlns:iot2="http://schemas.microsoft.com/appx/manifest/iot/windows10/2"  
  IgnorableNamespaces="uap mp desktop4 iot2">
  ...
  <Applications>
    <Application Id="App"
      ...
      desktop4:SupportsMultipleInstances="true"
      iot2:SupportsMultipleInstances="true">
      ...
    </Application>
  </Applications>
   ...
</Package>
```

## <a name="multi-instance-activation-redirection"></a>多实例激活重定向

 对 UWP 应用的多实例支持不仅仅能够启动应用的多个实例。 当你考虑是启动应用的新实例还是激活已经运行的实例时，可以进行自定义。 例如，如果启动应用以编辑另一实例中已在编辑的文件，你可能会想要将激活操作重定向到该实例，而不是打开另一个已在编辑该文件的实例。

若要查看其工作原理，请观看此视频，了解如何创建多实例 UWP 应用。

> [!VIDEO https://www.youtube.com/embed/clnnf4cigd0]

如上所示，**多实例重定向 UWP 应用**模板将 `SupportsMultipleInstances` 添加到 package.appxmanifest 文件，并且还将 **Program.cs**（或 **Program.cpp**，如果使用的是模板的 C++ 版本）添加到包含 `Main()` 函数的项目中。 用于重定向激活操作的逻辑包含在 `Main` 函数中。 **Program.cs**的模板如下所示。

[**AppInstance. RecommendedInstance**](/uwp/api/windows.applicationmodel.appinstance.recommendedinstance)属性表示此激活请求的 shell 提供的首选实例（如果有）（如果没有，则为 `null`）。 如果 shell 提供了一个首选项，则可以将激活重定向到该实例，也可以在选择时将其忽略。

``` csharp
public static class Program
{
    // This example code shows how you could implement the required Main method to
    // support multi-instance redirection. The minimum requirement is to call
    // Application.Start with a new App object. Beyond that, you may delete the
    // rest of the example code and replace it with your custom code if you wish.

    static void Main(string[] args)
    {
        // First, we'll get our activation event args, which are typically richer
        // than the incoming command-line args. We can use these in our app-defined
        // logic for generating the key for this instance.
        IActivatedEventArgs activatedArgs = AppInstance.GetActivatedEventArgs();

        // If the Windows shell indicates a recommended instance, then
        // the app can choose to redirect this activation to that instance instead.
        if (AppInstance.RecommendedInstance != null)
        {
            AppInstance.RecommendedInstance.RedirectActivationTo();
        }
        else
        {
            // Define a key for this instance, based on some app-specific logic.
            // If the key is always unique, then the app will never redirect.
            // If the key is always non-unique, then the app will always redirect
            // to the first instance. In practice, the app should produce a key
            // that is sometimes unique and sometimes not, depending on its own needs.
            string key = Guid.NewGuid().ToString(); // always unique.
                                                    //string key = "Some-App-Defined-Key"; // never unique.
            var instance = AppInstance.FindOrRegisterInstanceForKey(key);
            if (instance.IsCurrentInstance)
            {
                // If we successfully registered this instance, we can now just
                // go ahead and do normal XAML initialization.
                global::Windows.UI.Xaml.Application.Start((p) => new App());
            }
            else
            {
                // Some other instance has registered for this key, so we'll 
                // redirect this activation to that instance instead.
                instance.RedirectActivationTo();
            }
        }
    }
}
```

`Main()` 是要运行的第一件事。 它在[**OnLaunched**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application#Windows_UI_Xaml_Application_OnLaunched_Windows_ApplicationModel_Activation_LaunchActivatedEventArgs_)和[**OnActivated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application#Windows_UI_Xaml_Application_OnActivated_Windows_ApplicationModel_Activation_IActivatedEventArgs_)之前运行。 这样便能够确定在应用中的任何其他初始化代码运行之前，是激活此实例还是其他实例。

以上代码决定是激活应用程序的现有实例还是新实例。 使用一个密钥来确定是否存在要激活的现有实例。 例如，如果可启动应用来[处理文件激活](https://docs.microsoft.com/windows/uwp/launch-resume/handle-file-activation)，则可能将文件名作为密钥。 然后，可检查是否已使用该密钥注册了应用的一个实例，然后激活它，而不是打开一个新实例。 这是代码背后的理念： `var instance = AppInstance.FindOrRegisterInstanceForKey(key);`

如果找到使用该密钥注册的实例，表示该实例已激活。 如果未找到密钥，则当前实例（当前正在运行 `Main` 的实例）将创建其应用程序对象并开始运行。

## <a name="background-tasks-and-multi-instancing"></a>后台任务和多实例

- 进程外后台任务支持多实例。 通常情况下，每个新的触发器都会产生后台任务的新实例（尽管从技术角度来讲，多个后台任务可能在同一个主机进程中运行）。 尽管如此，仍会创建后台任务的另一个实例。
- 进程内后台任务不支持多实例。
- 后台音频任务不支持多实例。
- 当应用注册后台任务时，通常首先会检查该任务是否已经注册，然后删除并重新注册它，或者不执行任何操作以保留现有的注册。 这仍是多实例应用的典型行为。 但是，多实例应用可能会选择在每个实例的基础上注册另一个后台任务名称。 这将导致多次注册相同的触发器，并且触发器触发时将激活多个后台任务实例。
- 应用服务为每个连接启动单独的应用服务后台任务实例。 这对多实例应用保持不变，即多实例应用的每个实例都将获得自己的应用服务后台任务实例。 

## <a name="additional-considerations"></a>其他注意事项

- 面向桌面和物联网 (IoT) 项目的 UWP 应用支持多实例。
- 若要避免发生争用和争用导致的问题，多实例应用需要采取措施对访问设置、应用本地存储以及能在多个实例之间共享的任何其他资源（例如用户文件、数据存储等）进行分区/同步。 提供了标准同步机制，如互斥体、信号量、事件等。
- 如果应用的 Package.appxmanifest 文件中有 `SupportsMultipleInstances`，则其扩展无需声明 `SupportsMultipleInstances`。 
- 如果将 `SupportsMultipleInstances` 添加到除后台任务或应用服务之外的任何其他扩展，并且托管该扩展的应用也不在其 Package.appxmanifest 文件中声明 `SupportsMultipleInstances`，则会发生架构错误。
- 应用可以使用清单中的[**资源**](https://docs.microsoft.com/windows/uwp/launch-resume/declare-background-tasks-in-the-application-manifest)组声明将多个后台任务分为同一主机。 这与多实例相冲突，在多实例中，每个激活操作会进入单独的主机。 因此，应用无法在其清单中同时声明 `SupportsMultipleInstances` 和 `ResourceGroup`。

## <a name="sample"></a>示例

有关多实例激活重定向的示例，请参阅[多实例示例](https://github.com/Microsoft/AppModelSamples/tree/master/Samples/BananaEdit)。

## <a name="see-also"></a>另请参阅

[AppInstance.FindOrRegisterInstanceForKey](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appinstance#Windows_ApplicationModel_AppInstance_FindOrRegisterInstanceForKey_System_String_)
[AppInstance.GetActivatedEventArgs](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appinstance#Windows_ApplicationModel_AppInstance_GetActivatedEventArgs)
[AppInstance.RedirectActivationTo](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appinstance#Windows_ApplicationModel_AppInstance_RedirectActivationTo)
[处理应用激活](https://docs.microsoft.com/windows/uwp/launch-resume/activate-an-app)
