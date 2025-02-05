---
description: 本指南展示如何开始使用 WinUI 3 UI 创建 UWP 应用。
title: 适用于 UWP 应用的 WinUI 3 入门（预览）
ms.date: 03/19/2021
ms.topic: article
keywords: windows 10, uwp, winui
ms.localizationpriority: high
ms.custom: 19H1
ms.openlocfilehash: 0f6f8a17a4f3d6ca684854a64de05da87b27c44c
ms.sourcegitcommit: 7f2a09e8d5d37cb5860a5f2ece5351ea6907b94c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "105730531"
---
# <a name="get-started-with-winui-3-for-uwp-apps-preview"></a>适用于 UWP 应用的 WinUI 3 入门（预览）

利用 [WinUI 3 - Project Reunion 0.5 Preview](release-notes/winui3-project-reunion-0.5-preview.md) 中包含的项目模板，即可创建具有完全基于 WinUI 3 生成的用户界面的通用 Windows 平台 (UWP) 应用。 在你使用这些项目模板创建应用时，应用的整个用户界面都是通过 WinUI 3 提供的窗口、控件和样式实现的。 有关支持的 WinUI 3 项目模板的完整列表，请参阅 [适用于 WinUI 3 的项目模板](winui-project-templates-in-visual-studio.md#project-templates-for-winui-3)。

WinUI 3 作为 Project Reunion 包的一部分提供。 若要详细了解 Project Reunion，请参阅[使用 Project Reunion 0.5 构建桌面 Windows 应用](../../project-reunion/index.md)。

> [!NOTE] 
> WinUI 3 对生成 UWP 应用的支持目前为预览版，尚未做好生产准备。 不能在 Microsoft Store 中提供 WinUI 3 UWP 应用。

## <a name="prerequisites"></a>先决条件

若要将 WinUI 3 用于 UWP 预览版项目模板，请按照针对 Project Reunion 的[设置开发环境](../../project-reunion/get-started-with-project-reunion.md#set-up-your-development-environment)指南中的说明配置开发计算机。 

> [!NOTE]
> 不能使用 Project Reunion 0.5 VSIX 创建 WinUI 3 UWP 应用。 必须下载 [Project Reunion 0.5 **Preview** VSIX](https://aka.ms/projectreunion/previewdownload)，以获取 UWP 预览版项目模板并使用 WinUI 3 生成 UWP 应用。 

## <a name="create-a-winui-3-app-in-uwp-for-c"></a>在 UWP 中创建适用于 C# 的 WinUI 3 应用

1. 使用 Visual Studio 2019 创建一个新项目。
   - 如果 Visual Studio 已在运行，请选择“文件” -> “新建” -> “项目”  。

       :::image type="content" source="images/WinUI-and-UWP/vs2019-menu-file-new-project.png" alt-text="Visual Studio 2019 -“文件”->“新建”->“项目”菜单":::

   - 否则，启动 Visual Studio 并选择“创建新项目”。

       :::image type="content" source="images/WinUI-and-UWP/vs2019-splash-new-project.png" alt-text="Visual Studio 2019 - 创建新项目":::

2. 在“创建新项目”对话框中，从项目下拉筛选器中分别选择“C#”、“Windows”和“WinUI”   。

3. 选择“空白应用(UWP 版 WinUI) - 预览版”项目类型，然后单击“下一步” 。

    :::image type="content" source="images/WinUI-and-UWP/vs2019-create-new-project-dialog.png" alt-text="Visual Studio 2019 -“创建新项目”对话框":::

4. 输入项目名称，根据需要选择任何其他选项，然后单击“创建”。

    :::image type="content" source="images/WinUI-and-UWP/vs2019-configure-new-project-dialog.png" alt-text="“配置新项目”对话框的屏幕截图，其中突出显示了“位置”文本框和“创建”选项。":::

5. 在下面的对话框中，将“目标版本”设置为 Windows 10 版本 1903（内部版本 18362），并将“最低版本”设置为 Windows 10 版本 1809（内部版本 17763），然后单击“确定”。

    :::image type="content" source="images/WinUI-min-target-version.png" alt-text="“目标版本”和“最低版本”对话框":::

6. Visual Studio 使用以下对象生成“UWP 版 WinUI”项目：

    - 项目名称(通用 Windows)：包含应用程序代码。 这是你的项目解决方案的默认启动项目。

        :::image type="content" source="images/WinUI-and-UWP/vs2019-project.png" alt-text="“解决方案资源管理器”面板的屏幕截图，其中突出显示了通用 Windows 解决方案。":::

    - Package.appxmanifest：包含系统部署、显示或更新应用所需的信息。 有关更多详细信息，请参阅[应用包清单](/uwp/schemas/appxpackage/appx-package-manifest)

        :::image type="content" source="images/WinUI-and-UWP/vs2019-file-package-manifest.png" alt-text="Visual Studio 2019 - 应用包清单":::

    - App.xaml/App.xaml.cs：定义 `Application` 类的代码文件，该类表示你的应用实例。

        :::image type="content" source="images/WinUI-and-UWP/vs2019-file-app-xaml.png" alt-text="Visual Studio 2019 - App.xaml 文件":::

        :::image type="content" source="images/WinUI-and-UWP/vs2019-file-app-xaml-cs.png" alt-text="Visual Studio 2019 - App.xaml.cs 文件":::

    - MainPage.xaml/MainPage.xaml.cs：代表应用显示的主窗口的代码文件。 这些类派生自 WinUI 提供的 **Microsoft.UI.Xaml** 命名空间中的类型。

        :::image type="content" source="images/WinUI-and-UWP/vs2019-file-mainpage-xaml.png" alt-text="Visual Studio 2019 - MainPage.xaml 文件":::

        :::image type="content" source="images/WinUI-and-UWP/vs2019-file-mainpage-xaml-cs.png" alt-text="Visual Studio 2019 - MainPage.xaml.cs 文件":::

7. 若要将新项添加到应用项目，请在“解决方案资源管理器”中右键单击“项目名称(通用 Windows)”项目节点，然后选择“添加” -> “新项”   。 在“添加新项”对话框中，选择“WinUI”选项卡，选择要添加的项，然后单击“添加”。 有关可用项的更多详细信息，请参阅[适用于 WinUI 3 的项模板](winui-project-templates-in-visual-studio.md#item-templates-for-winui-3)。

    :::image type="content" source="images/WinUI-and-UWP/vs2019-add-new-item-dialog.png" alt-text="Visual Studio 2019 - “添加新项”对话框":::

8. 生成、部署和启动应用以查看其外观。

    1. 你可以在本地计算机上、模拟器或仿真器中或者在远程设备上调试应用。 从下拉菜单中选择目标设备。

        :::image type="content" source="images/WinUI-and-UWP/vs2019-menu-target-device.png" alt-text="“本地计算机”下拉列表的屏幕截图。":::

    1. 按 F5，单击“生成”按钮或选择“调试”->“启动调试”以生成并运行解决方案，并确认该应用运行无误 。

        :::image type="content" source="images/WinUI-and-UWP/vs2019-project-running.png" alt-text="正在运行的应用的屏幕截图，其中显示了“单击我”按钮。":::

## <a name="known-issues-and-limitations"></a>已知问题和限制

请参阅 [Windows UI 库 3 - Project Reunion 0.5 Preview](release-notes/winui3-project-reunion-0.5-preview.md) 的[限制和已知问题](index.md#limitations-and-known-issues)部分。

## <a name="related-topics"></a>相关主题

- [Windows UI 库 3 - Project Reunion 0.5 预览版](release-notes/winui3-project-reunion-0.5-preview.md)
- [创建你的第一个应用](/windows/uwp/get-started/your-first-app)
