---
title: 对基元使用深度和效果
description: 下面我们将向你介绍如何对基元使用深度、透视、颜色和其他效果。
ms.assetid: 71ef34c5-b4a3-adae-5266-f86ba257482a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 游戏, 深度, 效果, 基元, directx
ms.localizationpriority: medium
ms.openlocfilehash: 99931ef0abef10cb5c517c4c5be04e2afe3056a2
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89159011"
---
# <a name="use-depth-and-effects-on-primitives"></a>对基元使用深度和效果



下面我们将向你介绍如何对基元使用深度、透视、颜色和其他效果。

**目标：** 创建一个 3D 对象并向其应用基本顶点照明和着色。

## <a name="prerequisites"></a>必备条件


我们假定你熟悉 C++。 你还需要具有图形编程概念方面的基本经验。

我们还假定你已阅读[快速入门：设置 DirectX 资源并显示图像](setting-up-directx-resources.md)和[创建着色器和绘制基元](creating-shaders-and-drawing-primitives.md)。

**完成所需时间：** 20 分钟。

<a name="instructions"></a>Instructions
------------
### <a name="1-defining-cube-variables"></a>1. 定义立方体变量

首先，我们需要为立方体定义 **SimpleCubeVertex** 和 **ConstantBuffer** 结构。 这些结构用于指定立方体的顶点位置和颜色以及查看立方体的方式。 使用 [**ComPtr**](/cpp/windows/comptr-class) 声明 [**ID3D11DepthStencilView**](/windows/desktop/api/d3d11/nn-d3d11-id3d11depthstencilview) 和 [**ID3D11Buffer**](/windows/desktop/api/d3d11/nn-d3d11-id3d11buffer) 并声明 **ConstantBuffer** 的实例。

```cpp
struct SimpleCubeVertex
{
    DirectX::XMFLOAT3 pos;   // Position
    DirectX::XMFLOAT3 color; // Color
};

struct ConstantBuffer
{
    DirectX::XMFLOAT4X4 model;
    DirectX::XMFLOAT4X4 view;
    DirectX::XMFLOAT4X4 projection;
};

// This class defines the application as a whole.
ref class Direct3DTutorialFrameworkView : public IFrameworkView
{
private:
    Platform::Agile<CoreWindow> m_window;
    ComPtr<IDXGISwapChain1> m_swapChain;
    ComPtr<ID3D11Device1> m_d3dDevice;
    ComPtr<ID3D11DeviceContext1> m_d3dDeviceContext;
    ComPtr<ID3D11RenderTargetView> m_renderTargetView;
    ComPtr<ID3D11DepthStencilView> m_depthStencilView;
    ComPtr<ID3D11Buffer> m_constantBuffer;
    ConstantBuffer m_constantBufferData;
```

### <a name="2-creating-a-depth-stencil-view"></a>2. 创建深度模具视图

除了创建呈现器目标视图之外，还需要创建深度模具视图。 深度模具视图让 Direct3D 可以高效地在离相机较远的对象前面呈现离相机较近的对象。 在创建深度模具缓冲区视图之前，必须先创建深度模具缓冲区。 我们将填充 [**D3D11 \_ TEXTURE2D \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_texture2d_desc) 来描述深度模具缓冲区，然后调用 [**ID3D11Device：： CreateTexture2D**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createtexture2d) 来创建深度模具缓冲区。 若要创建深度模具视图，我们将填充 [**D3D11 \_ 深度 \_ 模具 \_ 视图 \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_depth_stencil_view_desc) 来描述深度模具视图，并将深度模具视图说明和深度模具缓冲区传递到 [**ID3D11Device：： CreateDepthStencilView**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createdepthstencilview)。

