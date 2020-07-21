---
title: 版本自适应应用
description: 了解如何在保持与以前版本的兼容性的同时利用新 API
ms.date: 05/17/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b947d0b6cc83dc6bca45efb7103a933e79972e3b
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "67317453"
---
# <a name="version-adaptive-apps-use-new-apis-while-maintaining-compatibility-with-previous-versions"></a>版本自适应应用：在保持与以前版本的兼容性的同时使用新 API

每个 Windows 10 SDK 版本都添加你会想要利用的精彩的新功能。 然而，并非所有客户都会同时将其设备更新为最新版本的 Windows 10，因此你希望确保你的应用所适用的设备范围能够尽可能的广泛。 我们在此处显示如何设计应用，以便它不仅可以在较早版本的 Windows 10 上运行，还可以在应用运行在装有最新更新的设备上运行时利用新功能。

若要确保应用支持最广泛的 Windows 10 设备，需采取 3 个步骤。

- 首先，配置要面向最新 API 的 Visual Studio 项目。 这将影响编译应用时发生的情况。
- 其次，执行运行时检查，以确保仅调用正在运行应用的设备上存在的 API。
- 第三，在 Windows 10 的最低版本和目标版本上测试应用。

## <a name="configure-your-visual-studio-project"></a>配置 Visual Studio 项目

支持多个 Windows 10 版本的第一步是在 Visual Studio 项目中指定*目标*和*最低*支持的操作系统/SDK 版本。

- *目标*：Visual Studio 编译应用代码和运行所有工具所面向的 SDK 版本。 编译时，此 SDK 版本中的所有 API 和资源均可在你的应用代码中使用。
- *最低要求*：支持可在其上运行应用的最早操作系统版本的 SDK 版本（并且将通过应用商店部署到它），以及 Visual Studio 编译应用标记代码时所面向的版本。 

在运行时，你的应用将针对其部署到的操作系统版本运行，因此如果使用资源或调用 API（它们在该版本中并未提供），应用将引发异常。 我们将在本文的后面部分介绍如何使用运行时检查来调用正确的 API。

“目标”和“最低”设置指定操作系统/SDK 版本范围的限度。 但是，如果你在最低版本上测试你的应用，则能够确定它将在“最低”和“目标”之间的任意版本上运行。

> [!TIP]
> Visual Studio 不会向你发出有关 API 兼容性的警告。 由你负责测试并确保你的应用在“最低”和“目标”（包含这两者）之间的所有操作系统版本上按预期方式执行。

当你在 Visual Studio 2015 Update 2 或更高版本中创建新项目时，系统将提示你设置应用支持的目标版本和最低版本。 默认情况下，目标版本是安装的最高 SDK 版本，最低版本是安装的最低 SDK 版本。 你只能从安装在你的计算机上的 SDK 版本中选择“目标”和“最低”。 

![在 Visual Studio 中设置目标 SDK](images/vs-target-sdk-1.png)

我们通常建议你保留默认值。 但是，如果你已安装了 SDK 的预览版本，并且要编写生产代码，则应将目标版本从预览版 SDK 更改为最新的官方 SDK 版本。 

若要更改已在 Visual Studio 中创建的项目的最低版本和目标版本，请转到“项目”-&gt;“属性”-&gt;“应用程序”选项卡 -&gt;“目标”。

![在 Visual Studio 中更改目标 SDK](images/vs-target-sdk-2.png)

为便于参考，下表显示了每个 SDK 的内部版本号。

| 友好名称 | 版本 | OS/SDK 内部版本 |
| ---- | ---- | ---- |
| RTM | 1507 | 10240 |
| 十一月更新 | 1511 | 10586 |
| 周年更新 | 1607 | 14393 |
| Creators 更新 | 1703 | 15063 |
| Fall Creators Update | 1709 | 16299 |
| 2018 年 4 月更新 | 1803 | 17134 |
| 2018 年 10 月更新 | 1809 | 17763 |
| 2019 年 5 月更新 | 1903 | 18362 |

