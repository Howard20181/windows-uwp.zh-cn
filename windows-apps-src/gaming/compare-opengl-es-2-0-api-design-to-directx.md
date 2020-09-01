---
title: 规划从 OpenGL ES 2.0 到 Direct3D 的移植
description: 如果你移植 iOS 或 Android 平台中的游戏，那么你可能需要在 OpenGL ES 2.0 方面进行大量投资。
ms.assetid: a31b8c5a-5577-4142-fc60-53217302ec3a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 游戏, opengl, direct3d
ms.localizationpriority: medium
ms.openlocfilehash: dddbf3a1009ccbaeb7fd0695d995cd17ba6182d3
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89165341"
---
# <a name="plan-your-port-from-opengl-es-20-to-direct3d"></a>规划从 OpenGL ES 2.0 到 Direct3D 的移植




**重要的 API**

-   [Direct3D 11](/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11)
-   [Visual C++](/previous-versions/60k1461a(v=vs.140))

如果你移植 iOS 或 Android 平台中的游戏，那么你可能需要在 OpenGL ES 2.0 方面进行大量投资。 如果你准备将你的图形管道代码库移动到 Direct3D 11 和 Windows 运行时，那么在开始之前你应该考虑以下事项。

大多数移植工作通常涉及初始浏览该代码库，然后在两个模型之间映射常用的 API 和模式。 你会发现，如果你花一段时间来阅读和查看该主题，那么这个过程就会变得容易一些。

下面是将图形从 OpenGL ES 2.0 移植到 Direct3D 11 时要注意的一些事项。

## <a name="notes-on-specific-opengl-es-20-providers"></a>有关特定 OpenGL ES 2.0 提供商的说明


本部分中的移植主题参考了由 Khronos Group 创建的 OpenGL ES 2.0 规范的 Windows 实现。 所有 OpenGL ES 2.0 代码示例都是使用 Visual Studio 2012 和 Windows C 基本语法开发的。 如果你使用的是 Objective-C (iOS) 或 Java (Android) 代码库，那么请注意，所提供的 OpenGL ES 2.0 代码示例可能没有使用类似的 API 调用语法或参数。 本指南尽量保持与平台无关。

本文档对 OpenGL ES 代码和参考仅使用 2.0 规范 API。 如果是从 OpenGL ES 1.1 或 3.0 进行移植，那么该内容仍然有用，但是某些 OpenGL ES 2.0 代码示例和上下文可能不太熟悉。

这些主题中的 Direct3D 11 示例使用具有组件扩展 (CX) 的 Microsoft Windows C++。 有关此版本的 c + + 语法的详细信息，请参阅[运行时平台的组件扩展](/cpp/windows/component-extensions-for-runtime-platforms)和[ (c + + \\ CX) 的快速参考](/cpp/cppcx/quick-reference-c-cx) [Visual C++](/previous-versions/60k1461a(v=vs.140))。

## <a name="understand-your-hardware-requirements-and-resources"></a>了解硬件要求和资源


OpenGL ES 2.0 所支持的图形处理功能集大致映射到 Direct3D 9.1 中提供的功能。 如果你想利用 Direct3D 11 中提供的更高级的功能，请在规划移植时查看 [Direct3D 11](/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11) 文档， 或在完成初始工作之后查看[从 DirectX 9 移植到通用 Windows 平台 (UWP)](porting-your-directx-9-game-to-windows-store.md) 主题。

若要使你的初始移植工作更简单，请从 Visual Studio Direct3D 模板开始。 该模板为你提供已配置好的基本呈现器，并且支持 UWP 应用功能 （如在窗口更改时重新创建资源）以及 Direct3D 功能级别。

## <a name="understand-direct3d-feature-levels"></a>了解 Direct3D 功能级别


Direct3D 11 为 \_ 11 1 (direct3d 9.1) 提供对硬件 "功能级别" 的支持 \_ 。 这些功能级别指示某些图形功能和资源的可用性。 通常，大多数 OpenGL ES 2.0 平台支持 Direct3D 9.1 (功能级别 9 \_ 1) 功能集。

