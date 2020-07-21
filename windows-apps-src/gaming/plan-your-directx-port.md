---
title: 规划 DirectX 移植
description: 规划从 DirectX 9 到 DirectX 11 和通用 Windows 平台 (UWP) 的游戏移植项目 - 升级图形代码并将游戏放置到 Windows 运行时环境中。
ms.assetid: 3c0c33ca-5d15-ae12-33f8-9b5d8da08155
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, directx, 移植
ms.localizationpriority: medium
ms.openlocfilehash: 032eeaf2a17ef244287e25e6d9ff32a12c61e137
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258469"
---
# <a name="plan-your-directx-port"></a>规划 DirectX 移植



**摘要**

-   规划 DirectX 移植
-   [从 Direct3D 9 到 Direct3D 11 的重要更改](understand-direct3d-11-1-concepts.md)
-   [功能映射](feature-mapping.md)


规划从 DirectX 9 到 DirectX 11 和通用 Windows 平台 (UWP) 的游戏移植项目：升级图形代码并将游戏放置到 Windows 运行时环境中。

## <a name="plan-to-port-graphics-code"></a>规划移植图形代码


在开始将游戏移植到 UWP 之前，请务必确保游戏没有任何 Direct3D 8 残余。 确保游戏不包含固定函数管道的任何遗留物。 有关已弃用功能的完整列表（包括固定管道功能），请参阅[已弃用的功能](https://docs.microsoft.com/windows/desktop/direct3d10/d3d10-graphics-programming-guide-api-features-deprecated)。

从 Direct3D 9 升级到 Direct3D 11 不仅仅是搜索和替换的更改。 你需要知道 Direct3D 设备、设备上下文以及图形基础结构之间的差别，并且需要了解自从 Direct3D 9 以来的其他重要更改。 你可以通过阅读本部分中的其他主题来开始这个过程。

你必须将 D3DX 和 DXUT 帮助程序库替换为你自己的帮助程序库或替换为社区工具。 有关详细信息，请参阅[功能映射](feature-mapping.md)部分。

> **请注意**   可以使用[DirectX](https://github.com/Microsoft/DirectXTK)工具包或[DIRECTXTEX](https://github.com/Microsoft/DirectXTex)来替换以前由 D3DX 和 DXUT 提供的某些功能。

 

应使用着色器模型4第9级\_1 或 9\_3 功能将以汇编语言编写的着色器升级到 HLSL，并且需要将为效果库编写的着色器更新为较新版本的 HLSL 语法。 有关详细信息，请参阅[功能映射](feature-mapping.md)部分。

熟悉不同的 [Direct3D 功能级别](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel-intro)。 功能级别通过定义已知的功能集对广泛的视频硬件进行分类。 每个功能集都大致与 Direct3D 的版本相对应，从 9.1 到 11.2。 所有功能级别都使用 DirectX 11 API。

## <a name="plan-to-port-win32-ui-code-to-corewindow"></a>规划将 Win32 UI 代码移植到 CoreWindow


UWP 应用在为应用容器创建的窗口（称为 [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow)）中运行。 你的游戏通过从 [**IFrameworkView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.IFrameworkView) 继承来控制该窗口，与桌面窗口相比，该窗口要求的实现细节较少。 游戏的主循环将位于 [**IFrameworkView::Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.run) 方法中。

UWP 应用的生命周期与桌面应用的生命周期大相径庭。 需要经常保存该游戏，因为当发生暂停事件时，应用 仅有有限的时间来停止运行代码，并且你也希望确保在应用恢复时玩家可以立即回到他们之前所在的位置。 保存游戏的频率应该只需保持恢复时有连续的玩游戏体验即可，不能太过频繁，否则游戏保存会影响帧速率或者导致游戏时断时续。 当游戏从某个已终止状态恢复时，可能需要加载 游戏状态。

[DirectXMath](https://docs.microsoft.com/windows/desktop/dxmath/ovw-xnamath-progguide) 可以用作 D3DXMath 和 XNAMath 的替代项，如果你需要一个数学库，那么它可以派上用场。 DirectXMath 包含快速、便携的数据类型以及进行对齐和打包操作以便用于着色器的类型。

已经对原生库（如[互锁 API](https://docs.microsoft.com/windows/desktop/Sync/what-s-new-in-synchronization)）进行了扩展，以支持 ARM 内部函数。 如果游戏使用互锁 API， 可以继续在 DirectX 11 和 UWP 中使用它们。

我们的模板和代码示例使用目前你可能还不熟悉的 C++ 新功能。 例如，将异步方法 与 [**lambda expressions**](https://docs.microsoft.com/cpp/cpp/lambda-expressions-in-cpp) 结合使用可加载 Direct3D 资源，而无需阻止 UI 线程。

有两个概念供你经常使用：

-   托管参考（[ **^ 运算符**](https://docs.microsoft.com/cpp/windows/handle-to-object-operator-hat-cpp-component-extensions)）和[**托管类**](https://docs.microsoft.com/cpp/windows/classes-and-structs-cpp-component-extensions)（ref 类）是 Windows 运行时的基础部分。 你需要使用托管 ref 类与 Windows 运行时组件交互，例如 [**IFrameworkView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.IFrameworkView)（演练中有关于此内容的详细信息）。
-   使用 Direct3D 11 COM 接口时，使用 [**Microsoft::WRL::ComPtr**](https://docs.microsoft.com/cpp/windows/comptr-class) 模板类型以使 COM 指针更易于使用。

 

 




