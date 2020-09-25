---
title: 流式资源区域的平铺方式
description: 创建流式资源时，尺寸、格式元素大小以及 mipmap 和/或数组切片的数量（如适用）决定了支持整个图面区域所需的磁贴数量。
ms.assetid: 3485FD8D-2A06-4B0A-8810-ECF37736F94B
keywords:
- 流式资源区域的平铺方式
- 资源区域, 平铺
- 尺寸表, 资源, 平铺
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: fae56f673aa3d952b7e85490ec79676c2ac6a48a
ms.sourcegitcommit: eda7bbe9caa9d61126e11f0f1a98b12183df794d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91220340"
---
# <a name="how-a-streaming-resources-area-is-tiled"></a>流式资源区域的平铺方式


创建流式资源时，尺寸、格式元素大小以及 mipmap 和/或数组切片的数量（如适用）决定了支持整个图面区域所需的磁贴数量。 磁贴内的像素/字节布局由实现来确定。 适合磁贴的像素数量取决于格式元素大小，并且是固定和相同的，无论你是否使用标准重排。

以下部分中的表已明确定义给定图面大小和格式元素宽度使用的磁贴数，并且可根据这些表预测磁贴数。 对于包含 mipmap 的资源或图面尺寸不填充磁贴的情况，存在一些限制；请参阅 [Mipmap 打包](mipmap-packing.md)。

只要应用程序不依赖于用一种格式写入内存并用另一种格式读取内存所产生的结果，不同的流式资源就可以指向具有不同格式的相同内存。 但是，如果格式属于相同的格式系列（即，格式具有相同的无类型父格式），则应用程序可以依赖于用一种格式写入内存并用另一种格式读取内存所产生的结果。 例如，DXGI \_ format \_ R8G8B8A8 \_ UNORM 和 dxgi \_ format \_ R8G8B8A8 UINT 彼此兼容， \_ 但不兼容 dxgi \_ format \_ R16G16 \_ UNORM。

例外情况是，来自别名为另一种格式的格式中的出血数据已被明确定义：如果磁贴的所有位都完全包含 0，则该磁贴可以使用将内存内容解释为 0 的任何格式（不考虑内存布局）。 因此，磁贴可以使用格式 DXGI \_ 格式 R8 UNORM 清除为 0x00 \_ ， \_ 并使用一种格式，如 dxgi \_ format \_ R32G32 FLOAT， \_ 并显示内容仍 (0.0 f，0.0 f) 。

磁贴内的数据布局不取决于磁贴在资源整体中映射到的位置。 例如，可以一次性在图面的不同位置重新使用磁贴，且磁贴在所有位置都具有一致的行为。

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
<td align="left"><p><a href="texture2d-and-texture2darray-subresource-tiling.md">Texture2D 和 Texture2DArray 子资源平铺</a></p></td>
<td align="left"><p>这些表显示了 <a href="/windows/desktop/direct3dhlsl/sm5-object-texture2d"><strong>Texture2D</strong></a> 和 <a href="/windows/desktop/direct3dhlsl/sm5-object-texture2darray"><strong>Texture2DArray</strong></a> 子资源的平铺方式。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="texture3d-subresource-tiling.md">Texture3D 子资源平铺</a></p></td>
<td align="left"><p>此表显示了如何平铺 <a href="/windows/desktop/direct3dhlsl/sm5-object-texture3d"><strong>Texture3D</strong></a> 子资源。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="buffer-tiling.md">缓冲区平铺</a></p></td>
<td align="left"><p><a href="introduction-to-buffers.md">缓冲区</a>资源分为64kb 个磁贴，最后一个磁贴中的某些空白空间（如果大小不是64kb 的倍数）。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="mipmap-packing.md">Mipmap 打包</a></p></td>
<td align="left"><p>可将一定数量的 mip（每个数组切片）打包到一定数量的磁贴中，这取决于流式资源的尺寸、格式、mipmap 的数量和数组切片。</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[创建流式资源](creating-streaming-resources.md)

 

 