可以从 [Windows SDK 和仿真器存档](https://developer.microsoft.com/windows/downloads/sdk-archive)下载任何发布的 SDK 版本。 可以从 [Windows 预览体验成员](https://insider.windows.com/for-developers/)站点的开发人员部分下载最新的 Windows Insider Preview SDK。

 有关 Windows 10 更新的详细信息，请参阅 [Windows 10 版本信息](https://www.microsoft.com/itpro/windows-10/release-information)。 有关 Windows 10 支持生命周期的重要信息，请参阅 [Windows 生命周期情况说明书](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)。

## <a name="perform-api-checks"></a>执行 API 检查

版本自适应应用的关键是 API 协定与 [ApiInformation](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation) 类结合。 此类可用于检测指定的 API 协定、类型或成员是否存在，以便你可以跨不同的设备和 OS 版本安全地进行 API 调用。

### <a name="api-contracts"></a>API 协定

设备系列中的 API 集将细分为 API 协定。 你可以使用 **ApiInformation.IsApiContractPresent** 方法来测试是否存在 API 合约。 若要测试大量 API（它们均位于同一版 API 协定中）的存在，这会很有用。

```csharp
    bool isScannerDeviceContract_1_Present =
        Windows.Foundation.Metadata.ApiInformation.IsApiContractPresent
            ("Windows.Devices.Scanners.ScannerDeviceContract", 1);
```

什么是 API 协定？ 本质上，API 协定表示功能 - 一组一起交付某些特定功能的相关 API。 假设的 API 协定可能表示包含两个类、五个接口、一个结构、两个枚举等的一组 API。

逻辑相关的类型可组合成 API 协定，并且从 Windows 10 起，每个 Windows 运行时 API 都是某个 API 协定的成员。 可以通过 API 协定检查特定功能或 API 在设备上的可用性，有效地检查设备的功能，而不是检查特定的设备或 OS。 实现 API 协定中的任何 API 的平台必须实现该 API 协定中的每一个 API。 这意味着你可以测试正在运行的 OS 是否支持特定的 API 协定。如果支持，则调用该 API 协定中的任何一个 API，无需单独对每一个进行检查。

最大和最常用的 API 协定是 **Windows.Foundation.UniversalApiContract**。 它包含通用 Windows 平台中的大部分 API。 [设备系列扩展 SDK 和 API 协定](https://docs.microsoft.com/uwp/extension-sdks/)文档介绍了各种可用的 API 协定。 你会看到其中大部分表示一组功能相关的 API。

> [!NOTE]
> 如果安装了尚未在文档中介绍的预览 Windows 软件开发工具包 (SDK)，也可在“Platform.xml”文件中找到关于 API 协定的信息，该文件位于 SDK 安装文件夹“\(Program Files (x86))\Windows Kits\10\Platforms\<platform>\<SDK version>\Platform.xml”。

### <a name="version-adaptive-code-and-conditional-xaml"></a>版本自适应代码和条件 XAML

在所有版本的 Windows 10 中，可以在代码中的条件中使用 ApiInformation 类，测试是否存在要调用的 API。 在自适应代码中，可以使用类的不同方式（如 IsTypePresent、IsEventPresent、IsMethodPresent 和 IsPropertyPresent）在需要的级别测试 API。

有关详细信息和示例，请参阅 **[版本自适应代码](version-adaptive-code.md)** 。

如果应用最低版本是版本 15063 (Creators Update) 或更高版本，则可使用条件 XAML  在标记中设置属性和实例化对象，无需使用代码隐藏。 条件 XAML 提供在标记中使用 ApiInformation.IsApiContractPresent 方法的一种途径。

有关详细信息和示例，请参阅 **[条件 XAML](conditional-xaml.md)** 。

## <a name="test-your-version-adaptive-app"></a>测试版本自适应应用

使用版本自适应代码或条件 XAML 编写版本自适应应用时，需要分别在运行 Windows 10 最低版本和目标版本的设备上对其进行测试。

不能在一个设备上测试所有条件代码路径。 若要确保测试所有代码路径，需要在运行受支持的最低 OS 版本的远程设备（或虚拟机）上部署和测试应用。
有关远程调试的详细信息，请参阅[部署和调试 UWP 应用](deploying-and-debugging-uwp-apps.md)。

## <a name="related-articles"></a>相关文章

- [UWP 应用是什么](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide)
- [使用 API 合约动态检测功能](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/)
- [API 协定](https://channel9.msdn.com/Events/Build/2015/3-733)（Build 2015 视频）
