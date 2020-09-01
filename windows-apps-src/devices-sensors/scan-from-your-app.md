---
ms.assetid: 374D1983-60E0-4E18-ABBB-04775BAA0F0D
title: 从应用扫描
description: 在此处了解如何通过使用平板扫描仪、送纸器或自动配置的扫描源从你的应用扫描内容。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c306c225d200fe0636b3195699afe0441bc252bf
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89175431"
---
# <a name="scan-from-your-app"></a>从应用扫描


**重要的 API**

-   [**Windows.Devices.Scanners**](/uwp/api/Windows.Devices.Scanners)
-   [**DeviceInformation**](/uwp/api/Windows.Devices.Enumeration.DeviceInformation)
-   [**DeviceClass**](/uwp/api/Windows.Devices.Enumeration.DeviceClass)

在此处了解如何通过使用平板扫描仪、送纸器或自动配置的扫描源从你的应用扫描内容。

**重要提示**   [**Windows 设备扫描器**](/uwp/api/Windows.Devices.Scanners)api 是桌面[设备系列](../get-started/universal-application-platform-guide.md)的一部分。 应用仅可以在桌面版的 Windows 10 上使用这些 API。

若要从你的应用进行扫描，你必须首先声明一个新的 [**DeviceInformation**](/uwp/api/Windows.Devices.Enumeration.DeviceInformation) 对象并获取 [**DeviceClass**](/uwp/api/Windows.Devices.Enumeration.DeviceClass) 类型，以此来列出可用的扫描仪。 仅列出并向应用提供带有 WIA 驱动程序的本地安装的扫描仪。

在应用列出可用的扫描仪后，它可以使用基于扫描仪类型的自动配置的扫描设置，或使用可用的平板或送纸器扫描源进行扫描。 要使用自动配置的设置，扫描仪必须启用自动配置，并且不可同时配备平板和送纸器扫描仪。 有关详细信息，请参阅[自动配置的扫描](/windows-hardware/drivers/image/auto-configured-scanning)。

## <a name="enumerate-available-scanners"></a>枚举可用扫描仪

Windows 不会自动检测扫描仪。 你必须执行此步骤以使应用与该扫描仪进行通信。 在本示例中，使用 [**Windows.Devices.Enumeration**](/uwp/api/Windows.Devices.Enumeration) 命名空间进行扫描仪设备枚举。

1.  首先，将这些 using 语句添加到你的类定义文件。

``` csharp
    using Windows.Devices.Enumeration;
    using Windows.Devices.Scanners;
```

2.  接着，实现一个设备查看器以开始枚举扫描仪。 有关详细信息，请参阅[枚举类](enumerate-devices.md)。

```csharp
    void InitDeviceWatcher()
    {
       // Create a Device Watcher class for type Image Scanner for enumerating scanners
       scannerWatcher = DeviceInformation.CreateWatcher(DeviceClass.ImageScanner);

       scannerWatcher.Added += OnScannerAdded;
       scannerWatcher.Removed += OnScannerRemoved;
       scannerWatcher.EnumerationCompleted += OnScannerEnumerationComplete;
    }
```

3.  为添加扫描仪创建事件处理程序。

```csharp
    private async void OnScannerAdded(DeviceWatcher sender,  DeviceInformation deviceInfo)
    {
       await
       MainPage.Current.Dispatcher.RunAsync(
             Windows.UI.Core.CoreDispatcherPriority.Normal,
             () =>
             {
                MainPage.Current.NotifyUser(String.Format("Scanner with device id {0} has been added", deviceInfo.Id), NotifyType.StatusMessage);

                // search the device list for a device with a matching device id
                ScannerDataItem match = FindInList(deviceInfo.Id);

                // If we found a match then mark it as verified and return
                if (match != null)
                {
                   match.Matched = true;
                   return;
                }

                // Add the new element to the end of the list of devices
                AppendToList(deviceInfo);
             }
       );
    }
```

## <a name="scan"></a>扫描

1.  **获取 ImageScanner 对象**

