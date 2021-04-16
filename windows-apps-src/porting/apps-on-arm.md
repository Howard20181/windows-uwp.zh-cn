---
title: 基于 ARM 的 Windows 10
description: 本文概述了 ARM 上的体验和应用运行方式，存在哪些限制，以及可以在何处了解详细信息。
ms.date: 05/22/2020
ms.topic: article
keywords: windows 10 s, 始终连接, ARM, ARM64, x86 模拟
ms.localizationpriority: medium
ms.openlocfilehash: ff763ea543e8dd6592e1f8502438a684ec38be16
ms.sourcegitcommit: 6cd970686d1ea7176b7e6651f349a14551709820
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559368"
---
# <a name="windows-10-on-arm"></a>基于 ARM 的 Windows 10
起初，Windows 10（与 Windows 10 移动版不同）只能在采用 x86 和 x64 处理器的电脑上运行。 现在，Windows 10 桌面可以在使用秋季创建者更新或更新版本的 ARM64 处理器的计算机上运行。 ARM CPU 体系结构的省电特性使这些电脑拥有全天的电池使用时间，并且支持移动数据网络。 这些电脑将提供出色的应用程序兼容性，让你在不进行任何修改的情况下运行现有的 x86 win32 应用程序。 有关详细信息或演示，请查看 "始终连接的 PC" 的第 [9 频道视频](https://channel9.msdn.com/Events/Build/2017/P4171)。

在本文中，我们使用术语 *ARM* 作为在 ARM64（通常也称为 *AArch64*）处理器上运行 Windows 10 桌面版的电脑的简写，  使用术语 *ARM32*（在其他文档中通常称为 *ARM*）作为 32 位 ARM 体系结构的简写。

## <a name="apps-and-experiences-on-arm"></a>ARM 上的应用和体验

### <a name="built-in-windows-10-experiences-apps-and-drivers"></a>内置 Windows 10 体验、应用和驱动程序
Windows 10 的内置体验（例如，Edge、Cortana、开始菜单和资源管理器）都是本机的，并作为 ARM64 运行。 这还包括所有设备驱动程序，如图形、网络或硬盘。 这可确保你获得最佳用户体验，并以 Qualcomm Snapdragon 处理器的完全本机速度运行设备中的电池寿命。

### <a name="universal-windows-platform-uwp-apps"></a>通用 Windows 平台 (UWP) 应用
ARM 上的 Windows 10 从 Microsoft Store 运行所有 x86、ARM32 和 ARM64 [UWP 应用](../get-started/universal-application-platform-guide.md) 。 ARM32 和 ARM64 应用在无需任何模拟的情况下运行，而 x86 应用在仿真下运行。 如果你是 UWP 开发人员，请确保为你的应用提交 ARM 包，因为这将为设备提供最佳的用户体验。 有关详细信息，请参阅[应用包体系结构](/windows/msix/package/device-architecture)。

>[!NOTE]
> 若要生成 UWP 应用程序以本机方式面向 ARM64 平台，你必须安装有 Visual Studio 2017 版本15.9 或更高版本或 Visual Studio 2019。 有关详细信息，请参阅[此博客文章](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development)。


>[!IMPORTANT]
> ARM 上的 Windows 10 支持从存储在 ARM64 设备上的 x86、ARM32 和 ARM64 UWP 应用。 当用户在 ARM64 设备上下载 UWP 应用时，操作系统将自动安装可用应用的最佳版本。 如果向应用商店提交应用的 x86、ARM32 和 ARM64 版本，操作系统会自动安装 ARM64 版本的应用。 如果只提交应用的 x86 和 ARM32 版本，操作系统将安装 ARM32 版本。 如果只提交 x86 版本的应用，操作系统会安装该版本并在仿真下运行它。 有关体系结构的详细信息，请参阅[应用包体系结构](/windows/msix/package/device-architecture)。

### <a name="win32-apps"></a>Win32 应用
除了 UWP 应用之外，ARM 上的 Windows 10 还可以运行未修改的 x86 Win32 应用，并具有良好的性能和无缝的用户体验，就像任何 PC 一样。 这些 x86 Win32 应用无需重新编译 ARM，甚至不能意识到它们是在 ARM 处理器上运行的。

### <a name="x86-64-apps"></a>x86-64 应用
在版本21277中添加了对 x86 64 应用程序的初始支持，当前正在进一步开发。 如果某个应用的 x64 Win32 版本不起作用，则大多数应用还提供 x86 版本。 如果选择应用程序体系结构，只需选择32位 x86 版本即可在 ARM PC 上的 Windows 10 上运行应用的32位版本。

## <a name="downloads"></a>下载

Visual Studio 2019 为 ARM 上的 Windows 10 提供了多个工具下载。 仍在使用 Visual Studio 2017 的用户可以使用安装程序来查找和安装类似的工具和包。 请注意，若要执行这些步骤，您必须使用 Visual Studio 2019。

### <a name="visual-c-redistributable"></a>Visual C++ Redistributable

适用于 ARM 应用的 Visual C++ 可再发行包。 访问 [Visual studio 下载页面](https://visualstudio.microsoft.com/downloads/) 向下滚动到 **"所有下载**"，打开 **其他工具和框架**，然后导航到 " **Microsoft Visual C++ 可再发行组件 for Visual Studio 2019** " 条目。 选择 **ARM64** 单选按钮，然后选择 " **下载**"。

### <a name="remote-tools"></a>远程工具

Visual Studio 远程工具适用于 ARM 应用。 访问 [Visual studio 下载页面](https://visualstudio.microsoft.com/downloads/) 向下滚动到 **"所有下载**"、"打开 **Visual studio 2019 的工具**"，然后导航到 **Visual Studio 远程工具 2019** 条目。 选择 "**ARM64* " 单选按钮，然后单击 " **下载**"。


## <a name="in-this-section"></a>在本节中
|主题 | 说明 |
|-----|-----|
|[x86 仿真在 ARM 上的工作原理](apps-on-arm-x86-emulation.md)|详细介绍如何在 ARM 上模拟 x86 应用。|
|[ARM 上的 x86 应用疑难解答](apps-on-arm-troubleshooting-x86.md)|在 ARM 上运行 x86 应用的常见问题以及如何解决这些问题。 |
|[ARM 上的 ARM 应用疑难解答](apps-on-arm-troubleshooting-arm32.md)|在 ARM 上运行时，ARM32 和 ARM64 应用的常见问题，以及如何解决这些问题。 |
|[ARM 上的程序兼容性疑难解答](apps-on-arm-program-compat-troubleshooter.md)|介绍当你的应用无法在 ARM 上正常工作时如何调整兼容性设置。 |

## <a name="related-topics"></a>相关主题
|主题 | 说明 |
|-----|-----|
|[使用 WDK 生成 ARM64 驱动程序](/windows-hardware/drivers/develop/building-arm64-drivers)|有关构建 ARM64 驱动程序的说明。 |
| [调试基于 ARM 的 x86 应用](/windows-hardware/drivers/debugger/debugging-arm64) | 调试基于 ARM 的 x86 应用的指南。 |
