---
title: 交换链缩放和覆盖
description: 了解如何创建已缩放的交换链以提高在移动设备上的渲染速度，以及如何使用覆盖交换链（如果可用）来提高视觉质量。
ms.assetid: 3e4d2d19-cac3-eebc-52dd-daa7a7bc30d1
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 游戏, 交换链缩放, 覆盖, directx
ms.localizationpriority: medium
ms.openlocfilehash: ade5812999a3fe085a7c2091363857d7eefa1870
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89165161"
---
# <a name="swap-chain-scaling-and-overlays"></a>交换链缩放和覆盖



了解如何创建已缩放的交换链以提高在移动设备上的渲染速度，以及如何使用覆盖交换链（如果可用）来提高视觉质量。

## <a name="swap-chains-in-directx-112"></a>DirectX 11.2 中的交换链


Direct3D 11.2 使你可以使用从非原生（已降低）分辨率放大的交换链创建通用 Windows 平台 (UWP) 应用，从而实现更快速的填充率。 Direct3D 11.2 中还引入了用于通过硬件覆盖进行呈现的 API，这样你就可以在另一个交换链中使用原生分辨率显示 UI。 它允许你的游戏能够完全使用原始分辨率来绘制 UI，同时还能维持较高的帧速率，从而能够充分发挥移动设备和高 DPI 屏幕（如 3840 X 2160）的优势。 本文说明了如何使用覆盖交换链。

Direct3D 11.2 中还引入了一项通过翻转模型交换链来减少延迟的新功能。 请参阅[利用 DXGI 1.3 交换链减少延迟](reduce-latency-with-dxgi-1-3-swap-chains.md)。

## <a name="use-swap-chain-scaling"></a>使用交换链缩放


当你的游戏在下层硬件上或在已经为节能进行了优化的硬件上运行时，使用比屏幕自身能够提供的分辨率更低的分辨率渲染实时游戏内容有很多好处。 为此，用于渲染游戏内容的交换链必须小于原始分辨率，否则必须使用交换链的一个子区域。

1.  首先，创建一个完全使用原始分辨率的交换链。

    ```cpp
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
    swapChainDesc.Scaling = DXGI_SCALING_STRETCH;

    // This sequence obtains the DXGI factory that was used to create the Direct3D device above.
    ComPtr<IDXGIDevice3> dxgiDevice;
    DX::ThrowIfFailed(
        m_d3dDevice.As(&dxgiDevice)
        );

    ComPtr<IDXGIAdapter> dxgiAdapter;
    DX::ThrowIfFailed(
        dxgiDevice->GetAdapter(&dxgiAdapter)
        );

    ComPtr<IDXGIFactory2> dxgiFactory;
    DX::ThrowIfFailed(
        dxgiAdapter->GetParent(IID_PPV_ARGS(&dxgiFactory))
        );

    ComPtr<IDXGISwapChain1> swapChain;
    DX::ThrowIfFailed(
        dxgiFactory->CreateSwapChainForCoreWindow(
            m_d3dDevice.Get(),
            reinterpret_cast<IUnknown*>(m_window.Get()),
            &swapChainDesc,
            nullptr,
            &swapChain
            )
        );

    DX::ThrowIfFailed(
        swapChain.As(&m_swapChain)
        );
    ```

2.  然后，选择此交换链的一个子区域，通过将源大小设置为降低的分辨率来扩大该交换链子区域。

    DX 前台交换链示例按百分比计算了缩小的尺寸：

    ```cpp
    m_d3dRenderSizePercentage = percentage;

    UINT renderWidth = static_cast<UINT>(m_d3dRenderTargetSize.Width * percentage + 0.5f);
    UINT renderHeight = static_cast<UINT>(m_d3dRenderTargetSize.Height * percentage + 0.5f);

    // Change the region of the swap chain that will be presented to the screen.
    DX::ThrowIfFailed(
        m_swapChain->SetSourceSize(
            renderWidth,
            renderHeight
            )
        );
    ```

3.  创建一个与交换链子区域相匹配的视区。

    ```cpp
    // In Direct3D, change the Viewport to match the region of the swap
    // chain that will now be presented from.
    m_screenViewport = CD3D11_VIEWPORT(
        0.0f,
        0.0f,
        static_cast<float>(renderWidth),
        static_cast<float>(renderHeight)
        );

    m_d3dContext->RSSetViewports(1, &m_screenViewport);
    ```

4.  如果正在使用 Direct2D，需要对旋转转换进行调整以补偿源区域。

## <a name="create-a-hardware-overlay-swap-chain-for-ui-elements"></a>为 UI 元素创建一个硬件覆盖交换链。


