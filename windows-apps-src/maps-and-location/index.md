---
title: 地图和位置概述
description: 本部分介绍如何在应用中显示地图、使用地图服务、查找位置和设置地理围栏。 本部分还介绍如何将 Windows 地图应用启动到特定地图、路线或一组逐向路线。
ms.assetid: F4C1F094-CF46-4B15-9D80-C1A26A314521
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 地图, 位置, 地图服务
ms.localizationpriority: medium
ms.openlocfilehash: 7f39e365cd15a10f775a89cf32747b4bf0bbd87a
ms.sourcegitcommit: f727b68e86a86c94eff00f67ed79a1c12666e7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75685067"
---
# <a name="maps-and-location-overview"></a>地图和位置概述




本部分介绍如何在应用中显示地图、使用地图服务、查找位置和设置地理围栏。 本部分还介绍如何将 Windows 地图应用启动到特定地图、路线或一组逐向路线。

> [!TIP]
> 若要了解有关在应用中使用地图和位置的详细信息，请从 GitHub 上的 [Windows-universal-samples 存储库](https://github.com/Microsoft/Windows-universal-samples)中下载以下示例：
-   [通用 Windows 平台 (UWP) 地图示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)
-   [UWP 地理位置示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Geolocation)

 

## <a name="display-maps"></a>显示地图


使用 [**Windows.UI.Xaml.Controls.Maps**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps) 命名空间中的 API 在应用中以 2D、3D 或街景视图方式显示地图。 可以使用图钉、图像、图形或 XAML UI 元素在地图上标记兴趣点 (POI)。 还可以覆盖平铺图像或完全替换地图图像。

| 主题 | 说明 |
|-------|-------------|
| [请求地图身份验证密钥](authentication-key.md) | 应用必须先进行身份验证，然后才能在 [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) 命名空间中使用 [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) 和地图服务。 若要对应用进行身份验证，必须指定地图身份验证密钥。 本文介绍如何从[必应地图开发人员中心](https://www.bingmapsportal.com/)请求地图身份验证密钥并将其添加到应用。 |
| [使用 2D、3D 和街景视图显示地图](display-maps.md) | 通过使用 [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) 类，在应用中显示可自定义的地图。 本主题还介绍了鸟瞰图 3D 视图和街景视图。 |
| [在地图上显示目标点 (POI)](display-poi.md) | 使用图钉、图像、图形和 XAML UI 元素向地图添加兴趣点 (POI)。 |
| [覆盖地图上的平铺图像](overlay-tiled-images.md) | 使用磁贴源覆盖地图上的第三方或自定义平铺图像。 使用磁贴源覆盖专业信息（例如，天气数据、人口数据或地震数据），或者使用磁贴源替换整个默认地图。 |



## <a name="access-map-services"></a>访问地图服务

通过使用 [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) 命名空间中的 API 将路线、方向和地理编码功能添加到应用。

| 主题 | 说明 |
|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [请求地图身份验证密钥](authentication-key.md) | 应用必须先进行身份验证，然后才能在 [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) 命名空间中使用 [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) 和地图服务。 若要对应用进行身份验证，必须指定地图身份验证密钥。 本文介绍如何从[必应地图开发人员中心](https://www.bingmapsportal.com/)请求地图身份验证密钥并将其添加到应用。 |
| [在地图上显示目标点 (POI)](display-poi.md) | 使用图钉、图像、图形和 XAML UI 元素向地图添加兴趣点 (POI)。 |
| [显示路线和方向](routes-and-directions.md) | 请求路线和方向并在应用中显示它们。 |
| [执行地理编码和反向地理编码](geocoding.md) | 通过调用 [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) 命名空间中 [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) 类的方法将地址转换为地理位置（地理编码），以及将地理位置转换为地址（反向地理编码）。 |
| [查找并下载供离线使用的地图包](https://docs.microsoft.com/uwp/api/windows.services.maps.offlinemaps)| 在过去，你的应用必须将用户定向到“设置”应用来下载离线地图。 现在，你可以使用 [Windows.Services.Maps.OfflineMaps](https://docs.microsoft.com/uwp/api/windows.services.maps.offlinemaps) 命名空间中的类在特定区域中查找已下载的包（基于 [Geopoint](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geopoint)、[GeoboundingBox](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geoboundingbox)，等等）。 <br> 你还可以在不需要用户离开应用的情况下检查和侦听地图包的下载状态以及开始下载。 <br> 在参考内容和[通用 Windows 平台 (UWP) 地图示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)中，你都可以找到有关如何执行此操作的示例。

## <a name="get-the-users-location"></a>获取用户位置

使用 [**Windows.Devices.Geolocation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation) 命名空间中的 API 获取用户的当前位置，并在位置变化时在应用中接收通知。 这些 API 成员还经常在地图 API 的参数中使用。 [**Windows.Devices.Geolocation.Geofencing**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geofencing) 命名空间中的 API 会在用户进入或退出地理围栏（预定义的地理区域）时通知你的应用。

| 主题 | 说明 |
|-------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [请求地图身份验证密钥](authentication-key.md) | 应用必须先进行身份验证，然后才能在 [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) 命名空间中使用 [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) 和地图服务。 若要对应用进行身份验证，必须指定地图身份验证密钥。 本文介绍如何从[必应地图开发人员中心](https://www.bingmapsportal.com/)请求地图身份验证密钥并将其添加到应用。 |
| [位置感知应用设计指南](guidelines-and-checklist-for-detecting-location.md) | 需要访问用户位置的应用的性能指南。 |
| [获取用户位置](get-location.md) | 获取对用户位置的访问权限，然后检索该位置。 | 
| [有关使用访问跟踪的指南](guidelines-for-visits.md) | 了解如何使用功能强大的访问跟踪功能进行更实用的位置跟踪。 |
| [地理围栏设计指南](guidelines-for-geofencing.md) | 使用地理围栏功能的应用的性能指南。 |
| [设置地理围栏](set-up-a-geofence.md) | 在应用中设置地理围栏并了解如何处理前台和后台中的通知。 |

## <a name="launch-the-windows-maps-app"></a>启动 Windows 地图应用

应用可以启动 Windows 地图应用（如此处所示）来显示特定地图和逐向路线。 考虑使用 Windows 地图应用提供地图功能，而不是在你自己的应用中直接提供该功能。 有关详细信息，请参阅[启动 Windows 地图应用](https://docs.microsoft.com/windows/uwp/launch-resume/launch-maps-app)。

![Windows 地图应用的示例。](images/mapnyc.png)

## <a name="related-topics"></a>相关主题

* [UWP 地图示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)
* [UWP 地理位置示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Geolocation)
* [必应地图开发人员中心](https://www.bingmapsportal.com/)
* [获取当前位置](get-location.md)
* [位置感知应用设计指南](guidelines-and-checklist-for-detecting-location.md)
* [地图设计指南](controls-map.md)
* [隐私感知应用设计指南](https://docs.microsoft.com/windows/uwp/security/index)
* [Build 2015 视频：在 Windows 应用中跨手机、平板电脑和 PC 利用地图和位置](https://channel9.msdn.com/Events/Build/2015/2-757)
* [UWP 路况应用示例](https://github.com/Microsoft/Windows-appsample-trafficapp)
