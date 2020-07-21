---
title: 蓝牙广告
description: 本部分包含有关如何通过 AdvertisementWatcher 和 AdvertisementPublisher API 的用户将蓝牙低功耗 (LE) 广告集成到通用 Windows 平台 (UWP) 应用的文章。
ms.date: 06/26/2020
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ff10bbc0-03a7-492c-b5fe-c5b9ce8ca32e
ms.localizationpriority: medium
ms.openlocfilehash: 2300871292e08588b0c2124c67a379d403ae53b3
ms.sourcegitcommit: 015291bdf2e7d67076c1c85fc025f49c840ba475
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2020
ms.locfileid: "85469522"
---
# <a name="bluetooth-le-advertisements"></a>蓝牙低能耗广告


**重要的 API**

-   [**Windows.Devices.Bluetooth.Advertisement**](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement)

本文概述了适用于通用 Windows 平台 (UWP) 应用的低功耗 (LE) 蓝牙广告信标。  

> [!Important]
> 必须在*appxmanifest.xml*中声明 "蓝牙" 功能。
>
> `<Capabilities> <DeviceCapability Name="bluetooth" /> </Capabilities>`

## <a name="overview"></a>概述

开发人员可以使用 LE 广告 API 执行以下两项主要功能：

-   [广告观察程序](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementwatcher)：侦听附近信标并根据负载或邻近感应将它们过滤掉。  
-   [广告发布程序](https://docs.microsoft.com/uwp/api/windows.devices.bluetooth.advertisement.bluetoothleadvertisementpublisher)：定义负载使 Windows 能够以开发人员的名义发布广告。  

Github 上的[蓝牙广告示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BluetoothAdvertisement)中有完整的示例代码。

## <a name="basic-setup"></a>基本设置

若要在通用 Windows 平台应用中使用基本蓝牙 LE 功能，你必须检查在 Package.appxmanifest 中的蓝牙功能。

1. 打开 Package.appxmanifest
2. 转到“功能”选项卡
3. 在左侧列表中查找蓝牙并选中它旁边的框。

## <a name="publishing-advertisements"></a>发布广告

蓝牙 LE 广告允许你的设备不断以信标方式发出特定负载，称为广告。 如果任何附近支持蓝牙 LE 的设备都设置为侦听此特定广告，则这些设备可以看到此广告。

> **注意**：对于用户隐私，播发的生存期与你的应用程序的生存期相关联。 你可以创建 BluetoothLEAdvertisementPublisher，并在后台任务中为后台广告调用“开始”。 有关后台任务的详细信息，请参阅[启动、恢复和后台任务](https://docs.microsoft.com/windows/uwp/launch-resume/index)。

### <a name="basic-publishing"></a>基本发布

有多种不同的方法将数据添加到广告。 此示例显示了一种常见方法来创建特定于公司的广告。 

首先，创建广告发布者，该发布者控制设备是否以信标发出特定广告。

```csharp
BluetoothLEAdvertisementPublisher publisher = new BluetoothLEAdvertisementPublisher();
```

其次，创建自定义数据部分。 此示例使用未分配的 **CompanyId** 值 *0xFFFE* 并将文本 *Hello World* 添加到广告。 

```csharp
// Add custom data to the advertisement
var manufacturerData = new BluetoothLEManufacturerData();
manufacturerData.CompanyId = 0xFFFE;

var writer = new DataWriter();
writer.WriteString("Hello World");

// Make sure that the buffer length can fit within an advertisement payload (~20 bytes). 
// Otherwise you will get an exception.
manufacturerData.Data = writer.DetachBuffer();

// Add the manufacturer data to the advertisement publisher:
publisher.Advertisement.ManufacturerData.Add(manufacturerData);
```

现在，已创建并设置了发布者，接下来可以**开始播发**。

```csharp
publisher.Start();
```

## <a name="watching-for-advertisements"></a>监视广告

### <a name="basic-watching"></a>基本监视

以下代码演示了如何创建蓝牙 LE 广告监视程序、设置回调，并开始监视所有 LE 广告。

```csharp
BluetoothLEAdvertisementWatcher watcher = new BluetoothLEAdvertisementWatcher();
watcher.Received += OnAdvertisementReceived;
watcher.Start();
``` 

```csharp
private async void OnAdvertisementReceived(BluetoothLEAdvertisementWatcher watcher, BluetoothLEAdvertisementReceivedEventArgs eventArgs)
{
    // Do whatever you want with the advertisement
}
```

#### <a name="active-scanning"></a>活动扫描
若要同时接收扫描响应广告，请在创建监视程序后进行以下设置。 请注意，这将导致更大的能源消耗，并且在后台模式下不可用。

```csharp
watcher.ScanningMode = BluetoothLEScanningMode.Active;
```

### <a name="watching-for-a-specific-advertisement-pattern"></a>监视特定广告模式

有时，你想要侦听特定广告。 在此情况下，侦听与虚构公司之间的包含负载的广告（标识为 0xFFFE），并在广告中包含字符串 *Hello World* 。 这可以与基本发布示例配对，使一台 Windows 计算机发布广告，而另一台进行监视。 请确保在启动监视程序之前设置此广告筛选器！

```csharp
var manufacturerData = new BluetoothLEManufacturerData();
manufacturerData.CompanyId = 0xFFFE;

// Make sure that the buffer length can fit within an advertisement payload (~20 bytes). 
// Otherwise you will get an exception.
var writer = new DataWriter();
writer.WriteString("Hello World");
manufacturerData.Data = writer.DetachBuffer();

watcher.AdvertisementFilter.Advertisement.ManufacturerData.Add(manufacturerData);
```

### <a name="watching-for-a-nearby-advertisement"></a>监视附近的广告

有时，你仅希望当设备广告已经在范围内时触发监视程序。 你可以定义自己的范围，只需注意，值将被剪裁为介于 0 和 -128 之间。 

```csharp
// Set the in-range threshold to -70dBm. This means advertisements with RSSI >= -70dBm 
// will start to be considered "in-range" (callbacks will start in this range).
watcher.SignalStrengthFilter.InRangeThresholdInDBm = -70;

// Set the out-of-range threshold to -75dBm (give some buffer). Used in conjunction 
// with OutOfRangeTimeout to determine when an advertisement is no longer 
// considered "in-range".
watcher.SignalStrengthFilter.OutOfRangeThresholdInDBm = -75;

// Set the out-of-range timeout to be 2 seconds. Used in conjunction with 
// OutOfRangeThresholdInDBm to determine when an advertisement is no longer 
// considered "in-range"
watcher.SignalStrengthFilter.OutOfRangeTimeout = TimeSpan.FromMilliseconds(2000);
```

### <a name="gauging-distance"></a>评估距离

在触发蓝牙 LE 监视程序的回调时，eventArgs 包含一个 RSSI 值，告诉你接收的信号强度（蓝牙信号的强度）。

```csharp
private async void OnAdvertisementReceived(BluetoothLEAdvertisementWatcher watcher, BluetoothLEAdvertisementReceivedEventArgs eventArgs)
{
    // The received signal strength indicator (RSSI)
    Int16 rssi = eventArgs.RawSignalStrengthInDBm;
}
```

这可以大致转换为距离，但不应用来测量真正的距离，因为每个单独的无线收发器是不同的。 不同的环境因素可以导致难以测量距离（如墙壁、无线收发器的外盒，甚至空气湿度）。

判断纯距离的一个替代方法是定义“类别”。 无线电设备在非常接近时通常会报告 0 至 -50 DBm，在中等距离时报告 -50至 -90，在很远时报告低于 -90。 试用和错误最适合用来确定要用于应用的类别。