```cpp
        // Once the render target view is created, create a depth stencil view.  This
        // allows Direct3D to efficiently render objects closer to the camera in front
        // of objects further from the camera.

        D3D11_TEXTURE2D_DESC backBufferDesc = {0};
        backBuffer->GetDesc(&backBufferDesc);

        D3D11_TEXTURE2D_DESC depthStencilDesc;
        depthStencilDesc.Width = backBufferDesc.Width;
        depthStencilDesc.Height = backBufferDesc.Height;
        depthStencilDesc.MipLevels = 1;
        depthStencilDesc.ArraySize = 1;
        depthStencilDesc.Format = DXGI_FORMAT_D24_UNORM_S8_UINT;
        depthStencilDesc.SampleDesc.Count = 1;
        depthStencilDesc.SampleDesc.Quality = 0;
        depthStencilDesc.Usage = D3D11_USAGE_DEFAULT;
        depthStencilDesc.BindFlags = D3D11_BIND_DEPTH_STENCIL;
        depthStencilDesc.CPUAccessFlags = 0;
        depthStencilDesc.MiscFlags = 0;
        ComPtr<ID3D11Texture2D> depthStencil;
        DX::ThrowIfFailed(
            m_d3dDevice->CreateTexture2D(
                &depthStencilDesc,
                nullptr,
                &depthStencil
                )
            );

        D3D11_DEPTH_STENCIL_VIEW_DESC depthStencilViewDesc;
        depthStencilViewDesc.Format = depthStencilDesc.Format;
        depthStencilViewDesc.ViewDimension = D3D11_DSV_DIMENSION_TEXTURE2D;
        depthStencilViewDesc.Flags = 0;
        depthStencilViewDesc.Texture2D.MipSlice = 0;
        DX::ThrowIfFailed(
            m_d3dDevice->CreateDepthStencilView(
                depthStencil.Get(),
                &depthStencilViewDesc,
                &m_depthStencilView
                )
            );
```

### <a name="3-updating-perspective-with-the-window"></a>3. 通过窗口更新透视

根据窗口大小更新常量缓冲区的透视投影参数。 将参数固定为 70 度视区，深度范围从 0.01 到 100。

```cpp
        // Finally, update the constant buffer perspective projection parameters
        // to account for the size of the application window.  In this sample,
        // the parameters are fixed to a 70-degree field of view, with a depth
        // range of 0.01 to 100.  For a generalized camera class, see Lesson 5.

        float xScale = 1.42814801f;
        float yScale = 1.42814801f;
        if (backBufferDesc.Width > backBufferDesc.Height)
        {
            xScale = yScale *
                static_cast<float>(backBufferDesc.Height) /
                static_cast<float>(backBufferDesc.Width);
        }
        else
        {
            yScale = xScale *
                static_cast<float>(backBufferDesc.Width) /
                static_cast<float>(backBufferDesc.Height);
        }

        m_constantBufferData.projection = DirectX::XMFLOAT4X4(
            xScale, 0.0f,    0.0f,  0.0f,
            0.0f,   yScale,  0.0f,  0.0f,
            0.0f,   0.0f,   -1.0f, -0.01f,
            0.0f,   0.0f,   -1.0f,  0.0f
            );
```

### <a name="4-creating-vertex-and-pixel-shaders-with-color-elements"></a>4. 使用颜色元素创建顶点着色器和像素着色器

在此应用中，我们将创建比上一教程[创建着色器和绘制基元](creating-shaders-and-drawing-primitives.md)中介绍的更为复杂的顶点着色器和像素着色器。 此应用的顶点着色器会将每个顶点位置转换为投影空间，并将顶点颜色传递到像素着色器中。

用于描述顶点着色器代码布局的应用的 [**D3D11 \_ 输入 \_ 元素 \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_input_element_desc) 结构的数组具有两个 layout 元素：一个元素定义顶点位置，另一个元素定义颜色。

我们将创建用于定义环行立方体的顶点、索引和常量缓冲区。

**定义环行立方体**

1.  首先，我们定义立方体。 向每个顶点分配一个颜色和位置。 这样可以让像素着色器对每个面进行不同的着色，从而区分这些面。
2.  接下来，我们使用多维数据集定义来描述顶点和索引缓冲区 ([**D3D11 \_ BUFFER \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_buffer_desc) 和 [**D3D11 \_ SUBRESOURCE \_ 数据**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_subresource_data)) 。 为每个缓冲区调用一次 [**ID3D11Device::CreateBuffer**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createbuffer)。
3.  接下来，创建 ([**D3D11 \_ 缓冲区 \_ DESC**](/windows/desktop/api/d3d11/ns-d3d11-d3d11_buffer_desc)) 的常量缓冲区，以便将模型、视图和投影矩阵传递到顶点着色器。 稍后，我们可以使用该常量缓冲区来旋转立方体并向其应用一个透视投影。 调用 [**ID3D11Device::CreateBuffer**](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createbuffer) 来创建常量缓冲区。
4.  接着，指定与相机位置 X = 0、Y = 1、Z = 2 对应的视图转换。
5.  最后，声明 *degree* 变量，该变量将用于通过每帧旋转立方体来实现立方体的动画效果。

