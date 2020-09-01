---
ms.assetid: 3b75d881-bdcf-402b-a330-23cd29d68e53
description: 本文列出了与音频设备相关的 DeviceInformation 属性
title: 音频设备信息属性
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 6181625690b4abddd4fdd4cbd9032bee64b6658d
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89161201"
---
# <a name="audio-device-information-properties"></a>音频设备信息属性

本文列出了与音频设备相关的设备信息属性。 在 Windows 上，每台硬件设备均已关联了 [**DeviceInformation**](/uwp/api/Windows.Devices.Enumeration.DeviceInformation) 属性，该属性提供关于设备的详细信息，可供你在需要关于设备的特定信息时或生成设备选择器时使用。 有关在 Windows 上枚举设备的常规信息，请参阅[枚举设备](../devices-sensors/enumerate-devices.md)和[设备信息属性](../devices-sensors/device-information-properties.md)。


|名称|类型|描述|
|------------------------------------------------------------|------------|------------------------------------------------------|
|**System.Devices.AudioDevice.Microphone.SensitivityInDbfs**|Double|以相对于满刻度的分贝 (dBFS) 为单位指定麦克风敏感度。|
|**System.Devices.AudioDevice.Microphone.SignalToNoiseRatioInDb**|Double|指定以分贝 (dB) 单位测量的麦克风信号噪声比 (SNR)。|
|**System.Devices.AudioDevice.SpeechProcessingSupported**|布尔|指示音频设备是否支持语音处理。|
|**System.Devices.AudioDevice.RawProcessingSupported**|布尔|指示音频设备是否支持原始处理。|
|**System.Devices.MicrophoneArray.Geometry**|未签名的字符[]|麦克风阵列的几何结构数据。|

## <a name="related-topics"></a>相关主题

* [枚举设备](../devices-sensors/enumerate-devices.md)
* [设备信息属性](../devices-sensors/device-information-properties.md)
* [生成设备选择器](../devices-sensors/build-a-device-selector.md)
* [媒体播放](media-playback.md)