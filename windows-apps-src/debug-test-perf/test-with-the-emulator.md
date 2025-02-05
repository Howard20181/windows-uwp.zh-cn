---
ms.assetid: 7234DD5F-8E86-424E-99A0-93D01F1311F2
title: 使用适用于 Windows 10 移动版的 Microsoft 模拟器进行测试
description: 通过适用于 Windows 10 移动版的 Microsoft 仿真器随附的工具，模拟与某个设备的真实交互并测试你的应用功能。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 6e20d56485ad7b5a54b9e951e8ad8ee331b5da8f
ms.sourcegitcommit: 249100d990cd5cf2854c59fa66803b7f83d5db96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105939112"
---
# <a name="test-with-the-microsoft-emulator-for-windows-10-mobile"></a>使用适用于 Windows 10 移动版的 Microsoft 模拟器进行测试

通过适用于 Windows 10 移动版的 Microsoft 仿真器随附的工具，模拟与某个设备的真实交互并测试你的应用功能。 该仿真器是一个桌面应用程序，它可以模拟运行 Windows 10 的移动设备。 它提供了一个虚拟环境，你可以在其中调试并测试 Windows App，而无需使用物理设备。 它还为你的应用程序原型提供了隔离的环境。

该模拟器旨在提供与实际设备相当的性能。 但是，建议先在物理设备上测试你的应用，再将其发布到 Microsoft Store。

你可以通过针对各种屏幕分辨率和屏幕大小配置使用唯一的 Windows 10 移动版模拟器图像，测试自己的通用应用。 通过使用 Microsoft 模拟器随附的工具，你可以模拟与某台设备的真实交互并测试你的应用的各种功能。

## <a name="system-requirements"></a>系统要求

你的计算机必须满足以下要求：

CPU（如果这些设置可用，可以在 BIOS 中启用它们）

