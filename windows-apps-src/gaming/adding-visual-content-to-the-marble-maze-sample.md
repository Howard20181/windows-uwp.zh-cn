---
title: 向 Marble Maze 示例添加可视内容
description: 本文档介绍了 Marble Maze 游戏如何在通用 Windows 平台 (UWP) 应用环境中使用 Direct3D 和 Direct2D，以便你可了解相关模式并在处理自己的游戏内容时调整它们。
ms.assetid: 6e43422e-e1a1-b79e-2c4b-7d5b4fa88647
ms.date: 09/08/2017
ms.topic: article
keywords: Windows 10, uwp, 游戏, 示例, directx, 图形
ms.localizationpriority: medium
ms.openlocfilehash: 8e00842a03eecb91e22cedf987830b28e960efd0
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258542"
---
# <a name="adding-visual-content-to-the-marble-maze-sample"></a>向 Marble Maze 示例添加可视内容




本文档介绍了 Marble Maze 游戏如何在通用 Windows 平台 (UWP) 应用环境中使用 Direct3D 和 Direct2D，以便你可了解相关模式并在处理自己的游戏内容时调整它们。 若要了解可视游戏组件如何融入 Marble Maze 的总体应用程序结构中，请参阅 [Marble Maze 应用程序结构](marble-maze-application-structure.md)。

我们在开发 Marble Maze 的可视方面时遵循以下基本步骤：

1.  创建一个初始化 Direct3D 和 Direct2D 环境的基本框架。
2.  使用图像和模型编辑程序设计在游戏中显示的2D 和3D 资产。
3.  确保二维和3D 资产正确加载并出现在游戏中。
4.  集成可增强游戏资源可视质量的顶点和像素着色器。
5.  集成游戏逻辑，例如动画和用户输入。

我们还将首先重点介绍如何在二维资产上添加3D 资产。 例如，我们在添加菜单系统和计时器之前，会重点介绍核心游戏逻辑。

我们在开发过程中还需要重复一些步骤多次。 例如，在对网格和大理石模型进行更改时，还必须更改一些支持这些模型的着色器代码。

