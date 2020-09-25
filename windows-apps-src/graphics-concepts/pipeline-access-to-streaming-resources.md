---
title: 对流式资源的管道访问
description: 流式资源可用于着色器资源视图 (SRV)、呈现目标视图 (RTV)、深度模板视图 (DSV) 和无序访问视图 (UAV)，以及未使用视图的某些盲点（如顶点缓冲区绑定）。
ms.assetid: 18DA5D61-930D-4466-8EFE-0CED566EA4A6
keywords:
- 对流式资源的管道访问
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 15b37e471e45a1c2ca604c1a5bf28ace69e35ad3
ms.sourcegitcommit: eda7bbe9caa9d61126e11f0f1a98b12183df794d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91220320"
---
# <a name="pipeline-access-to-streaming-resources"></a>对流式资源的管道访问


流式资源可用于着色器资源视图 (SRV)、呈现目标视图 (RTV)、深度模板视图 (DSV) 和无序访问视图 (UAV)，以及未使用视图的某些盲点（如顶点缓冲区绑定）。 有关受支持的绑定的列表，请参阅[流式资源创建参数](streaming-resource-creation-parameters.md)。 各种 D3D Copy 运算也适用于流式资源。

如果一个或多个视图中的多个磁贴坐标绑定到同一内存位置，来自同一内存的不同路径的读取和写入将按内存访问的非确定性和不可重复顺序执行。

如果来自着色器内存访问足迹的所有磁贴都映射到唯一的磁贴，则行为在所有实现上都与具有相同非平铺形式内存内容的图面相同。

## <a name="span-idin-this-sectionspanin-this-section"></a><span id="in-this-section"></span>本部分中的内容


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">主题</th>
<th align="left">描述</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="srv-behavior-with-non-mapped-tiles.md">使用非映射磁贴的 SRV 行为</a></p></td>
<td align="left"><p>涉及非映射磁贴的着色器资源视图 (SRV) 读取的行为取决于硬件支持的程度。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="uav-behavior-with-non-mapped-tiles.md">使用非映射磁贴的 UAV 行为</a></p></td>
<td align="left"><p>无序访问视图 (UAV) 读取和写入的行为依赖于硬件支持的级别。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="rasterizer-behavior-with-non-mapped-tiles.md">使用非映射磁贴的光栅器行为</a></p></td>
<td align="left"><p>本节介绍使用非映射磁贴的光栅器行为。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="tile-access-limitations-with-duplicate-mappings.md">使用重复映射的磁贴访问限制</a></p></td>
<td align="left"><p>使用重复映射的磁贴访问存在一些限制，例如在复制具有重叠的源和目标的流式资源时，或在呈现给在呈现区域中共享的磁贴时。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="streaming-resources-texture-sampling-features.md">流式资源纹理采样功能</a></p></td>
<td align="left"><p>流式资源纹理采样功能包括获取有关映射区域的着色器状态反馈，检查正在访问的所有数据是否已在资源中映射，进行固定以帮助着色器避开 Mip 映射流式资源中已知的非映射区域，以及发现为整个纹理筛选器足迹完全映射的最小 LOD。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="hlsl-streaming-resources-exposure.md">HLSL 流式资源暴露</a></p></td>
<td align="left"><p>需要使用特定的 Microsoft 高级着色器语言 (HLSL) 语法来支持 <a href="/windows/desktop/direct3dhlsl/d3d11-graphics-reference-sm5">着色器模型 5</a>中的流式处理资源。</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[流式资源](streaming-resources.md)

 

 