-   硬件协助的虚拟化（[检查兼容性](https://www.microsoft.com/download/details.aspx?id=592)）。
-   二级地址转换 (SLAT)。
-   基于硬件的数据执行保护 (DEP)。

RAM

-   4 GB 或更多。

操作系统

-   Windows 8 或更高版本（强烈推荐 Windows 10）
-   64 位
-   专业版或更高版本

若要查看 BIOS 要求，请参阅[如何为 Windows Phone 8 仿真器启用 Hyper-V](/previous-versions/windows/apps/jj863509(v=vs.105))。

若要查看 RAM 和操作系统的要求，请在“控制面板”中选择“系统和安全”  ，然后选择“系统”  。

适用于 Windows 10 移动版的 Microsoft 模拟器要求使用 Visual Studio 2015 或更高版本；它不后向兼容早期版本的 Visual Studio。

适用于 Windows 10 移动版的 Microsoft 模拟器无法加载面向 Windows Phone OS 7.1 之前的 Windows Phone 操作系统版本的应用。

## <a name="installing-uninstalling-and-running-the-emulator"></a>安装、卸载和运行模拟器

### <a name="installing"></a>安装
适用于 Windows 10 移动版的 Microsoft 模拟器作为 Windows 10 SDK 的一部分进行提供。 Windows 10 SDK 和模拟器可随 Visual Studio 安装。 请参阅 [Visual Studio 下载页](https://developer.microsoft.com/windows/downloads)。

你还可以使用 [Microsoft 模拟器安装程序](https://download.microsoft.com/download/E/3/4/E347E8C5-BD79-49AF-941C-D08893A45268/emulator/EmulatorSetup.exe)来安装适用于 Windows 10 移动版的 Microsoft 模拟器。

### <a name="uninstalling"></a>卸载

你可以使用 Visual Studio 安装程序/修复程序来卸载适用于 Windows 10 移动版的 Microsoft 模拟器。 或者，你也可以使用“控制面板”  下的“程序和功能”  删除该仿真器。

当卸载适用于 Windows 10 移动版的 Microsoft 模拟器时，为供该模拟器使用而创建的 Hyper-V 虚拟以太网适配器不会自动删除。 你可以从“控制面板”  的“网络连接”  中手动删除此虚拟适配器。

### <a name="running"></a>运行

从 Visual Studio 的“运行”下拉菜单中选择模拟设备，即可在该模拟器中运行应用  。

![提供模拟器以及分辨率、大小和内存](images/em-list.png)

## <a name="whats-new-in-microsoft-emulator-for-windows-10-mobile"></a>适用于 Windows 10 移动版的 Microsoft 模拟器的新增功能

除了提供对 Universal Windows Platform (UWP) 的支持外，该模拟器还添加了以下功能：

-   鼠标输入模式支持，可区分鼠标输入和单一触摸输入。
-   NFC 支持。 借助这款模拟器，不仅可以模拟 NFC，还可以测试和开发支持 NFC/邻近感应的通用应用。
-   本机硬件加速使用本地显卡提高模拟器中的图形性能。 必须安装支持的图形卡，并在仿真器的“其他工具”  设置用户界面的“传感器”  选项卡上启用加速，以便使用加速。

## <a name="features-that-you-can-test-in-the-emulator"></a>可以在仿真器中测试的功能

除了在上一部分中所述的新功能，还可以在适用于 Windows 10 移动版的 Microsoft 模拟器中测试以下常用功能。

-   **屏幕分辨率、屏幕大小和内存**。 通过在各种模拟器图像上测试应用，模拟各种屏幕分辨率、物理大小和内存限制，从而将应用扩展到更广泛的市场。

![提供模拟器以及分辨率、大小和内存](images/em-list.png)

-   **屏幕配置**。 将模拟器从纵向模式更改为横向模式。 更改缩放设置以使模拟器适合你的桌面屏幕。

-   **网络**。 网络支持可与 Windows Phone 模拟器集成。 默认情况下网络处于启用状态。 你无需为 Windows Phone 模拟器安装网络驱动程序，也无需在大多数环境中手动配置网络选项。

    该模拟器使用主计算机的网络连接。 它不会显示为网络上的单独设备。 这可消除用户在使用 Windows Phone SDK 8.0 模拟器时遇到的某些配置问题。

-   **语言和区域设置**。 通过在 Windows Phone 模拟器中更改显示语言和区域设置，为应用进入国际市场做好准备。

    在运行的仿真器上，转到“设置”  应用，选择“系统”  设置，然后选择“语言”  或“区域”  。 更改要测试的设置。 如果出现提示信息，请单击“重新启动手机”  以应用新设置并重新启动仿真器。

-   **应用程序生命周期和逻辑删除**。 通过更改项目属性的“调试”  页面上的“在调试期间取消激活后进行逻辑删除”  选项的值，测试行为或测试取消激活或逻辑删除的应用。

-   **本地文件夹存储（之前称为“隔离存储”）** 。 运行模拟器时将保留隔离存储中的数据，但该数据会在关闭模拟器后丢失。

-   **麦克风**。 需要在主计算机上配备和使用麦克风。

-   **手机键盘**。 仿真器支持将开发计算机上的硬件键盘映射到 Windows Phone 上的键盘。 键行为与在 Windows Phone 设备中相同

-   **锁屏界面**。 打开模拟器时，在计算机键盘上按 F12 两次。 F12 键可模拟手机上的电源按钮。 第一次按键将关闭屏幕。 第二次按键将再次打开屏幕，同时显示锁屏界面。 使用鼠标向上滑动锁屏可解锁屏幕。

## <a name="features-that-you-cant-test-in-the-emulator"></a>无法在模拟器中测试的功能

仅可以在物理设备上测试以下功能。

-   指南针
-   陀螺仪
-   振动控制器
-   亮度。 模拟器的亮度级别始终很高。
-   高分辨率的视频。 分辨率高于 VGA 分辨率 (640 x 480) 的视频无法可靠地显示，在内存仅为 512MB 的模拟器映像上尤其如此。

## <a name="mouse-input"></a>鼠标输入

通过使用 Windows 电脑上的物理鼠标或触控板和模拟器工具栏上的鼠标输入按钮，即可模拟鼠标输入。 如果你的应用允许用户使用与其 Windows 10 设备配对的鼠标提供输入，此功能将很有用。

点击模拟器工具栏上的鼠标输入按钮，即启用鼠标输入。 现在，模拟器 Chrome 内的任何单击事件都将作为鼠标事件发送到模拟器 VM 内运行的 Windows 10 移动版操作系统。

![支持鼠标输入的模拟器屏幕](images/emulator-with-mouse-enabled.png)

支持鼠标输入的模拟器屏幕。

![模拟器工具栏上的鼠标输入按钮](images/emulator-showing-mouse-input-button-bar.png)

仿真器工具栏上的鼠标输入按钮。

## <a name="keyboard-input"></a>键盘输入

仿真器支持将开发计算机上的硬件键盘映射到 Windows Phone 上的键盘。 键行为与在 Windows Phone 设备中相同。 

默认情况下，不启用硬件键盘。 此实现等效于使用之前必须部署的滑动键盘。 在启用硬件键盘之前，仿真器只接受控制键的键输入。

仿真器不支持本地化版本的 Windows 开发计算机键盘上的特殊字符。 若要输入本地化键盘上显示的特殊字符，请改用软件输入面板 (SIP)。 

若要在仿真器中使用计算机的键盘，请按 F4。

若要在仿真器中停止使用计算机的键盘，请按 F4。

下表列出了硬件键盘上可用于模拟按钮的键以及 Windows Phone 上的其他控件。

请注意，在仿真器中内部版本 10.0.14332 中，计算机硬件键映射已更改。 下表第二列中的值表示这些新键。 

计算机硬件键（仿真器内部版本 10.0.14295 及更早版本） | 计算机硬件键（仿真器内部版本 10.0.14332 及更早版本） | Windows Phone 硬件按钮 | 注意
--------------------- | ------------------------- | ----------------------------- | -----
F1 | WIN + ESC | BACK | 长按可实现预期效果。
F2 | WIN + F2 | START | 长按可实现预期效果。
F3 | WIN + F3 | SEARCH |  
F4 | F4（无变化） | 在使用本地计算机的键盘和不使用本地计算机的键盘之间切换。 | 
F6 | WIN + F6 | CAMERA HALF | 半按专用相机按钮。
F7 | WIN + F7 | CAMERA FULL | 专用相机按钮。
F9 | WIN + F9 | VOLUME UP | 
F10 | WIN + F10 | VOLUME DOWN | 
F12 | WIN + F12 | POWER | 按两次 F12 键可启用锁屏界面。 长按可实现预期效果。
ESC | WIN + ESC | BACK | 长按可实现预期效果。
 


## <a name="near-field-communications-nfc"></a>近场通信 (NFC)

通过使用仿真器“其他工具”  菜单中的“NFC”  选项卡，即可对在 Windows 10 移动版上使用已启用的近场通信 (NFC) 功能的应用进行生成和测试。 对于从邻近感应方案（如点击以共享）到卡仿真（如点击以支付）等多种方案，NFC 非常有用。

你可以采用以下方式来测试你的应用：用一对模拟器模拟一对手机触碰到一起，或模拟点击某个标记。 此外，Windows 10 移动设备还支持 HCE（主机卡仿真）功能，而且借助手机模拟器，即可通过 APDU 响应式命令通信，模拟点击你的设备以进入支付终端。

“NFC”选项卡支持以下三种模式：

-   邻近感应模式
-   HCE（主机卡仿真）模式
-   智能卡读卡器模式

在所有模式中，模拟器窗口都有三个关注领域。

-   左上角部分特定于已选中模式。 此部分的功能取决于所选模式，将在下面的特定于模式部分中作详细介绍。
-   右上角部分会列出日志。 当你将一对设备触碰到一起（或触碰到 POS 终端）时，将记录触碰事件，而当设备取消触碰时，将记录取消触碰事件。 此部分还记录你的应用是否在连接中断前响应或记录你在模拟器 UI 中执行的任何其他操作，并带有时间戳。 日志在模式切换后仍然保留，并且你可以随时通过点击“日志”  屏幕上方的“清除”  按钮清除日志。
-   屏幕的下半部分是消息日志，显示通过当前选择的连接发送或接收的所有消息的报道，具体取决于所选择的模式。

> **重要提示** 当你首次启动触碰器工具时，你会收到一个 Windows 防火墙提示。 你必须选中全部 3 个复选框，并允许该工具通过防火墙，否则该工作将在无提示的情况下无法运行。

在启动快速启动安装程序后，请确保按照上述说明选中防火墙提示上的全部 3 个复选框。 此外，必须安装触碰器工具，并且在与 Microsoft 模拟器相同的物理主机上使用它。

### <a name="proximity-mode"></a>邻近感应模式

若要模拟一对手机触碰到一起，你将需要启动一对 Windows Phone 8 模拟器。 由于 Visual Studio 不支持同时运行两个相同的模拟器，因此你需要为每个模拟器选择不同的分辨率，以便解决该问题。

![NFC 邻近感应页](images/emulator-nfc-proximity.png)

当你选中“启用对等设备发现”  复选框时，“对等设备”  下拉框会显示 Microsoft 仿真器（在相同的物理主机或本地网络中运行）以及运行模拟器驱动程序的 Windows 计算机（在相同的计算机或本地网络中运行）。

当两个模拟器都在运行时：

-   在“对等设备”  列表中选择你希望设为目标的仿真器。
-   选择“发送到对等设备”  单选按钮。
-   单击“触碰”  按钮。 这将模拟两台设备触碰到一起，并且你应该能听到 NFC 触碰通知声音。
-   若要断开这两台设备的连接，只需点击“取消触碰”  按钮。

此外，你可以启用“自动取消触碰(秒)”  复选框，可在其中指定你希望设备触碰的秒数，它们将在指定的秒数后自动取消触碰（模拟用户在现实生活中的预期行为，他们只会暂时共同拿起电话）。 但请注意，当前在连接取消触碰后不提供消息日志。

若要模拟从标记读取消息或从另一台设备接收消息：

-   选择“发送给自己”  单选按钮以测试只需一个支持 NFC 的设备的方案。
-   单击“触碰”  按钮。 这将模拟设备触碰到一个标记，并且你应该会听到 NFC 触碰通知声音
-   若要断开连接，只需点击“取消触碰”  按钮即可。

使用邻近模式，你可以插入消息，如同它们来自某个标记或另一个对等设备一样。 该工具允许你发送以下类型的消息。

-   WindowsURI
-   WindowsMime
-   WritableTag
-   Pairing:Bluetooth
-   NDEF
-   NDEF:MIME
-   NDEF:URI
-   NDEF:wkt.U

你可以通过编辑“负载”  窗口或在文件中提供它们来创建这些消息。 有关这些类型以及如何使用它们的详细信息，请参考 [**ProximityDevice.PublishBinaryMessage**](/uwp/api/windows.networking.proximity.proximitydevice.publishbinarymessage) 参考页面的“备注”部分。

Windows 8 驱动程序工具包 (WDK) 包括一个驱动程序示例，该示例将公开与 Windows Phone 8 模拟器相同的协议。 你需要下载 DDK、构建该示例驱动程序、在 Windows 8 设备上安装它，然后将 Windows 8 设备的 IP 地址或主机名添加到设备列表，并将其与另一台 Windows 8 设备或一个 Windows Phone 8 模拟器触碰。

### <a name="host-card-emulation-hce-mode"></a>主机卡仿真 (HCE) 模式

在主机卡仿真 (HCE) 模式下，你可以通过编写自己的自定义脚本来模拟智能卡读卡器终端（例如销售点 (POS) 终端）来测试基于 HCE 的卡仿真应用程序。 此工具假定你熟悉读取器终端（例如 POS、锁屏提醒读取器或公交卡读卡器）和智能卡（你正在应用程序中进行模拟）之间发送的命令响应对（符合 ISO-7816-4）。

![NFC HCE 页面](images/emulator-nfc-hce.png)

-   通过在脚本编辑器部分中单击“添加”  按钮来创建新脚本。 你可以为脚本提供一个名称，并且在完成编辑后，你可以使用“保存”  按钮来保存脚本。
-   已保存的脚本将在你下次启动模拟器时可用。
-   通过点击脚本编辑器窗口中的“播放”  按钮来运行脚本。 此操作会导致模拟手机触碰到终端并发送在脚本中编写的命令。 此外，你可以依次点击“触碰”  和“播放”  按钮，在你单击“播放”  之前，脚本将不会运行。
-   通过点击“停止”  按钮来停止发送命令，这会停止向应用程序发送命令，但是设备会保持触碰状态，直到你点击“取消触碰”  按钮。
-   通过在下拉菜单中选择脚本并点击“删除”  按钮来删除脚本。
-   仿真器工具不会检查脚本的语法，直到你使用“播放”  按钮运行脚本。 由脚本发送的消息取决于卡仿真应用的实现。

还可以使用 MasterCard 提供的终端模拟器工具 ([https://www.terminalsimulator.com/](https://www.terminalsimulator.com/ )) 进行支付应用测试。

-   选中脚本编辑器窗口下的“启用 MasterCard”  侦听器复选框，然后从 MasterCard 启动模拟器。
-   使用此工具，你可以生成通过 NFC 工具中继到在模拟器上运行的应用程序的命令。

若要了解有关 HCE 支持以及在 Windows 10 移动版中如何开发 HCE 应用的详细信息，请参考 [Microsoft NFC 团队博客](https://blogs.msdn.com/b/nfc/)。

### <a name="how-to-create-scripts-for-hce-testing"></a>如何创建用于 HCE 测试的脚本

脚本编写为 C# 代码，并且将在你单击“播放”  按钮时调用你的脚本的 Run 方法，此方法采用 IScriptProcessor 接口，此接口用于收发 APDU 命令、输出到日志窗口和控制等待来自手机的 APDU 响应的超时。

以下是有关哪些功能可用的参考：

```csharp     
        public interface IScriptProcessor
        {
            // Sends an APDU command given as a hex-encoded string, and returns the APDU response
            string Send(string s);

            // Sends an APDU command given as a byte array, and returns the APDU response
            byte[] Send(byte[] s);

            // Logs a string to the log window
            void Log(string s);

            // Logs a byte array to the log window
            void Log(byte[] s);

            // Sets the amount of time the Send functions will wait for an APDU response, after which
            // the function will fail
            void SetResponseTimeout(double seconds);
        }
```

### <a name="smart-card-reader-mode"></a>智能卡读卡器模式

该仿真器可连接到主计算机上的智能卡读卡器设备，以便使所插入或触碰的智能卡显示到手机应用程序中，并且可使用 [**Windows.Devices.SmartCards.SmartCardConnection**](/uwp/api/Windows.Devices.SmartCards.SmartCardConnection) 类与 APDU 进行通信。 若要实现此功能，你将需要一个连接到计算机的兼容智能卡读卡器设备，USB 智能卡读卡器（NFC/非接触式和插入/接触式均可）广泛可用。 若要使仿真器可以使用连接的智能卡读卡器，首先选择“读卡器”  模式，该模式应该显示一个列出连接到主系统的所有兼容智能卡读卡器的下拉框，然后从该下拉框中选择你希望连接的智能卡读卡器设备。

请注意，并非所有支持 NFC 的智能卡读卡器都支持某些类型的 NFC 卡，其中有一些不支持标准 PC/SC 存储卡 APDU 命令。

## <a name="multi-point-input"></a>多点输入

通过使用仿真器工具栏上的“多点输入”  按钮，可模拟收缩和缩放、旋转和平移对象的多点触摸输入。 如果应用显示的照片、地图或其他视觉元素用户可以缩放、旋转或平移，此功能将很有用。

1.  点击仿真器工具栏上的“多点触摸输入”  按钮以启动多点输入。 两个触摸点将围绕中心点显示在模拟器屏幕上。
2.  右键单击并拖动其中一个触摸点以进行放置，无需触摸屏幕。
3.  左键单击并拖动其中一个触摸点以模拟收缩和缩放、旋转或平移。
4.  点击仿真器工具栏上的“单点输入”  按钮以还原正常输入。

以下屏幕截图显示多点触摸输入。

1.  左侧的小图显示了仿真器工具栏上的“多点触摸输入”  按钮。
2.  中间的图像在点击“多点触摸输入”  按钮显示触摸点后显示仿真器屏幕。
3.  右边的图像在拖动触摸点缩放图像后显示模拟器屏幕。

![模拟器工具栏上的多点输入选项](images/em-multipoint.png)

## <a name="accelerometer"></a>加速计

通过使用仿真器的“其他工具”  的“加速计”  选项卡，测试可跟踪手机移动的应用。

可通过实时输入或预先记录的输入测试加速计传感器。 只有一种记录的数据可用于模拟手机摇动。 无法为加速计记录或保存你自己的模拟。

1.  在“方向”  下拉列表中选择所需的起始方向。

2.  -   选择输入的类型。

        **由实时输入运行模拟**

        在加速计模拟器的中间，拖动彩色点以模拟设备在三维平面中的移动。

        水平方向移动该点可使模拟器从一侧旋转到另一侧。 垂直方向移动该点可绕 X 轴前后旋转模拟器。 拖动该点时，X、Y 和 Z 坐标基于旋转计算进行更新。 不能将该点移动到触摸板区域的边界圆以外。

        可选，单击“重置”  以还原起始方向。

    -   **由录制的输入运行模拟**

        在“记录的数据”  部分中，单击“播放”  按钮以开始播放模拟的数据。 “记录的数据”  列表中提供的唯一选项是摇动。 当模拟器播放数据时，它不会在屏幕上移动。

![模拟器的“其他工具”中的“加速计”页面](images/em-accelerometer.png)

## <a name="location-and-driving"></a>位置和驱动

通过使用仿真器的“其他工具”  的“加速计”  选项卡，测试使用导航和地理围栏的应用。 对于在类似于真实世界的条件下模拟驾车、骑行和步行，此功能将很有用。

当你以不同的速度并使用不同的精度配置文件从一个位置移动到另一个位置时，可以测试你的应用。 位置模拟器可以帮助你识别你的用法在位置 API 用法中的更改，可用于改进用户体验。 例如，该工具可帮助你识别必须优化地理围栏参数（例如大小或停留时间），才能检测地理围栏在不同的应用场景中是否成功。

“位置”  选项卡支持三种模式。 在所有模式中，当仿真器收到新位置时，该位置可用于触发 [**PositionChanged**](/uwp/api/windows.devices.geolocation.geolocator.positionchanged) 事件或在你的位置感知应用中响应 [**GetGeopositionAsync**](/uwp/api/windows.devices.geolocation.geolocator.getgeopositionasync) 调用。

-   在“固定”  模式中，你可以将图钉放置在地图上。 当你单击“播放所有点”  时，位置模拟器会以在“秒每钉”  文本框中指定的间隔将每个图钉的位置逐个发送给仿真器。

-   在“实时”  模式中，你可以将图钉放置在地图上。 位置模拟器会在你将其放置在地图上时立即将每个图钉的位置发送给模拟器。

-   在“路线”  模式中，你可以将图钉放置在地图上来指示路点，位置模拟器将自动计算路线。 路线包括沿该路线以二分之一的间隔设置的不可见图钉。 例如，如果你选择“步行”  速度配置文件（假定速度为每小时 5 千米），则以 1.39 米的间隔生成不可见的图钉。 当你单击“播放所有点”  时，位置模拟器会以由在下拉列表中选定的速度配置文件确定的间隔将每个图钉的位置逐个发送给仿真器。

在位置模拟器的所有模式中，你可以执行以下操作。

-   你可以使用“搜索”  框搜索某个位置。

-   你可以“放大”  和“缩小”  地图。

-   你可以将当前的数据点集保存为 XML 文件，并在以后重新加载该文件以重复使用相同的数据点。

-   你可以“打开或关闭图钉模式”  并“清除所有点”  。

在“固定”和“路线”模式中，你还可以执行以下操作。

-   保存你创建的路线，以供以后使用。

-   加载之前创建的路线。 你甚至可以加载在早期版本的工具中创建的路线文件。

-   通过删除图钉（在“固定”模式中）或路点（在“路线”模式中）修改路线。

**准确度配置文件**

在位置模拟器的所有模式中，你可以选择“精度配置文件”  下拉列表中的以下精度配置文件之一。

| 配置文件  | 说明                                        |
|----------|----------------------------------------------------|
| 固定点 | 假设完全准确的位置读数。 此设置不实际，但对于测试应用的逻辑很有用。  |
| 城市    | 假设建筑限制视图中的卫星数量，但通常具有高密度手机基站和可用于定位的 Wi-Fi 接入点。 |
| 城郊 | 假设卫星定位相对较好，且手机基站的密度也良好，但 Wi-Fi 接入点的密度并不高。  |
| 农村    | 假设卫星定位良好，但手机基站的密度较低，并且几乎没有可用于定位的 Wi-Fi 接入点。 |

**速度配置文件**

在“路线”  模式中，你可以选择下拉列表中的以下速度配置文件之一。

| 配置文件 | 每小时速度               | 每秒速度 | 说明 | 
|---------|------------------------------|------------------|-------------|
| 速度限制 | 路线的速度限制 | 不适用   | 以发布的速度限制遍历路线。 |
| 步行     | 5 km/h                   | 1.39 m           | 以 5 km/h 的自然步行速度遍历路线。 |
| 骑行      | 25 km/h                  | 6.94 m           | 以 25 km/h 的自然骑行速度遍历路线。 |
| 迅速        |                          |                  |以快于发布的速度限制的速度遍历路线。 |

**“路线”模式**

“路线”模式具有以下功能和限制。

-   “路线”模式需要 Internet 连接。

-   当选中“城市”、“城郊”或“农村”精度配置文件时，位置模拟器将计算基于卫星的模拟位置、模拟的 Wi-Fi 位置，以及每个图钉的模拟手机位置。 你的应用仅接收以下一个位置。 当前位置的三组坐标均以不同颜色显示在地图上和“当前位置”  列表中。

-   沿路线设置的图钉精度并不一致。 某些图钉使用卫星精度、某些图钉使用 Wi-Fi 精度，某些图钉使用手机精度。

-   可为路线选择的路点不得超过 20 个。

-   当你选择新的精度配置文件时，地图上可见和不可见图钉的位置仅可以生成一次。 当你在同一模拟器会话期间使用相同的精度配置文件播放多次路线时，将重复使用之前生成的位置。

以下屏幕截图显示了“路线”模式。 橙色线表示路线。 蓝色点表示汽车的准确位置，该位置基于卫星的定位确定。 红色点和绿色点表示通过使用 Wi-Fi 和手机定位以及“城郊”精度配置文件计算的不太准确的位置。 这三个计算的位置还显示在“当前位置”  列表中。

![模拟器的“其他工具”中的“位置”页面](images/em-drive.png)

**有关位置模拟器的详细信息**

-   你可以请求一个位置，其精度设置为“默认值”。 已修复 Windows Phone 8 版本的位置模拟器中存在的限制，该限制要求你请求其精度设置为“高”的某个位置。

-   当你在模拟器中设置地理围栏时，创建为地理围栏引擎提供“准备”时间以了解和调整到移动模式的模拟。

-   模拟的位置属性只包括“纬度”、“经度”、“精度”和PositionSource。 位置模拟器不会模拟其他属性，例如“速度”、“方向”等。

## <a name="network"></a>Network (网络)

通过仿真器的“其他工具”  的“网络”  选项卡，使用不同的网络速度和不同的信号强度测试你的应用。 如果你的应用调用 Web 服务或传输数据，此功能将很有用。

网络模拟功能可帮助你确保你的应用在真实世界中运行良好。 Windows Phone 模拟器运行在通常具有快速 WiFi 或以太网连接的计算机上。 但是，你的应用运行在通常通过缓慢的手机连接而连接的手机上。

1.  检查“启用网络模拟”  以使用不同的网络速度和不同的信号强度测试你的应用。
2.  在“网络速度”  下拉列表中，选择以下选项之一：
    -   无网络
    -   2G
    -   3G
    -   4G

3.  在“信号强度”  下拉列表中，选择以下选项之一：
    -   完好
    -   平均值
    -   差

4.  清除“启用网络模拟”  还原默认行为，从而使用开发计算机的网络设置。

还可以在“网络”  选项卡上查看当前网络设置。

![模拟器的“其他工具”中的“网络”页面](images/em-network.png)

## <a name="sd-card"></a>SD 卡

通过仿真器的“其他工具”  的“SD 卡”  选项卡，使用模拟的可移动 SD 卡测试你的应用。 如果你的应用读取或写入文件，此功能将很有用。

![模拟器的“其他工具”中的“SD 卡”页面](images/em-sdcard.png)

“SD 卡”  选项卡使用开发计算机上的某个文件夹在手机中模拟可移动 SD 卡。

1.  “选择一个文件夹”  。

    单击“浏览”  选取开发计算机上的某个文件夹以保留模拟的 SD 卡的内容。

2.  “插入 SD 卡”  。

    选择某个文件夹后，单击“插入 SD 卡”  。 插入 SD 卡后，将发生以下情况：

    -   如果你未指定文件夹，或文件夹无效，将发生错误。
    -   开发计算机上的指定文件夹中的文件将复制到模拟器上模拟的 SD 卡的根文件夹中。 进度栏指示同步操作的进度。
    -   “插入 SD 卡”  按钮将更改为“弹出 SD 卡”  。
    -   如果你在同步操作正在进行时单击“弹出 SD 卡”  ，将取消该操作。

3.  或者，也可以选择或清除“弹出 SD 卡时，将更新的文件重新同步到本地文件夹”  。

    默认情况下该选项处于启用状态。 如果此选项处于启用状态，当你弹出 SD 卡时，文件将从模拟器重新同步到开发计算机上的文件夹。

4.  “弹出 SD 卡”  。

    单击“弹出 SD 卡”  。 弹出 SD 卡后，将发生以下情况：

    -   如果你已选择“弹出 SD 卡时，将更新的文件重新同步到本地文件夹”  ，将发生以下情况：
        -   模拟器上模拟的 SD 卡上的文件将复制到开发计算机上指定的文件夹中。 进度栏指示同步操作的进度。
        -   “弹出 SD 卡”  按钮将更改为“取消同步”  。
        -   如果你在同步操作正在进行时单击“取消同步”  ，将弹出该卡，并且同步操作的结果会不完整。
    -   “弹出 SD 卡”  按钮将更改回“插入 SD 卡”  。

> **注意** 由于手机使用的 SD 卡已使用 FAT32 文件系统进行格式化，所以最大容量为 32GB。

读取和写入模拟 SD 卡的速度受到限制以模仿真实速度。 访问 SD 卡的速度慢于访问计算机的硬盘驱动器的速度。

## <a name="notifications"></a>通知

使用仿真器的“其他工具”  的“通知”  选项卡，向你的应用发送推送通知。 如果你的应用接收推送通知，此功能将很有用。

你可以轻松测试推送通知，无需创建发布应用后所需的工作云服务。

1.  **启用模拟。**

    选择“已启用”  后，在仿真器上部署的所有应用都使用模拟引擎，而非 WNS 或 MPN 服务，直到禁用模拟。

2.  **选择某个应用以接收通知。**

    将使用部署到仿真器的支持推送通知的所有应用自动填充AppId  列表。 在下拉列表中选择某个应用。

    如果你在启用模拟后部署另一个支持推送通知的应用，请单击“刷新”  ，以将该应用添加到列表中。

3.  **选择通知通道。**

    在AppId  列表中选择某个应用后，将使用注册选定应用的所有通知通道自动填充“URI”  列表。 在下拉列表中选择某个通知通道。

4.  **选择通知类型。**

    在“URI”  列表中选择某个通知通道后，将使用适用于通知服务的所有类型自动填充“通知类型”  列表。 在下拉列表中选择某个通知类型。

    模拟器使用 URI 格式的通知通道确定应用使用的是 WNS 推送通知还是 MPN 推送通知。

    模拟支持所有通知类型。 默认通知类型为“磁贴”  。

    -   支持以下 WNS 通知类型：

        -   原始
        -   toast

            当应用使用 WNS 通知并且你选择“Toast”  通知类型时，模拟选项卡将显示“标记”  和“组”  字段。 你可以选择这些选项并输入“标记”  和“组”  值，以管理通知中心中的 Toast 通知。

        -   Tile
        -   徽章

    -   支持以下 MPN 通知类型。

        -   原始
        -   toast
        -   Tile

5.  **选择通知模板。**

    在“通知类型”  列表中选择某个通知类型后，将使用适用于通知类型的所有模板自动填充“模板”  列表。 在下拉列表中选择某个模板。

    模拟支持所有模板类型。

6.  **可选择更改通知负载。**

    在“模板”  列表中选择某个模板后，将使用适用于该模板的示例负载自动填充“通知负载”  文本框。 在“通知负载”  文本框中查看示例负载。

    -   可以发送示例负载，无需更改。

    -   可以在文本框中编辑示例负载。

    -   可以单击“加载”  以从某个文本或 XML 文件加载负载。

    -   可以单击“保存”  以保存负载的 XML 文本，以供以后再次使用。

    模拟器不会验证负载的 XML 文本。

7.  **发送推送通知。**

    单击“发送”  以将推送通知传递到选定的应用。

    屏幕将显示一条指示成功或失败的消息。

![模拟器的“其他工具”中的“通知”页面](images/em-notifications.png)

## <a name="sensors"></a>传感器

通过仿真器的“其他工具”  的“传感器”  选项卡，测试应用在不具有所有可选传感器或相机功能的低成本手机上的工作方式。 如果你的应用使用相机或某些手机传感器，并且你希望你的应用可以尽可能扩大市场范围，此功能将很有用。

-   默认情况下，所有传感器在“可选传感器”  列表中都处于启用状态。 选择或清除单个复选框以启用或禁用单个传感器。
-   更改你的选择后，请单击“应用”  。 然后，你必须重新启动模拟器。
-   如果你进行了某些更改，当你切换选项卡或关闭“其他工具”  窗口，但没有单击“应用”  时，将丢弃你的更改。
-   你的设置将保留在模拟器会话之间，直到你更改或重置它们。 如果你捕获了一个检查点，这些设置将与该检查点一起保存。 仅为你使用的特定仿真器（例如“仿真器 8.1 WVGA 4" 512MB”  ）保留设置。

![模拟器的“其他工具”中的“传感器”页面](images/em-sensors.png)

**传感器选项**

可以启用或禁用以下可选硬件传感器：

-   环境光传感器
-   前置摄像头
-   陀螺仪
-   指南针（磁力计）
-   NFC
-   软件按钮（仅在某些高分辨率的模拟器映像上）。

**相机选项**

通过在“可选传感器”  列表中选中或清除复选框，可以启用或禁用可选前置摄像头。

还可以在“相机”  下拉列表中选择以下相机配置文件之一。

-   Windows Phone 8.0 相机。
-   Windows Phone 8.1 相机。

下面是受每个配置文件支持的相机功能列表。

| 功能            | Windows Phone 8.0 相机 | Windows Phone 8.1 相机  |
|--------------------|--------------------------|---------------------------|
| 解决方法         | 640 x 480 (VGA)          | 640 x 480 (VGA) 或更高 |
| Autofocus          | 是                      | 是                       |
| Flash              | 否                       | 是                       |
| 缩放               | 2x（数字或光学）  | 2x（数字或光学）   |
| 视频分辨率   | 640 x 480 (VGA)          | 640 x 480 (VGA) 或更高 |
| 预览分辨率 | 640 x 480 (VGA)          | 640 x 480 (VGA)           |

## <a name="frame-rate-counters"></a>帧速率计数器

在 Windows Phone 模拟器中使用帧速率计数器以监视运行的应用的性能。

![Windows Phone 模拟器中的帧速率计数器](images/em-frameratecounters.PNG)

**帧速率计数器说明**

下表介绍了每个帧速率计数器。

| 帧速率计数器                           | 说明        |
|----------------------------------------------|--------------------|
| 复合（渲染）线程帧速率 (FPS) | 更新的屏幕的速率。  |
| 用户界面线程帧速率 (FPS)       | 正在运行的 UI 线程的速率。    |
| 纹理内存使用情况                         | 正在应用中使用的纹理的视频内存和系统内存副本。    |
| 表面计数器                              | 正被传递到 GPU 以供处理的显式表面的数量。     |
| 中间表面计数器                 | 作为缓存表面的结果而生成的隐式表面的数量。    |
| 屏幕填充率计数器                     | 对于屏幕而言，每帧绘制的像素的数量。 值 1 表示当前屏幕分辨率中的像素数量，例如 480 x 800 像素。 |

**启用和禁用帧速率计数器**

你可以在代码中启用或禁用显示帧速率计数器。 在 Visual Studio 中创建 Windows Phone 应用项目时，默认情况下，以下用于启用帧速率计数器的代码将添加在文件 App.xaml.cs 中。 若要禁用帧速率计数器，请将 **EnableFrameRateCounter** 设置为 **false** 或注释掉代码行。

> [!div class="tabbedCodeSnippets"]
>```csharp
>// Show graphics profiling information while debugging.
>if (System.Diagnostics.Debugger.IsAttached)
>{
>    // Display the current frame rate counters.
>    Application.Current.Host.Settings.EnableFrameRateCounter = true;
>    
>    // other code…
>}
>```
>```vb
>' Show graphics profiling information while debugging.
>If System.Diagnostics.Debugger.IsAttached Then
>
>    ' Display the current frame rate counters.
>    Application.Current.Host.Settings.EnableFrameRateCounter = True
>
>    ' other code...
>End If
>```

## <a name="known-issues"></a>已知问题

下面是模拟器的已知问题，以及解决问题的建议方法（如果遇到问题）。

### <a name="error-message-failed-while-removing-virtual-ethernet-switch"></a>错误消息：“删除虚拟以太网交换机时失败”

在某些情况下，包括在更新到新的 Windows 10 外部测试版之后，与模拟器相关联的虚拟网络交换机可能会进入无法通过用户界面删除的状态。

若要在此情况下恢复，请在管理员命令提示符下运行“netcfg -d”：`C:\Program Files (x86)\Microsoft XDE\<version>\XdeCleanup.exe`。 当命令结束运行时，需要重新启动计算机才能完成恢复过程。

**注意** 此命令将删除所有网络设备，而不仅仅是与模拟器相关联的设备。 当计算机再次启动时，将自动发现所有硬件网络设备。
 
### <a name="unable-to-launch-the-emulators"></a>无法启动模拟器

Microsoft 模拟器包括 XDECleanup.exe，此工具用于删除所有虚拟机、diff 磁盘和特定于模拟器的网络交换机，它已随附于模拟器 (XDE) 二进制文件。 如果仿真器虚拟机进入错误状态，你应使用此工具将其清理。 从管理员命令提示符运行该工具：`C:\Program Files (x86)\Microsoft XDE\<version>\XdeCleanup.exe`

> **注意** XDECleanup.exe 删除所有特定于模拟器的 Hyper-V VM，并且它还将删除任何 VM 检查点或已保存的状态。

### <a name="uninstall-windows-10-for-mobile-image"></a>卸载 Windows 10 移动版映像

当你安装仿真器时，Windows 10 移动版 VHD 映像已安装，它在控制面板的“程序和功能”  列表中获取自己的条目。 如果想要卸载该映像，请在已安装的程序列表中找到“Windows 10 移动版映像 - \<version\>”，右键单击它，然后选择“卸载” 。

在当前版本中，必须随后手动删除模拟器的 VHD 文件。 如果已将模拟器安装到默认路径，则 VHD 文件位于 C:\\Program Files (x86)\\Windows Kits\\10\\Emulation\\Mobile\\\<version\>\\flash.vhd。

### <a name="how-to-disable-hardware-accelerated-graphics"></a>如何禁用硬件加速图形

默认情况下，Windows 10 移动版模拟器将使用硬件加速图形。 如果你在启动已启用硬件加速的模拟器时遇到了问题，可以通过设置注册表值将其关闭。

若要禁用硬件加速，请执行以下操作：

1. 启动“注册表编辑器”。
2. 创建以下注册表子项（如果不存在）：HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Xde\10.0
3. 右键单击 10.0 文件夹、指向“新建”  ，然后单击“DWORD 值”  。
4. 键入DisableRemoteFx  ，然后按 Enter。
5. 双击DisableRemoteFx  、在“值”  数据框中输入 1、选择“十进制”  选项，然后单击“确定”  。
6. 关闭注册表编辑器。

**注意：** 设置此注册表值后，必须针对你在 Visual Studio 中启动的配置删除 Hyper-V 管理器中的虚拟机，然后重新启动使用软件呈现图形的仿真器。

## <a name="support-resources"></a>支持资源

若要在开始使用 Windows 10 工具时查找答案并解决问题，请访问 [Windows 10 工具论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=Win10SDKToolsIssues)。 若要查看 Windows 10 开发的所有论坛，请访问[此链接](https://social.msdn.microsoft.com/Forums/windowsapps/en-US/home?forum=wpdevelop)。

## <a name="related-topics"></a>相关主题

* [在模拟器中运行 Windows Phone 应用](/visualstudio/debugger/run-windows-phone-apps-in-the-emulator?view=vs-2015&preserve-view=true)
* [Windows 和 Windows Phone SDK 存档](https://developer.microsoft.com/windows/downloads/sdk-archive)
 
