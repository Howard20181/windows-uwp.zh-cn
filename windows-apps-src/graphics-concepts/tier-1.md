---
title: 第 1 层
description: 了解影响 Direct3D 中流式处理资源功能的第1层支持的常规和特定限制。
ms.assetid: 153DA429-0C99-4691-AEB4-124FD9B17BE2
keywords:
- 第 1 层
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 2ff90c75e3f4aefa28ddad5528d39d2f2d541bb0
ms.sourcegitcommit: 5481bb34def681bc60fbfa42d9779053febec468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304639"
---
# <a name="tier-1"></a>第 1 层


本部分将介绍第 1 层支持。

## <a name="span-idtier_1_general_limitationsspanspan-idtier_1_general_limitationsspanspan-idtier_1_general_limitationsspantier-1-general-limitations"></a><span id="Tier_1_general_limitations"></span><span id="tier_1_general_limitations"></span><span id="TIER_1_GENERAL_LIMITATIONS"></span>第 1 层常规限制


-   最低功能级别 11.0 所用硬件。
-   无绗缝支持。
-   无 Texture1D 或 Texture3D 支持。
-   无 2、8 或 16 样本多重采样抗锯齿 (MSAA) 支持。 只有 4x 是必需的，没有 128 位格式的情况以外。
-   没有标准重排模式（64KB 磁贴和尾部 mip 打包中的布局取决于硬件供应商）。
-   存在重复映射时磁贴的访问方式受到限制。 请参阅[存在重复映射时的磁贴访问限制](tile-access-limitations-with-duplicate-mappings.md)。

## <a name="span-idspecific_limitations_affecting_tier_1_onlyspanspan-idspecific_limitations_affecting_tier_1_onlyspanspan-idspecific_limitations_affecting_tier_1_onlyspanspecific-limitations-affecting-tier-1-only"></a><span id="Specific_limitations_affecting_tier_1_only"></span><span id="specific_limitations_affecting_tier_1_only"></span><span id="SPECIFIC_LIMITATIONS_AFFECTING_TIER_1_ONLY"></span>仅影响第 1 层的特定限制


### <a name="span-idreading_writing_to_streaming_resources_that_have_null_mappingsspanspan-idreading_writing_to_streaming_resources_that_have_null_mappingsspanspan-idreading_writing_to_streaming_resources_that_have_null_mappingsspanreadingwriting-to-streaming-resources-that-have-null-mappings"></a><span id="Reading_writing_to_streaming_resources_that_have_NULL_mappings"></span><span id="reading_writing_to_streaming_resources_that_have_null_mappings"></span><span id="READING_WRITING_TO_STREAMING_RESOURCES_THAT_HAVE_NULL_MAPPINGS"></span>读/写到具有 NULL 映射的流式处理资源

流式资源可以具有 **NULL** 映射，但对其的读取或写入操作会产生未定义的结果，包括设备删除。 通过将单个虚拟页面映射到所有空白区域，应用程序可以避免此问题。 当你向映射到多个渲染目标位置的页面写入或渲染时请格外小心，因为写入顺序将是未定义的。

### <a name="span-idno_shader_instructions_for_clamping_lod_and_mapped_status_feedbackspanspan-idno_shader_instructions_for_clamping_lod_and_mapped_status_feedbackspanspan-idno_shader_instructions_for_clamping_lod_and_mapped_status_feedbackspanno-shader-instructions-for-clamping-lod-and-mapped-status-feedback"></a><span id="No_shader_instructions_for_clamping_LOD_and_mapped_status_feedback"></span><span id="no_shader_instructions_for_clamping_lod_and_mapped_status_feedback"></span><span id="NO_SHADER_INSTRUCTIONS_FOR_CLAMPING_LOD_AND_MAPPED_STATUS_FEEDBACK"></span>没有用于钳位 LOD 和映射状态反馈的着色器说明

不提供针对固定 LOD 和映射状态反馈的着色器指令。 请参阅 [HLSL 流式资源暴露](hlsl-streaming-resources-exposure.md)。

