---
title: 设置游戏项目
description: 开发游戏的第一步是在 Microsoft Visual Studio 中设置一个项目。 为游戏开发专门配置项目后，可以在以后将其重新用作模板类型。
ms.assetid: 9fde90b3-bf79-bcb3-03b6-d38ab85803f2
ms.date: 06/24/2020
ms.topic: article
keywords: windows 10, uwp, 游戏, 设置, directx
ms.localizationpriority: medium
ms.openlocfilehash: c192a81208c7cf372e27d9fb30233f8391939688
ms.sourcegitcommit: 73ec979ce6b9701e7135fd0541bf932b0847908e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881700"
---
# <a name="set-up-the-game-project"></a>设置游戏项目

> [!NOTE]
> 本主题是 [使用 DirectX 教程系列 (UWP) 游戏创建简单通用 Windows 平台](tutorial--create-your-first-uwp-directx-game.md) 的一部分。 该链接上的主题设置了序列的上下文。

开发游戏的第一步是在 Microsoft Visual Studio 中创建一个项目。 为游戏开发专门配置项目后，可以在以后将其重新用作模板类型。

## <a name="objectives"></a>目标

* 使用项目模板在 Visual Studio 中创建一个新项目。
* 通过检查 **App** 类的源文件来了解游戏的入口点和初始化。
* 查看游戏循环。
* 查看项目的 **appxmanifest.xml** 文件。

## <a name="create-a-new-project-in-visual-studio"></a>在 Visual Studio 中创建新项目

> [!NOTE]
> 有关设置 Visual Studio 以进行 C++/WinRT 部署的信息 &mdash; 包括安装和使用 C++/WinRT Visual Studio 扩展 (VSIX) 和 NuGet 包（两者共同提供项目模板，并生成支持）的信息 &mdash; 请参阅[适用于 C++/WinRT 的 Visual Studio 支持](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)。

首先安装 (或更新以) 最新版本的 c + +/WinRT Visual Studio 扩展 (VSIX) ;请参阅上述说明。 然后，在 Visual Studio 中，基于 **核心应用 (c + +/WinRT)** 项目模板创建一个新项目。 面向 Windows SDK 的最新正式发布（非预览）版本。

## <a name="review-the-app-class-to-understand-iframeworkviewsource-and-iframeworkview"></a>查看 **App** 类以了解 **IFrameworkViewSource** 和 **IFrameworkView**

在核心应用项目中，打开源代码文件 `App.cpp` 。 在中， **应用程序** 类的实现表示应用程序及其生命周期。 当然，在这种情况下，我们知道该应用程序是一个游戏。 但我们将它称为 *应用程序* ，以便更常见地讨论通用 WINDOWS 平台 (UWP) 应用程序如何初始化。

### <a name="the-wwinmain-function"></a>WWinMain 函数

