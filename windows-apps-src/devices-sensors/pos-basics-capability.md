---
title: PointOfService 设备功能
description: 使用 Windows.Devices.PointOfService 命名空间需要有 PointOfService 功能
ms.date: 05/02/2018
ms.topic: article
keywords: windows 10, uwp, 服务点, pos
ms.localizationpriority: medium
ms.openlocfilehash: f120e093ab65224ca4c32b64640100b6abd1a36a
ms.sourcegitcommit: 0301f794f994e604ffc131de7e40ffcede3530c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72036259"
---
# <a name="pointofservice-device-capability"></a>PointOfService 设备功能
你可以通过声明应用程序包清单中的功能请求访问 PointOfService API] 你可以使用 Microsoft Visual Studio 中的清单设计器声明大多数功能，也可以手动添加。  

> [!Important]
> 如果未在应用程序清单中声明**PointOfService**功能，则在尝试使用 PointOfService 命名空间中的 API 时，你将收到错误**system.unauthorizedaccessexception** 。 

## <a name="declare-capability-using-manifest-designer"></a>使用清单设计器声明功能

1. 在**解决方案资源管理器**中，展开你的 UWP 应用程序的项目节点。
2. 双击 **Package.appxmanifest** 文件。  
*如果清单文件已在 XML 代码视图中打开，则 Visual Studio 会提示你关闭此文件。*
3. 单击**功能**选项卡
4. 单击功能列表中**服务点**旁边的复选框以启用服务点设备功能


## <a name="declare-capability-manually"></a>手动声明功能

1. 在**解决方案资源管理器**中，展开你的 UWP 应用程序的项目节点。
2. 右键单击 **Package.appxmanifest** 文件并选择**查看代码**
3. 将 PointOfService DeviceCapability 元素添加到你的应用程序清单的“功能”部分。  

```xml
  <Capabilities>
    <DeviceCapability Name="pointOfService" />
  </Capabilities>
   ```
