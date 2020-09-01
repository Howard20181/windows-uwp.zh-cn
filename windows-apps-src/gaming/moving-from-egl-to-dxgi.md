---
title: 将 EGL 代码与 DXGI 和 Direct3D 进行比较
description: DirectX Graphics Interface (DXGI) 以及若干个 Direct3D API 所起的作用与 EGL 相同。 本主题帮助你从 EGL 的角度了解 DXGI 和 Direct3D 11。
ms.assetid: 90f5ecf1-dd5d-fea3-bed8-57a228898d2a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, egl, dxgi, direct3d
ms.localizationpriority: medium
ms.openlocfilehash: a9e521810c5220d412afb98d25a00f5ac499af1b
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89165211"
---
# <a name="compare-egl-code-to-dxgi-and-direct3d"></a>将 EGL 代码与 DXGI 和 Direct3D 进行比较




**重要的 API**

-   [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1)
-   [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1)
-   [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow)

DirectX Graphics Interface (DXGI) 以及若干个 Direct3D API 所起的作用与 EGL 相同。 本主题帮助你从 EGL 的角度了解 DXGI 和 Direct3D 11。

和 EGL 一样，DXGI 和 Direct3D 提供配置图形资源、获取你的着色器要绘制到的呈现上下文以及在窗口中显示结果的方法。 但是，DXGI 和 Direct3D 具有更多选项，因此从 EGL 移植时需要花费更多精力才能获得正确设置。

> **注意**   本指南基于适用于 EGL 1.4 的 Khronos 组开放式规范，如下所示： [Khronos 本机平台图形界面 (EGL 1.4 2011-版) \[ PDF \] ](https://www.khronos.org/registry/egl/specs/eglspec.1.4.20110406.pdf)。 本指南中未涉及语法中特定于其他平台和开发语言的差别。

 

## <a name="how-does-dxgi-and-direct3d-compare"></a>DXGI 和 Direct3D 如何进行比较？


与 DXGI 和 Direct3D 相比，EGL 的一大优势是，它开始绘制到窗口图面时相对比较简单。 这是因为 OpenGL ES 2.0（以及 EGL）是由多个平台供应商实现的规范，而 DXGI 和 Direct3D 是硬件供应商驱动程序必须符合的一个参考。 这意味着 Microsoft 必须实现一组 API，这组 API 能够支持最广泛的供应商功能集，而不是侧重于特定供应商提供的某个子集，或者是通过将特定于供应商的设置命令组合成更简单的 API。 另一方面，Direct3D 提供了一组简单的 API，这些 API 涉及非常广泛的图形硬件平台和功能级别，并且为使用该平台的开发人员提供了更多灵活性。

和 EGL 一样，DXGI 和 Direct3D 都提供用于以下行为的 API：

-   获取、读取并写入到帧缓冲区（在 DXGI 中称为“交换链”）。
-   将帧缓冲区与 UI 窗口相关联。
-   获取并配置要在其中进行绘制的呈现上下文。
-   向特定呈现上下文的图形管道发出命令。
-   创建和管理着色器资源，并将这些资源与呈现内容相关联。
-   呈现到特定呈现目标（如纹理）。
-   使用图形资源更新具有呈现结果的窗口显示图面。

若要查看配置图形管道的基本 Direct3D 过程，请查阅 Microsoft Visual Studio 2015 中的 DirectX 11 应用（通用 Windows）模板。 其中的基本呈现类为在其上设置 Direct3D 11 图形基础结构、配置基本资源以及支持通用 Windows 平台 (UWP) 应用功能（如屏幕旋转）提供了一个良好的基线。

与 Direct3D 11 相比，EGL 提供的 API 相对较少，如果不熟悉特定于该平台的命名和行话，则导航 EGL 可能会有很大难度。 下面这个简单概述可帮助你应对这种挑战。

首先，查看基本的 EGL 对象到 Direct3D 接口的映射：

| EGL 抽象 | 类似的 Direct3D 表示形式                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EGLDisplay**  | 在 Direct3D（适用于 UWP 应用）中，通过 [**Windows::UI::CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow)API（或显示 HWND 的 **ICoreWindowInterop** 接口）获取显示句柄。 适配器和硬件配置分别通过 [**IDXGIAdapter**](/windows/desktop/api/dxgi/nn-dxgi-idxgiadapter) 和 [**IDXGIDevice1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgidevice2) COM 接口进行设置。                                                                                                                                                                                                                                                           |
| **EGLSurface**  | 在 Direct3D 中，缓冲区和其他窗口资源（可见资源或屏幕外资源）由特定的 DXGI 接口创建和配置，其中包括 [**IDXGIFactory2**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2)，它是用于获取 DXGI 资源（如 [**IDXGISwapChain1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1)（显示缓冲区））的工厂模式实现。 表示图形设备及其资源的 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 通过 [**D3D11Device::CreateDevice**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) 获取。 对于呈现目标，使用 [**ID3D11RenderTargetView**](/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview) 接口。 |
| **EGLContext**  | 在 Direct3D 中，使用 [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) 接口配置命令并向图形管道发出命令。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **EGLConfig**   | 在 Direct3D 11 中，通过在 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 接口上使用方法创建和配置图形资源，如缓冲区、纹理、模具以及着色器。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

 

现在，下面介绍了在 DXGI 和适用于 UWP 应用的 Direct3D 中设置简单图形显示、资源和上下文的最基本过程。

1.  通过 调用 [**CoreWindow::GetForCurrentThread**](/uwp/api/windows.ui.core.corewindow.getforcurrentthread) 来为应用核心 UI 线程获取 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow) 对象的句柄。
2.  对于 UWP 应用，使用 [**IDXGIFactory2::CreateSwapChainForCoreWindow**](/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow) 从 [**IDXGIAdapter2**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiadapter2) 获取交换链，并向其传递在步骤 1 中获取的 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow) 引用。 反过来，你将获得 [**IDXGISwapChain1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1) 实例。 将其范围设置为呈现器对象及其呈现线程。
3.  通过调用 [**D3D11Device::CreateDevice**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) 方法获取 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 和 [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) 实例。 还要将它们的范围设置为呈现器对象。
4.  使用呈现器的 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 对象上的方法创建着色器、纹理和其他资源。
5.  使用呈现器的 [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) 对象上的方法定义缓冲区、运行着色器并管理管道阶段。
6.  当管道已执行且已将框架绘制到后台缓冲区时，使用 [**IDXGISwapChain1::Present1**](/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1) 将其显示到屏幕。