```cpp
        
        auto loadVSTask = DX::ReadDataAsync(L"SimpleVertexShader.cso");
        auto loadPSTask = DX::ReadDataAsync(L"SimplePixelShader.cso");
        
        
        auto createVSTask = loadVSTask.then([this](const std::vector<byte>& vertexShaderBytecode) {        
          ComPtr<ID3D11VertexShader> vertexShader;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateVertexShader(
                  vertexShaderBytecode->Data,
                  vertexShaderBytecode->Length,
                  nullptr,
                  &vertexShader
                  )
              );

          // Create an input layout that matches the layout defined in the vertex shader code.
          // For this lesson, this is simply a DirectX::XMFLOAT3 vector defining the vertex position, and
          // a DirectX::XMFLOAT3 vector defining the vertex color.
          const D3D11_INPUT_ELEMENT_DESC basicVertexLayoutDesc[] =
          {
              { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0,  0, D3D11_INPUT_PER_VERTEX_DATA, 0 },
              { "COLOR",    0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
          };

          ComPtr<ID3D11InputLayout> inputLayout;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateInputLayout(
                  basicVertexLayoutDesc,
                  ARRAYSIZE(basicVertexLayoutDesc),
                  vertexShaderBytecode->Data,
                  vertexShaderBytecode->Length,
                  &inputLayout
                  )
              );
        });
        
        
        // Load the raw pixel shader bytecode from disk and create a pixel shader with it.
        auto createPSTask = loadPSTask.then([this](const std::vector<byte>& pixelShaderBytecode) {
          ComPtr<ID3D11PixelShader> pixelShader;
          DX::ThrowIfFailed(
              m_d3dDevice->CreatePixelShader(
                  pixelShaderBytecode->Data,
                  pixelShaderBytecode->Length,
                  nullptr,
                  &pixelShader
                  )
              );
        });
        
        
        // Create vertex and index buffers that define a simple unit cube.
        auto createCubeTask = (createPSTask && createVSTask).then([this] () {

          // In the array below, which will be used to initialize the cube vertex buffers,
          // each vertex is assigned a color in addition to a position.  This will allow
          // the pixel shader to color each face differently, enabling them to be distinguished.
          SimpleCubeVertex cubeVertices[] =
          {
              { float3(-0.5f, 0.5f, -0.5f), float3(0.0f, 1.0f, 0.0f) }, // +Y (top face)
              { float3( 0.5f, 0.5f, -0.5f), float3(1.0f, 1.0f, 0.0f) },
              { float3( 0.5f, 0.5f,  0.5f), float3(1.0f, 1.0f, 1.0f) },
              { float3(-0.5f, 0.5f,  0.5f), float3(0.0f, 1.0f, 1.0f) },

              { float3(-0.5f, -0.5f,  0.5f), float3(0.0f, 0.0f, 1.0f) }, // -Y (bottom face)
              { float3( 0.5f, -0.5f,  0.5f), float3(1.0f, 0.0f, 1.0f) },
              { float3( 0.5f, -0.5f, -0.5f), float3(1.0f, 0.0f, 0.0f) },
              { float3(-0.5f, -0.5f, -0.5f), float3(0.0f, 0.0f, 0.0f) },
          };

          unsigned short cubeIndices[] =
          {
              0, 1, 2,
              0, 2, 3,

              4, 5, 6,
              4, 6, 7,

              3, 2, 5,
              3, 5, 4,

              2, 1, 6,
              2, 6, 5,

              1, 7, 6,
              1, 0, 7,

              0, 3, 4,
              0, 4, 7
          };

          D3D11_BUFFER_DESC vertexBufferDesc = {0};
          vertexBufferDesc.ByteWidth = sizeof(SimpleCubeVertex) * ARRAYSIZE(cubeVertices);
          vertexBufferDesc.Usage = D3D11_USAGE_DEFAULT;
          vertexBufferDesc.BindFlags = D3D11_BIND_VERTEX_BUFFER;
          vertexBufferDesc.CPUAccessFlags = 0;
          vertexBufferDesc.MiscFlags = 0;
          vertexBufferDesc.StructureByteStride = 0;

          D3D11_SUBRESOURCE_DATA vertexBufferData;
          vertexBufferData.pSysMem = cubeVertices;
          vertexBufferData.SysMemPitch = 0;
          vertexBufferData.SysMemSlicePitch = 0;

          ComPtr<ID3D11Buffer> vertexBuffer;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateBuffer(
                  &vertexBufferDesc,
                  &vertexBufferData,
                  &vertexBuffer
                  )
              );

          D3D11_BUFFER_DESC indexBufferDesc;
          indexBufferDesc.ByteWidth = sizeof(unsigned short) * ARRAYSIZE(cubeIndices);
          indexBufferDesc.Usage = D3D11_USAGE_DEFAULT;
          indexBufferDesc.BindFlags = D3D11_BIND_INDEX_BUFFER;
          indexBufferDesc.CPUAccessFlags = 0;
          indexBufferDesc.MiscFlags = 0;
          indexBufferDesc.StructureByteStride = 0;

          D3D11_SUBRESOURCE_DATA indexBufferData;
          indexBufferData.pSysMem = cubeIndices;
          indexBufferData.SysMemPitch = 0;
          indexBufferData.SysMemSlicePitch = 0;

          ComPtr<ID3D11Buffer> indexBuffer;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateBuffer(
                  &indexBufferDesc,
                  &indexBufferData,
                  &indexBuffer
                  )
              );


          // Create a constant buffer for passing model, view, and projection matrices
          // to the vertex shader.  This will allow us to rotate the cube and apply
          // a perspective projection to it.

          D3D11_BUFFER_DESC constantBufferDesc = {0};
          constantBufferDesc.ByteWidth = sizeof(m_constantBufferData);
          constantBufferDesc.Usage = D3D11_USAGE_DEFAULT;
          constantBufferDesc.BindFlags = D3D11_BIND_CONSTANT_BUFFER;
          constantBufferDesc.CPUAccessFlags = 0;
          constantBufferDesc.MiscFlags = 0;
          constantBufferDesc.StructureByteStride = 0;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateBuffer(
                  &constantBufferDesc,
                  nullptr,
                  &m_constantBuffer
                  )
              );

          // Specify the view transform corresponding to a camera position of
          // X = 0, Y = 1, Z = 2.  For a generalized camera class, see Lesson 5.

          m_constantBufferData.view = DirectX::XMFLOAT4X4(
              -1.00000000f, 0.00000000f,  0.00000000f,  0.00000000f,
               0.00000000f, 0.89442718f,  0.44721359f,  0.00000000f,
               0.00000000f, 0.44721359f, -0.89442718f, -2.23606800f,
               0.00000000f, 0.00000000f,  0.00000000f,  1.00000000f
              );

        });
        
        // This value will be used to animate the cube by rotating it every frame.
        float degree = 0.0f;
        
```