对于每个 [**ImageScannerScanSource**](/uwp/api/Windows.Devices.Scanners.ImageScannerScanSource) 枚举类型，无论它是 **Default**、**AutoConfigured**、**Flatbed** 还是 **Feeder**，必须首先通过调用 [**ImageScanner.FromIdAsync**](/uwp/api/windows.devices.scanners.imagescanner.fromidasync) 方法来创建 [**ImageScanner**](/uwp/api/Windows.Devices.Scanners.ImageScanner) 对象，如下所示。

 ```csharp
    ImageScanner myScanner = await ImageScanner.FromIdAsync(deviceId);
 ```

2.  **仅扫描**

要以默认设置进行扫描，你的应用将依靠 [**Windows.Devices.Scanners**](/uwp/api/Windows.Devices.Scanners) 命名空间选择一个扫描仪并从该来源进行扫描。 未更改扫描设置。 可能的扫描仪为自动配置、平板或送纸器。 此类型的扫描最有可能产生成功的扫描操作，即使它从错误的来源进行扫描，如从平板扫描仪而不是从送纸器。

**注意**   如果用户将文档放入送纸器，扫描程序将改为从平板扫描。 如果用户尝试从空的送纸器进行扫描，扫描作业将不会产生任何扫描后的文件。
 
```csharp
    var result = await myScanner.ScanFilesToFolderAsync(ImageScannerScanSource.Default,
        folder).AsTask(cancellationToken.Token, progress);
```

3.  **从自动配置、平板或送纸器来源进行扫描**

应用可以使用设备的[自动配置的扫描](/windows-hardware/drivers/image/auto-configured-scanning)来以最佳的扫描设置进行扫描。 使用此选项，设备本身可以根据要扫描的内容确定最佳扫描设置，例如颜色模式和扫描分辨率。 设备在运行时为每个新的扫描作业选择扫描设置。

**注意**   并非所有扫描仪都支持此功能，因此，在使用此设置之前，应用必须检查扫描程序是否支持此功能。

在本示例中，应用首先检查扫描仪是否可以进行自动配置，然后进行扫描。 要指定平板扫描仪或送纸器扫描仪，只需使用 **Flatbed** 或 **Feeder** 替换 **AutoConfigured**。

```csharp
    if (myScanner.IsScanSourceSupported(ImageScannerScanSource.AutoConfigured))
    {
        ...
        // Scan API call to start scanning with Auto-Configured settings.
        var result = await myScanner.ScanFilesToFolderAsync(
            ImageScannerScanSource.AutoConfigured, folder).AsTask(cancellationToken.Token, progress);
        ...
    }
```

## <a name="preview-the-scan"></a>预览扫描

你可以在扫描到文件夹之前添加代码以预览该扫描。 在以下示例中，应用检查 **Flatbed** 扫描仪是否支持预览，然后预览该扫描。

```csharp
if (myScanner.IsPreviewSupported(ImageScannerScanSource.Flatbed))
{
    rootPage.NotifyUser("Scanning", NotifyType.StatusMessage);
                // Scan API call to get preview from the flatbed.
                var result = await myScanner.ScanPreviewToStreamAsync(
                    ImageScannerScanSource.Flatbed, stream);
```

## <a name="cancel-the-scan"></a>取消扫描

你可以让用户在扫描中途取消扫描作业，如下所示。

```csharp
void CancelScanning()
{
    if (ModelDataContext.ScenarioRunning)
    {
        if (cancellationToken != null)
        {
            cancellationToken.Cancel();
        }                
        DisplayImage.Source = null;
        ModelDataContext.ScenarioRunning = false;
        ModelDataContext.ClearFileList();
    }
}
```

## <a name="scan-with-progress"></a>显示进度的扫描

1.  创建 **System.Threading.CancellationTokenSource** 对象。

```csharp
cancellationToken = new CancellationTokenSource();
```

2.  设置进度事件处理程序并获取扫描的进度。

```csharp
    rootPage.NotifyUser("Scanning", NotifyType.StatusMessage);
    var progress = new Progress<UInt32>(ScanProgress);
```

## <a name="scanning-to-the-pictures-library"></a>扫描到图片库

用户可以使用 [**FolderPicker**](/uwp/api/Windows.Storage.Pickers.FolderPicker) 类动态扫描到任何文件夹，但是你必须在清单中声明*图片库*功能以允许用户扫描到该文件夹。 有关应用功能的详细信息，请参阅[应用功能声明](../packaging/app-capability-declarations.md)。