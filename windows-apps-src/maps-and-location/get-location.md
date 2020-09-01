---
title: 获取用户位置
description: 查找用户的位置并响应位置更改。 对用户位置的访问由“设置”应用中的隐私设置来管理。 本主题还介绍了如何查看你的应用是否具有访问用户位置的权限。
ms.assetid: 24DC9A41-8CC1-48B0-BC6D-24BF571AFCC8
ms.date: 11/28/2017
ms.topic: article
keywords: windows 10, uwp, 地图, 位置, 位置功能
ms.localizationpriority: medium
ms.openlocfilehash: 79c34af48cf1b2d860d2a170fd642ef05945c15d
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89158711"
---
# <a name="get-the-users-location"></a>获取用户位置




查找用户的位置并响应位置更改。 对用户位置的访问由“设置”应用中的隐私设置来管理。 本主题还介绍了如何查看你的应用是否具有访问用户位置的权限。

**提示**若要了解有关在你的应用中访问用户位置的详细信息，请从 GitHub 上的 [Windows-universal-samples 存储库](https://github.com/Microsoft/Windows-universal-samples)下载以下示例。

-   [通用 Windows 平台 (UWP) 地图示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)

## <a name="enable-the-location-capability"></a>启用位置功能


1.  在**解决方案资源管理器**中，双击 **package.appxmanifest** 并选择**功能**选项卡。
2.  在**功能**列表中，选中**位置**框。 这将向程序包清单文件中添加 `location` 设备功能。

```XML
  <Capabilities>
    <!-- DeviceCapability elements must follow Capability elements (if present) -->
    <DeviceCapability Name="location"/>
  </Capabilities>
```

## <a name="get-the-current-location"></a>获取当前位置


此部分介绍如何使用 [**Windows.Devices.Geolocation**](/uwp/api/Windows.Devices.Geolocation) 命名空间中的 API 检测用户的地理位置。

### <a name="step-1-request-access-to-the-users-location"></a>步骤 1：请求访问用户的位置

除非你的应用具有粗位置功能 (请参阅注意) ，在尝试访问该位置之前，必须使用 [**RequestAccessAsync**](/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) 方法请求访问用户的位置。 必须从 UI 线程调用 **RequestAccessAsync** 方法，并且你的应用必须在前台。 只有在用户授予相应的应用权限后，你的应用才可以访问用户的位置信息。\*

```csharp
using Windows.Devices.Geolocation;
...
var accessStatus = await Geolocator.RequestAccessAsync();
```



[**RequestAccessAsync**](/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) 方法提示用户提供访问其位置的权限。 仅提示用户一次（每个应用）。 在他们第一次授予或拒绝授予权限之后，此方法不会再提示用户提供权限。 若要在提示之后帮助用户更改位置权限，我们建议提供位置设置的链接，如本主题中后面部分所示。

>注意：粗糙位置功能允许你的应用程序获取有意模糊处理 (不精确的) 位置，而无需获取用户的显式权限 (系统范围内的位置交换机仍必须处于 **打开**，但) 。 若要了解如何在应用中使用粗糙位置，请参阅[**定位**](/uwp/api/windows.devices.geolocation.geolocator)类中的[**AllowFallbackToConsentlessPositions**](/uwp/api/windows.devices.geolocation.geolocator.allowfallbacktoconsentlesspositions)方法。

### <a name="step-2-get-the-users-location-and-register-for-changes-in-location-permissions"></a>步骤 2：获取用户的位置并注册位置权限的更改

[**GetGeopositionAsync**](/uwp/api/windows.devices.geolocation.geolocator.getgeopositionasync) 方法执行当前位置的一次读取。 在此处，**switch** 语句将与 **accessStatus**（来自上一示例）结合使用以便仅在允许访问用户位置时进行操作。 如果允许访问用户位置，该代码将创建 [**Geolocator**](/uwp/api/Windows.Devices.Geolocation.Geolocator) 对象、注册位置权限的更改，并请求用户位置。

```csharp
switch (accessStatus)
{
    case GeolocationAccessStatus.Allowed:
        _rootPage.NotifyUser("Waiting for update...", NotifyType.StatusMessage);

        // If DesiredAccuracy or DesiredAccuracyInMeters are not set (or value is 0), DesiredAccuracy.Default is used.
        Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = _desireAccuracyInMetersValue };

        // Subscribe to the StatusChanged event to get updates of location status changes.
        _geolocator.StatusChanged += OnStatusChanged;

        // Carry out the operation.
        Geoposition pos = await geolocator.GetGeopositionAsync();

        UpdateLocationData(pos);
        _rootPage.NotifyUser("Location updated.", NotifyType.StatusMessage);
        break;

    case GeolocationAccessStatus.Denied:
        _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);
        LocationDisabledMessage.Visibility = Visibility.Visible;
        UpdateLocationData(null);
        break;

    case GeolocationAccessStatus.Unspecified:
        _rootPage.NotifyUser("Unspecified error.", NotifyType.ErrorMessage);
        UpdateLocationData(null);
        break;
}
```

### <a name="step-3-handle-changes-in-location-permissions"></a>步骤 3：处理位置权限的更改

[**Geolocator**](/uwp/api/Windows.Devices.Geolocation.Geolocator) 对象触发 [**StatusChanged**](/uwp/api/windows.devices.geolocation.geolocator.statuschanged) 事件以指示用户位置设置已更改。 该事件通过参数的 **Status** 属性（类型为 [**PositionStatus**](/uwp/api/Windows.Devices.Geolocation.PositionStatus)）传递相应的状态。 请注意，此方法不是从 UI 线程中调用的，并且 [**Dispatcher**](/uwp/api/Windows.UI.Core.CoreDispatcher) 对象调用了 UI 更改。

```csharp
using Windows.UI.Core;
...
async private void OnStatusChanged(Geolocator sender, StatusChangedEventArgs e)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        // Show the location setting message only if status is disabled.
        LocationDisabledMessage.Visibility = Visibility.Collapsed;

        switch (e.Status)
        {
            case PositionStatus.Ready:
                // Location platform is providing valid data.
                ScenarioOutput_Status.Text = "Ready";
                _rootPage.NotifyUser("Location platform is ready.", NotifyType.StatusMessage);
                break;

            case PositionStatus.Initializing:
                // Location platform is attempting to acquire a fix.
                ScenarioOutput_Status.Text = "Initializing";
                _rootPage.NotifyUser("Location platform is attempting to obtain a position.", NotifyType.StatusMessage);
                break;

            case PositionStatus.NoData:
                // Location platform could not obtain location data.
                ScenarioOutput_Status.Text = "No data";
                _rootPage.NotifyUser("Not able to determine the location.", NotifyType.ErrorMessage);
                break;

            case PositionStatus.Disabled:
                // The permission to access location data is denied by the user or other policies.
                ScenarioOutput_Status.Text = "Disabled";
                _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);

                // Show message to the user to go to location settings.
                LocationDisabledMessage.Visibility = Visibility.Visible;

                // Clear any cached location data.
                UpdateLocationData(null);
                break;

            case PositionStatus.NotInitialized:
                // The location platform is not initialized. This indicates that the application
                // has not made a request for location data.
                ScenarioOutput_Status.Text = "Not initialized";
                _rootPage.NotifyUser("No request for location is made yet.", NotifyType.StatusMessage);
                break;

            case PositionStatus.NotAvailable:
                // The location platform is not available on this version of the OS.
                ScenarioOutput_Status.Text = "Not available";
                _rootPage.NotifyUser("Location is not available on this version of the OS.", NotifyType.ErrorMessage);
                break;

            default:
                ScenarioOutput_Status.Text = "Unknown";
                _rootPage.NotifyUser(string.Empty, NotifyType.StatusMessage);
                break;
        }
    });
}
```

## <a name="respond-to-location-updates"></a>响应位置更新


本部分介绍如何使用 [**PositionChanged**](/uwp/api/windows.devices.geolocation.geolocator.positionchanged) 事件来接收一段时间内的用户的位置更新。 由于用户随时可能会取消对位置的访问，请务必调用 [**RequestAccessAsync**](/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) 和使用 [**StatusChanged**](/uwp/api/windows.devices.geolocation.geolocator.statuschanged) 事件，如上一部分中所示。

本部分假定你已启用位置功能并且已从前台应用的 UI 线程调用 [**RequestAccessAsync**](/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync)。

### <a name="step-1-define-the-report-interval-and-register-for-location-updates"></a>步骤 1：定义报告间隔和注册位置更新

在本示例中，将一个 **switch** 语句与 **accessStatus**（来自上一示例）一起使用，以便仅在允许访问用户位置时进行操作。 如果允许访问用户位置，该代码将创建 [**Geolocator**](/uwp/api/Windows.Devices.Geolocation.Geolocator) 对象、指定跟踪类型并注册位置更新。

[**Geolocator**](/uwp/api/Windows.Devices.Geolocation.Geolocator) 对象可以基于位置更改（基于距离的跟踪）或时间更改（基于定期的跟踪）触发 [**PositionChanged**](/uwp/api/windows.devices.geolocation.geolocator.positionchanged) 事件。

-   对于基于距离的跟踪，设置 [**MovementThreshold**](/uwp/api/windows.devices.geolocation.geolocator.movementthreshold) 属性。
-   对于基于周期的跟踪，设置 [**ReportInterval**](/uwp/api/windows.devices.geolocation.geolocator.reportinterval) 属性。

如果两个属性都未设置，则每隔 1 秒（相当于 `ReportInterval = 1000`）返回一个位置。 此处，使用 2 秒 (`ReportInterval = 2000`) 报告间隔。
```csharp
using Windows.Devices.Geolocation;
...
var accessStatus = await Geolocator.RequestAccessAsync();

switch (accessStatus)
{
    case GeolocationAccessStatus.Allowed:
        // Create Geolocator and define perodic-based tracking (2 second interval).
        _geolocator = new Geolocator { ReportInterval = 2000 };

        // Subscribe to the PositionChanged event to get location updates.
        _geolocator.PositionChanged += OnPositionChanged;

        // Subscribe to StatusChanged event to get updates of location status changes.
        _geolocator.StatusChanged += OnStatusChanged;

        _rootPage.NotifyUser("Waiting for update...", NotifyType.StatusMessage);
        LocationDisabledMessage.Visibility = Visibility.Collapsed;
        StartTrackingButton.IsEnabled = false;
        StopTrackingButton.IsEnabled = true;
        break;

    case GeolocationAccessStatus.Denied:
        _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);
        LocationDisabledMessage.Visibility = Visibility.Visible;
        break;

    case GeolocationAccessStatus.Unspecified:
        _rootPage.NotifyUser("Unspecificed error!", NotifyType.ErrorMessage);
        LocationDisabledMessage.Visibility = Visibility.Collapsed;
        break;
}
```

### <a name="step-2-handle-location-updates"></a>步骤 2：处理位置更新

[**Geolocator**](/uwp/api/Windows.Devices.Geolocation.Geolocator) 对象通过触发 [**PositionChanged**](/uwp/api/windows.devices.geolocation.geolocator.positionchanged) 事件以指示用户的位置更改或时间已过，具体取决于配置它的方式。 该事件通过参数的 **Position** 属性（属于类型 [**Geoposition**](/uwp/api/Windows.Devices.Geolocation.Geoposition)）来传递相应的位置信息。 在此示例中，该方法不是从 UI 线程中调用的，并且 [**Dispatcher**](/uwp/api/Windows.UI.Core.CoreDispatcher) 对象调用了 UI 更改。

```csharp
using Windows.UI.Core;
...
async private void OnPositionChanged(Geolocator sender, PositionChangedEventArgs e)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        _rootPage.NotifyUser("Location updated.", NotifyType.StatusMessage);
        UpdateLocationData(e.Position);
    });
}
```

## <a name="change-the-location-privacy-settings"></a>更改位置隐私设置


如果位置隐私设置不允许你的应用访问用户位置，我们建议提供一个指向 **“设置”** 应用中的 **“位置隐私设置”** 的便捷链接。 在此示例中，“超链接”控件用于导航到 `ms-settings:privacy-location` URI。

```xml
<!--Set Visibility to Visible when access to location is denied -->  
<TextBlock x:Name="LocationDisabledMessage" FontStyle="Italic"
                 Visibility="Collapsed" Margin="0,15,0,0" TextWrapping="Wrap" >
          <Run Text="This app is not able to access Location. Go to " />
              <Hyperlink NavigateUri="ms-settings:privacy-location">
                  <Run Text="Settings" />
              </Hyperlink>
          <Run Text=" to check the location privacy settings."/>
</TextBlock>
```

此外，你的应用可以调用 [**LaunchUriAsync**](/uwp/api/windows.system.launcher.launchuriasync) 方法，以从代码启动 **“设置”** 应用。 有关详细信息，请参阅[启动 Windows 设置应用](../launch-resume/launch-settings-app.md)。

```csharp
using Windows.System;
...
bool result = await Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-location"));
```

## <a name="troubleshoot-your-app"></a>对应用进行故障排除


在你的应用可以访问用户位置之前，必须在设备上启用 **“位置”**。 在**设置**应用中，检查以下**位置隐私设置**是否已打开：

-   **“此设备的位置...”** 已 **“打开”**（在 Windows 10 移动版中不适用）
-   位置服务设置（**位置**）已**打开**
-   在 **“选择可以使用你的位置的应用”** 下，你的应用已设置为 **“打开”**

## <a name="related-topics"></a>相关主题

* [UWP 地理位置示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Geolocation)
* [地理围栏设计指南](./guidelines-for-geofencing.md)
* [位置感知应用设计指南](./guidelines-and-checklist-for-detecting-location.md)