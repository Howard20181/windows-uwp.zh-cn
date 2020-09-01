---
title: 使用非映射磁贴的光栅器行为
description: 了解有关 DepthStencilView 的详细信息，请参阅 (DSV) 和 RenderTargetView (RTV) 光栅化行为与非映射磁贴。
ms.assetid: AC7B818D-E52B-4727-AEA2-39CFDC279CE7
keywords:
- 使用非映射磁贴的光栅器行为
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: f8085d8d29a86c0c5da82f6cb98c57c037b81beb
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89171881"
---
# <a name="span-iddirect3dconceptsrasterizer_behavior_with_non-mapped_tilesspanrasterizer-behavior-with-non-mapped-tiles"></a><span id="direct3dconcepts.rasterizer_behavior_with_non-mapped_tiles"></span>使用非映射磁贴的光栅器行为


本节介绍使用非映射磁贴的光栅器行为。

## <a name="span-iddepthstencilviewspanspan-iddepthstencilviewspanspan-iddepthstencilviewspandepthstencilview"></a><span id="DepthStencilView"></span><span id="depthstencilview"></span><span id="DEPTHSTENCILVIEW"></span>DepthStencilView


深度模具视图 (DSV) 的读取和写入行为依赖于硬件支持的程度。 有关具体要求，请参阅[流式资源功能层](streaming-resources-features-tiers.md)的整体读取和写入行为。

下面是理想的行为：

如果磁贴未在 DepthStencilView 中映射，来自读取深度的返回值将为 0，该值随后会馈送到为深度读取值配置的任何运算中。 对缺失的深度磁贴的写入将被丢弃。 写入处理的此理想定义不是[第 2 层](tier-2.md)要求的；对非映射磁贴的写入可能在后续读取可能选取的缓存中结束。

## <a name="span-idrendertargetviewspanspan-idrendertargetviewspanspan-idrendertargetviewspanrendertargetview"></a><span id="RenderTargetView"></span><span id="rendertargetview"></span><span id="RENDERTARGETVIEW"></span>RenderTargetView


呈现目标视图 (RTV) 的读取和写入行为依赖于硬件支持的程度。 有关具体要求，请参阅[流式资源功能层](streaming-resources-features-tiers.md)的整体读取和写入行为。

在所有实现上，同时绑定的不同 RTV（和 DSV）可能有不同的映射和非映射区域，并可能有不同大小的图面格式（这意味着不同的磁贴形状）。

下面是理想的行为：

来自 RTV 的读取在缺失的磁贴中返回 0，写入被丢弃。 写入处理的此理想定义不是[第 2 层](tier-2.md)要求的；对非映射磁贴的写入可能在后续读取可能选取的缓存中结束。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[对流式资源的管道访问](pipeline-access-to-streaming-resources.md)

 

 




