---
ms.assetid: 78D833B9-E528-4BCA-9C48-A757F17E6C22
title: Windows 应用认证工具包
description: 若要为你的应用创造在 Microsoft Store 中发布的最佳机会或令其通过 Windows 认证，请在提交应用进行认证之前先在本地进行验证和测试。 本主题显示了如何安装并运行 Windows 应用认证工具包。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 应用认证
ms.localizationpriority: medium
ms.openlocfilehash: 174ff4e588d75293ecb729312883f4792196c87a
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79110103"
---
# <a name="windows-app-certification-kit"></a>Windows 应用认证工具包

若要让应用[获得 Windows 认证](/windows/win32/win_cert/windows-certification-portal)或准备将其[发布到 Microsoft Store](/windows/uwp/publish/app-submissions)，应首先在本地进行验证和测试。 本主题演示了如何安装并运行 [Windows 应用认证工具包](https://developer.microsoft.com/windows/develop/app-certification-kit)，以确保应用安全有效。

## <a name="prerequisites"></a>必备条件

测试通用 Windows 应用的先决条件：

- 必须安装并运行 Windows 10。
- 必须安装 [Windows 应用认证工具包](https://developer.microsoft.com/windows/downloads/app-certification-kit/)，该版本包含在适用于 Windows 10 的 Windows 软件开发工具包 (SDK) 中。
- 必须[启用设备进行开发](/windows/uwp/get-started/enable-your-device-for-development)。
- 必须将要测试的 Windows 应用部署到计算机。

> [!NOTE]
> **就地升级：** 安装更高版本的 [Windows 应用认证工具包](https://developer.microsoft.com/windows/develop/app-certification-kit)将替换已安装的所有早期版本的工具包。

## <a name="validate-your-windows-app-using-the-windows-app-certification-kit-interactively"></a>以交互方式使用 Windows 应用认证工具包验证 Windows 应用

1. 从“开始”  菜单中，搜索“应用”  、找到“Windows 工具包”  ，然后单击“Windows 应用认证工具包”  。

2. 从“Windows 应用认证工具包”中，选择你希望执行的验证类别。 例如：如果你要验证 Windows 应用，请选择“验证 Windows 应用”  。

    你可能会直接浏览到要测试的应用，或从 UI 的列表选择应用。 首次运行 Windows 应用认证工具包时，UI 将列出已安装在计算机上的所有 Windows 应用。 对于任何后续的运行，UI 将显示已验证的最新 Windows 应用。 如果未列出要测试的应用，则可以单击“我的应用未列出”  来获取系统上安装的所有应用的完整列表。

3. 在已输入或选定要测试的应用后，单击“下一步”  。

4. 在下一屏幕中，你将看到与正在测试的应用类型相对应的测试工作流。 如果列表中的某一测试灰显，则表示该测试不适用于你的环境。 例如，如果你正在 Windows 7 上测试 Windows 10 应用，则只有静态测试才能应用到工作流。 请注意，Microsoft Store 可能会应用此工作流程中的所有测试。 选择要运行的测试，然后单击“下一步”  。

    Windows App 认证工具包开始验证该应用。

5. 测试后，在提示符处输入要保存测试报告的文件夹的位置。

    Windows 应用认证工具包将创建一个 HTML 及一个 XML 报告并将它保存在此文件夹中。

6. 打开报告文件并查看测试结果。

> [!NOTE]
> 如果使用的是 Visual Studio，则在创建应用包时可以运行 Windows 应用认证工具包。 请参阅[打包 UWP 应用](/windows/msix/package/packaging-uwp-apps)以了解操作方法。

## <a name="validate-your-windows-app-using-the-windows-app-certification-kit-from-a-command-line"></a>从命令行使用 Windows 应用认证工具包验证 Windows 应用

> [!IMPORTANT]
> Windows 应用认证工具包必须在活动用户会话的上下文中运行。

1. 在命令窗口中，导航到包含 Windows 应用认证工具包的目录。

    **注意**   默认路径为 C:\\Program Files\\Windows 工具包\\10\\应用认证工具包\\。

2. 按此顺序输入以下命令，以测试已安装在你的测试计算机上的应用：

    `appcert.exe reset`

    `appcert.exe test -packagefullname [package full name] -reportoutputpath [report file name]`

    或者，你也可以使用以下命令（如果未安装应用）。 Windows App 认证工具包将打开相应程序包，然后应用适当的测试工作流：

    `appcert.exe reset`

    `appcert.exe test -appxpackagepath [package path] -reportoutputpath [report file name]`

3. 在测试完成后，打开名为 `[report file name]` 的报告文件并查看测试结果。

**注意**   可以从某个服务运行 Windows 应用认证工具包，但是该服务必须在活动用户会话内启动工具包过程，并且不得在 Session0 中运行。

**注意**   有关 Windows 应用认证工具包命令行的更多信息，请输入命令 `appcert.exe /?`

## <a name="testing-with-a-low-power-computer"></a>使用低能耗电脑进行测试

Windows 应用认证工具包的性能测试阈值基于低能耗电脑的性能。

执行测试的计算机的特性会影响测试结果。 为了确定你的应用性能是否符合 [Microsoft Store 策略](https://docs.microsoft.com/legal/windows/agreements/store-policies)，我们建议你在低能耗计算机上测试应用，例如基于 Intel Atom 处理器的计算机，屏幕分辨率为 1366x768（或更高），使用旋转硬盘驱动器（相对于固态硬盘驱动器）。

随着低能耗计算机的发展，其性能特征可能会随时间的推移而改变。 请参考最新的 [Microsoft Store 策略](https://docs.microsoft.com/legal/windows/agreements/store-policies)，并使用最新版本的 Windows 应用认证工具包测试你的应用，以确保你的应用符合最新性能要求。

## <a name="related-topics"></a>相关主题

- [Windows 应用认证工具包测试](windows-app-certification-kit-tests.md)
- [Microsoft Store 策略](https://docs.microsoft.com/legal/windows/agreements/store-policies)
