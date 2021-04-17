---
description: 了解 Project Reunion、它为开发人员提供的好处、现在开发人员可使用的功能以及如何提供反馈。
title: 使用 Project Reunion 0.5 构建桌面 Windows 应用
ms.topic: article
ms.date: 03/19/2021
keywords: windows win32, 桌面开发, project reunion
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: 6674b18c0a042b857b14972c55e7e57f01acc03d
ms.sourcegitcommit: df14e7768acdb243190e3418db5afa5d65c5ff88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107574612"
---
# <a name="build-desktop-windows-apps-with-project-reunion-05"></a>使用 Project Reunion 0.5 构建桌面 Windows 应用

Project Reunion 是一组新的开发人员组件和工具，它们代表着 Windows 应用开发平台的下一步发展。 Project Reunion 提供了一组统一的 API 和工具，各种目标 Windows 10 OS 版本上的任何桌面应用都能够一致地使用它们。

Project Reunion 不会替代现有的桌面 Windows 应用平台和框架，例如 .NET（包括 Windows 窗体和 WPF）和 C++/Win32。 而是通过一组通用的 API 和工具（开发人员可以在这些平台中使用）来对这些现有平台进行补充。 有关更多详细信息，请参阅 [Project Reunion 的优势](#benefits-of-project-reunion-for-windows-developers)。

> [!NOTE]
> 在生产环境中，支持在 MSIX 打包桌面应用（C#/.NET 5 或 C++/Win32）中使用 Project Reunion 0.5。 可将使用 Project Reunion 0.5 的打包桌面应用发布到 Microsoft Store。 对于 UWP 应用，Project Reunion 0.5 仅作为预览版提供。 生产环境中使用的 UWP 应用不支持此版本。
>
>Project Reunion 是一个代号，在未来的版本中可能会更改。

## <a name="overview"></a>概述

Project Reunion 提供一个 Visual Studio 2019 的扩展，其中包括配置为在新项目中使用 Project Reunion 组件的项目模板。 也可以通过可在现有项目中安装的 NuGet 包来使用 Project Reunion 库。 有关详细信息，请参阅 [Project Reunion 入门](get-started-with-project-reunion.md)。

生成使用 Project Reunion 的应用后，可将其部署到其他计算机。 有关详细信息，请参阅[部署使用 Project Reunion 的应用](deploy-apps-that-use-project-reunion.md)。

Project Reunion 0.5 包含以下可在应用中使用的 API 和组件集。 可在[此处](https://github.com/microsoft/ProjectReunion/blob/master/docs/README.md)了解有关将其他组件引入 Project Reunion 的未来计划的详细信息。

| 组件 | 说明 |
|---------|-------------|
| [Windows UI 库 3](../winui/winui3/index.md) | Windows UI 库 (WinUI) 3 是适用于 Windows 应用的下一代 Windows 用户体验 (UX) 平台。 此版本包含 Visual Studio 项目模板和 NuGet 包，前者有助于你开始[使用基于 WinUI 的用户界面构建应用](..\winui\winui3\winui-project-templates-in-visual-studio.md)，后者包含 WinUI 库。  |
| [使用 MRT Core 管理资源](mrtcore/mrtcore-overview.md) | MRT Core 提供 API 来加载和管理你的应用所使用的资源。 MRT Core 是新式 [Windows 资源管理系统](/windows/uwp/app-resources/resource-management-system)的简化版本。 |
| [使用 DWriteCore 呈现文本](dwritecore.md) | 通过 DWriteCore，你可以获取用于呈现文本的所有当前 DirectWrite 功能，包括与设备无关的文本布局系统、硬件加速文本、多格式文本和广泛的语言支持。  |

## <a name="benefits-of-project-reunion-for-windows-developers"></a>面向 Windows 开发人员的 Project Reunion 优势

Project Reunion 提供了各种 Windows API，其实现与 OS 分离，并通过 NuGet 包发布给开发人员。 Project Reunion 并不打算替换 Windows SDK。 Windows SDK 将继续按原样工作，并且 Windows 的许多核心组件将通过 API 不断改进，这些 API 通过 OS 和 Windows SDK 版本发布。 建议开发人员按照自己的进度采用 Project Reunion。

### <a name="unified-api-surface-across-desktop-app-platforms"></a>在不同的桌面应用平台中实现统一的 API 图面

想要创建桌面 Windows 应用的开发人员必须在多个应用平台和框架之间进行选择。 尽管每个平台都提供了许多可供使用其他平台构建的应用使用的功能和 API，但是某些功能和 API 仅供特定平台使用。 Project Reunion 统一了所有桌面 Windows 10 应用对 Windows API 的访问。 无论选择哪种应用模型，你都可以访问 Project Reunion 中提供的同一组 Windows API。

随着时间的推移，我们计划对 Project Reunion 进行进一步的投资，以减少不同应用模型之间的差异。 Project Reunion 将同时提供 WinRT API 和本机 C API。

### <a name="consistent-support-across-windows-10-versions"></a>在多个 Windows 10 版本中实现一致的支持

随着 Windows API 随新 OS 版本的发展而不断发展，开发人员必须使用[版本自适应代码](/windows/uwp/debug-test-perf/version-adaptive-code)等技术来解决版本中的所有差异，以吸引其应用程序受众。 这可能会增加代码和开发体验的复杂性。

Project Reunion API 适用于 Windows 10 版本 1809 以及 Windows 10 的所有更高版本。 这意味着，只要你的客户使用的是 Windows 10 版本 1809 或任何更高版本，就可以在新的 Project Reunion API 和功能发布后立即使用，而无需编写版本自适应代码。

### <a name="faster-release-cadence"></a>加快发布节奏

新的 Windows API 和功能通常与每年以一次或两次的节奏发布的 OS 版本相关。 Project Reunion 将以更快的节奏发布更新，使你能够在创建 Windows 开发平台后更早、更快地访问这些创新。

## <a name="limitations-and-known-issues"></a>限制和已知问题

以下限制和已知问题对于 Project Reunion 0.5 普遍适用。

- 桌面应用（C#/.NET 5 或 C++/Win32）：不能在未打包的桌面应用（C#/.NET 5 或 C++/Win32）中使用 Project Reunion 0.5。 此版本仅支持在 MSIX 打包桌面应用中使用。
- UWP 应用：生产环境中使用的 UWP 应用不支持 Project Reunion 0.5。 若要在 UWP 应用中使用 Project Reunion，必须使用生产环境不支持的 Project Reunion 0.5 扩展的预览版本。 有关安装预览版扩展的详细信息，请参阅[设置开发环境](get-started-with-project-reunion.md#set-up-your-development-environment)。
- [WinUI 3 开发人员工具限制](..\winui\winui3\index.md#developer-tools)还适用于任何使用 Project Reunion 0.5 的项目。

以下限制和已知问题适用于特定开发人员方案。

### <a name="using-the-project-reunion-nuget-package-in-existing-projects"></a>在现有项目中使用 Project Reunion NuGet 包

如果要[在现有项目中使用 Project Reunion 0.5 NuGet 包](get-started-with-project-reunion.md#use-project-reunion-in-an-existing-project)，请注意以下限制：

- 支持将 Project Reunion 0.5 NuGet 包用于生产环境中的桌面（C#/.NET 5 和 C++/WinRT）项目。 它可用作 UWP 项目的开发人员预览版，不支持用于生产环境中的 UWP 项目。
- Project Reunion 0.5 NuGet 包（名为 Microsoft.ProjectReunion）包含其他子包（包括 Microsoft.ProjectReunion.Foundation 和 Microsoft.ProjectReunion.WinUI）这些子包包含 WinUI、MRT Core 和 DWriteCore 等组件的实现。   不能单独安装这些子包以便仅在项目中引用某些组件。 必须安装 Microsoft.ProjectReunion 包，它包含所有组件。  
- 如果将 Project Reunion 0.5 NuGet 包安装到现有项目，则只能在项目中使用属于 Project Reunion 的非 WinUI 3 组件。 要使用 WinUI 3，必须使用上一节中介绍的其中一个 WinUI 3 项目模板创建一个新项目。


#### <a name="asta-to-sta-threading-model"></a>ASTA 到 STA 线程模型

如果要将代码从现有 UWP 应用迁移到使用 Project Reunion 的新 C#/.NET 5 或 C++/Win32 WinUI 3 项目，请注意，新项目使用[单线程单元 (STA)](/windows/win32/com/single-threaded-apartments) 线程模型，而不是 UWP 应用使用的[应用程序 STA (ASTA)](https://devblogs.microsoft.com/oldnewthing/20210224-00/?p=104901) 线程模型。 如果代码采用 ASTA 线程模型的非可重入行为，则代码可能不会按预期方式运行。

## <a name="developer-roadmap"></a>开发人员路线图

有关最新的 Project Reunion 计划，请参阅我们的[路线图](https://github.com/microsoft/ProjectReunion/blob/main/docs/roadmap.md)。

## <a name="give-feedback-and-contribute"></a>提供反馈和参与

我们正在将 Project Reunion 做为开源项目进行构建。 我们在 [Github 页面](https://github.com/microsoft/ProjectReunion)上提供了许多有关我们将如何构建 Project Reunion，以及你如何参与此开发过程的详细信息。 查看我们的[参与者指南](https://github.com/microsoft/ProjectReunion/blob/master/docs/contributor-guide.md)，以提出问题、开始讨论或提出功能建议。 我们希望确保 Project Reunion 能够为像你这样的开发人员带来最大的好处。

## <a name="related-topics"></a>相关主题

- [使用 Project Reunion 构建桌面 Windows 应用](index.md)
- [Project Reunion 入门](get-started-with-project-reunion.md)
- [部署使用 Project Reunion 的应用](deploy-apps-that-use-project-reunion.md)
- [Windows UI 库 3](../winui/winui3/index.md)
- [使用 MRT Core 管理资源](mrtcore/mrtcore-overview.md)
- [使用 DWriteCore 呈现文本](dwritecore.md)