## <a name="review-directx-graphics-features-and-apis"></a>查看 DirectX 图形功能和 API


| API 系列                                                | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [DXGI](/windows/desktop/direct3ddxgi/dx-graphics-dxgi)                     | DirectX 图形基础结构 (DXGI) 提供图形硬件和 Direct3D 之间的接口。 它使用 [**IDXGIAdapter**](/windows/desktop/api/dxgi/nn-dxgi-idxgiadapter) 和 [**IDXGIDevice1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgidevice2) COM 接口设置设备适配器和硬件配置。 使用它来创建和配置你的缓冲区以及其他窗口资源。 值得注意的是，[**IDXGIFactory2**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2) 工厂模式 iis 用来获取图形资源，包括交换链（一组帧缓冲区）。 由于 DXGI 拥有交换链，因此 [**IDXGISwapChain1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1) 接口用来向屏幕提供帧。 |
| [Direct3D](/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11)       | Direct3D 是一组 API，这些 API 提供图形接口的视觉表示并且允许你使用它绘制图形。 在功能方面，版本 11 大致能够与 OpenGL 4.3 相媲美。 （另一方面，尽管 OpenGL ES 2.0 与 DirectX9 和 OpenGL 2.0 在功能方面非常相似，但它具有 OpenGL 3.0 的统一着色器管道。）大多数繁重的工作是通过 ID3D11Device1 和 ID3D11DeviceContext1 接口完成的，这两个接口分别提供对各个资源和子资源以及呈现上下文的访问。                                                                                                                                          |
| [Direct2D](/windows/desktop/Direct2D/direct2d-portal)                      | Direct2D 提供一组用于 GPU 加速 2D 呈现的 API。 在作用方面，它与 OpenVG 非常相似。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| [DirectWrite](/windows/desktop/DirectWrite/direct-write-portal)            | DirectWrite 提供一组用于 GPU 加速高质量字体呈现的 API。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| [DirectXMath](/windows/desktop/dxmath/directxmath-portal)                  | DirectXMath 提供一组 API 和宏，用于处理常见的线性代数和三角函数类型、值以及函数。 这些类型和函数设计为与 Direct3D 及其着色器操作很好地结合使用。                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| [DirectX HLSL](/windows/desktop/direct3dhlsl/dx-graphics-hlsl-common-core) | Direct3D 着色器使用的当前 HLSL 语法。 它实现 Direct3D 着色器模型 5.0。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

 

## <a name="review-the-windows-runtime-apis-and-template-library"></a>查看 Windows 运行时 API 和模板库


Windows 运行时 API 为 UWP 应用提供整体基础结构。 [在此处](/uwp/api/)进行查看。

移植图形管道时使用的主要 Windows 运行时 API 包括：

-   [**Windows：： UI：： Core：： CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow)
-   [**Windows::UI::Core::CoreDispatcher**](/uwp/api/Windows.UI.Core.CoreDispatcher)
-   [**Windows::ApplicationModel::Core::IFrameworkView**](/uwp/api/Windows.ApplicationModel.Core.IFrameworkView)
-   [**Windows：： Windows.applicationmodel.resources.core：： Core：： CoreApplicationView**](/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView)