### <a name="5-rotating-and-drawing-the-cube-and-presenting-the-rendered-image"></a>5. 旋转和绘制立方体并显示呈现的图像

我们将进入一个不断呈现和显示场景的无限循环。 调用 **rotationY** 内嵌函数 (BasicMath.h)，使用旋转量设置使立方体模型矩阵绕 Y 轴旋转的值。 然后，调用 [**ID3D11DeviceContext::UpdateSubresource**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-updatesubresource) 来更新常量缓冲区并旋转立方体模型。 调用 [**ID3D11DeviceContext::OMSetRenderTargets**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) 以将呈现器目标指定为输出目标。 在此 **OMSetRenderTargets** 调用中，传递深度模具视图。 调用 [**ID3D11DeviceContext::ClearRenderTargetView**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-clearrendertargetview) 以将呈现目标清空为纯蓝色，并调用 [**ID3D11DeviceContext::ClearDepthStencilView**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-cleardepthstencilview) 清空深度缓冲区。

在该无限循环中，我们还需要在蓝色图面上绘制立方体。

**绘制立方体**

1.  首先，调用 [**ID3D11DeviceContext::IASetInputLayout**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetinputlayout) 来描述如何将顶点缓冲区数据流传输到输入程序集阶段。
2.  接着，调用 [**ID3D11DeviceContext::IASetVertexBuffers**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetvertexbuffers) 和 [**ID3D11DeviceContext::IASetIndexBuffer**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetindexbuffer) 将顶点缓冲区和索引缓冲区绑定到输入程序集阶段。
3.  接下来，我们将 [**ID3D11DeviceContext：： IASetPrimitiveTopology**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetprimitivetopology) 与 [**D3D11 \_ 基元 \_ 拓扑 \_ TRIANGLESTRIP**](/previous-versions/windows/desktop/legacy/ff476189(v=vs.85)) 值一起调用，以指定输入汇编程序阶段将顶点数据解释为三角形条带。
4.  接着，调用 [**ID3D11DeviceContext::VSSetShader**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetshader) 以使用顶点着色器代码初始化顶点着色器阶段，并调用 [**ID3D11DeviceContext::PSSetShader**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetshader) 以使用像素着色器代码初始化像素着色器阶段。
5.  接着，调用 [**ID3D11DeviceContext::VSSetConstantBuffers**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetconstantbuffers) 设置由顶点着色器管道阶段使用的常量缓冲区。
6.  最后，调用 [**ID3D11DeviceContext::DrawIndexed**](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-drawindexed) 绘制立方体并将其提交给呈现管道。