要更详细地检查此过程，请查看 [DirectX 图形入门](/windows/desktop/getting-started-with-directx-graphics)。 本文的其余部分介绍许多基本图形管道设置和管理的常见步骤。
> **注意**   Windows 桌面应用程序具有不同的 Api，用于获取 Direct3D 交换链（如[**D3D11Device：： CreateDeviceAndSwapChain**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdeviceandswapchain)），且不使用[**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow)对象。

 

## <a name="obtaining-a-window-for-display"></a>获取用于显示的窗口


在此示例中，eglGetDisplay 为特定于 Microsoft Windows 平台的窗口资源传递了一个 HWND。 其他平台（如 Apple 的 iOS (Cocoa) 和 Google 的 Android）对于窗口资源拥有不同的句柄或参考，并且调用语法也可能会有所不同。 获取显示之后，将其初始化，设置首选配置并创建一个图面（包含你可以绘制到其中的后台缓冲区）。

通过 EGL 获取显示并对其进行配置。

``` syntax
// Obtain an EGL display object.
EGLDisplay display = eglGetDisplay(GetDC(hWnd));
if (display == EGL_NO_DISPLAY)
{
  return EGL_FALSE;
}

// Initialize the display
if (!eglInitialize(display, &majorVersion, &minorVersion))
{
  return EGL_FALSE;
}

// Obtain the display configs
if (!eglGetConfigs(display, NULL, 0, &numConfigs))
{
  return EGL_FALSE;
}

// Choose the display config
if (!eglChooseConfig(display, attribList, &config, 1, &numConfigs))
{
  return EGL_FALSE;
}

// Create a surface
surface = eglCreateWindowSurface(display, config, (EGLNativeWindowType)hWnd, NULL);
if (surface == EGL_NO_SURFACE)
{
  return EGL_FALSE;
}
```