### <a name="span-idalignment_constraints_for_standard_tile_shapesspanspan-idalignment_constraints_for_standard_tile_shapesspanspan-idalignment_constraints_for_standard_tile_shapesspanalignment-constraints-for-standard-tile-shapes"></a><span id="Alignment_constraints_for_standard_tile_shapes"></span><span id="alignment_constraints_for_standard_tile_shapes"></span><span id="ALIGNMENT_CONSTRAINTS_FOR_STANDARD_TILE_SHAPES"></span>针对标准磁贴形状的对齐约束

仅保证所有维度均为标准磁贴大小倍数的 mip（从最精细的开始）支持标准磁贴形状并且可以拥有任意方式映射/未映射的单个磁贴。 流式资源中的第一个 mipmap（任何维度的大小并非标准磁贴大小的倍数）以及所有粗粒度 mipmap 可以具有非标准平铺形状，同时适合此组 mip 的 N 64KB 磁贴（N 已报告给应用程序）。 这些 N 磁贴被视为打包为一个单位，必须由应用程序在任意给定时间完全映射或完全取消映射，但各个 N 磁贴的映射可以位于磁贴池内的任意非连续位置。

### <a name="span-idarray_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_sizespanspan-idarray_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_sizespanspan-idarray_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_sizespanarray-of-mipmaps-that-arent-a-multiple-of-standard-tile-size"></a><span id="Array_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_size"></span><span id="array_of_mipmaps_that_aren_t_a_multiple_of_standard_tile_size"></span><span id="ARRAY_OF_MIPMAPS_THAT_AREN_T_A_MULTIPLE_OF_STANDARD_TILE_SIZE"></span>非标准磁贴大小倍数的 mipmap 数组

对于拥有所有维度均非标准磁贴大小倍数的任何 mipmap 的流式资源，数组大小不得大于 1。

### <a name="span-idswitching_between_referencing_tiles_in_a_tile_pool_via_a_buffer_and_texture_resourcespanspan-idswitching_between_referencing_tiles_in_a_tile_pool_via_a_buffer_and_texture_resourcespanspan-idswitching_between_referencing_tiles_in_a_tile_pool_via_a_buffer_and_texture_resourcespanswitching-between-referencing-tiles-in-a-tile-pool-via-a-buffer-and-texture-resource"></a><span id="Switching_between_referencing_tiles_in_a_tile_pool_via_a_Buffer_and_Texture_resource"></span><span id="switching_between_referencing_tiles_in_a_tile_pool_via_a_buffer_and_texture_resource"></span><span id="SWITCHING_BETWEEN_REFERENCING_TILES_IN_A_TILE_POOL_VIA_A_BUFFER_AND_TEXTURE_RESOURCE"></span>在通过缓冲区和纹理资源引用磁贴池中的磁贴之间切换

若要在磁贴池中[通过某个](introduction-to-buffers.md)[纹理](introduction-to-textures.md)资源引用相同磁贴的位置之间进行切换，或将定义映射到这些磁贴池磁贴的磁贴映射的最新更新或磁贴映射的最新更新必须与 \* 将用于访问磁贴的资源维度) 相同的资源 (维度。 否则行为将是未定义状态，可能出现设备重置等情况。

因此，举例来说，更新磁贴映射以定义某个缓冲区的磁贴映射，然后通过 [**Texture2D**](/windows/desktop/direct3dhlsl/sm5-object-texture2d) 资源更新指向磁贴池中相同磁贴的磁贴映射，再通过缓冲区访问磁贴，属于无效操作。 解决办法可以是在缓冲区和纹理（反之亦然）共享磁贴之间切换时重新定义某个资源的磁贴映射，或者永远不在缓冲区资源和纹理资源之间共享磁贴池中的磁贴。

### <a name="span-idmin_max_reduction_filteringspanspan-idmin_max_reduction_filteringspanspan-idmin_max_reduction_filteringspanminmax-reduction-filtering"></a><span id="Min_Max_reduction_filtering"></span><span id="min_max_reduction_filtering"></span><span id="MIN_MAX_REDUCTION_FILTERING"></span>最小值/最大值减少筛选

不支持最小值/最大值减少筛选。 请参阅[流式资源纹理采样功能](streaming-resources-texture-sampling-features.md)。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[流式资源功能层](streaming-resources-features-tiers.md)

 

 