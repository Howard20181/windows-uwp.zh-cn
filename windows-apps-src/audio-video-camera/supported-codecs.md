---
ms.assetid: 9347AD7C-3A90-4073-BFF4-9E8237398343
description: 本文将列出 UWP 应用的音频和视频编解码器以及格式支持。
title: 支持的编解码器
ms.date: 04/01/2021
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2d5b9fd8ed0cdde7af38de2c855d2738458eb571
ms.sourcegitcommit: ffe124f95967c49993c8dacc7c4c17f5a534a163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504006"
---
# <a name="supported-codecs"></a>支持的编解码器

本文针对每个设备系列列出了默认情况下 UWP 应用的音频、视频以及图像编解码器和格式可用性。 请注意，这些表列出了指定设备系列安装的 Windows 10 所附带的编解码器。 用户和应用可以安装可供使用的其他编解码器。 你可以在运行时查询特定设备当前可用的一组编解码器。 有关详细信息，请参阅[查询设备上所安装的编解码器](codec-query.md)。

在下表中，“D”表示解码器支持，而“E”表示编码器支持。

## <a name="audio-codec--format-support"></a>音频编解码器和格式支持

下表显示每个设备系列的音频编解码器和格式支持。

> [!NOTE] 
> 其中表明 AMR-NB 支持，此编解码器在服务器 SKU 上不受支持。

 

### <a name="desktop"></a>桌面

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-3</th>
<th align="left">MPEG-2</th>
<th align="left">ADTS</th>
<th align="left">ASF</th>
<th align="left">RIFF</th>
<th align="left">AVI</th>
<th align="left">AC-3</th>
<th align="left">AMR</th>
<th align="left">3GP</th>
<th align="left">FLAC</th>
<th align="left">WAV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">HE-AAC v1 / AAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">HE-AAC v2 / eAAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AAC-LC</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">AC3</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>

<tr class="even">
<td align="left">ALAC</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AMR-NB<sup>*</sup></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">FLAC</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">G.711 (A-Law, µ-law)</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">GSM 6.10</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">IMA ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">LPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">MP3</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-1/2</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MS ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">WMA 1/2/3</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">WMA Pro</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">WMA 语音</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>*</sup> 在 Windows Server Sku 上不可用。

### <a name="mobile"></a>移动

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-3</th>
<th align="left">MPEG-2</th>
<th align="left">ADTS</th>
<th align="left">ASF</th>
<th align="left">RIFF</th>
<th align="left">AVI</th>
<th align="left">AC-3</th>
<th align="left">AMR</th>
<th align="left">3GP</th>
<th align="left">FLAC</th>
<th align="left">WAV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">HE-AAC v1 / AAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">HE-AAC v2 / eAAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AAC-LC</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">AC3</td>
<td align="left">D, 仅在 Lumia 图标上, 830, 930, 1520</td>
<td align="left"></td>
<td align="left">D, 仅在 Lumia 图标上, 830, 930, 1520</td>
<td align="left"></td>
<td align="left">D, 仅在 Lumia 图标上, 830, 930, 1520</td>
<td align="left">D, 仅在 Lumia 图标上, 830, 930, 1520</td>
<td align="left">D, 仅在 Lumia 图标上, 830, 930, 1520</td>
<td align="left">D, 仅在 Lumia 图标上, 830, 930, 1520</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>

<tr class="even">
<td align="left">ALAC</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AMR-NB</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">FLAC</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">G.711 (A-Law, µ-law)</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">GSM 6.10</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">IMA ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">LPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">MP3</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-1/2</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MS ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">WMA 1/2/3</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">WMA Pro</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">WMA 语音</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

 

### <a name="windows-10-iot"></a>WIndows 10 IoT

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-3</th>
<th align="left">MPEG-2</th>
<th align="left">ADTS</th>
<th align="left">ASF</th>
<th align="left">RIFF</th>
<th align="left">AVI</th>
<th align="left">AC-3</th>
<th align="left">AMR</th>
<th align="left">3GP</th>
<th align="left">FLAC</th>
<th align="left">WAV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">HE-AAC v1 / AAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">HE-AAC v2 / eAAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AAC-LC</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">AC3</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>

<tr class="even">
<td align="left">ALAC</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AMR-NB</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">FLAC</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">G.711 (A-Law, µ-law)</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">GSM 6.10</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">IMA ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">LPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">MP3</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-1/2</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MS ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">WMA 1/2/3</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">WMA Pro</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">WMA 语音</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>



### <a name="xbox"></a>Xbox

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-3</th>
<th align="left">MPEG-2</th>
<th align="left">ADTS</th>
<th align="left">ASF</th>
<th align="left">RIFF</th>
<th align="left">AVI</th>
<th align="left">AC-3</th>
<th align="left">AMR</th>
<th align="left">3GP</th>
<th align="left">FLAC</th>
<th align="left">WAV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">HE-AAC v1 / AAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">HE-AAC v2 / eAAC+</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AAC-LC</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">AC3</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>

<tr class="even">
<td align="left">ALAC</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AMR-NB</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">FLAC</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">G.711 (A-Law, µ-law)</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">GSM 6.10</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">IMA ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">LPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">MP3</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-1/2</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MS ADPCM</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">WMA 1/2/3</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">WMA Pro</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">WMA 语音</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

 

## <a name="video-codec--format-support"></a>视频编解码器和格式支持

下表显示每个设备系列的视频编解码器和格式支持。