使用交换链缩放时，固有的一点不足是 UI 也随之缩小，可能会导致 UI 模糊和难于使用。 在硬件支持覆盖交换链的设备上，通过在与实时游戏内容不同的交换链中使用原生分辨率来呈现 UI，完全解决了这个问题。 请注意，此技术只适用于 [**CoreWindow**](/uwp/api/Windows.UI.Core.CoreWindow) 交换链，不能用于 XAML 互操作。

使用以下步骤创建一个可使用硬件覆盖功能的前台交换链。 在执行这些步骤之前，需要先按照以上所述为实时游戏内容创建一个交换链。

1.  首先，确定 DXGI 适配器是否支持覆盖。 从交换链中获取 DXGI 输出适配器：

    ```cpp
    ComPtr<IDXGIAdapter> outputDxgiAdapter;
    DX::ThrowIfFailed(
        dxgiFactory->EnumAdapters(0, &outputDxgiAdapter)
        );

    ComPtr<IDXGIOutput> dxgiOutput;
    DX::ThrowIfFailed(
        outputDxgiAdapter->EnumOutputs(0, &dxgiOutput)
        );

    ComPtr<IDXGIOutput2> dxgiOutput2;
    DX::ThrowIfFailed(
        dxgiOutput.As(&dxgiOutput2)
        );
    ```

    如果输出适配器为 [**SupportsOverlays**](/windows/desktop/api/dxgi1_3/nf-dxgi1_3-idxgioutput2-supportsoverlays) 返回 True，则 DXGI 适配器支持覆盖。

    ```cpp
    m_overlaySupportExists = dxgiOutput2->SupportsOverlays() ? true : false;
    ```
    
    > **注意**   如果 DXGI 适配器支持覆盖，则继续执行下一步。 如果设备不支持覆盖，使用多个交换链进行呈现时速度将会下降。 这时需要在相同的交换链中以降低的分辨率作为实时游戏内容来呈现 UI。

     

2.  使用 [**IDXGIFactory2::CreateSwapChainForCoreWindow**](/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow) 创建前台交换链。 必须在提供给*pDesc*参数的[**DXGI \_ 交换 \_ 链 \_ DESC1**](/windows/desktop/api/dxgi1_2/ns-dxgi1_2-dxgi_swap_chain_desc1)中设置以下选项：

    -   指定 [**DXGI \_ 交换 \_ 链 \_ 标志 \_ 前景 \_ 层**](/windows/desktop/api/dxgi/ne-dxgi-dxgi_swap_chain_flag) 交换链标志以指示前台交换链。
    -   使用 [**DXGI \_ alpha 模式 \_ 预 \_ 乘**](/windows/desktop/api/dxgi1_2/ne-dxgi1_2-dxgi_alpha_mode) alpha 模式标志。 始终对前台交换链进行预乘。
    -   设置 [**DXGI \_ 缩放 \_ 无**](/windows/desktop/api/dxgi1_2/ne-dxgi1_2-dxgi_scaling) 标志。 前台交换链始终以原生分辨率运行。

    ```cpp
     foregroundSwapChainDesc.Flags = DXGI_SWAP_CHAIN_FLAG_FOREGROUND_LAYER;
     foregroundSwapChainDesc.Scaling = DXGI_SCALING_NONE;
     foregroundSwapChainDesc.AlphaMode = DXGI_ALPHA_MODE_PREMULTIPLIED; // Foreground swap chain alpha values must be premultiplied.
    ```

    > **注意**   每次调整交换链大小时，请再次设置[**DXGI \_ 交换 \_ 链 \_ 标志 \_ 前台 \_ **](/windows/desktop/api/dxgi/ne-dxgi-dxgi_swap_chain_flag) 。

    ```cpp
    HRESULT hr = m_foregroundSwapChain->ResizeBuffers(
        2, // Double-buffered swap chain.
        static_cast<UINT>(m_d3dRenderTargetSize.Width),
        static_cast<UINT>(m_d3dRenderTargetSize.Height),
        DXGI_FORMAT_B8G8R8A8_UNORM,
        DXGI_SWAP_CHAIN_FLAG_FOREGROUND_LAYER // The FOREGROUND_LAYER flag cannot be removed with ResizeBuffers.
        );
    ```

3.  如果使用两个交换链，请将最大帧延迟增加到 2，以便 DXGI 适配器有时间同时（在同一 VSync 间隔内）显示两个交换链。

    ```cpp
    // Create a render target view of the foreground swap chain's back buffer.
    if (m_foregroundSwapChain)
    {
        ComPtr<ID3D11Texture2D> foregroundBackBuffer;
        DX::ThrowIfFailed(
            m_foregroundSwapChain->GetBuffer(0, IID_PPV_ARGS(&foregroundBackBuffer))
            );

        DX::ThrowIfFailed(
            m_d3dDevice->CreateRenderTargetView(
                foregroundBackBuffer.Get(),
                nullptr,
                &m_d3dForegroundRenderTargetView
                )
            );
    }
    ```

