---
title: PointOfService 设备对象
description: 了解如何创建 PointOfService 设备对象，并了解通用 Windows 平台 (UWP) 应用程序模型中的设备对象生命周期。
ms.date: 06/19/2018
ms.topic: article
keywords: windows 10, uwp, 服务点, pos
ms.localizationpriority: medium
ms.openlocfilehash: db6b47a29e302cb962e5b91cfba823eb7f89db81
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89156601"
---
# <a name="pointofservice-device-objects"></a>PointOfService 设备对象

## <a name="creating-a-device-object"></a>创建设备对象

从更新枚举或存储的 DeviceID 确定了要使用的 PointOfService 设备后，你只需调用具有你以编程方式选择或者用户选择用来创建新服务点设备对象的 [**DeviceID**](/uwp/api/windows.devices.enumeration.deviceinformation.id) 的 [**FromIdAsync**](/uwp/api/windows.devices.pointofservice.barcodescanner.fromidasync)。

此示例尝试通过使用 DeviceID 的 FromIdAsync 创建新的 BarcodeScanner 对象。 如果创建对象时出现问题，将写入调试消息。

```Csharp

    BarcodeScanner barcodeScanner = await BarcodeScanner.FromIdAsync(DeviceId);

    if(barcodeScanner != null)
    {
        // after successful creation, claim the scanner for exclusive use and enable it to exchange data
    }
    else
    {
        Debug.WriteLine("Failure to create barcodeScanner object");
    }
    
```

有了设备对象后，你可以访问设备的方法、属性和事件。  

## <a name="device-object-lifecycle"></a>设备对象生命周期

在 Windows 8 之前，应用的生命周期非常简单。 Win32 和 .NET 应用正在运行或未运行，并且 PointOfService 外设通常是针对完整应用生命周期的要求。 当用户最小化应用或离开应用时，它们将继续运行。 随着便携设备和电源管理变得日益重要，这种情况不再可行。

Windows 8 随 UWP 应用引入了新应用程序模型。 在高级别上，添加了新的已暂停状态。 当用户最小化 UWP 应用或切换到其他应用后，该应用会立刻处于暂停状态。 这意味着应用的线程停止，应用保留在内存中，除非操作系统需要回收资源，并且表示 PointOfService 外设的任何设备对象都自动关闭以允许其他应用程序访问外设。 当用户切换回应用时，它可以快速还原到运行状态，并还原提供的 PointOfService 外设连接，它们在恢复后仍然可用。

你可以通过 \<DeviceObject\>.Closed 事件处理程序检测对象在何时因任何原因关闭，然后为在将来重新建立的连接记下设备 ID。   或者，你可能希望在应用挂起通知中处理此情况，以便保存设备 ID，以便在应用恢复通知时重新建立设备连接。  请确保不要在事件处理程序上两次处理，也不要对 \<DeviceObject\>.Closed 和应用暂停上的设备对象执行重复操作。

> [!TIP]
> 请参阅以下主题了解有关 Windows 10 通用 Windows 平台 (UWP) 应用程序生命周期的详细信息：
> - [Windows 10 通用 Windows 平台 (UWP) 应用生命周期](../launch-resume/app-lifecycle.md)
> - [处理应用暂停](../launch-resume/suspend-an-app.md)
> - [处理应用恢复](../launch-resume/resume-an-app.md)