---
ms.assetid: 9322B3A3-8F06-4329-AFCB-BE0C260C332C
description: 本文指导你完成针对各种部署和调试目标的步骤。
title: 部署和调试通用 Windows 平台 (UWP) 应用
ms.date: 04/08/2019
ms.topic: article
keywords: windows 10, uwp, 调试, 测试, 性能
ms.localizationpriority: medium
ms.openlocfilehash: d948af1ce10b3752ba9f20454d8dea72916de692
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75683850"
---
# <a name="deploying-and-debugging-uwp-apps"></a>部署和调试 UWP 应用

本文指导你完成针对各种部署和调试目标的步骤。

通过 Microsoft Visual Studio，可以在各种 Windows 10 设备上部署和调试通用 Windows 平台 (UWP) 应用。 Visual Studio 将处理在目标设备上生成和注册应用的过程。

## <a name="picking-a-deployment-target"></a>选取部署目标

若要选取目标，请转到“开始调试”  按钮旁边的调试目标下拉列表，然后选择你希望将应用部署到的目标。 选取目标后，选择“开始调试 (F5)”  在该目标上进行部署和调试，或者按“Ctrl+F5”  仅对该目标进行部署。

![调试设备目标列表](images/debug-device-target-list.png)

- “仿真器”  会将应用部署到当前开发计算机上的仿真环境。 此选项仅在应用的“目标平台最低版本”  低于或等于开发计算机的操作系统时可用。
- “本地计算机”  会将应用部署到当前开发计算机。 此选项仅在应用的“目标平台最低版本”  低于或等于开发计算机的操作系统时可用。
- “远程计算机”  允许你指定要部署应用的远程目标。 可以在[指定远程设备](#specifying-a-remote-device)中找到有关部署到远程计算机的详细信息。
- “设备”  会将应用部署到 USB 连接设备。 该设备必须已针对开发人员解锁，并且已解锁屏幕。
- “仿真器”  目标会使用名称中指定的配置将应用启动并部署到仿真器。 模拟器仅适用于运行 Windows 8.1 或更高版本并且支持 Hyper-V 的计算机。

## <a name="debugging-deployed-apps"></a>调试已部署的应用

通过依次选择“调试”  、“附加到进程”  ，Visual Studio 还可以附加到任何运行的 UWP 应用进程。 附加到运行的进程不需要原始 Visual Studio 项目，但是在调试没有原始代码的进程时，加载进程的[符号](#symbols)有很大的帮助。  

此外，可以通过依次选择“调试”  、“其他”  和“调试安装的应用程序包”  来附加和调试任何安装的应用包。

![“调试安装的应用程序包”对话框](images/gs-debug-uwp-apps-002.png)

选择“不启动，但在启动时调试代码”  将导致 Visual Studio 调试程序在你在自定义时打开它时附加到你的 UWP 应用。 这是从[不同的启动方法](../xbox-apps/automate-launching-uwp-apps.md)调试控件路径的有效方法，如使用自定义参数的协议激活。  

可以在 Windows 8.1 或更高版本上开发和编译 UWP 应用，但需要运行 Windows 10。 如果你要在 Windows 8.1 电脑上开发 UWP 应用，你可以远程调试在另一台 Windows 10 设备上运行的 UWP 应用，前提是主机和目标计算机在同一个 LAN 上。 若要执行此操作，请在两台计算机上下载和安装 [Visual Studio 远程工具](https://visualstudio.microsoft.com/downloads/)。 已安装的版本必须与你已安装的现有 Visual Studio 版本匹配，并且你选择的体系结构（x86、x64）还必须与你的目标应用的体系结构匹配。

## <a name="package-layout"></a>程序包布局

在 Visual Studio 2015 更新 3 中，我们添加了一个选项，让开发人员可以指定其 UWP 应用的布局路径。 当你生成应用时，这将确定程序包布局将复制到磁盘上的位置。 默认情况下，此属性相对于项目的根目录设置。 如果未修改此属性，行为将与以前版本的 Visual Studio 保持相同。

可以在项目的 **Debug** 属性中设置此属性。

如果你在为应用创建程序包时希望将所有布局文件都包含在程序包中，则必须添加项目属性 `<IncludeLayoutFilesInPackage>true</IncludeLayoutFilesInPackage>`。

若要添加此属性：

1. 右键单击该项目，然后选择“上载项目”  。
2. 右键单击该项目，然后选择“编辑 [projectname].xxproj”  （.xxproj 将根据项目语言而更改）。
3. 添加该属性，然后重新加载项目。

## <a name="specifying-a-remote-device"></a>指定远程设备

### <a name="c-and-microsoft-visual-basic"></a>C# 和 Microsoft Visual Basic

若要指定适用于 C# 或 Microsoft Visual Basic 应用的远程计算机，请在调试目标下拉列表中选择“远程计算机”  。 “远程连接”  对话框将出现，它将允许你指定 IP 地址或选择一台已发现的设备。 默认情况下，选择“通用”  身份验证模式。 若要确定要使用的身份验证模式，请参阅[身份验证模式](#authentication-modes)。

![“远程连接”对话框](images/debug-remote-connections.png)

若要返回到此对话框，可以打开项目属性，然后转到“调试”  选项卡。从此处，选择“**远程计算机**:”旁边的“查找” 

![“调试”选项卡](images/debug-remote-machine-config.png)

若要将应用部署到创意者更新之前的远程电脑，你还需要在目标电脑上下载并安装 Visual Studio 远程工具。 有关完整说明，请参阅[远程电脑说明](#remote-pc-instructions)。  但是，从创意者更新开始，电脑还支持远程部署。  

### <a name="c-and-javascript"></a>C++ 和 JavaScript

若要为 C++ 或 JavaScript UWP 应用指定远程计算机目标，请执行以下步骤：

1. 在“解决方案资源管理器”  中，右键打击项目，然后单击“属性”  。
2. 转到“调试”  设置，然后在“要启动的调试器”  下选择“远程计算机”  。
3. 输入“计算机名称”  （或单击 **“定位”** 以找到一台计算机），然后设置“身份验证类型”  属性。

![“调试”属性页](images/debug-property-pages.png)

指定计算机后，可以在调试目标下拉列表中选择“远程计算机”  ，以返回到该指定的计算机。 一次只能选择一台远程计算机。

### <a name="remote-pc-instructions"></a>远程电脑说明

> [!NOTE]
> 仅 Windows 10 的较早版本需要这些说明。  从创意者更新开始，可以像对待 Xbox 那样对待电脑。  也就是说，可以在电脑的“开发人员模式”菜单中启用“设备发现”，并使用通用身份验证与电脑进行 PIN 配对和连接。

若要部署到创意者更新之前的远程电脑，目标电脑上必须已安装 Visual Studio 远程工具。 远程电脑上运行的 Windows 版本还必须大于或等于你的应用的“目标平台最低版本”  属性值。 在安装远程工具后，必须在目标电脑上启动远程调试器。

若要执行此操作，请在“开始”  菜单中搜索“远程调试器”  、打开它，然后在系统提示时允许该调试器配置你的防火墙设置。 默认情况下，调试器启动时会执行 Windows 身份验证。 如果两台电脑上的登录用户不是同一人，这将要求用户提供凭据。

若要将它更改为“无身份验证”  ，请在“远程调试器”  中依次转到“工具”   -&gt;“选项”，然后将它设置为“无身份验证”   。 在设置远程调试器后，你还必须确保已将主机设备设置为[开发人员模式](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。 在这之后，你可以从部署计算机进行部署。

有关详细信息，请参阅 [Visual Studio 下载中心](https://visualstudio.microsoft.com/downloads/)页面。

## <a name="passing-command-line-debug-arguments"></a>传递命令行调试参数

在 Visual Studio 2019 中，可以在开始调试 UWP 应用程序时传递命令行调试参数。 可以通过 [  Application](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application) 类的 **OnLaunched** 方法中的 *args* 参数访问命令行调试参数。 若要指定命令行调试参数，请打开项目的属性并导航到“调试”  选项卡。

> [!NOTE]
> 这在适用于 C#、VB 和 C++ 的 Visual Studio 2017（版本 15.1）中可用。 JavaScript 在更高版本中可用。 命令行调试参数可用于所有部署类型（模拟器除外）。

对于 C# 和 VB UWP 项目，你会在“启动选项”  下看到“命令行参数:”  字段。

![命令行参数](images/command-line-arguments.png)

对于 C++ 和 JS UWP 项目，你会在“调试属性”  中看到“命令行参数”  字段。

![命令行参数 C++ 和 JS](images/command-line-arguments-cpp.png)

指定命令行参数之后，可以在应用的 OnLaunched  方法中访问参数值。 [LaunchActivatedEventArgs  ](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.launchactivatedeventargs) 对象 args  将具有 **Arguments** 属性，其值设置为“命令行参数”  字段中的文本。

![命令行参数 C++ 和 JS](images/command-line-arguments-debugging.png)

## <a name="authentication-modes"></a>身份验证模式

有三个身份验证模式可用于远程计算机部署：

- **通用（未加密协议）** ：部署到远程设备时，请使用此身份验证模式。 当前，这适用于 IoT 设备、Xbox 设备和 HoloLens 设备，以及具有创意者更新或更高版本的操作系统的电脑。 仅应在受信任的网络上使用通用（未加密协议）。 调试连接对于恶意用户来说容易遭受攻击，这些用户可以截获和更改在开发与远程计算机之间传递的数据。
- **Windows**：此身份验证模式仅适用于运行 Visual Studio 远程工具的远程电脑（台式机或笔记本电脑）。 当你有权访问目标计算机的登录用户的凭据时，使用此身份验证模式。 这是适用于远程部署的最安全通道。
- **None**：此身份验证模式仅适用于运行 Visual Studio 远程工具的远程电脑（台式机或笔记本电脑）。 当在有一个登录的测试帐户的环境中设置测试计算机并且无法输入凭据时，使用此身份验证模式。 确保远程调试器设置已设置为接受“无身份验证”。

## <a name="advanced-remote-deployment-options"></a>高级远程部署选项

随着 Visual Studio 2015 更新 3 和 Windows 10 周年更新的发布，某些 Windows 10 设备具有新的高级远程部署选项。 可以在项目属性的“调试”  菜单上找到高级远程部署选项。

新属性包括：

- 部署类型
- 程序包注册路径
- 在设备上保留所有文件，即使不再属于布局的文件也是如此。

### <a name="requirements"></a>要求

若要利用高级远程部署选项，你必须满足以下要求：

- 通过 Windows 10 工具 1.4.1 或更高版本（这些版本包含 Windows 10 周年更新 SDK）安装 Visual Studio 2015 Update 3 或更新的 Visual Studio 版本。建议使用包含更新的最新 Visual Studio 版本，以确保获取所有最新的开发和安全功能。
- 面向 Windows 10 周年更新 Xbox 远程设备或 Windows 10 创意者更新电脑
- 使用通用身份验证模式

### <a name="properties-pages"></a>“属性”页面

对于 C# 或 Visual Basic UWP 应用，“属性”页面的外观将如下所示。

![CS 或 VB 属性](images/advanced-remote-deploy-cs.png)

对于 C++ UWP 应用，“属性”页面的外观将如下所示。

![Cpp 属性](images/advanced-remote-deploy-cpp.png)

### <a name="copy-files-to-device"></a>将文件复制到设备

“将文件复制到设备”  将在物理上通过网络将文件传输到远程 设备。 它将复制并注册生成到“布局文件夹路径”  的程序包布局。 Visual Studio 将使复制到设备的文件与 Visual Studio 项目中的文件保持同步；但是，有一个“在设备上保留所有文件，即使不再属于布局的文件也是如此”  的选项。 选择此选项意味着，之前已复制到远程设备但不再属于项目的所有文件都将保留在远程设备上。

你在“将文件复制到设备”  时所指定的“程序包注册路径”  是在远程设备上复制文件的物理位置。 此路径可以指定为任何相对路径。 部署这些文件的位置将相对于部署文件根目录，此根目录取决于目标设备。 指定此路径对于共享同一台设备的多个开发人员，并且适用于具有某些版本差异的程序包。

> [!NOTE]
>  目前，运行 Windows 10 周年更新的 Xbox 和运行 Windows 10 创意者更新的电脑上支持将文件复制到设备。

在远程设备上，布局复制到以下默认位置：`\\MY-DEVKIT\DevelopmentFiles\PACKAGE-REGISTRATION-PATH`

### <a name="register-layout-from-network"></a>从网络注册布局

当你选择从网络注册布局时，可以将程序包布局生成到网络共享，然后在远程设备上直接从网络注册该布局。 这需要你指定可从远程设备上访问的布局文件夹路径（网络共享）。 “布局文件夹路径”  属性是相对于运行 Visual Studio 的电脑设置的路径，而“程序包注册路径”  属性是相同的路径，但相对于远程设备指定。

若要从网络成功注册布局，必须先使“布局文件夹路径”  成为共享网络文件夹。 若要执行此操作，请在文件资源管理器中右键单击该文件夹、依次选择“共享”&gt;“特定用户”  ，然后选择你希望与之共享文件夹的用户。 当你尝试从网络注册布局时，系统将提示你输入凭据，以确保你注册为具有共享访问权限的用户。

有关帮助，请查看以下示例：

- 示例 1（本地布局文件夹，可作为网络共享进行访问）：
  - 布局文件夹路径   = `D:\Layouts\App1`
  - 包注册路径   = `\\NETWORK-SHARE\Layouts\App1`

- 示例 2（网络布局文件夹）：
  - 布局文件夹路径   = `\\NETWORK-SHARE\Layouts\App1`
  - 包注册路径   = `\\NETWORK-SHARE\Layouts\App1`

当你首次从网络注册布局时，你的凭据将缓存到目标设备上，以便你无需重复登录。 若要删除缓存的凭据，可以将 Windows 10 SDK 中的 [WinAppDeployCmd.exe 工具](https://docs.microsoft.com/windows/uwp/packaging/install-universal-windows-apps-with-the-winappdeploycmd-tool)与 **deletecreds** 命令结合使用。

当你从网络注册布局时，你无法选择“在设备上保留所有文件“  ，因为没有任何文件在物理上复制到远程设备。

> [!NOTE]
>  目前，运行 Windows 10 周年更新的 Xbox 和运行 Windows 10 创意者更新的电脑上支持从网络注册布局。

在远程设备上，布局注册到下面的默认位置，具体取决于设备系列：`Xbox: \\MY-DEVKIT\DevelopmentFiles\XrfsFiles` - 这是链接到程序包注册路径  的符号链接。电脑不会使用符号链接，而是直接注册程序包注册路径 

## <a name="debugging-options"></a>调试选项

在 Windows 10 中，使用称为[预启动](https://docs.microsoft.com/windows/uwp/launch-resume/handle-app-prelaunch)的技术主动启动然后暂停应用，改进 UWP 应用的启动性能。 许多应用不需要执行任何特殊操作就即在此模式下工作，但某些应用可能需要调整它们的行为。 若要帮助调试这些代码路径中的任何问题，你可以在 Visual Studio 中使用预启动模式开始调试该应用。

既可调试 Visual Studio 项目（“调试”   -&gt;“其他调试目标”   -&gt;“调试通用 Windows 应用预启动”  ），也可调试计算机上已安装的应用（选择“调试”   -&gt;“其他调试目标”   -&gt;调试已安装的应用包”  ，并选中“通过预启动激活应用”  复选框）。 有关详细信息，请参阅[调试 UWP 预启动](https://blogs.msdn.com/b/visualstudioalm/archive/2015/11/30/debug-uwp-prelaunch-with-vs2015.aspx)。

可以在启动项目的“调试”  属性页上设置以下部署选项。

- **允许本地网络环回**

  出于安全原因，以标准方式安装的 UWP 应用不允许对安装了它的设备进行网络调用。 默认情况下，Visual Studio 部署针对已部署应用从此规则创建豁免。 此豁免允许你在单台计算机上测试通信过程。 将你的应用提交到 Microsoft Store 之前，应在不豁免的情况下测试它。

  若要从应用中删除网络环回豁免：

  - 在 C# 和 Visual Basic“调试” **** 属性页上，清除“允许本地网络环回”  复选框。
  - 在 JavaScript 和 C++“调试”  属性页上，将“允许本地网络环回”  值设置为“否”  。

- **不启动，但在启动时调试代码/启动应用程序**

  若要将部署配置为启动应用时自动开始一个调试会话：

  - 在 C# 和 Visual Basic“调试” **** 属性页上，选中“不启动，但在其开始运行时调试代码”  复选框。
  - 在 JavaScript 和 C++“调试”  属性页上，将“启动应用程序”  值设置为“是”  。

## <a name="symbols"></a>符号

符号文件包含各种在调试代码时非常有用的数据，如变量、函数名称和入口点地址，从而使你可以更好地了解异常和调用堆栈执行顺序。 适用于 Windows 的大多数变体的符号通过 [Microsoft 符号服务器](https://msdl.microsoft.com/download/symbols)提供，或者可以下载，以供在[下载 Windows 符号包](https://docs.microsoft.com/windows-hardware/drivers/debugger/debugger-download-symbols)中更快速地离线查找。

若要为 Visual Studio 设置符号选项，请依次选择“工具”&gt;“选项”  ，然后在对话窗口中依次转到“调试”&gt;“符号”  。

![“选项”对话框](images/gs-debug-uwp-apps-004.png)

若要使用 [WinDbg](#windbg) 在调试会话中加载符号，请将 **sympath** 变量设置为符号包位置。 例如，运行以下命令将从 Microsoft 符号服务器加载符号，然后将它们缓存在 C:\Symbols 目录中：

```cmd
.sympath SRV*C:\Symbols*http://msdl.microsoft.com/download/symbols
.reload
```

你可以使用 `‘;’` 分隔符添加多个路径，或使用 `.sympath+` 命令。 有关使用 WinDbg 的更高级符号运算，请参阅[公共和私有符号](https://docs.microsoft.com/windows-hardware/drivers/debugger/public-and-private-symbols)。

## <a name="windbg"></a>WinDbg

WinDbg 是 Windows 调试工具套件随附的一款功能强大的调试器，它包含在 [Windows SDK](https://developer.microsoft.com/) 中。 Windows SDK 安装允许你安装 Windows 调试工具作为独立产品。 尽管对调试本机代码非常有用，但我们不建议为使用托管代码或 HTML5 编写的应用使用 WinDbg。

若要将 WinDbg 用于 UWP 应用，你将需要先使用 PLMDebug 为应用包禁用进程周期管理 (PLM)，如[进程周期管理 (PLM) 的测试和调试工具](testing-debugging-plm.md)中所述。

```cmd
plmdebug /enableDebug [PackageFullName] ""C:\Program Files\Debugging Tools for Windows (x64)\WinDbg.exe\" -server npipe:pipe=test"
```

与 Visual Studio 相反，WinDbg 的大部分核心功能依赖于向命令窗口提供命令。 所提供的命令允许你查看执行状态、调查用户模式崩溃转储并在各种模式下进行调试。

WinDbg 中最常用的命令之一是 `!analyze -v`，该命令用于检索大量有关当前异常的信息，包括：

- FAULTING_IP：故障发生时的指令指针
- EXCEPTION_RECORD：当前异常的地址、代码和标志
- STACK_TEXT：异常之前的堆栈跟踪

有关所有 WinDbg 命令的完整列表，请参阅[调试器命令](https://docs.microsoft.com/windows-hardware/drivers/debugger/debugger-commands)。

## <a name="related-topics"></a>相关主题

- [进程周期管理 (PLM) 的测试和调试工具](testing-debugging-plm.md)
- [调试、测试和性能](index.md)