> [!NOTE] 
> 其中表明 H.265 支持，该设备系列内的所有设备不一定都受支持。

 

### <a name="desktop"></a>桌面

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">fMP4</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-2 PS</th>
<th align="left">MPEG-2 TS</th>
<th align="left">MPEG-1</th>
<th align="left">3GPP</th>
<th align="left">3GPP2</th>
<th align="left">AVCHD</th>
<th align="left">ASF</th>
<th align="left">AVI</th>
<th align="left">MKV</th>
<th align="left">DV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">MPEG-1<sup>*</sup></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-2<sup>*</sup></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MPEG-4（第 2 部分）</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">H。<sup>**</sup></td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">H.264</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">H.263</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">VC-1</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">WMV7/8/9</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">WMV9 屏幕</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">DV</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left">D</td>
</tr>

<tr class="even">
<td align="left">VP8</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>

<tr class="even">
<td align="left">VP9</td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>

<tr class="even">
<td align="left">AV1<sup>**</sup></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>

<tr class="odd">
<td align="left">Motion JPEG</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>*</sup> 安装可选的 Microsoft UWP DVD 应用程序时，可以使用 MPEG-2/MPEG-2 编解码器。

<sup>**</sup> 可以通过安装相应的可选编解码器包来提供 AV1。

### <a name="mobile"></a>移动

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">fMP4</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-2 PS</th>
<th align="left">MPEG-2 TS</th>
<th align="left">3GPP</th>
<th align="left">AVCHD</th>
<th align="left">ASF</th>
<th align="left">AVI</th>
<th align="left">MKV</th>
</tr>
</thead>
<tbody>

<tr class="odd">
<td align="left">MPEG-4（第 2 部分）</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">H.265</td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">H.264</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">H.263</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">VC-1</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
</tr>

<tr class="odd">
<td align="left">Motion JPEG</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
</tbody>
</table>

 

### <a name="windows-10-iot"></a>Windows 10 IoT

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">fMP4</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-2 PS</th>
<th align="left">MPEG-2 TS</th>
<th align="left">MPEG-1</th>
<th align="left">3GPP</th>
<th align="left">3GPP2</th>
<th align="left">AVCHD</th>
<th align="left">ASF</th>
<th align="left">AVI</th>
<th align="left">MKV</th>
<th align="left">DV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">MPEG-1</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-2<sup>*</sup></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MPEG-4（第 2 部分）</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">H。<sup>**</sup></td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">H.264</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">H.263</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">VC-1</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">WMV7/8/9</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">WMV9 屏幕</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">VP8</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">VP9</td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">AV1<sup>**</sup></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">DV</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">Motion JPEG</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>*</sup> 安装可选的 Microsoft UWP DVD 应用程序时，可以使用 MPEG-2/MPEG-2 编解码器。

<sup>**</sup> 可以通过安装相应的可选编解码器包来提供 AV1。
 



 

### <a name="xbox"></a>Xbox

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器/容器</th>
<th align="left">fMP4</th>
<th align="left">MPEG-4</th>
<th align="left">MPEG-2 PS</th>
<th align="left">MPEG-2 TS</th>
<th align="left">MPEG-1</th>
<th align="left">3GPP</th>
<th align="left">AVCHD</th>
<th align="left">ASF</th>
<th align="left">AVI</th>
<th align="left">MKV</th>
<th align="left">DV</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">MPEG-1</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">MPEG-2</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">MPEG-4（第 2 部分）</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">H.265</td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">H.264</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">H.263</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">VC-1</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">VP8</td>
<td align="left"></td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">VP9</td>
<td align="left">D</td>
<td align="left">D/E</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">AV1</td>
<td align="left">D</td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">DV</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
</tr>
<tr class="odd">
<td align="left">Motion JPEG</td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
<td align="left">D</td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

## <a name="image-codec--format-support"></a>图像编解码器和格式支持 

<table>
<colgroup>
<col width="7%" />
<col width="7%" />
<col width="7%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">编解码器</th>
<th align="left">桌面</th>
<th align="left">其他设备系列</th>
</tr>
</thead>
<tr class="odd">
<td align="left">BMP</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
</tr>
<tr class="even">
<td align="left">DDS</td>
<td align="left">D/E<sup>1</sup></td>
<td align="left">D/E<sup>1</sup></td>
</tr>
<tr class="odd">
<td align="left">DNG</td>
<td align="left">D<sup>2</sup></td>
<td align="left">D<sup>2</sup></td>
</tr>
<tr class="even">
<td align="left">GIF</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">ICO</td>
<td align="left">D</td>
<td align="left">D</td>
</tr>
<tr class="even">
<td align="left">JPEG</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">JPEG-XR</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
</tr>
<tr class="even">
<td align="left">PNG</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
</tr>
<tr class="odd">
<td align="left">TIFF</td>
<td align="left">D/E</td>
<td align="left">D/E</td>
</tr>
<tr class="even">
<td align="left">相机 RAW</td>
<td align="left">D<sup>3</sup></td>
<td align="left">否</td>
</tr>
</table>

<sup>1</sup> 支持使用 BC1 到 BC5 压缩的 DDS 映像。  
<sup>2</sup> 支持具有非 RAW 嵌入预览的 DNG 图像。  
<sup>3</sup> 仅支持某些相机 RAW 格式。  

有关图像编解码器的详细信息，请参阅[本机 WIC 编解码器](/windows/desktop/wic/native-wic-codecs)。