此外，Windows 运行时 C++ 模板库 (WRL) 是提供创作和使用 Windows 运行时组件的低级方法模板库。 最好将 UWP 应用的 Direct3D 11 API 与该库中的接口和类型结合使用，如智能指针 ([ComPtr](/cpp/windows/comptr-class))。 有关 WRL 的详细信息，请阅读 [Windows 运行时 C++ 模板库(WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl)。

## <a name="change-your-coordinate-system"></a>更改坐标系


有时容易让移植工作混乱的一个不同之处是要将 OpenGL 的传统右手坐标系更改为 Direct3D 默认的左手坐标系。 坐标模型方面的这个更改会影响游戏的很多部分，从顶点缓冲区的设置和配置到很多矩阵数学函数。 需要进行两个最重要的更改是：

-   调换三角形顶点的顺序以便 Direct3D 从前面按顺时针方向遍历它们。 例如，如果你的顶点在 OpenGL 管道中的索引为 0、1 和 2，则会按照 0、2、1 的顺序将它们传递给 Direct3D。
-   使用视图矩阵将世界空间在 z 方向缩放 -1.0f，从而有效地反转 z 轴坐标。 为此，请在视图矩阵中将 M31、M32 以及 M33 位置处的值的符号反向（当将其移植到 [**Matrix**](/windows/desktop/direct3d9/matrix4x4) 类型时）。 如果 M34 不为 0，则也要将它的符号反向。

但是，Direct3D 可以支持右手坐标系。 DirectXMath 提供了很多可以在左手和右手坐标系上操作以及跨两个坐标系操作的函数。 这些函数可以用来保存某些原始网格数据和矩阵处理。 它们包括：

| DirectXMath 矩阵函数                                                   | 描述                                                                                                                 |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| [**XMMatrixLookAtLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlookatlh)                               | 使用一个相机位置、一个向上的方向和一个焦点为左手坐标系构建一个视图矩阵。       |
| [**XMMatrixLookAtRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlookatrh)                               | 使用一个相机位置、一个向上的方向和一个焦点为右手坐标系构建一个视图矩阵。      |
| [**XMMatrixLookToLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlooktolh)                               | 使用一个相机位置、一个向上的方向和一个相机方向为左手坐标系构建一个视图矩阵。  |
| [**XMMatrixLookToRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlooktorh)                               | 使用一个相机位置、一个向上的方向和一个相机方向为右手坐标系构建一个视图矩阵。 |
| [**XMMatrixOrthographicLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographiclh)                   | 为左手坐标系构建一个正交投影矩阵。                                                 |
| [**XMMatrixOrthographicOffCenterLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographicoffcenterlh) | 为左手坐标系构建一个自定义正交投影矩阵。                                           |
| [**XMMatrixOrthographicOffCenterRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographicoffcenterrh) | 为右手坐标系构建一个自定义正交投影矩阵。                                          |
| [**XMMatrixOrthographicRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographicrh)                   | 为右手坐标系构建一个正交投影矩阵。                                                |
| [**XMMatrixPerspectiveFovLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectivefovlh)               | 根据视野构建一个左手透视投影矩阵。                                                |
| [**XMMatrixPerspectiveFovRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectivefovrh)               | 根据视野构建一个右手透视投影矩阵。                                               |
| [**XMMatrixPerspectiveLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectivelh)                     | 构建一个左手透视投影矩阵。                                                                         |
| [**XMMatrixPerspectiveOffCenterLH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectiveoffcenterlh)   | 构建一个自定义版本的左手透视投影矩阵。                                                     |
| [**XMMatrixPerspectiveOffCenterRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectiveoffcenterrh)   | 构建一个自定义版本的右手透视投影矩阵。                                                    |
| [**XMMatrixPerspectiveRH**](/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectiverh)                     | 构建一个右手透视投影矩阵。                                                                        |

 

## <a name="opengl-es20-to-direct3d-11-porting-frequently-asked-questions"></a>OpenGL ES2.0 到 Direct3D 11 移植常见问题解答


-   问题：“通常情况下，我可以在我的 OpenGL 代码中搜索某些字符串或模式并将它们替换为 Direct3D 同等内容？”
-   解答：不可以。 OpenGL ES 2.0 和 Direct3D 11 来自不同的图形管道模型。 尽管它们表面上在概念和 API 之间有一些相似之处，如呈现上下文和着色器的实例化，但你也应该查看本指南以及 Direct3D 11 参考以便再次创建管道时你可以做出最佳选择，而不是尝试 1 对 1 映射。 但是，如果你从 GLSL 移植到 HLSL，那么为 GLSL 变量、内部函数以及函数创建一组常用的别名不仅会使移植更加容易，而且还可以保留唯一一组着色器代码文件。

 

 