> [!NOTE]
> 与本文档对应的示例代码位于 [DirectX Marble Maze 游戏示例](https://github.com/microsoft/Windows-appsample-marble-maze)中。

 
本文档讨论了在处理 DirectX 和可视游戏内容时（即在初始化 DirectX 图形库、加载场景资源，以及更新和呈现场景时）的一些重要事项：

-   添加游戏内容通常涉及到许多步骤。 这些步骤也常常需要重复执行。 游戏开发人员通常首先要重点介绍如何添加3D 游戏内容，然后添加2D 内容。
-   通过尽可能支持更大范围的图形硬件，获得更多客户并为他们提供出色的体验。
-   将设计时和运行时格式明确分开。 构建设计时资源结构，以最大限度提高灵活性并在内容上实现快速迭代。 格式化并压缩资源，以在运行时尽可能高效地加载和渲染。
-   在 UWP 应用中创建 Direct3D 和 Direct2D 设备，与在经典的 Windows 桌面应用中非常相似。 一个重要的区别是交换链与输出窗口的关联方式。
-   设计游戏时，请确保所选择的网格格式支持你的重要方案。 例如，如果游戏需要碰撞，请确保你可从网格获取碰撞数据。
-   通过在渲染所有场景对象之前首先更新它们，将游戏逻辑与渲染逻辑分开。
-   通常会绘制三维场景对象，然后绘制出现在场景前面的任何2D 对象。
-   将图形与垂直空白同步，以确保游戏不会花时间绘制从不会在显示器上实际显示的帧。 *垂直空白*是指一个帧完成绘制到监视器并开始下一个帧之间的时间。

## <a name="getting-started-with-directx-graphics"></a>DirectX 图形入门


当我们规划了大理石迷宫通用 Windows 平台（UWP）游戏时，我们C++选择了和 Direct3D 11.1，因为它们是创建三维游戏的最佳选择，需要对渲染和高性能进行最大程度的控制。 DirectX 11.1 支持从 DirectX 9 到 DirectX 11 的硬件，因此可帮助你更高效地获得更多客户，因为你无需为每个早期的 DirectX 版本重写代码。

大理石迷宫使用 Direct3D 11.1 呈现3D 游戏资产，即大理石和迷宫。 大理石迷宫还使用 Direct2D、DirectWrite 和 Windows 图像处理组件（WIC）来绘制2D 游戏资产，如菜单和计时器。

游戏开发需要规划。 如果你不熟悉 DirectX 图形，建议你阅读[directx：](directx-getting-started.md)入门以熟悉创建 UWP DirectX 游戏的基本概念。 阅读本文档并浏览大理石迷宫源代码时，可以参考以下资源，了解有关 DirectX 图形的更深入信息：

-   [Direct3d 11 图形](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11)：介绍 direct3d 11，它是一款功能强大的硬件加速三维图形 API，用于在 Windows 平台上呈现3d 几何。
-   [Direct2D](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-portal)：介绍 Direct2D，这是一个硬件加速的2D 图形 API，为二维几何、位图和文本提供高性能和高质量的渲染。
-   [DirectWrite](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal)：介绍 DirectWrite，它支持高质量的文本呈现。
-   [Windows 映像组件](https://docs.microsoft.com/windows/desktop/wic/-wic-lh)：介绍了 WIC，它是为数字图像提供低级别 API 的可扩展平台。

### <a name="feature-levels"></a>功能级别

Direct3D 11 引入了一个名为 "*功能级别*" 的范例。 功能级别是明确定义的 GPU 功能的集合。 使用功能级别，可让你的游戏能在较早版本的 Direct3D 硬件上运行。 Marble Maze 支持功能级别 9.1，因为它不需要来自更高级别的高级功能。 我们建议你尽可能支持大范围的硬件并扩展游戏内容，以便拥有高端或低端计算机的客户都拥有出色的体验。 有关功能级别的详细信息，请参阅[下层硬件上的 Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel)。

## <a name="initializing-direct3d-and-direct2d"></a>初始化 Direct3D 和 Direct2D


设备代表显示适配器。 在 UWP 应用中创建 Direct3D 和 Direct2D 设备，与在经典的 Windows 桌面应用中非常相似。 主要区别在于将 Direct3D 交换链连接到窗口化系统的方式。

**DeviceResources** 类是管理 Direct3D 和 Direct2D 的基础。 此类处理一般基础结构，而不是特定于游戏的资产。 大理石迷宫定义**MarbleMazeMain**类来处理游戏特定的资产，该资产具有对**DeviceResources**对象的引用，以使其能够访问 Direct3D 和 Direct2D。

在初始化期间， **DeviceResources**构造函数会创建与设备无关的资源和 Direct3D 和 Direct2D 设备。

```cpp
// Initialize the Direct3D resources required to run. 
DX::DeviceResources::DeviceResources() :
    m_screenViewport(),
    m_d3dFeatureLevel(D3D_FEATURE_LEVEL_9_1),
    m_d3dRenderTargetSize(),
    m_outputSize(),
    m_logicalSize(),
    m_nativeOrientation(DisplayOrientations::None),
    m_currentOrientation(DisplayOrientations::None),
    m_dpi(-1.0f),
    m_deviceNotify(nullptr)
{
    CreateDeviceIndependentResources();
    CreateDeviceResources();
}
```

**DeviceResources** 类将此功能分开，以便它可更轻松地响应环境变化。 例如，它在窗口大小更改时调用 **CreateWindowSizeDependentResources** 方法。

###  <a name="initializing-the-direct2d-directwrite-and-wic-factories"></a>初始化 Direct2D、DirectWrite 和 WIC 工厂

**DeviceResources::CreateDeviceIndependentResources** 方法为 Direct2D、DirectWrite 和 WIC 创建工厂。 在 DirectX 图形中，工厂是创建图形资源的起点。 大理石迷宫指定**D2D1\_工厂\_类型\_单个\_串接**，因为它在主线程上执行所有绘制。

```cpp
// These are the resources required independent of hardware. 
void DX::DeviceResources::CreateDeviceIndependentResources()
{
    // Initialize Direct2D resources.
    D2D1_FACTORY_OPTIONS options;
    ZeroMemory(&options, sizeof(D2D1_FACTORY_OPTIONS));

#if defined(_DEBUG)
    // If the project is in a debug build, enable Direct2D debugging via SDK Layers.
    options.debugLevel = D2D1_DEBUG_LEVEL_INFORMATION;
#endif

    // Initialize the Direct2D Factory.
    DX::ThrowIfFailed(
        D2D1CreateFactory(
            D2D1_FACTORY_TYPE_SINGLE_THREADED,
            __uuidof(ID2D1Factory2),
            &options,
            &m_d2dFactory
            )
        );

    // Initialize the DirectWrite Factory.
    DX::ThrowIfFailed(
        DWriteCreateFactory(
            DWRITE_FACTORY_TYPE_SHARED,
            __uuidof(IDWriteFactory2),
            &m_dwriteFactory
            )
        );

    // Initialize the Windows Imaging Component (WIC) Factory.
    DX::ThrowIfFailed(
        CoCreateInstance(
            CLSID_WICImagingFactory2,
            nullptr,
            CLSCTX_INPROC_SERVER,
            IID_PPV_ARGS(&m_wicFactory)
            )
        );
}
```

###  <a name="creating-the-direct3d-and-direct2d-devices"></a>创建 Direct3D 和 Direct2D 设备

**DeviceResources：： CreateDeviceResources**方法调用[D3D11CreateDevice](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice)以创建表示 Direct3D 显示适配器的设备对象。 因为大理石迷宫支持功能级别9.1 和更高版本， **DeviceResources：： CreateDeviceResources**方法在**featureLevels**数组中指定级别9.1 到11.1。 Direct3D 按顺序遍历该列表并为应用提供第一个可用的功能级别。 因此， **D3D\_功能\_级别**数组项从最高到最低列出，以便应用获得可用的最高功能级别。 **DeviceResources::CreateDeviceResources** 方法通过查询从 **D3D11CreateDevice** 返回的 Direct3D 11 设备来获取 Direct3D 11.1 设备。

```cpp
// This flag adds support for surfaces with a different color channel ordering
// than the API default. It is required for compatibility with Direct2D.
UINT creationFlags = D3D11_CREATE_DEVICE_BGRA_SUPPORT;

#if defined(_DEBUG)
    if (DX::SdkLayersAvailable())
    {
        // If the project is in a debug build, enable debugging via SDK Layers 
        // with this flag.
        creationFlags |= D3D11_CREATE_DEVICE_DEBUG;
    }
#endif

// This array defines the set of DirectX hardware feature levels this app will support.
// Note the ordering should be preserved.
// Don't forget to declare your application's minimum required feature level in its
// description.  All applications are assumed to support 9.1 unless otherwise stated.
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
ComPtr<ID3D11DeviceContext> context;

HRESULT hr = D3D11CreateDevice(
    nullptr,                    // Specify nullptr to use the default adapter.
    D3D_DRIVER_TYPE_HARDWARE,   // Create a device using the hardware graphics driver.
    0,                          // Should be 0 unless the driver is D3D_DRIVER_TYPE_SOFTWARE.
    creationFlags,              // Set debug and Direct2D compatibility flags.
    featureLevels,              // List of feature levels this app can support.
    ARRAYSIZE(featureLevels),   // Size of the list above.
    D3D11_SDK_VERSION,          // Always set this to D3D11_SDK_VERSION for UWP apps.
    &device,                    // Returns the Direct3D device created.
    &m_d3dFeatureLevel,         // Returns feature level of device created.
    &context                    // Returns the device immediate context.
    );

if (FAILED(hr))
{
    // If the initialization fails, fall back to the WARP device.
    // For more information on WARP, see:
    // https://go.microsoft.com/fwlink/?LinkId=286690
    DX::ThrowIfFailed(
        D3D11CreateDevice(
            nullptr,
            D3D_DRIVER_TYPE_WARP, // Create a WARP device instead of a hardware device.
            0,
            creationFlags,
            featureLevels,
            ARRAYSIZE(featureLevels),
            D3D11_SDK_VERSION,
            &device,
            &m_d3dFeatureLevel,
            &context
            )
        );
}

// Store pointers to the Direct3D 11.1 API device and immediate context.
DX::ThrowIfFailed(
    device.As(&m_d3dDevice)
    );

DX::ThrowIfFailed(
    context.As(&m_d3dContext)
    );
```

然后，**DeviceResources::CreateDeviceResources** 方法创建 Direct2D 设备。 Direct2D 使用 Microsoft DirectX 图形基础结构 (DXGI) 来与 Direct3D 互操作。 DXGI 支持在图形运行时之间共享视频内存表面。 Marble Maze 使用来自 Direct3D 设备的基础 DXGI 设备，通过 Direct2D 工厂创建 Direct2D 设备。

```cpp
// Create the Direct2D device object and a corresponding context.
ComPtr<IDXGIDevice3> dxgiDevice;
DX::ThrowIfFailed(
    m_d3dDevice.As(&dxgiDevice)
    );

DX::ThrowIfFailed(
    m_d2dFactory->CreateDevice(dxgiDevice.Get(), &m_d2dDevice)
    );

DX::ThrowIfFailed(
    m_d2dDevice->CreateDeviceContext(
        D2D1_DEVICE_CONTEXT_OPTIONS_NONE,
        &m_d2dContext
        )
    );
```

有关 DXGI 及 Direct2D 与 Direct3D 之间互操作性的详细信息，请参阅 [DXGI 概述](https://docs.microsoft.com/windows/desktop/direct3ddxgi/d3d10-graphics-programming-guide-dxgi)和 [Direct2D 和 Direct3D 互操作性概述](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-and-direct3d-interoperation-overview)。

### <a name="associating-direct3d-with-the-view"></a>将 Direct3D 与视图关联

**DeviceResources::CreateWindowSizeDependentResources** 方法根据给定的窗口大小创建图形资源，例如交换链及 Direct3D 和 Direct2D 呈现目标。 DirectX UWP 应用与桌面应用的一个重要区别在于交换链与输出窗口的关联方式。 交换链负责显示缓冲区，设备在监视器上要渲染到该缓冲区。 [大理石迷宫应用程序结构](marble-maze-application-structure.md)描述了 UWP 应用的窗口化系统与桌面应用程序的不同之处。 由于 UWP 应用不适用于[HWND](https://docs.microsoft.com/windows/desktop/WinProg/windows-data-types)对象，大理石迷宫必须使用[IDXGIFactory2：： CreateSwapChainForCoreWindow](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow)方法将设备输出与视图相关联。 下面的示例展示了 **DeviceResources::CreateWindowSizeDependentResources** 方法创建交换链的部分。

```cpp
// Obtain the final swap chain for this window from the DXGI factory.
DX::ThrowIfFailed(
    dxgiFactory->CreateSwapChainForCoreWindow(
        m_d3dDevice.Get(),
        reinterpret_cast<IUnknown*>(m_window.Get()),
        &swapChainDesc,
        nullptr,
        &m_swapChain
        )
    );
```

若要最大程度地减少功耗，这一点对便携式计算机和平板电脑等备有电池的设备很重要， **DeviceResources：： CreateWindowSizeDependentResources**方法会调用[IDXGIDevice1：： SetMaximumFrameLatency](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice1-setmaximumframelatency)方法，以确保仅在垂直空白之后呈现游戏。 在本文档的显示[场景](#presenting-the-scene)部分中更详细地描述了垂直空白的同步。

```cpp
// Ensure that DXGI does not queue more than one frame at a time. This both 
// reduces latency and ensures that the application will only render after each
// VSync, minimizing power consumption.
DX::ThrowIfFailed(
    dxgiDevice->SetMaximumFrameLatency(1)
    );
```

**DeviceResources::CreateWindowSizeDependentResources** 方法以一种适合大部分游戏的方式初始化图形资源。

> [!NOTE]
> 术语*视图*在 Windows 运行时中具有不同的含义，而不是在 Direct3D 中。 在 Windows 运行时中，视图指的是应用的用户界面设置集合，包括显示区域和输入行为及其用于处理的线程。 指定你在创建视图时需要的配置和设置。 设置应用视图的过程将在 [Marble Maze 应用程序结构](marble-maze-application-structure.md)中介绍。
> 在 Direct3D 中，术语“视图”有多种含义。 资源视图定义资源可以访问的子资源。 例如，将一个纹理对象与一个着色器资源视图关联后，该着色器可访问该纹理。 资源视图的一个优点是，你可在呈现管道中的不同阶段以不同方式解释数据。 有关资源视图的详细信息，请参阅[资源视图](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-intro)。
> 在视图转换或视图转换矩阵的上下文中使用时，视图指的是相机的位置和方向。 视图转换会在围绕相机的位置和方向的世界中重新定位各个对象。 有关视图转换的详细信息，请参阅[视图转换 (Direct3D 9)](https://docs.microsoft.com/windows/desktop/direct3d9/view-transform)。 本主题将详细介绍 Marble Maze 如何使用资源和矩阵视图。

 

## <a name="loading-scene-resources"></a>加载场景资源


大理石迷宫使用在**BasicLoader**中声明的**BasicLoader**类来加载纹理和着色器。 大理石迷宫使用**SDKMesh**类为迷宫和大理石加载3d 网格。

若要确保应用能迅速响应，Marble Maze 以异步方式或在后台加载场景资源。 资源加载到后台后，游戏即可响应窗口事件。 这个过程将在本指南的[在后台加载游戏资产](marble-maze-application-structure.md#loading-game-assets-in-the-background)中详细介绍。

###  <a name="loading-the-2d-overlay-and-user-interface"></a>加载二维覆盖和用户界面

在 Marble Maze 中，覆盖图是显示在屏幕顶层的图像。 覆盖图始终显示在场景前面。 在大理石迷宫中，覆盖包含 Windows 徽标和文本字符串**DirectX 大理石迷宫游戏示例**。 覆盖的管理由在**SampleOverlay**中定义的**SampleOverlay**类执行。 尽管我们使用覆盖作为 Direct3D 示例的一部分，但你可适应此代码以显示任何出现在场景前面的图像。

覆盖的一个重要方面是，因为其内容不会发生变化， **SampleOverlay**类在初始化期间会将其内容绘制或缓存到[ID2D1Bitmap1](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1bitmap1)对象。 在绘制时，**SampleOverlay** 类仅需要向屏幕绘制位图。 这样，无需为每一帧都执行文本绘制等需要大量资源的例行任务。

用户界面（UI）由2D 组件（如菜单和打印头显示（HUDs））组成，它们显示在场景的前方。 Marble Maze 定义了以下 UI 元素：

-   使用户能够启动游戏或查看高分的菜单项。
-   在游戏开始前计时 3 秒的计时器。
-   跟踪已用游戏时间的计时器。
-   列出最快完成时间的表格。
-   游戏暂停时阅读**暂停**的文本。

大理石迷宫定义**UserInterface**中特定于游戏的 UI 元素。 Marble Maze 将 **ElementBase** 类定义为所有 UI 元素的基础类型。 **ElementBase** 类定义 UI 元素的属性，例如大小、位置、对齐方式和可视性。 它还控制元素的更新和呈现方式。

```cpp
class ElementBase
{
public:
    virtual void Initialize() { }
    virtual void Update(float timeTotal, float timeDelta) { }
    virtual void Render() { }

    void SetAlignment(AlignType horizontal, AlignType vertical);
    virtual void SetContainer(const D2D1_RECT_F& container);
    void SetVisible(bool visible);

    D2D1_RECT_F GetBounds();

    bool IsVisible() const { return m_visible; }

protected:
    ElementBase();

    virtual void CalculateSize() { }

    Alignment       m_alignment;
    D2D1_RECT_F     m_container;
    D2D1_SIZE_F     m_size;
    bool            m_visible;
};
```

通过为 UI 元素提供一个通用的基类，**UserInterface** 类（管理用户界面）仅需要具有一个 **ElementBase** 对象集合，这简化了 UI 管理并提供了一个可重用的用户界面管理器。 Marble Maze 定义派生自 **ElementBase** 的类型来实现特定于游戏的行为。 例如，**HighScoreTable** 定义了高分表的行为。 有关这些类型的详细信息，请参阅源代码。

> [!NOTE]
> 由于 XAML 使你可以更轻松地创建复杂的用户界面，如在模拟和策略游戏中找到的那些界面，因此请考虑是否使用 XAML 来定义 UI。 有关如何在 DirectX UWP 游戏中使用 XAML 开发用户界面的信息，请参阅[扩展游戏示例](tutorial-resources.md)，这是指 directx 3d 拍摄游戏示例。

 

###  <a name="loading-shaders"></a>加载着色器

Marble Maze 使用 **BasicLoader::LoadShader** 方法从文件加载着色器。

现在着色器是游戏中的 GPU 编程的基本单元。 几乎所有的3D 图形处理都是通过着色器驱动的，无论是模型转换、场景照明还是更复杂的几何处理，从字符的外观到镶嵌都是如此。 有关着色器编程模型的详细信息，请参阅 [HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl)。

Marble Maze 使用顶点和像素着色器。 顶点着色器始终在一个输入顶点上操作并生成一个顶点作为输出。 像素着色器接受数字值、纹理数据、插值的顶点值，以及其他数据，生成一种像素颜色作为输出。 因为着色器一次转换一个元素，所以提供多个着色器管道的图形硬件可并行处理多组元素。 可供 GPU 使用的并行管道数量可能比可供 CPU 使用的数量要多得多。 因此，即使基本着色器也能大大改善吞吐量。

在加载叠加后， **MarbleMazeMain：： LoadDeferredResources**方法会加载一个顶点着色器和一个像素着色器。 这些着色器的设计时版本分别定义在**BasicVertexShader. hlsl**和**BasicPixelShader**中。 Marble Maze 在渲染阶段将这些着色器同时应用到球和迷宫。

Marble Maze 项目同时包含着色器文件的 .hlsl（设计时格式）和 .cso（运行时格式）。 在生成时，Visual Studio 使用 fxc.exe 效果编译器将 .hlsl 源文件编译为 .cso 库着色器。 有关效果编译器工具的详细信息，请参阅[效果编译器工具](https://docs.microsoft.com/windows/desktop/direct3dtools/fxc)。

顶点着色器使用提供的模型、视图和投影矩阵来转换输入几何图形。 来自输入几何图形的位置数据被转换并输出两次，一次在屏幕空间中（这是渲染所必需的），另一次在世界空间中，让像素着色器能够执行照明计算。 表面法线矢量被转换到世界空间，这也由像素着色器用于照明。 纹理坐标原封不动地传递到像素着色器。

```hlsl
sPSInput main(sVSInput input)
{
    sPSInput output;
    float4 temp = float4(input.pos, 1.0f);
    temp = mul(temp, model);
    output.worldPos = temp.xyz / temp.w;
    temp = mul(temp, view);
    temp = mul(temp, projection);
    output.pos = temp;
    output.tex = input.tex;
    output.norm = mul(float4(input.norm, 0.0f), model).xyz;
    return output;
}
```

像素着色器接收顶点着色器的输出作为输入。 这个着色器执行照明计算来模拟一种虚边聚光效果，该聚光效果悬浮在迷宫上方且与弹珠的位置对齐。 直接朝向光源的表面的照明最强。 随着法线变为与光线垂直，散射组件逐渐缩小到 0；随着法线点远离光源，环境光逐渐消失。 接近弹珠（因此接近聚光效果的中心）的点的照明更强。 但是，弹珠下方的点的照明要柔和一些，以模拟虚化的阴影。 在真实环境中，一个像白色弹珠这样的物体会将聚光灯的光漫反射到场景中的其他对象上。 这非常接近弹珠的明亮部分的表面。 其他的照明因素包括相对角和离弹珠的距离。 最终的像素颜色包括采样的纹理和照明计算的结果。

```hlsl
float4 main(sPSInput input) : SV_TARGET
{
    float3 lightDirection = float3(0, 0, -1);
    float3 ambientColor = float3(0.43, 0.31, 0.24);
    float3 lightColor = 1 - ambientColor;
    float spotRadius = 50;

    // Basic ambient (Ka) and diffuse (Kd) lighting from above.
    float3 N = normalize(input.norm);
    float NdotL = dot(N, lightDirection);
    float Ka = saturate(NdotL + 1);
    float Kd = saturate(NdotL);

    // Spotlight.
    float3 vec = input.worldPos - marblePosition;
    float dist2D = sqrt(dot(vec.xy, vec.xy));
    Kd = Kd * saturate(spotRadius / dist2D);

    // Shadowing from ball.
    if (input.worldPos.z > marblePosition.z)
        Kd = Kd * saturate(dist2D / (marbleRadius * 1.5));

    // Diffuse reflection of light off ball.
    float dist3D = sqrt(dot(vec, vec));
    float3 V = normalize(vec);
    Kd += saturate(dot(-V, N)) * saturate(dot(V, lightDirection))
        * saturate(marbleRadius / dist3D);

    // Final composite.
    float4 diffuseTexture = Texture.Sample(Sampler, input.tex);
    float3 color = diffuseTexture.rgb * ((ambientColor * Ka) + (lightColor * Kd));
    return float4(color * lightStrength, diffuseTexture.a);
}
```

> [!WARNING]
> 已编译的像素着色器包含32算术指令和1个纹理指令。 此着色器应能在桌面计算机和高端平板电脑上出色地运行。 但是，低端计算机可能无法处理此着色器并仍然提供一种交互式帧速率。 考虑你的目标受众的典型硬件，设计你的着色器来符合该硬件的功能。

 

**MarbleMazeMain：： LoadDeferredResources**方法使用**BasicLoader：： LoadShader**方法加载着色器。 下面的示例加载顶点着色器。 此着色器的运行时格式为**BasicVertexShader**。 **M\_vertexShader**成员变量是[ID3D11VertexShader](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11vertexshader)对象。

```cpp
BasicLoader^ loader = ref new BasicLoader(m_deviceResources->GetD3DDevice());

D3D11_INPUT_ELEMENT_DESC layoutDesc [] =
{
    { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 24, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TANGENT", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 32, D3D11_INPUT_PER_VERTEX_DATA, 0 },
};
m_vertexStride = 44; // must set this to match the size of layoutDesc above

Platform::String^ vertexShaderName = L"BasicVertexShader.cso";
loader->LoadShader(
    vertexShaderName,
    layoutDesc,
    ARRAYSIZE(layoutDesc),
    &m_vertexShader,
    &m_inputLayout
    );
```

**M\_inputLayout**成员变量是[ID3D11InputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11inputlayout)对象。 输入布局对象封装了输入装配器 (IA) 阶段的输入状态。 IA 阶段的一个工作是提高着色器的效率，方法是使用系统生成的值（也称为*语义*）仅处理尚未处理的基元或顶点。

使用 [ID3D11Device::CreateInputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createinputlayout) 方法从输入元素描述数组创建一个输入布局。 该数组包含一个或多个输入元素；每个输入元素描述来自一个顶点缓冲区的一个顶点数据元素。 整个输入元素描述集合描述了来自将绑定到 IA 阶段的所有顶点缓冲区的顶点数据元素。 

上面的代码片段中的**layoutDesc**显示大理石迷宫使用的布局说明。 布局描述说明了一个包含 4 个顶点数据元素的顶点缓冲区。 数组中每个条目的重要部分是语义名称、数据格式和字节偏移。 例如，**POSITION** 元素指定对象空间中的顶点位置。 它从字节偏移 0 开始，包含 3 个浮点组件（共 12 个字节）。 **NORMAL** 元素指定法线矢量。 它从字节偏移 12 开始，因为它紧挨布局中的 **POSITION** 后出现，这需要 12 个字节。 **NORMAL** 元素包含一个 4 个组件、32 位的无符号整数。

将输入布局与顶点着色器定义的 **sVSInput** 结构对比，如下面的示例所示。 **sVSInput** 结构定义 **POSITION**、**NORMAL** 和 **TEXCOORD0** 元素。 DirectX 运行时将布局中的每个元素映射到着色器定义的输入结构。

```hlsl
struct sVSInput
{
    float3 pos : POSITION;
    float3 norm : NORMAL;
    float2 tex : TEXCOORD0;
};

struct sPSInput
{
    float4 pos : SV_POSITION;
    float3 norm : NORMAL;
    float2 tex : TEXCOORD0;
    float3 worldPos : TEXCOORD1;
};

sPSInput main(sVSInput input)
{
    sPSInput output;
    float4 temp = float4(input.pos, 1.0f);
    temp = mul(temp, model);
    output.worldPos = temp.xyz / temp.w;
    temp = mul(temp, view);
    temp = mul(temp, projection);
    output.pos = temp;
    output.tex = input.tex;
    output.norm = mul(float4(input.norm, 0.0f), model).xyz;
    return output;
}
```

文档[语义](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-semantics)详细介绍了每种可用的语义。

> [!NOTE]
> 在布局中，可以指定不用于使多个着色器共享同一布局的附加组件。 例如，着色器未使用 **TANGENT** 元素。 如果希望试验法线映射等技术，可使用 **TANGENT** 元素。 通过使用法线映射（也称为凹凸贴图），你可在对象表面创建凹凸效果。 有关凹凸贴图的详细信息，请参阅[凹凸贴图 (Direct3D 9)](https://docs.microsoft.com/windows/desktop/direct3d9/bump-mapping)。

 

有关 "输入程序集" 阶段的详细信息，请参阅[具有输入汇编](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage-getting-started)阶段的[输入组装器阶段](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage)和入门。

使用顶点和像素着色器呈现场景的过程将在本文后面的[呈现场景](#rendering-the-scene)一节介绍。

### <a name="creating-the-constant-buffer"></a>创建常量缓冲区

Direct3D 缓冲区将一组对象分组到一起。 常量缓冲区是一种可用于将数据传递给着色器的缓冲区。 Marble Maze 使用常量缓冲区来保留活动的场景对象的模型（或世界）视图，以及投影指标。

下面的示例演示了**MarbleMazeMain：： LoadDeferredResources**方法如何创建以后要保存矩阵数据的常量缓冲区。 该示例创建了一个**D3D11\_缓冲区\_DESC**结构，该结构使用**D3D11\_绑定\_常量\_缓冲区**标志将使用情况指定为常量缓冲区。 然后，该示例将该结构传递到 [ID3D11Device::CreateBuffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createbuffer) 方法。 **M\_constantBuffer**变量是一个[ID3D11Buffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11buffer)对象。

```cpp
// Create the constant buffer for updating model and camera data.
D3D11_BUFFER_DESC constantBufferDesc = {0};

// Multiple of 16 bytes
constantBufferDesc.ByteWidth = ((sizeof(ConstantBuffer) + 15) / 16) * 16;

constantBufferDesc.Usage               = D3D11_USAGE_DEFAULT;
constantBufferDesc.BindFlags           = D3D11_BIND_CONSTANT_BUFFER;
constantBufferDesc.CPUAccessFlags      = 0;
constantBufferDesc.MiscFlags           = 0;

// This will not be used as a structured buffer, so this parameter is ignored.
constantBufferDesc.StructureByteStride = 0;

DX::ThrowIfFailed(
    m_deviceResources->GetD3DDevice()->CreateBuffer(
        &constantBufferDesc,
        nullptr,    // leave the buffer uninitialized
        &m_constantBuffer
        )
    );
```

**MarbleMazeMain：： Update**方法稍后会更新**ConstantBuffer**对象，一个用于迷宫，另一个用于大理石。 在呈现每个对象之前， **MarbleMazeMain：： Render**方法会将每个**ConstantBuffer**对象绑定到常量缓冲区。 下面的示例显示了**MarbleMazeMain**中的**ConstantBuffer**结构。

```cpp
// Describes the constant buffer that draws the meshes.
struct ConstantBuffer
{
    XMFLOAT4X4 model;
    XMFLOAT4X4 view;
    XMFLOAT4X4 projection;

    XMFLOAT3 marblePosition;
    float marbleRadius;
    float lightStrength;
};
```

为了更好地了解常量缓冲区如何映射到着色器代码，请将**MarbleMazeMain**中的**ConstantBuffer**结构与**BasicVertexShader**中的顶点着色器定义的**ConstantBuffer**常量缓冲区进行比较。

```hlsl
cbuffer ConstantBuffer : register(b0)
{
    matrix model;
    matrix view;
    matrix projection;
    float3 marblePosition;
    float marbleRadius;
    float lightStrength;
};
```

**ConstantBuffer** 结构的布局可匹配 **cbuffer** 对象。 **cbuffer** 变量指定寄存器 b0，这意味着常量缓冲区数据存储在寄存器 0 中。 **MarbleMazeMain：： Render**方法在激活常量缓冲区时指定 register 0。 此过程将在本文档后面详细介绍。

有关常量缓冲区的详细信息，请参阅 [Direct3D 11 中的缓冲区简介](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro)。 有关 register 关键字的详细信息，请参阅 [register](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-variable-register)。

###  <a name="loading-meshes"></a>加载网格

Marble Maze 使用 SDK-Mesh 作为运行时格式，因为此格式提供了一种加载示例应用的网格数据的基本方式。 对于生产用途，你应使用一种可满足游戏具体要求的网格格式。

在加载顶点和像素着色器后， **MarbleMazeMain：： LoadDeferredResources**方法加载网格数据。 网格是一个顶点数据集合，通常包含位置、法线数据、颜色、材料和纹理坐标等信息。 网格通常在3D 创作软件中创建，并在独立于应用程序代码的文件中维护。 弹珠和迷宫是该游戏使用的两个网格示例。

Marble Maze 使用 **SDKMesh** 类管理网格。 此类在**SDKMesh**中声明。 **SDKMesh** 提供了加载、呈现和消除网格数据的方法。

> [!IMPORTANT]
> 大理石迷宫使用 SDK-网格格式，并提供**SDKMesh**类仅用于说明。 尽管 SDK-Mesh 格式对学习和创建原型很有用，但它是一种非常基本的格式，可能无法满足大多数游戏开发的需求。 我们建议使用一种可满足游戏具体要求的网格格式。

 

下面的示例演示了**MarbleMazeMain：： LoadDeferredResources**方法如何使用**SDKMesh：： Create**方法为迷宫和球加载网格数据。

```cpp
// Load the meshes.
DX::ThrowIfFailed(
    m_mazeMesh.Create(
        m_deviceResources->GetD3DDevice(),
        L"Media\\Models\\maze1.sdkmesh",
        false
        )
    );

DX::ThrowIfFailed(
    m_marbleMesh.Create(
        m_deviceResources->GetD3DDevice(),
        L"Media\\Models\\marble2.sdkmesh",
        false
        )
    );
```

###  <a name="loading-collision-data"></a>加载碰撞数据

尽管本节不会着重介绍 Marble Maze 如何实现弹珠与迷宫之间的力学模拟，但请注意，力学系统的网格几何图形在加载网格时已准备就绪。

```cpp
// Extract mesh geometry for the physics system.
DX::ThrowIfFailed(
    ExtractTrianglesFromMesh(
        m_mazeMesh,
        "Mesh_walls",
        m_collision.m_wallTriList
        )
    );

DX::ThrowIfFailed(
    ExtractTrianglesFromMesh(
        m_mazeMesh,
        "Mesh_Floor",
        m_collision.m_groundTriList
        )
    );

DX::ThrowIfFailed(
    ExtractTrianglesFromMesh(
        m_mazeMesh,
        "Mesh_floorSides",
        m_collision.m_floorTriList
        )
    );

m_physics.SetCollision(&m_collision);
float radius = m_marbleMesh.GetMeshBoundingBoxExtents(0).x / 2;
m_physics.SetRadius(radius);
```

加载冲突数据的方式很大程度上取决于所使用的运行时格式。 有关大理石迷宫如何从 SDK 网格文件加载冲突几何图形的详细信息，请参阅源代码中的**MarbleMazeMain：： ExtractTrianglesFromMesh**方法。

## <a name="updating-game-state"></a>更新游戏状态


通过在渲染所有场景对象之前首先更新它们，Marble Maze 将游戏逻辑与渲染逻辑分开。

[大理石迷宫应用程序结构](marble-maze-application-structure.md)描述了主要游戏循环。 更新场景（这是游戏循环的一部分）在处理 Windows 事件和输入之后和渲呈现场景之前执行。 **MarbleMazeMain：： Update**方法处理 UI 和游戏的更新。

### <a name="updating-the-user-interface"></a>更新用户界面

**MarbleMazeMain：： update**方法调用**UserInterface：： update**方法以更新 UI 的状态。

```cpp
UserInterface::GetInstance().Update(
    static_cast<float>(m_timer.GetTotalSeconds()), 
    static_cast<float>(m_timer.GetElapsedSeconds()));
```

**UserInterface::Update** 方法更新 UI 集合中的每个元素。

```cpp
void UserInterface::Update(float timeTotal, float timeDelta)
{
    for (auto iter = m_elements.begin(); iter != m_elements.end(); ++iter)
    {
        (*iter)->Update(timeTotal, timeDelta);
    }
}
```

派生自**ElementBase**的类（在**UserInterface**中定义）实现**更新**方法以执行特定行为。 例如，**StopwatchTimer::Update** 方法通过所提供的时间量来更新已经过去的时间，并更新它稍后将显示的文本。

```cpp
void StopwatchTimer::Update(float timeTotal, float timeDelta)
{
    if (m_active)
    {
        m_elapsedTime += timeDelta;

        WCHAR buffer[16];
        GetFormattedTime(buffer);
        SetText(buffer);
    }

    TextElement::Update(timeTotal, timeDelta);
}
```

###  <a name="updating-the-scene"></a>更新场景

**MarbleMazeMain：： Update**方法根据状态机（存储在**M_gameState**中的**GameState**）的当前状态更新游戏。 当游戏处于 "活动" 状态（**GameState：： InGameActive**）时，大理石迷宫将更新照相机以跟随大理石，更新常量缓冲区的视图矩阵部分，并更新物理学模拟。

下面的示例演示了**MarbleMazeMain：： Update**方法如何更新相机的位置。 大理石迷宫使用**m\_resetCamera**变量来标记相机必须重置为直接位于大理石的上方。 相机在游戏开始或弹珠从迷宫掉落时重置。 主菜单或高分显示屏幕处于活动状态时，相机设置在一个固定位置上。 否则，Marble Maze 使用 *timeDelta* 参数将相机的位置插入其当前位置与目标位置之间。 目标位置在弹珠的前方稍微靠上的位置。 使用已经过的帧时间，可使相机不断跟踪或追踪弹珠。

```cpp
static float eyeDistance = 200.0f;
static XMFLOAT3A eyePosition = XMFLOAT3A(0, 0, 0);

// Gradually move the camera above the marble.
XMFLOAT3A targetEyePosition;
XMStoreFloat3A(
    &targetEyePosition, 
    XMLoadFloat3A(&marblePosition) - (XMLoadFloat3A(&g) * eyeDistance));

if (m_resetCamera)
{
    eyePosition = targetEyePosition;
    m_resetCamera = false;
}
else
{
    XMStoreFloat3A(
        &eyePosition, 
        XMLoadFloat3A(&eyePosition) 
            + ((XMLoadFloat3A(&targetEyePosition) - XMLoadFloat3A(&eyePosition)) 
                * min(1, static_cast<float>(m_timer.GetElapsedSeconds()) * 8)
            )
    );
}

// Look at the marble. 
if ((m_gameState == GameState::MainMenu) || (m_gameState == GameState::HighScoreDisplay))
{
    // Override camera position for menus.
    XMStoreFloat3A(
        &eyePosition, 
        XMLoadFloat3A(&marblePosition) + XMVectorSet(75.0f, -150.0f, -75.0f, 0.0f));

    m_camera->SetViewParameters(
        eyePosition, 
        marblePosition, 
        XMFLOAT3(0.0f, 0.0f, -1.0f));
}
else
{
    m_camera->SetViewParameters(eyePosition, marblePosition, XMFLOAT3(0.0f, 1.0f, 0.0f));
}
```

下面的示例演示了**MarbleMazeMain：： Update**方法如何更新大理石和迷宫的常量缓冲区。 迷宫的模型或世界矩阵始终保留单位矩阵。 除了主要的对角（它包含所有元素），单位矩阵是一个由 0 组成的方形矩阵。 弹珠的模型矩阵基于它的位置矩阵与旋转矩阵的乘积。

```cpp
// Update the model matrices based on the simulation.
XMStoreFloat4x4(&m_mazeConstantBufferData.model, XMMatrixIdentity());

XMStoreFloat4x4(
    &m_marbleConstantBufferData.model, 
    XMMatrixTranspose(
        XMMatrixMultiply(
            marbleRotationMatrix, 
            XMMatrixTranslationFromVector(XMLoadFloat3A(&marblePosition))
        )
    )
);

// Update the view matrix based on the camera.
XMFLOAT4X4 view;
m_camera->GetViewMatrix(&view);
m_mazeConstantBufferData.view = view;
m_marbleConstantBufferData.view = view;
```

有关**MarbleMazeMain：： Update**方法如何读取用户输入并模拟大理石运动的信息，请参阅[向大理石迷宫示例添加输入和交互性](adding-input-and-interactivity-to-the-marble-maze-sample.md)。

## <a name="rendering-the-scene"></a>呈现场景


渲染一个场景时，通常包含以下步骤。

1.  设置当前渲染目标的深度模具缓冲区。
2.  清除渲染和模具视图。
3.  为绘制准备顶点和像素着色器。
4.  在场景中呈现三维对象。
5.  呈现要显示在场景前面的任何2D 对象。
6.  向监视器显示已呈现的图像。

**MarbleMazeMain：： Render**方法绑定呈现器目标和深度模具视图，清除这些视图，绘制场景，然后绘制覆盖区。

###  <a name="preparing-the-render-targets"></a>准备呈现目标

渲染场景之前，必须设置当前渲染目标的深度模具缓冲区。 如果你的场景无法保证在屏幕上的所有像素上绘制，应清除渲染和模具视图。 Marble Maze 清除每一帧上的呈现和模具视图，以确保没有来自前一帧的可视工件。

下面的示例演示了**MarbleMazeMain：： Render**方法如何调用[ID3D11DeviceContext：： OMSetRenderTargets](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets)方法将呈现器目标和深度模具缓冲区设置为当前的目标。

```cpp
auto context = m_deviceResources->GetD3DDeviceContext();

// Reset the viewport to target the whole screen.
auto viewport = m_deviceResources->GetScreenViewport();
context->RSSetViewports(1, &viewport);

// Reset render targets to the screen.
ID3D11RenderTargetView *const targets[1] = 
    { m_deviceResources->GetBackBufferRenderTargetView() };

context->OMSetRenderTargets(1, targets, m_deviceResources->GetDepthStencilView());

// Clear the back buffer and depth stencil view.
context->ClearRenderTargetView(
    m_deviceResources->GetBackBufferRenderTargetView(), 
    DirectX::Colors::Black);

context->ClearDepthStencilView(
    m_deviceResources->GetDepthStencilView(), 
    D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 
    1.0f, 
    0);
```

[ID3D11RenderTargetView](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview)和[ID3D11DepthStencilView](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11depthstencilview)接口支持 Direct3D 10 和更高版本提供的纹理视图机制。 有关纹理视图的详细信息，请参阅[纹理视图 (Direct3D 10)](https://docs.microsoft.com/windows/desktop/direct3d10/d3d10-graphics-programming-guide-resources-access-views)。 [OMSetRenderTargets](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets)方法准备 Direct3D 管道的输出合并阶段。 有关输出合并阶段的详细信息，请参阅[输出合并阶段](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-output-merger-stage)。

### <a name="preparing-the-vertex-and-pixel-shaders"></a>准备顶点和像素着色器

渲染场景对象之前，执行以下步骤来准备用于绘制的顶点和像素着色器：

1.  将着色器输入布局设置为当前布局。
2.  将顶点和像素着色器设置为当前着色器。
3.  使用你需要传递到着色器的数据更新任何常量缓冲区。

> [!IMPORTANT]
> 对于所有3D 对象，大理石迷宫使用一对顶点和像素着色器。 如果游戏使用多对着色器，必须在每次绘制使用不同着色器的对象时执行这些步骤。 要减少与更改着色器状态相关的开销，我们建议将对使用相同着色器的所有对象的渲染调用分为一组。

 

本文档中的[加载着色器](#loading-shaders)一节介绍了在创建顶点着色器时如何创建输入布局。 下面的示例演示了**MarbleMazeMain：： Render**方法如何使用[ID3D11DeviceContext：： IASetInputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetinputlayout)方法将此布局设置为当前布局。

```cpp
m_deviceResources->GetD3DDeviceContext()->IASetInputLayout(m_inputLayout.Get());
```

下面的示例演示了**MarbleMazeMain：： Render**方法如何使用[ID3D11DeviceContext：： VSSetShader](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetshader)和[ID3D11DeviceContext：:P ssetshader](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetshader)方法将顶点和像素着色器分别设置为当前着色器。

```cpp
// Set the vertex shader stage state.
m_deviceResources->GetD3DDeviceContext()->VSSetShader(
    m_vertexShader.Get(),   // use this vertex shader
    nullptr,                // don't use shader linkage
    0);                     // don't use shader linkage

m_deviceResources->GetD3DDeviceContext()->PSSetShader(
    m_pixelShader.Get(),    // use this pixel shader
    nullptr,                // don't use shader linkage
    0);                     // don't use shader linkage

m_deviceResources->GetD3DDeviceContext()->PSSetSamplers(
    0,                          // starting at the first sampler slot
    1,                          // set one sampler binding
    m_sampler.GetAddressOf());  // to use this sampler
```

在**MarbleMazeMain：： Render**设置着色器及其输入布局后，它将使用[ID3D11DeviceContext：： UpdateSubresource](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-updatesubresource)方法，通过迷宫的模型、视图和投影矩阵来更新常量缓冲区。 **UpdateSubresource** 方法将矩阵数据从 CPU 内存复制到 GPU 内存。 回忆一下，在**MarbleMazeMain：： Update**方法中更新了**ConstantBuffer**结构的模型和视图组件。 然后， **MarbleMazeMain：： Render**方法调用[ID3D11DeviceContext：： VSSetConstantBuffers](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetconstantbuffers)和[ID3D11DeviceContext：:P ssetconstantbuffers](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetconstantbuffers)方法将此常量缓冲区设置为当前缓冲区。

```cpp
// Update the constant buffer with the new data.
m_deviceResources->GetD3DDeviceContext()->UpdateSubresource(
    m_constantBuffer.Get(),
    0,
    nullptr,
    &m_mazeConstantBufferData,
    0,
    0);

m_deviceResources->GetD3DDeviceContext()->VSSetConstantBuffers(
    0,                                  // starting at the first constant buffer slot
    1,                                  // set one constant buffer binding
    m_constantBuffer.GetAddressOf());   // to use this buffer

m_deviceResources->GetD3DDeviceContext()->PSSetConstantBuffers(
    0,                                  // starting at the first constant buffer slot
    1,                                  // set one constant buffer binding
    m_constantBuffer.GetAddressOf());   // to use this buffer
```

**MarbleMazeMain：： Render**方法执行类似的步骤来准备要呈现的大理石。

### <a name="rendering-the-maze-and-the-marble"></a>呈现迷宫和弹珠

激活当前着色器后，即可绘制场景对象。 **MarbleMazeMain：： render**方法调用**SDKMesh：： render**方法以呈现迷宫网格。

```cpp
m_mazeMesh.Render(
    m_deviceResources->GetD3DDeviceContext(), 
    0, 
    INVALID_SAMPLER_SLOT, 
    INVALID_SAMPLER_SLOT);
```

**MarbleMazeMain：： Render**方法执行类似的步骤来呈现大理石。

如本文档前面所述，**SDKMesh** 类仅用于演示用途，我们不建议将它用于生产质量游戏中。 但请注意，由**SDKMesh：： Render**调用的**SDKMesh：： RenderMesh**方法使用[ID3D11DeviceContext：： IASetVertexBuffers](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetvertexbuffers)和[ID3D11DeviceContext：： IASetIndexBuffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetindexbuffer)方法来设置定义网格的当前顶点和索引缓冲区，并使用[ID3D11DeviceContext：:D rawindexed](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-drawindexedinstanced)方法来绘制缓冲区。 有关如何使用顶点和索引缓冲区的详细信息，请参阅 [Direct3D 11 中的缓冲区简介](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro)。

### <a name="drawing-the-user-interface-and-overlay"></a>绘制用户界面和覆盖

绘制三维场景对象之后，大理石迷宫绘制显示在场景前面的 2D UI 元素。

**MarbleMazeMain：： Render**方法通过绘制用户界面和重叠来结束。

```cpp
// Draw the user interface and the overlay.
UserInterface::GetInstance().Render(m_deviceResources->GetOrientationTransform2D());

m_deviceResources->GetD3DDeviceContext()->BeginEventInt(L"Render Overlay", 0);
m_sampleOverlay->Render();
m_deviceResources->GetD3DDeviceContext()->EndEvent();
```

**UserInterface：： Render**方法使用[ID2D1DeviceContext](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1devicecontext)对象来绘制 UI 元素。 此方法设置绘制状态、绘制所有活动的 UI 元素，然后还原以前的绘制状态。

```cpp
void UserInterface::Render(D2D1::Matrix3x2F orientation2D)
{
    m_d2dContext->SaveDrawingState(m_stateBlock.Get());
    m_d2dContext->BeginDraw();
    m_d2dContext->SetTransform(orientation2D);

    m_d2dContext->SetTextAntialiasMode(D2D1_TEXT_ANTIALIAS_MODE_GRAYSCALE);

    for (auto iter = m_elements.begin(); iter != m_elements.end(); ++iter)
    {
        if ((*iter)->IsVisible())
            (*iter)->Render();
    }

    // We ignore D2DERR_RECREATE_TARGET here. This error indicates that the device
    // is lost. It will be handled during the next call to Present.
    HRESULT hr = m_d2dContext->EndDraw();
    if (hr != D2DERR_RECREATE_TARGET)
    {
        DX::ThrowIfFailed(hr);
    }

    m_d2dContext->RestoreDrawingState(m_stateBlock.Get());
}
```

**SampleOverlay::Render** 方法使用一种类似技术来绘制覆盖位图。

###  <a name="presenting-the-scene"></a>呈现场景

绘制所有二维和三维场景对象之后，大理石迷宫将呈现的图像显示到监视器中。 它将图形与垂直空白同步，确保游戏不会花时间绘制从不会在显示器上实际显示的帧。 Marble Maze 在呈现场景时还会处理设备更改。

**MarbleMazeMain：： Render**方法返回后，游戏循环会调用**DX：:D eviceresources：:P 重发**方法，将呈现的图像发送到监视器或显示。 **DX：:D eviceresources：:P 重发**方法调用[IDXGISwapChain：:P 重新发送](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present)以执行当前操作，如以下示例中所示：

```cpp
// The first argument instructs DXGI to block until VSync, putting the application
// to sleep until the next VSync. This ensures we don't waste any cycles rendering
// frames that will never be displayed to the screen.
HRESULT hr = m_swapChain->Present(1, 0);
```

在此示例中， **m\_存在**是一个[IDXGISwapChain1](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1)对象。 此对象的初始化已在本文档的[初始化 Direct3D 和 Direct2D](#initializing-direct3d-and-direct2d) 一节中介绍。

IDXGISwapChain 的第一个参数[：:P 重发](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1)， *SyncInterval*，指定在呈现帧之前要等待的垂直空白的数目。 Marble Maze 指定 1，以便它等到下一个垂直空白。

[IDXGISwapChain：:P 重发](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present)方法返回一个错误代码，该代码指示设备已被删除或失败。 在此情况下，Marble Maze 重新初始化设备。

```cpp
// If the device was removed either by a disconnection or a driver upgrade, we
// must recreate all device resources.
if (hr == DXGI_ERROR_DEVICE_REMOVED)
{
    HandleDeviceLost();
}
else
{
    DX::ThrowIfFailed(hr);
}
```

## <a name="next-steps"></a>后续步骤


查阅[向 Marble Maze 示例添加输入和交互性](adding-input-and-interactivity-to-the-marble-maze-sample.md)，了解在使用输入设备时要记住的一些重要实践。 本文档讨论了大理石迷宫如何支持触摸、加速感应、Xbox 控制器和鼠标输入。

## <a name="related-topics"></a>相关主题


* [向大理石迷宫示例添加输入和交互性](adding-input-and-interactivity-to-the-marble-maze-sample.md)
* [大理石迷宫应用程序结构](marble-maze-application-structure.md)
* [开发大理石迷宫，其中的 UWP 游戏C++和 DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 