在 Direct3D 中，UWP 应用的主窗口由 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow) 对象表示，作为你为 Direct3D 构造的“视图提供程序”初始化过程的一部分，可以通过调用 [**CoreWindow::GetForCurrentThread**](/uwp/api/windows.ui.core.corewindow.getforcurrentthread) 来获得该对象。 （如果你使用的是 Direct3D-XAML 互操作，请使用 XAML 框架的视图提供程序。）[如何设置应用以显示视图](/previous-versions/windows/apps/hh465077(v=win.10))中介绍了创建 Direct3D 视图提供程序的过程。

获取用于 Direct3D 的 CoreWindow。

``` syntax
CoreWindow::GetForCurrentThread();
```

获取 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow) 参考之后，必须激活窗口，该窗口执行主对象的 **Run** 方法并开始进行窗口事件处理。 然后，创建一个 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 和一个 [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1)，并使用它们获取基础 [**IDXGIDevice1**](/windows/desktop/api/dxgi/nn-dxgi-idxgidevice1) 和 [**IDXGIAdapter**](/windows/desktop/api/dxgi/nn-dxgi-idxgiadapter) ，以便可以获取 [**IDXGIFactory2**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2) 对象，以便基于 [**DXGI \_ 交换 \_ 链 \_ DESC1**](/windows/desktop/api/dxgi1_2/ns-dxgi1_2-dxgi_swap_chain_desc1) 配置创建交换链资源。

在用于 Direct3D 的 CoreWindow 上配置并设置 DXGI 交换链。

``` syntax
// Called when the CoreWindow object is created (or re-created).
void SimpleDirect3DApp::SetWindow(CoreWindow^ window)
{
  // Register event handlers with the CoreWindow object.
  // ...

  // Obtain your ID3D11Device1 and ID3D11DeviceContext1 objects
  // In this example, m_d3dDevice contains the scoped ID3D11Device1 object
  // ...

  ComPtr<IDXGIDevice1>  dxgiDevice;
  // Get the underlying DXGI device of the Direct3D device.
  m_d3dDevice.As(&dxgiDevice);

  ComPtr<IDXGIAdapter> dxgiAdapter;
  dxgiDevice->GetAdapter(&dxgiAdapter);

  ComPtr<IDXGIFactory2> dxgiFactory;
  dxgiAdapter->GetParent(
    __uuidof(IDXGIFactory2), 
    &dxgiFactory);

  DXGI_SWAP_CHAIN_DESC1 swapChainDesc = {0};
  swapChainDesc.Width = static_cast<UINT>(m_d3dRenderTargetSize.Width); // Match the size of the window.
  swapChainDesc.Height = static_cast<UINT>(m_d3dRenderTargetSize.Height);
  swapChainDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM; // This is the most common swap chain format.
  swapChainDesc.Stereo = false;
  swapChainDesc.SampleDesc.Count = 1; // Don't use multi-sampling.
  swapChainDesc.SampleDesc.Quality = 0;
  swapChainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
  swapChainDesc.BufferCount = 2; // Use double-buffering to minimize latency.
  swapChainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL; // All UWP apps must use this SwapEffect.
  swapChainDesc.Flags = 0;

  // ...

  Windows::UI::Core::CoreWindow^ window = m_window.Get();
  dxgiFactory->CreateSwapChainForCoreWindow(
    m_d3dDevice.Get(),
    reinterpret_cast<IUnknown*>(window),
    &swapChainDesc,
    nullptr, // Allow on all displays.
    &m_swapChainCoreWindow);
}
```

准备帧后调用 [**IDXGISwapChain1::Present1**](/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1) 方法以便显示该帧。

请注意，在 Direct3D 11 中，没有等同于 EGLSurface 的抽象。 （存在 [**IDXGISurface1**](/windows/desktop/api/dxgi/nn-dxgi-idxgisurface1)，但其用法不同。）最接近的概念近似是 [**ID3D11RenderTargetView**](/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview) 对象，我们使用该对象分配纹理 ([**ID3D11Texture2D**](/windows/desktop/api/d3d11/nn-d3d11-id3d11texture2d)) 作为我们的着色器管道将要绘制到其中的后台缓冲区。

