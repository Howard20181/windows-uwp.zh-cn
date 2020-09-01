---
title: 移植游戏循环
description: 介绍如何实现通用 Windows 平台 (UWP) 游戏的窗口，以及如何显示游戏循环，包括如何构建 IFrameworkView 来控制全屏 CoreWindow。
ms.assetid: 070dd802-cb27-4672-12ba-a7f036ff495c
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, 游戏, 移植, 游戏循环, direct3d 9, directx 11
ms.localizationpriority: medium
ms.openlocfilehash: e62b7e6576ff1b39cbeba2c201f929952abd4105
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89159091"
---
# <a name="port-the-game-loop"></a>移植游戏循环



**摘要**

-   [第 1 部分：初始化 Direct3D 11](simple-port-from-direct3d-9-to-11-1-part-1--initializing-direct3d.md)
-   [第 2 部分：转换呈现框架](simple-port-from-direct3d-9-to-11-1-part-2--rendering.md)
-   第 3 部分：移植游戏循环


演示如何实现通用 Windows 平台 (UWP) 游戏的窗口以及如何使用游戏循环，包括如何构建 [**IFrameworkView**](/uwp/api/Windows.ApplicationModel.Core.IFrameworkView) 来控制全屏 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow)。 [将简单的 Direct3D 9 应用移植到 DirectX 11 和 UWP](walkthrough--simple-port-from-direct3d-9-to-11-1.md) 操作实例的第 3 部分。

## <a name="create-a-window"></a>创建窗口


若要设置一个具有 Direct3D 9 视区的桌面窗口，我们必须为桌面应用实现传统的窗口框架。 必须创建 HWND、设置窗口大小、 提供窗口处理回调、使其可见等。

UWP 环境具有更简单的系统。 不再设置传统的窗口，Microsoft Store 游戏使用 DirectX 实现 [**IFrameworkView**](/uwp/api/Windows.ApplicationModel.Core.IFrameworkView)。 对于要直接在应用容器内的 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow) 中运行的 DirectX 应用和游戏来说，存在该接口。

> **注意**   Windows 提供了指向资源（例如源应用程序对象和[**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow)）的托管指针。 请参阅 [**对象操作员 (^) 的句柄 **](/cpp/extensions/handle-to-object-operator-hat-cpp-component-extensions)。

 

你的“main”类需要从 [**IFrameworkView**](/uwp/api/Windows.ApplicationModel.Core.IFrameworkView) 继承并且实现五种 **IFrameworkView** 方法：[**Initialize**](/uwp/api/windows.applicationmodel.core.iframeworkview.initialize)、[**SetWindow**](/uwp/api/windows.applicationmodel.core.iframeworkview.setwindow)、[**Load**](/uwp/api/windows.applicationmodel.core.iframeworkview.load)、[**Run**](/uwp/api/windows.applicationmodel.core.iframeworkview.run) 和 [**Uninitialize**](/uwp/api/windows.applicationmodel.core.iframeworkview.uninitialize)。 除了创建 **IFrameworkView**（从本质上说，这是你的游戏所在的位置）之外，你还需要实现一个用于创建 **IFrameworkView** 实例的工厂类。 你的游戏仍然包含一个可执行文件，该文件具有一个名为 **main()** 的方法，但所有 main 均可以使用工厂来创建 **IFrameworkView** 实例。

main 函数

```cpp
//-----------------------------------------------------------------------------
// Required method for a DirectX-only app.
// The main function is only used to initialize the app's IFrameworkView class.
//-----------------------------------------------------------------------------
[Platform::MTAThread]
int main(Platform::Array<Platform::String^>^)
{
    auto direct3DApplicationSource = ref new Direct3DApplicationSource();
    CoreApplication::Run(direct3DApplicationSource);
    return 0;
}
```

IFrameworkView 工厂

```cpp
//-----------------------------------------------------------------------------
// This class creates our IFrameworkView.
//-----------------------------------------------------------------------------
ref class Direct3DApplicationSource sealed : 
    Windows::ApplicationModel::Core::IFrameworkViewSource
{
public:
    virtual Windows::ApplicationModel::Core::IFrameworkView^ CreateView()
    {
        return ref new Cube11();
    };
};
```

## <a name="port-the-game-loop"></a>移植游戏循环


下面我们看一看 Direct3D 9 实现中的游戏循环。 该代码位于应用的 main 函数中。 该循环的每次迭代都处理一个窗口消息或呈现一个帧。

Direct3D 9 桌面游戏中的游戏循环

```cpp
while(WM_QUIT != msg.message)
{
    // Process window events.
    // Use PeekMessage() so we can use idle time to render the scene. 
    bGotMsg = (PeekMessage(&msg, NULL, 0U, 0U, PM_REMOVE) != 0);

    if(bGotMsg)
    {
        // Translate and dispatch the message
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }
    else
    {
        // Render a new frame.
        // Render frames during idle time (when no messages are waiting).
        RenderFrame();
    }
}
```

该游戏循环非常相似 - 但在游戏的 UWP 版本中却更加简单：

游戏循环进入 [**IFrameworkView::Run**](/uwp/api/windows.applicationmodel.core.iframeworkview.run) 方法（而不是 **main()**），因为我们的游戏在 [**IFrameworkView**](/uwp/api/Windows.ApplicationModel.Core.IFrameworkView) 类中起作用。

不再实现消息处理框架和调用 [**PeekMessage**](/windows/desktop/api/winuser/nf-winuser-peekmessagea)，我们可以调用为我们的应用窗口的 [**CoreDispatcher**](/uwp/api/Windows.UI.Core.CoreDispatcher) 构建的 [**ProcessEvents**](/uwp/api/windows.ui.core.coredispatcher.processevents) 方法。 游戏循环无需分流和处理消息 - 只需调用 **ProcessEvents** 并继续。

Direct3D 11 Microsoft Store 游戏中的游戏循环

```cpp
// UWP apps should not exit. Use app lifecycle events instead.
while (true)
{
    // Process window events.
    auto dispatcher = CoreWindow::GetForCurrentThread()->Dispatcher;
    dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

    // Render a new frame.
    RenderFrame();
}
```

现在，我们已经拥有一个 UWP 应用，该应用设置与我们的 DirectX 9 示例相同的基本图形基础结构并呈现相同的彩色立方体。

## <a name="where-do-i-go-from-here"></a>我此时该如何操作？


为 [DirectX 11 移植 FAQ](directx-porting-faq.md) 创建书签。

DirectX UWP 模板包含可随时与游戏结合使用的强大 Direct3D 设备基础结构。 有关选取正确模板的指南，请参阅[从模板创建 DirectX 游戏项目](user-interface.md)。

若要深入了解 Microsoft Store 游戏开发文章，请访问以下文章：

-   [操作实例：使用 DirectX 的简单 UWP 游戏](tutorial--create-your-first-uwp-directx-game.md)
-   [游戏音频](working-with-audio-in-your-directx-game.md)
-   [游戏的移动外观控制](tutorial--adding-move-look-controls-to-your-directx-game.md)

 

 