4.  前台交换链始终使用预乘 Alpha。 每个像素的颜色值都应该在显示帧之前已经预乘 Alpha 值。 例如，一个 Alpha 值为 50% 的 100% 白色 BGRA 像素设置为 (0.5, 0.5, 0.5, 0.5)。

    可以在 "输出-合并" 阶段完成 alpha premultiplication 步骤，方法是应用 "混合" 状态 (请参阅[**ID3D11BlendState**](/windows/desktop/api/d3d11/nn-d3d11-id3d11blendstate) **) ，并 \_ \_ **将**SrcBlend** [**D3D11 \_ 呈现 \_ 目标 \_ \_ blend**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_render_target_blend_desc) 还可以使用已经预乘了 Alpha 值的资源。

    如果未完成 Alpha 预乘步骤，前台交换链上的颜色会比预期明亮。

5.  根据是否已经创建前台交换链，可能需要将 UI 元素的 Direct2D 绘图图面与前台交换链相关联。

    创建呈现目标视图：

    ```cpp
    // Create a render target view of the foreground swap chain's back buffer.
    if (m_foregroundSwapChain)
    {
        ComPtr<ID3D11Texture2D> foregroundBackBuffer;
        DX::ThrowIfFailed(
            m_foregroundSwapChain->GetBuffer(0, IID_PPV_ARGS(&foregroundBackBuffer))
            );

        DX::ThrowIfFailed(
            m_d3dDevice->CreateRenderTargetView(
                foregroundBackBuffer.Get(),
                nullptr,
                &m_d3dForegroundRenderTargetView
                )
            );
    }
    ```

    创建 Direct2D 绘图图面：

    ```cpp
    if (m_foregroundSwapChain)
    {
        // Create a Direct2D target bitmap for the foreground swap chain.
        ComPtr<IDXGISurface2> dxgiForegroundSwapChainBackBuffer;
        DX::ThrowIfFailed(
            m_foregroundSwapChain->GetBuffer(0, IID_PPV_ARGS(&dxgiForegroundSwapChainBackBuffer))
            );

        DX::ThrowIfFailed(
            m_d2dContext->CreateBitmapFromDxgiSurface(
                dxgiForegroundSwapChainBackBuffer.Get(),
                &bitmapProperties,
                &m_d2dTargetBitmap
                )
            );
    }
    else
    {
        // Create a Direct2D target bitmap for the swap chain.
        ComPtr<IDXGISurface2> dxgiSwapChainBackBuffer;
        DX::ThrowIfFailed(
            m_swapChain->GetBuffer(0, IID_PPV_ARGS(&dxgiSwapChainBackBuffer))
            );

        DX::ThrowIfFailed(
            m_d2dContext->CreateBitmapFromDxgiSurface(
                dxgiSwapChainBackBuffer.Get(),
                &bitmapProperties,
                &m_d2dTargetBitmap
                )
            );
    }

    m_d2dContext->SetTarget(m_d2dTargetBitmap.Get());
    ```

    ```cpp
    // Create a render target view of the swap chain's back buffer.
    ComPtr<ID3D11Texture2D> backBuffer;
    DX::ThrowIfFailed(
        m_swapChain->GetBuffer(0, IID_PPV_ARGS(&backBuffer))
        );

    DX::ThrowIfFailed(
        m_d3dDevice->CreateRenderTargetView(
            backBuffer.Get(),
            nullptr,
            &m_d3dRenderTargetView
            )
        );
    ```

6.  同时显示前台交换链和用于实时游戏内容的已缩放交换链。 由于两个交换链的帧延迟均已设置为 2， 因此 DXGI 可以在同一 VSync 间隔内显示它们。

    ```cpp
    // Present the contents of the swap chain to the screen.
    void DX::DeviceResources::Present()
    {
        // The first argument instructs DXGI to block until VSync, putting the application
        // to sleep until the next VSync. This ensures that we don't waste any cycles rendering
        // frames that will never be displayed to the screen.
        HRESULT hr = m_swapChain->Present(1, 0);

        if (SUCCEEDED(hr) && m_foregroundSwapChain)
        {
            m_foregroundSwapChain->Present(1, 0);
        }

        // Discard the contents of the render targets.
        // This is a valid operation only when the existing contents will be entirely
        // overwritten. If dirty or scroll rects are used, this call should be removed.
        m_d3dContext->DiscardView(m_d3dRenderTargetView.Get());
        if (m_foregroundSwapChain)
        {
            m_d3dContext->DiscardView(m_d3dForegroundRenderTargetView.Get());
        }

        // Discard the contents of the depth stencil.
        m_d3dContext->DiscardView(m_d3dDepthStencilView.Get());

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
    }
    ```

 

 