在 Direct3D 11 中，为交换链设置后台缓冲区

``` syntax
ComPtr<ID3D11RenderTargetView>    m_d3dRenderTargetViewWin; // scoped to renderer object

// ...

ComPtr<ID3D11Texture2D> backBuffer2;
    
m_swapChainCoreWindow->GetBuffer(0, IID_PPV_ARGS(&backBuffer2));

m_d3dDevice->CreateRenderTargetView(
  backBuffer2.Get(),
  nullptr,
    &m_d3dRenderTargetViewWin);
```

最佳做法是只要创建窗口或更改尺寸就调用该代码。 呈现期间，使用 [**ID3D11DeviceContext1::OMSetRenderTargets**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) 设置呈现目标视图，然后设置其他任何子资源，如顶点缓冲区或着色器。

``` syntax
// Set the render target for the draw operation.
m_d3dContext->OMSetRenderTargets(
        1,
        d3dRenderTargetView.GetAddressOf(),
        nullptr);
```

## <a name="creating-a-rendering-context"></a>创建呈现上下文


在 EGL 1.4 中，“display”表示一组窗口资源。 通常，应通过为显示对象提供一组属性并获取图面来为显示配置“surface”。 通过创建该上下文并将其绑定到图面和显示来创建用于显示图面内容的上下文。

调用流通常与此类似：

-   使用显示或窗口资源句柄调用 eglGetDisplay 并获取显示对象。
-   使用 eglInitialize 初始化屏幕。
-   使用 eglGetConfigs 和 eglChooseConfig 获取可用的屏幕配置并选择一个配置。
-   使用 eglCreateWindowSurface 创建一个窗口图面。
-   使用 eglCreateContext 创建一个用于绘制的屏幕上下文。
-   使用 eglMakeCurrent 将显示上下文绑定到显示和图面。

在上一部分中，我们创建了 EGLDisplay 和 EGLSurface，现在我们使用 EGLDisplay 创建一个上下文并使用配置的 EGLSurface 将输出参数化从而将该上下文与显示关联。

使用 EGL 1.4 获取呈现上下文

```cpp
// Configure your EGLDisplay and obtain an EGLSurface here ...
// ...

// Create a drawing context from the EGLDisplay
context = eglCreateContext(display, config, EGL_NO_CONTEXT, contextAttribs);
if (context == EGL_NO_CONTEXT)
{
  return EGL_FALSE;
}   
   
// Make the context current
if (!eglMakeCurrent(display, surface, surface, context))
{
  return EGL_FALSE;
}
```

Direct3D 11 中的呈现上下文由 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 对象（该对象表示适配器并允许你创建 Direct3D 资源，如缓冲区和着色器）和 [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) 对象（使用该对象，可以管理图形管道以及执行着色器）表示。

注意 Direct3D 功能级别！ 这些功能级别用于支持较旧的 Direct3D 硬件平台，从 DirectX 9.1 到 DirectX 11。 使用低功率图形硬件 的很多平台（如平板电脑）只能访问 DirectX 9.1 功能，所支持的较旧图形硬件范围可以是从 9.1 到 11。

使用 DXGI 和 Direct3D 创建呈现上下文

```cpp

// ... 

UINT creationFlags = D3D11_CREATE_DEVICE_BGRA_SUPPORT;
ComPtr<IDXGIDevice> dxgiDevice;

D3D_FEATURE_LEVEL featureLevels[] = 
{
        D3D_FEATURE_LEVEL_11_1,
        D3D_FEATURE_LEVEL_11_0,
        D3D_FEATURE_LEVEL_10_1,
        D3D_FEATURE_LEVEL_10_0,
        D3D_FEATURE_LEVEL_9_3,
        D3D_FEATURE_LEVEL_9_2,
        D3D_FEATURE_LEVEL_9_1
};

// Create the Direct3D 11 API device object and a corresponding context.
ComPtr<ID3D11Device> device;
ComPtr<ID3D11DeviceContext> d3dContext;

D3D11CreateDevice(
  nullptr, // Specify nullptr to use the default adapter.
  D3D_DRIVER_TYPE_HARDWARE,
  nullptr,
  creationFlags, // Set set debug and Direct2D compatibility flags.
  featureLevels, // List of feature levels this app can support.
  ARRAYSIZE(featureLevels),
  D3D11_SDK_VERSION, // Always set this to D3D11_SDK_VERSION for UWP apps.
  &device, // Returns the Direct3D device created.
  &m_featureLevel, // Returns feature level of device created.
  &d3dContext // Returns the device immediate context.
);
```