**WWinMain** 函数是应用的入口点。 下面是 **wWinMain** 与) 的 (`App.cpp` 。

```cppwinrt
int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    CoreApplication::Run(winrt::make<App>());
}
```

我们生成 **app** 类的实例， (这是创建) 的 **应用** 的实例，并将其传递给静态 [**CoreApplication**](/uwp/api/windows.applicationmodel.core.coreapplication.run) 方法。 请注意， **CoreApplication** 需要一个 [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) 接口。 因此， **App** 类需要实现该接口。

本主题后面的两个部分介绍了 [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) 和 [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) 接口。 这些界面 (和 **CoreApplication**) 表示应用程序使用 *视图提供程序* 提供 Windows 的方式。 Windows 使用该视图提供程序将您的应用程序与 Windows shell 连接起来，以便您可以处理应用程序生命周期事件。

### <a name="the-iframeworkviewsource-interface"></a>IFrameworkViewSource 接口

**App** 类确实实现了 [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource)，正如下面的列表中所示。

```cppwinrt
struct App : winrt::implements<App, IFrameworkViewSource, IFrameworkView>
{
    ...
    IFrameworkView CreateView()
    {
        return *this;
    }
    ...
}
```

实现 **IFrameworkViewSource** 的对象是一个 *视图提供程序工厂* 对象。 该对象的工作是制造并返回一个 *视图提供程序* 对象。

**IFrameworkViewSource** 具有单一方法 [**IFrameworkViewSource：： CreateView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource.createview)。 Windows 对传递给 **CoreApplication** 的对象调用。 如上所示，该方法的 **App：： CreateView** 实现返回 `*this` 。 换句话说， **应用程序** 对象将返回自身。 由于 **IFrameworkViewSource：： CreateView** 的返回值类型为 [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource)，因此 **应用** 类也需要实现 *该* 接口。 在上面的列表中可以看到它。

### <a name="the-iframeworkview-interface"></a>IFrameworkView 接口

实现 [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) 的对象是一个 *视图提供程序* 对象。 现在，我们提供了具有该视图提供程序的窗口。 这就是我们在 **wWinMain** 中创建的同一个 **应用程序** 对象。 因此， **App** 类既用作 *视图提供程序工厂* ，也用作 *视图提供程序*。

现在，Windows 可以调用 **IFrameworkView** 方法的 **应用** 类实现。 在这些方法的实现中，你的应用程序有机会执行一些任务，如初始化、开始加载所需的资源、连接相应的事件处理程序，以及接收你的应用程序将用于显示其输出的 [**CoreWindow**](/uwp/api/windows.ui.core.corewindow) 。

**IFrameworkView** 方法的实现按下面所示的顺序进行调用。

- [**初始化**](/uwp/api/windows.applicationmodel.core.iframeworkview.initialize)
- [**SetWindow**](/uwp/api/windows.applicationmodel.core.iframeworkview.setwindow)
- [**加载**](/uwp/api/windows.applicationmodel.core.iframeworkview.load)
- 引发 [**CoreApplicationView：：已激活**](/uwp/api/windows.applicationmodel.core.coreapplicationview.activated) 事件。 因此，如果您 (可以选择) 注册来处理该事件，则此时将调用 **OnActivated** 处理程序。
- [**运行**](/uwp/api/windows.applicationmodel.core.iframeworkview.run)
- [**撤消**](/uwp/api/windows.applicationmodel.core.iframeworkview.uninitialize)

下面是 **App** 类 (在) 中的主干 `App.cpp` ，其中显示了这些方法的签名。

```cppwinrt
struct App : winrt::implements<App, IFrameworkViewSource, IFrameworkView>
{
    ...
    void Initialize(Windows::ApplicationModel::Core::CoreApplicationView const& applicationView) { ... }
    void SetWindow(Windows::UI::Core::CoreWindow const& window) { ... }
    void Load(winrt::hstring const& entryPoint) { ... }
    void OnActivated(
        Windows::ApplicationModel::Core::CoreApplicationView const& applicationView,
        Windows::ApplicationModel::Activation::IActivatedEventArgs const& args) { ... }
    void Run() { ... }
    void Uninitialize() { ... }
    ...
}
```

这只是 **IFrameworkView** 的简介。 在 [定义游戏的 UWP 应用框架](tutorial--building-the-games-uwp-app-framework.md)中，我们更详细地介绍了这些方法以及如何实现这些方法。

### <a name="tidy-up-the-project"></a>整理项目

从项目模板创建的核心应用项目包含我们应该在此时整理的功能。 然后，可以使用该项目重新创建 (**Simple3DGameDX**) 的拍摄库游戏。 对中的 **App** 类进行以下更改 `App.cpp` 。

- 删除其数据成员。
- Delete **OnPointerPressed**、 **OnPointerMoved** 和 **AddVisual**
- 删除 **SetWindow** 中的代码。

项目将生成并运行，但它将仅在工作区中显示纯色。

## <a name="the-game-loop"></a>游戏循环

若要了解游戏循环的外观，请查看下载的 [Simple3DGameDX](/samples/microsoft/windows-universal-samples/simple3dgamedx/) 示例游戏的源代码。

**App** 类有一个名为 "GameMain" 的数据成员，其类型 *M_main* 为 " "。 与此类似，此成员用于 **App：： Run** 。

```cppwinrt
void Run()
{
    m_main->Run();
}
```

可以在中找到 **GameMain：： Run** `GameMain.cpp` 。 这是游戏的主要循环，这里提供了一个非常粗略的内容大纲，显示最重要的功能。

```cppwinrt
void GameMain::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible)
        {
            CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
            Update();
            m_renderer->Render();
            m_deviceResources->Present();
        }
        else
        {
            CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
}
```

下面简要介绍了此主要游戏循环执行的操作。

如果游戏窗口未关闭，请调度所有事件，更新计时器，然后呈现并显示图形管道的结果。 还有很多关于这些问题的说明，我们将在主题 [定义游戏的 UWP 应用框架](tutorial--building-the-games-uwp-app-framework.md)、 [呈现框架 I：](tutorial--assembling-the-rendering-pipeline.md)渲染和 [渲染框架 II：游戏渲染](tutorial-game-rendering.md)时执行此操作。 但这是 UWP DirectX 游戏的基本代码结构。

## <a name="review-and-update-the-packageappxmanifest-file"></a>检查和更新 package.appxmanifest 文件

**Appxmanifest.xml** 文件包含有关 UWP 项目的元数据。 这些元数据用于打包和启动游戏，并用于提交到 Microsoft Store。 该文件还包含一些重要信息，播放机的系统使用该信息来提供对游戏需要运行的系统资源的访问。

在 **解决方案资源管理器** 中双击 **appxmanifest.xml** 文件，启动 **清单设计器**。

![package.appx 清单编辑器的屏幕截图。](images/simple-dx-game-setup-app-manifest.png)

有关 **package.appxmanifest** 文件和打包的详细信息，请参阅 [清单设计器](/previous-versions/br230259(v=vs.140))。 现在，查看 " **功能** " 选项卡，并查看提供的选项。

![显示 Direct3D 应用的默认功能的屏幕截图。](images/simple-dx-game-setup-capabilities.png)

如果未选择游戏所使用的功能（如访问适用于全球高分数的 **Internet** ），则无法访问相应的资源和功能。 创建新游戏时，请确保选择游戏所需的 Api 所需的任何功能。

现在，让我们看看 **Simple3DGameDX** 示例游戏附带的文件的其余部分。

## <a name="review-other-important-libraries-and-source-code-files"></a>查看其他重要库和源代码文件

如果你确实想要为自己创建一种游戏项目模板，以便你可以将其作为开始点，以供将来项目使用，则你将需要复制 `GameMain.h` 和 `GameMain.cpp` 传出你下载的 [Simple3DGameDX](/samples/microsoft/windows-universal-samples/simple3dgamedx/) 项目，并将其添加到新的 Core 应用项目中。 研究这些文件，了解它们的作用，并删除任何特定于 **Simple3DGameDX** 的内容。 还注释掉依赖于尚未复制的代码的任何内容。 只需通过一个示例，就 `GameMain.h` 依赖于 `GameRenderer.h` 。 在将更多文件复制到 **Simple3DGameDX** 中时，可以取消注释。

下面是有关 **Simple3DGameDX** 中的某些文件的简短调查，你会发现模板中包含这些文件（如果你要做的话）。 在任何情况下，这些方法对于了解 **Simple3DGameDX** 本身的工作方式同样重要。

|源文件|文件文件夹|说明|
|------------------------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DeviceResources.h/.cpp|实用程序|定义 **DeviceResources** 类，该类控制所有 DirectX [设备资源](tutorial--assembling-the-rendering-pipeline.md#resource)。 还定义了 **IDeviceNotify** 接口，用于通知应用程序图形适配器设备丢失或重新创建。|
|DirectXSample.h|实用程序|实现 helper 函数（如 **ConvertDipsToPixels**）。 **ConvertDipsToPixels** 将以与设备无关的像素 (DIP) 为单位的长度转换为以物理像素为单位的长度。|
|GameTimer/.cpp|实用程序|定义用于游戏或交互呈现应用的高分辨率计时器。|
|GameRenderer.h/.cpp|渲染|定义 **GameRenderer** 类，该类实现基本渲染管道。|
|GameHud/.cpp|渲染|定义一个类，该类使用 Direct2D 和 DirectWrite 呈现游戏 (HUD) 的显示。|
|VertexShader. hlsl 和 VertexShaderFlat hlsl|着色器|包含基本顶点着色器 (HLSL) 代码的高级着色器语言。|
|PixelShader. hlsl 和 PixelShaderFlat hlsl|着色器|包含基本像素着色器 (HLSL) 代码的高级着色器语言。|
|ConstantBuffers. .hlsli|着色器|包含用于将模型视图投影 (MVP) 矩阵和每个顶点的数据传递到顶点着色器的常量缓冲区和着色器结构的数据结构定义。|
|pch.h/.cpp|不适用|包含常见的 c + +/WinRT、Windows 和 DirectX 包括。|

### <a name="next-steps"></a>后续步骤

此时，我们已展示了如何为 DirectX 游戏创建新的 UWP 项目、查看其中一些部分，并开始考虑如何将该项目转换为一种可重复使用的游戏模板。 还介绍了 **Simple3DGameDX** 示例游戏的一些重要部分。

下一节将 [定义游戏的 UWP 应用框架](tutorial--building-the-games-uwp-app-framework.md)。 我们将更密切地探讨 **Simple3DGameDX** 的工作方式。