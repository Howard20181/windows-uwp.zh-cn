---
description: 本文提供了有关在开发计算机上安装 Visual Studio 2019 项目中的扩展插件的说明，并在新项目或现有项目中使用 "项目使用"。
title: Project Reunion 入门
ms.topic: article
ms.date: 03/19/2021
keywords: windows win32, 桌面开发, project reunion
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: e3a5c891bb6bbeafa59baf933a4d8886e8430a16
ms.sourcegitcommit: 2772d98e0c8f039e7356f03fd09054dca1022c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107838689"
---
# <a name="get-started-with-project-reunion"></a>Project Reunion 入门

本文提供了有关在开发计算机上安装 Visual Studio 2019 项目中的扩展插件的说明，并在新项目或现有项目中使用 "项目使用"。 在安装和使用 "项目留尼汪岛" 之前，请参阅 [限制和已知问题](index.md#limitations-and-known-issues)。

> [!NOTE]
> 如果创建的项目使用的是早期版本的项目留尼汪岛或 WinUI 3，则可以 [更新项目以使用最新版本](update-existing-projects-to-the-latest-release.md)。

## <a name="set-up-your-development-environment"></a>设置开发环境

1. 确保开发计算机上已安装 Windows 10 版本 1809（内部版本 17763）或更高版本的 OS。

2. 如果尚未安装 [Visual Studio 2019 版本 16.10 预览版](https://visualstudio.microsoft.com/vs/preview/)（或更高版本），请先安装。

    > [!NOTE]
    > Visual Studio 2019 版本16.9 还支持项目的留尼汪岛，但并不支持所有 WinUI 3 工具功能。 有关 WinUI 3 工具支持的详细信息，请参阅 [WINDOWS UI Library 3-项目留尼汪岛 0.5](../winui/winui3/index.md)。

    安装 Visual Studio 时，必须包含以下组件：
    - 在安装对话框的“工作负载”选项卡上，确保选择了“通用 Windows 平台开发” 。
    - 在安装对话框的“单个组件”选项卡上，确保在“SDK、库和框架”部分选择了 Windows 10 SDK (10.0.19041.0)  。

    要构建 .NET 应用，还必须包含以下组件：
    - 在安装对话框的“工作负载”选项卡上，确保选择了“.NET 桌面开发” 。

    要构建 C++ 应用，还必须包含以下组件：
    - 在安装对话框的“工作负载”选项卡上，确保选择了“C++ 桌面开发” 。
    - 在安装对话框右侧的“安装详细信息”窗格中，确保在“通用 Windows 平台开发”部分选择了“C++ (v142)通用 Windows 平台工具”可选组件  。

3. 如果以前安装了 [Visual Studio 的 WinUI 3 预览版扩展](https://marketplace.visualstudio.com/items?itemName=Microsoft-WinUI.WinUIProjectTemplates)，请卸载该扩展。 有关如何卸载扩展的详细信息，请参阅[管理适用于 Visual Studio 的扩展](/visualstudio/ide/finding-and-using-visual-studio-extensions)。

4. 请确保系统中已为正式 NuGet 服务索引启用了 NuGet 包源 `https://api.nuget.org/v3/index.json` 。 

    1. 在 Visual Studio 中，选择 "**工具**" "  ->  **NuGet 包管理器**  ->  **包管理器设置**" 以打开 "**选项**" 对话框。 
    2. 在 " **选项** " 对话框的左窗格中，选择 " **包源** " 选项卡，并确保将指向的 **nuget.org** 的包源 `https://api.nuget.org/v3/index.json` 作为源 URL。 有关详细信息，请参阅 [常见 NuGet 配置](/nuget/consume-packages/configuring-nuget-behavior)。

5. 下载并安装适用于 Visual Studio 的项目留尼汪岛0.5 扩展。 扩展有两个版本：一个用于桌面 (c #/.NET 5 或 c + +/WinRT) 应用，另一个用于 UWP 应用。

    若要在 desktop 中使用 Project 留尼汪岛 (c #/.NET 5 或 c + +/WinRT) 应用：
    - 在 Visual Studio 2019 中，单击“扩展” > “管理扩展”，搜索 Project Reunion，然后安装 Project Reunion 扩展   。
    - 或者，可以直接从 Visual Studio Marketplace 下载并安装 [项目留尼汪岛0.5 扩展](https://marketplace.visualstudio.com/items?itemName=ProjectReunion.MicrosoftProjectReunion) 。

    若要在 UWP 应用中使用 Project 留，你必须安装不支持在生产环境中使用的扩展预览版本：
    - 卸载项目的任何现有版本。
    - 在 Visual Studio 2019 中，单击 "**扩展**" "  >  **管理扩展**"，然后单击左下角的 "**更改扩展设置**"。 安装旧版本之前，请关闭为所有用户安装的包的自动更新。
    - 下载并安装 [项目留尼汪岛0.5 预览扩展](https://download.microsoft.com/download/9/9/8/9981a84b-8fd8-4645-9dce-c62761601f17/ProjectReunion.Extension.vsix)。

    有关如何向 Visual Studio 添加 VSIX 包的说明，请参阅 [管理 Visual studio 的扩展](/visualstudio/ide/finding-and-using-visual-studio-extensions)。

    ![正在安装的项目留扩展的屏幕截图](images/reunion-extension-install.png)

6. 若要在 Visual Studio 2019 16.10 Preview 中使用 WinUI 3 工具（如 "实时可视化树"、"热重载" 和 "实时属性资源管理器"），必须使用 Visual Studio 预览功能启用 WinUI 3 工具。 有关说明，请参阅 [如何在 VS 16.9 预览版4中为 WinUI 3 启用 UI 工具](https://github.com/microsoft/microsoft-ui-xaml/issues/4140)。

## <a name="create-a-new-project-that-uses-project-reunion"></a>创建使用 Project Reunion 的新项目

适用于 Visual Studio 2019 (包含适用于 "桌面应用" 和 "UWP 应用的预览扩展" 的扩展的项目 "0.5 扩展") 提供使用基于 WinUI 3 的 UI 层生成项目的项目模板，并提供对所有其他项目的所有其他 Api 的访问权限。 有关可用项目模板的详细信息，请参阅 [Visual Studio 中的 WinUI 3 项目模板](..\winui\winui3\winui-project-templates-in-visual-studio.md)。

> [!NOTE]
> 支持在生产环境中使用的桌面 (c #/.NET 5 和 c + +/WinRT) 项目模板。 UWP 项目模板仅作为开发人员预览版提供，不能用于生成适用于生产环境的应用。

若要创建使用项目留尼汪岛0.5 的新项目，请执行以下操作：

1. 按照以下文章中的说明进行操作：

    - [适用于桌面应用的 WinUI 3 入门](..\winui\winui3\get-started-winui3-for-desktop.md)
    - [适用于 UWP 应用的 WinUI 3 入门（预览）](..\winui\winui3\get-started-winui3-for-uwp.md)
    - [构建基本的 WinUI 3 桌面应用](..\winui\winui3\desktop-build-basic-winui3-app.md)

2. 创建项目后，除了桌面和 UWP 应用常用的所有其他 Windows 和 .NET API 之外，你还可以使用以下 Project Reunion API 和组件。

    - [Windows UI 库 3](../winui/winui3/index.md)
    - [使用 MRT Core 管理资源](mrtcore/mrtcore-overview.md)
    - [使用 DWriteCore 呈现文本](dwritecore.md)

要确认你的新项目使用 Project Reunion，请在解决方案资源管理器中展开自己项目下的“依赖项” > “包”节点  。 你应该会看到此节点下列出了若干 Microsoft.ProjectReunion 包，如下图所示。

![解决方案资源管理器窗格中的 Project Reunion 包的屏幕截图](images/reunion-packages.png)

## <a name="use-project-reunion-in-an-existing-project"></a>在现有项目中使用 Project Reunion

如果你有想要在其中使用项目的现有项目，则可以在项目中安装项目留尼汪岛 0.5 NuGet 包。 此方案有 [一些限制](index.md#using-the-project-reunion-nuget-package-in-existing-projects)。

1. 在 Visual Studio 2019 中，打开现有桌面项目（C#/.NET 5 或 C++/WinRT）或 UWP 项目。

    > [!NOTE]
    > 如果有 c #/.NET 5 桌面项目，请确保将项目文件中的 **TargetFramework** 元素分配给特定于 windows 10 的 .net 5 名字对象，例如 **NET 5.0-Windows 10.0.19041.0**，以便它可以调用 Windows 运行时 api。 有关详细信息，请参阅[此部分](../../apps/desktop/modernize/desktop-to-uwp-enhance.md#net-5-use-the-target-framework-moniker-option)。

2. 确保已启用[包引用](/nuget/consume-packages/package-references-in-project-files)：

    1. 在 Visual Studio 中，单击“工具”->“NuGet 程序包管理器”->“程序包管理器设置”。
    2. 确保为“默认包管理格式”选择 PackageReference 。

3. 在“解决方案资源管理器”  中，右键单击你的项目并选择“管理 NuGet 包”  。

4. 在“NuGet 包管理器”窗口中，选择“浏览”选项卡，然后搜索 `Microsoft.ProjectReunion` 。

5. 找到 Microsoft.ProjectReunion 包后，在“NuGet 包管理器”窗口的右窗格中，单击“安装”  。

    ![正在安装的项目留尼汪岛 NuGet 包的屏幕截图](images/reunion-nuget-install.png)

6. **仅适用于 c #/.NET 5 项目**：为了接收来自最新稳定版本的项目留尼汪岛0.5 的所有修补程序，需要更新项目文件，以将 .net SDK 显式设置为最新版本。 有关详细信息，请参阅 [.NET SDK 参考](index.md#net-sdk-references)。

7. 安装 **ProjectReunion** 包后，可以在项目中使用以下项目占用的 api 和组件：

    - [使用 MRT Core 管理资源](mrtcore/mrtcore-overview.md)
    - [使用 DWriteCore 呈现文本](dwritecore.md)

## <a name="samples"></a>示例

目前，你可以浏览以下 Project Reunion 示例。

- [DWriteCore 库示例](https://github.com/microsoft/Project-Reunion-Samples/tree/main/DWriteCore/DWriteCoreGallery)：此示例应用程序演示了 [DWriteCore](dwritecore.md) API。
- [MRT 核心示例](https://github.com/microsoft/Project-Reunion-Samples/tree/main/MrtCore)：此示例应用程序演示了 [MRT 核心](mrtcore/mrtcore-overview.md) API。
- [Hello World 示例](https://github.com/microsoft/Project-Reunion-Samples/tree/main/HelloWorld/reunioncppdesktopsampleapp)：此示例演示了与 Project Reunion NuGet 包的基本集成。
- [Xaml 控件库](https://aka.ms/winui3/xcg)：这是一个展示所有 WinUI 3 控件的示例应用。 

## <a name="related-topics"></a>相关主题

- [使用 Project Reunion 构建桌面 Windows 应用](index.md)
- [部署使用 Project Reunion 的应用](deploy-apps-that-use-project-reunion.md)