## <a name="drawing-into-a-texture-or-pixmap-resource"></a>绘制到纹理或 pixmap 资源


若要使用 OpenGL ES 2.0 绘制到纹理，请配置一个像素缓冲区或 PBuffer。 成功配置并为其创建 EGLSurface 之后，你可以为其提供呈现上下文并执行着色器管道来绘制到纹理。

使用 OpenGL ES 2.0 绘制到像素缓冲区

``` syntax
// Create a pixel buffer surface to draw into
EGLConfig pBufConfig;
EGLint totalpBufAttrs;

const EGLint pBufConfigAttrs[] =
{
    // Configure the pBuffer here...
};
 
eglChooseConfig(eglDsplay, pBufConfigAttrs, &pBufConfig, 1, &totalpBufAttrs);
EGLSurface pBuffer = eglCreatePbufferSurface(eglDisplay, pBufConfig, EGL_TEXTURE_RGBA); 
```

在 Direct3D 11 中，创建一个 [**ID3D11Texture2D**](/windows/desktop/api/d3d11/nn-d3d11-id3d11texture2d) 资源并使该资源成为呈现目标。 使用 D3D11 的 " [**呈现目标 \_ 视图 \_ " \_ \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_render_target_view_desc)配置呈现目标。 当你使用此呈现器目标在设备) 上下文上调用 [**ID3D11DeviceContext：:D 原始**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-draw) 方法 (或类似的绘图 \* 操作时，结果将绘制为纹理。

使用 Direct3D 11 绘制到纹理中

``` syntax
ComPtr<ID3D11Texture2D> renderTarget1;

D3D11_RENDER_TARGET_VIEW_DESC renderTargetDesc = {0};
// Configure renderTargetDesc here ...

m_d3dDevice->CreateRenderTargetView(
  renderTarget1.Get(),
  nullptr,
  &m_d3dRenderTargetViewWin);

// Later, in your render loop...

// Set the render target for the draw operation.
m_d3dContext->OMSetRenderTargets(
        1,
        d3dRenderTargetView.GetAddressOf(),
        nullptr);
```

可以将该纹理传递给着色器（如果该着色器与 [**ID3D11ShaderResourceView**](/windows/desktop/api/d3d11/nn-d3d11-id3d11shaderresourceview) 关联）。

## <a name="drawing-to-the-screen"></a>绘制到屏幕


使用 EGLContext 配置你的缓冲区并更新数据之后，应运行绑定到它的着色器并使用 glDrawElements 将结果绘制到后台缓冲区。 通过调用 eglSwapBuffers 显示后台缓冲区。

打开 GL ES 2.0：绘制到屏幕。

``` syntax
glDrawElements(GL_TRIANGLES, renderer->numIndices, GL_UNSIGNED_INT, 0);

eglSwapBuffers(drawContext->eglDisplay, drawContext->eglSurface);
```

在 Direct3D 11 中，配置缓冲区并使用 [**IDXGISwapChain::Present1**](/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1) 绑定着色器。 然后，调用[**ID3D11DeviceContext1：:D 原始**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-draw) \* 方法之一来运行着色器并将结果绘制到配置为交换链的后台缓冲区的呈现器目标。 然后，你只需通过调用 **IDXGISwapChain::Present1** 将后台缓冲区呈现给屏幕。

Direct3D 11：绘制到屏幕。

``` syntax

m_d3dContext->DrawIndexed(
        m_indexCount,
        0,
        0);

// ...

m_swapChainCoreWindow->Present1(1, 0, &parameters);
```

## <a name="releasing-graphics-resources"></a>释放图形资源


在 EGL 中，通过将 EGLDisplay 传递给 eglTerminate 释放窗口资源。

使用 EGL 1.4 终止屏幕

```cpp
EGLBoolean eglTerminate(eglDisplay);
```

在 UWP 应用中，可以使用 [**CoreWindow::Close**](/uwp/api/windows.ui.core.corewindow.close) 关闭 CoreWindow，但它仅用于辅助的 UI 窗口。 不能关闭主 UI 线程及其关联的 CoreWindow；它们由操作系统来终止。 但是，当关闭辅助 CoreWindow 时，会引发 [**CoreWindow::Closed**](/uwp/api/windows.ui.core.corewindow.closed) 事件。

## <a name="api-reference-mapping-for-egl-to-direct3d-11"></a>EGL 到 Direct3D 11 的 API 参考映射


| EGL API                          | 类似的 Direct3D 11 API 或行为                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| eglBindAPI                       | 不适用。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| eglBindTexImage                  | 调用 [**ID3D11Device::CreateTexture2D**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createtexture2d) 来设置 2D 纹理。                                                                                                                                                                                                                                                                                                                                                                                          |
| eglChooseConfig                  | Direct3D 未提供一组默认的帧缓冲区配置。 交换链的配置                                                                                                                                                                                                                                                                                                                                                                                           |
| eglCopyBuffers                   | 若要复制缓冲区数据，请调用 [**ID3D11DeviceContext::CopyStructureCount**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-copystructurecount)。 若要复制资源，请调用 [**ID3DDeviceCOntext::CopyResource**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-copyresource)。                                                                                                                                                                                                                                                      |
| eglCreateContext                 | 通过调用 [**D3D11CreateDevice**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice)（它会返回 Direct3D 设备的句柄以及默认的 Direct3D 即时上下文（[**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) 对象））创建一个 Direct3D 设备上下文。 还可以通过在返回的 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 对象上调用 [**ID3D11Device2::CreateDeferredContext**](/windows/desktop/api/d3d11_2/nf-d3d11_2-id3d11device2-createdeferredcontext2) 来创建 Direct3D 延迟上下文。 |
| eglCreatePbufferFromClientBuffer | 所有缓冲区都作为 Direct3D 子资源进行读取和写入，如 [**ID3D11Texture2D**](/windows/desktop/api/d3d11/nn-d3d11-id3d11texture2d)。 使用一个方法（如 [**ID3D11DeviceContext1:CopyResource**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-copyresource)）从一种兼容的子资源类型复制到另一种兼容的子资源类型。                                                                                                                                                                                                     |
| eglCreatePbufferSurface          | 若要创建没有交换链的 Direct3D 设备，请调用静态的 [**D3D11CreateDevice**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) 方法。 对于 Direct3D 呈现目标视图，请调用 [**ID3D11Device::CreateRenderTargetView**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createrendertargetview)。                                                                                                                                                                                                                               |
| eglCreatePixmapSurface           | 若要创建没有交换链的 Direct3D 设备，请调用静态的 [**D3D11CreateDevice**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) 方法。 对于 Direct3D 呈现目标视图，请调用 [**ID3D11Device::CreateRenderTargetView**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createrendertargetview)。                                                                                                                                                                                                                               |
| eglCreateWindowSurface           | 获取一个 [**IDXGISwapChain1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1)（对于显示缓冲区）和一个 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1)（用于图形设备及其资源的虚拟接口）。 使用 **ID3D11Device1** 定义一个你可以用来创建帧缓冲区（提供给 **IDXGISwapChain1**）的 [**ID3D11RenderTargetView**](/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview)。                                                                                         |
| eglDestroyContext                | 不适用。 使用 [**ID3D11DeviceContext::DiscardView1**](/windows/desktop/api/d3d11_1/nf-d3d11_1-id3d11devicecontext1-discardview1) 去除呈现目标视图。 若要关闭父 [**ID3D11DeviceContext1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1)，请将该实例设置为 null 并等待平台回收其资源。 不能直接破坏设备上下文。                                                                                                                                                |
| eglDestroySurface                | 不适用。 当平台关闭 UWP 应用的 CoreWindow 时，会清除图形资源。                                                                                                                                                                                                                                                                                                                                                                                                 |
| eglGetCurrentDisplay             | 调用 [**CoreWindow::GetForCurrentThread**](/uwp/api/windows.ui.core.corewindow.getforcurrentthread) 来获取对当前主应用窗口的引用。                                                                                                                                                                                                                                                                                                                                                         |
| eglGetCurrentSurface             | 这是当前的 [**ID3D11RenderTargetView**](/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview)。 通常，它的作用域设置为你的呈现器对象。                                                                                                                                                                                                                                                                                                                                                         |
| eglGetError                      | 以 DirectX 接口上大多数方法返回的 HRESULT 形式获取错误。 如果该方法未返回 HRESULT，请调用 [**GetLastError**](/windows/desktop/api/errhandlingapi/nf-errhandlingapi-getlasterror)。 若要将系统错误转换为 HRESULT 值，请使用 [**hresult \_ FROM \_ WIN32**](/windows/desktop/api/winerror/nf-winerror-hresult_from_win32)   宏。                                                                                                                                                                                                  |
| eglInitialize                    | 调用 [**CoreWindow::GetForCurrentThread**](/uwp/api/windows.ui.core.corewindow.getforcurrentthread) 来获取对当前主应用窗口的引用。                                                                                                                                                                                                                                                                                                                                                         |
| eglMakeCurrent                   | 使用 [**ID3D11DeviceContext1::OMSetRenderTargets**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) 设置用于在当前上下文上绘制的呈现目标。                                                                                                                                                                                                                                                                                                                                  |
| eglQueryContext                  | 不适用。 但是，你可能会从 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 实例以及某些配置数据中获取呈现目标。 （有关可用方法的列表，请参阅该链接。）                                                                                                                                                                                                                                                                                           |
| eglQuerySurface                  | 不适用。 但是，你可能会通过 [**ID3D11Device1**](/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) 实例上的方法来获取有关视区和当前图形硬件的数据。 （有关可用方法的列表，请参阅该链接。）                                                                                                                                                                                                                                                                               |
| eglReleaseTexImage               | 不适用。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| eglReleaseThread                 | 有关常规的 GPU 多线程，请阅读[多线程](/windows/desktop/direct3d11/overviews-direct3d-11-render-multi-thread-intro)。                                                                                                                                                                                                                                                                                                                                                                              |
| eglSurfaceAttrib                 | 使用 [**D3D11 \_ render \_ target \_ view \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_render_target_view_desc) 配置 Direct3D RENDER target 视图，                                                                                                                                                                                                                                                                                                                                                               |
| eglSwapBuffers                   | 使用[**IDXGISwapChain1::Present1**](/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1) 。                                                                                                                                                                                                                                                                                                                                                                                                                     |
| eglSwapInterval                  | 请参阅 [**IDXGISwapChain1**](/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1)。                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| eglTerminate                     | 用于显示图形管道输出的 CoreWindow 由操作系统进行管理。                                                                                                                                                                                                                                                                                                                                                                                          |
| eglWaitClient                    | 对于共享图面，请使用 IDXGIKeyedMutex。 有关常规的 GPU 多线程，请阅读[多线程](/windows/desktop/direct3d11/overviews-direct3d-11-render-multi-thread-intro)。                                                                                                                                                                                                                                                                                                                                    |
| eglWaitGL                        | 对于共享图面，请使用 IDXGIKeyedMutex。 有关常规的 GPU 多线程，请阅读[多线程](/windows/desktop/direct3d11/overviews-direct3d-11-render-multi-thread-intro)。                                                                                                                                                                                                                                                                                                                                    |
| eglWaitNative                    | 对于共享图面，请使用 IDXGIKeyedMutex。 有关常规的 GPU 多线程，请阅读[多线程](/windows/desktop/direct3d11/overviews-direct3d-11-render-multi-thread-intro)。                                                                                                                                                                                                                                                                                                                                    |

 

 

 