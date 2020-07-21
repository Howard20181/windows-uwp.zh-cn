---
ms.assetid: bf0a8b01-79f1-4944-9d78-9741e235dbe9
title: 适用于 HoloLens 的 Device Portal
description: 了解适用于 HoloLens 的 Windows Device Portal 是如何支持你远程配置和管理你的 HoloLens 设备。
ms.date: 01/03/2019
ms.topic: article
keywords: Windows 10, uwp, 设备门户
ms.localizationpriority: medium
ms.openlocfilehash: 059ce14f85ebe7d955ba2da8897ab47109f74a72
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79401966"
---
# <a name="device-portal-for-hololens"></a>适用于 HoloLens 的 Device Portal


## <a name="set-up-device-portal-on-hololens"></a>在 HoloLens 上设置 Device Portal

### <a name="enable-device-portal"></a>启用 Device Portal

1. 打开 HoloLens 的电源，然后戴上设备。
2. 在 HoloLens（第一代）上执行[开始手势](https://docs.microsoft.com/hololens/hololens2-basic-usage#start-gesture)或[开花手势](https://developer.microsoft.com/mixed-reality#Bloom)，以启动主菜单。
3. 凝视“设置”磁贴，然后在 HoloLens（第一代）上执行[敲击](https://developer.microsoft.com/mixed-reality#Press_and_release)手势，或者在 HoloLens 2 上通过[触摸或手部射线](https://docs.microsoft.com/hololens/hololens2-basic-usage)选择该磁贴  。 在选择“设置”应用后，将启动该应用。
4. 选择“更新”  菜单项。
5. 选择“面向开发人员”  菜单项。
6. 启用“开发人员模式”  。
7. [向下滚动](https://developer.microsoft.com/mixed-reality#Navigation)，然后启用 Device Portal。


### <a name="pair-your-device"></a>配对设备

#### <a name="connect-over-wi-fi"></a>通过 WLAN 连接 

1. 将 HoloLens 连接到 WLAN。
2. 查找设备的 IP 地址。 在设备上的“设置”>“网络和 Internet”>“Wi-Fi”>“硬件属性”下查找 IP 地址  。 你还可以问“你好小娜，我的 IP 地址是多少？”

3. 在电脑上的 Web 浏览器中，转到 `https://<YOUR_HOLOLENS_IP_ADDRESS>`
    - 浏览器中将显示以下消息：“此网站的安全证书有问题。” 由于颁发给 Device Portal 的证书是测试证书，因此会显示上述消息。 你可以暂时忽略此证书错误并继续。

#### <a name="connect-over-usb"></a>通过 USB 连接 

1. 安装工具，以确保 Windows 10 开发人员工具随附的 Visual Studio Update 1 已安装在电脑上。 这支持 USB 连接。
2. 使用适用于 HoloLens（第一代）的 micro-USB 数据线或适用于 HoloLens 2 的 USB-C 数据线将 HoloLens 连接到电脑。
3. 通过电脑上的 Web 浏览器，转到 `http://127.0.0.1:10080`。

> [!IMPORTANT]
> 如果你的电脑找不到设备，请尝试使用 HoloLens 设备的实际网络 IP 地址，而不是 `http://127.0.0.1:10080`。

#### <a name="connect-to-an-emulator"></a>连接到仿真器 

也可以将 Device Portal 与仿真器结合使用。 若要连接到 Device Portal，请使用工具栏。 单击此图标：
- 打开设备门户：在仿真器中打开 HoloLens OS 的 Windows 设备门户。

#### <a name="create-a-username-and-password"></a>创建用户名和密码 

首次连接到 HoloLens 上的 Device Portal 时，需要创建用户名和密码。
1. 在电脑上的 Web 浏览器中，输入 HoloLens 的 IP 地址。 将打开“设置访问”页面。
2. 单击或点击“请求 PIN”，然后查看 HoloLens 屏幕以获取生成的 PIN。
3. 输入设备文本框上显示的 PIN。
4. 输入将用于连接到 Device Portal 的用户名。 无需使用 Microsoft 帐户 (MSA) 名称或域名作为用户名。
5. 输入密码并进行确认。 密码长度必须至少为七个字符。 无需使用 MSA 密码或域密码作为密码。
6. 单击“配对”以连接到 HoloLens 上的 Windows Device Portal。

如果你希望能随时更改此用户名和密码，可通过以下方式访问设备的安全页面来重复此过程：单击右上角的“安全”链接或导航到 `https://<YOUR_HOLOLENS_IP_ADDRESS>/devicesecurity.htm`。

#### <a name="security-certificate"></a>安全证书 

如果你在浏览器中看到“证书错误”，可以通过创建与该设备的信任关系来修复该错误。

每个 HoloLens 会生成唯一的自签名证书以供其 SSL 连接使用。 默认情况下，此证书不受电脑的 Web 浏览器信任，因此你可能会看到“证书错误”。 通过从你的 HoloLens 下载此证书（借助 USB 或信任的 WLAN 网络）并在你的电脑上信任该证书，可以安全地连接到设备。
1. 确保你处于安全网络（USB 或信任的 WLAN 网络）中。
2. 从 Device Portal 的“安全”页下载此设备的证书 - 单击图标列表右上角的“安全”链接或导航到 `https://<YOUR_HOLOLENS_IP_ADDRESS>/devicesecurity.htm`

3. 在电脑上安装“受信任的根证书颁发机构”存储中的证书 - 在 Windows 菜单中键入：管理计算机证书并启动小程序。
    - 展开“受信任的根证书颁发机构”文件夹。
    - 单击“证书”文件夹。
    - 在“操作”菜单中选择：“所有任务”>“导入...”
    - 使用从 Device Portal 下载的证书文件，完成“证书导入向导”。

4. 重新启动浏览器。


## <a name="device-portal-pages"></a>Device Portal 页面 

### <a name="home"></a>主页 

Device Portal 会话从主页开始。 可从主页的左侧导航栏访问其他页面。

主页顶部的工具栏提供对常用状态和功能的访问。
- **联机**：指示设备是否已连接到 Wi-Fi。
- **关机**：关闭设备。
- **重启**：关闭再打开设备的电源。
- **安全**：打开“设备安全性”页。
- **冷**：指示设备的温度。
- **交流电源**：指示设备是否已接上电源并正在充电。
- **帮助**：打开 REST 接口文档页。

主页将显示以下信息：
- **设备状态**：监视设备的运行状况并报告致命错误。
- **Windows 信息**：显示 HoloLens 的名称和当前安装的 Windows 版本。
- **首选项**部分包含以下设置：
    - **IPD**：设置瞳孔间距 (IPD)，它是在用户直视前方时两个瞳孔中心之间的距离，以毫米为单位。 该设置将立即生效。 在设置你的设备时，会自动计算该默认值。 此设置仅适用于 HoloLens（第一代），Hololens 2 会计算眼睛位置。  
    - **设备名称**：为 HoloLens 指定一个名称。 更改此值后，必须重新启动设备才能使该值生效。 单击“保存”后，对话框将询问是要立即重新启动设备还是稍后重新启动它。
    - **睡眠设置**：设置当设备已接上电源并使用电池时进入睡眠状态之前要等待的时长。

### <a name="3d-view"></a>3D 视图 

使用“3D 视图”页面可查看 HoloLens 感知周围环境的方式。 使用鼠标即可导览该视图：
- **旋转**：左键单击 + 鼠标；
- **平移**：右键单击 + 鼠标；
- **缩放**：鼠标滚轮滚动。
- **跟踪选项**：通过选中“强制视觉跟踪”可启用持续视觉跟踪。 “暂停”会停止视觉跟踪。
- **视图选项**：设置 3D 视图中的选项：- 跟踪：指示视觉跟踪是否处于活动状态。
- **显示地面**：显示方格形式的地平面。
- **显示锥体**：显示视锥。
- **显示防抖动平面**：显示 HoloLens 用于稳定动作的平面。
- **显示网格**：显示代表你周围环境的图面映射网格。
- **显示细节**：显示手的位置、头部旋转四元数，以及设备原点矢量的实时变化。
- **全屏按钮**：以全屏模式显示 3D 视图。 按 ESC 键可退出全屏视图。

- 图面重构：单击或敲击“更新”可显示设备的最新空间映射网格。 完成全面检查可能需要一些时间，最多只需几秒钟。 在 3D 视图中，网格不会自动更新，必须手动单击“更新”才能获取设备的最新网格。 单击“保存”可在电脑上将当前的空间映射网格保存为 obj 文件。

### <a name="mixed-reality-capture"></a>混合现实捕获 

使用“混合现实捕获”页面可保存 HoloLens 中的媒体流。
- 设置：通过检查以下设置来控制捕获的媒体流：- 全息图：捕获视频流中的全息内容。 呈现全息图时使用的是单声道，而不是立体声。
- **PV 相机**：捕获照片/视频相机中的视频流。
- **麦克风音频**：捕获麦克风阵列中的音频。
- **应用音频**：捕获当前正在运行的应用中的音频。
- **实时预览质量**：选择用于实时预览的屏幕分辨率、帧速率和流处理速率。

- 单击或点击“实时预览”按钮可显示捕获流。 “停止实时预览”会停止捕获流。
- 单击或点击“录制”可使用指定的设置开始录制混合现实流。 “停止录制”会结束录制，并保存该录制。
- 单击或点击“拍摄照片”可从捕获流中捕获静止图像。
- **视频和照片**：显示在设备上捕获的视频和照片列表。

请注意，当你正从 Device Portal 中录制或流处理实时预览时，HoloLens 应用将无法捕获 MRC 照片或视频。

### <a name="system-performance"></a>系统性能 

HoloLens 上的“系统性能”工具有 3 个可以记录的附加指标。 

可用的指标如下所示：
- **SoC 电源**：片上系统的瞬时用电量，为一分钟平均值
- **系统电源**：系统的瞬时用电量，为一分钟平均值
- **帧速率**：每秒帧数、每秒丢失的垂直消隐数和连续丢失的垂直消隐数

### <a name="app-crash-dumps-page"></a>应用故障转储页面 

此页面允许你收集旁加载应用的故障转储。 对于要收集其故障转储的每个应用，请选中对应的“故障转储启用”复选框。 返回到此页面可收集故障转储。 可在 Visual Studio 中打开转储文件以供调试。

### <a name="kiosk-mode"></a>展台模式 

展台模式的启用会限制用户启动新应用或更改正在运行的应用。 启用展台模式后，“绽放”手势和 Cortana 处于禁用状态，并且已放置的应用不会显示在用户周围环境中。

选中“启用展台模式”会将 HoloLens 置于展台模式中。 从启动应用下拉列表中选择启动时要运行的应用。 单击或点击“保存”以提交设置。

请注意，即使展台模式未启用，该应用也会在启动时运行。 选择“无”可使在启动时不运行任何应用。

### <a name="simulation"></a>模拟 

允许你记录和播放用于测试的输入数据。
- **捕获房间**：用于下载模拟房间文件，该文件包含用户周围环境的空间映射网格。 命名该房间，然后单击“捕获”以在电脑上将该数据保存为 .xef 文件。 此房间文件可以加载到 HoloLens 仿真器中。
- **录制**：选中要录制的流、为录制内容命名，然后单击或敲击“录制”开始录制。 使用你的 HoloLens 执行操作，然后单击“停止”可在电脑上将该数据保存为 .xef 文件。 可以在 HoloLens 仿真器或设备上加载此文件。
- **播放**：单击或敲击“上传录制内容”可从电脑中选择 xef 文件，然后将数据发送到 HoloLens。
- **控制模式**：在 HoloLens 上，从下拉列表中选择“默认”或“模拟”，然后单击或敲击“设置”按钮选中该模式。 相反，选择“模拟”会禁用 HoloLens 上的真实传感器，并使用已上载的模拟数据。 如果切换到“模拟”，HoloLens 将不会响应真实用户，除非切换回“默认”。


### <a name="virtual-input"></a>虚拟输入 

将键盘输入从远程计算机发送到 HoloLens。

单击或点击虚拟键盘下的区域，即可将键击发送到 HoloLens。 在“输入文本”文本框中键入，然后单击或点击“发送”以将键击发送到活动应用。

## <a name="see-also"></a>另请参阅

* [Windows 设备门户概述](device-portal.md)
* [设备门户核心 API 参考](https://docs.microsoft.com/windows/uwp/debug-test-perf/device-portal-api-core)（所有 Windows 10 设备通用的 API）
* [设备门户混合现实 API 参考](https://docs.microsoft.com/windows/mixed-reality/device-portal-api-reference)（适用于 HoloLens 的所有 REST API 的扩展列表）