调用 [**IDXGISwapChain::Present**](/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present) 以向窗口显示呈现的图像。

```cpp
            // Update the constant buffer to rotate the cube model.
            m_constantBufferData.model = XMMatrixRotationY(-degree);
            degree += 1.0f;

            m_d3dDeviceContext->UpdateSubresource(
                m_constantBuffer.Get(),
                0,
                nullptr,
                &m_constantBufferData,
                0,
                0
                );

            // Specify the render target and depth stencil we created as the output target.
            m_d3dDeviceContext->OMSetRenderTargets(
                1,
                m_renderTargetView.GetAddressOf(),
                m_depthStencilView.Get()
                );

            // Clear the render target to a solid color, and reset the depth stencil.
            const float clearColor[4] = { 0.071f, 0.04f, 0.561f, 1.0f };
            m_d3dDeviceContext->ClearRenderTargetView(
                m_renderTargetView.Get(),
                clearColor
                );

            m_d3dDeviceContext->ClearDepthStencilView(
                m_depthStencilView.Get(),
                D3D11_CLEAR_DEPTH,
                1.0f,
                0
                );

            m_d3dDeviceContext->IASetInputLayout(inputLayout.Get());

            // Set the vertex and index buffers, and specify the way they define geometry.
            UINT stride = sizeof(SimpleCubeVertex);
            UINT offset = 0;
            m_d3dDeviceContext->IASetVertexBuffers(
                0,
                1,
                vertexBuffer.GetAddressOf(),
                &stride,
                &offset
                );

            m_d3dDeviceContext->IASetIndexBuffer(
                indexBuffer.Get(),
                DXGI_FORMAT_R16_UINT,
                0
                );

            m_d3dDeviceContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

            // Set the vertex and pixel shader stage state.
            m_d3dDeviceContext->VSSetShader(
                vertexShader.Get(),
                nullptr,
                0
                );

            m_d3dDeviceContext->VSSetConstantBuffers(
                0,
                1,
                m_constantBuffer.GetAddressOf()
                );

            m_d3dDeviceContext->PSSetShader(
                pixelShader.Get(),
                nullptr,
                0
                );

            // Draw the cube.
            m_d3dDeviceContext->DrawIndexed(
                ARRAYSIZE(cubeIndices),
                0,
                0
                );

            // Present the rendered image to the window.  Because the maximum frame latency is set to 1,
            // the render loop will generally be throttled to the screen refresh rate, typically around
            // 60 Hz, by sleeping the application on Present until the screen is refreshed.
            DX::ThrowIfFailed(
                m_swapChain->Present(1, 0)
                );
```

## <a name="summary-and-next-steps"></a>总结和后续步骤


我们已对基元使用了深度、透视、颜色和其他效果。

接下来，我们要向基元应用纹理。

[向基元应用纹理](applying-textures-to-primitives.md)

 

 