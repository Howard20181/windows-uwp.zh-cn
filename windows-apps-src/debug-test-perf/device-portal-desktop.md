---
ms.assetid: 5c34c78e-9ff7-477b-87f6-a31367cd3f8b
title: 适用于桌面设备的 Windows 设备门户
description: 了解 Windows 设备门户如何在台式计算机上提供设置、诊断和自动化功能。
ms.date: 01/08/2021
ms.topic: article
ms.custom: contperf-fy21q3
keywords: windows 10, uwp, 设备门户
ms.localizationpriority: medium
ms.openlocfilehash: 11bfc81f045887dfdbba9d380eedd6b9ff40709a
ms.sourcegitcommit: 02d220ef0ec0ecd7ed733086ba164ee9653d9602
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2021
ms.locfileid: "98055980"
---
# <a name="windows-device-portal-for-desktop"></a>适用于桌面设备的 Windows 设备门户

Windows 设备门户 (WDP) 是一个设备管理和调试工具，使用它，可以从 web 浏览器通过 HTTP 配置和管理设备设置并查看诊断信息。 有关其他设备上的 WDP 详细信息，请参阅 [Windows 设备门户概述](device-portal.md)。

可以使用 WDP 进行以下操作：

- 管理设备设置（类似于“Windows 设置”应用）
- 查看并操作正在运行的进程的列表
- 安装、删除、启动和终止应用
- 更改 WLAN 配置文件、查看信号强度并查看 ipconfig 详细信息
- 查看 CPU、内存、I/O、网络和 GPU 使用情况的实时图
- 收集进程转储
- 收集 ETW 跟踪
- 操作旁加载应用的独立存储

## <a name="set-up-windows-device-portal-on-a-desktop-device"></a>在桌面设备上设置 Windows 设备门户

### <a name="turn-on-developer-mode"></a>打开开发人员模式

从 Windows 10 版本 1607 开始，适用于桌面的一些较新功能仅在启用开发人员模式时可用。 有关如何启用开发人员模式的信息，请参阅[启用设备进行开发](/windows/apps/get-started/enable-your-device-for-development)。

> [!IMPORTANT]
> 有时，由于网络或兼容性问题，开发人员模式无法在你的设备上正确安装。 有关解决这些问题的帮助，请参阅[启用设备进行开发的相关部分](/windows/apps/get-started/enable-your-device-for-development#failure-to-install-developer-mode-package)。

### <a name="turn-on-windows-device-portal"></a>打开 Windows 设备门户

可以在“设置”的“面向开发人员”部分中启用 WDP。 当你启用它时，你还必须创建相应的用户名和密码。 不要使用你的 Microsoft 帐户或其他 Windows 凭据。

![“设置”应用的“Windows 设备门户”部分](images/device-portal/device-portal-desk-settings.png)

启用 WDP 后，你将在该部分的底部看到 Web 链接。 记下追加到所列 URL 末尾的端口号：此端口号在启用 WDP 时随机生成，但应在桌面重启后保持一致。

这些链接提供了连接到 WDP 的两种方法：通过本地网络（包括 VPN）或通过本地主机。 连接后，你应该会看到类似下面的屏幕：

![Windows 设备门户](images/device-portal/device-portal-example.png)

### <a name="turn-off-windows-device-portal"></a>关闭 Windows 设备门户

可以在“Windows 设置”的“面向开发人员”部分中禁用 WDP 。

### <a name="connect-to-windows-device-portal"></a>连接到 Windows 设备门户

要通过本地主机进行连接，请打开浏览器窗口，然后输入此处显示的 URI 之一（根据你正在使用的连接类型）。

- Localhost：`http://127.0.0.1:<PORT>` 或 `http://localhost:<PORT>`
- 本地网络：`https://<IP address of the desktop>:<PORT>`

身份验证和安全通信要求使用 HTTPS。

如果在信任本地网络上的所有用户、设备上没有任何私人信息并且具有独特要求的受保护环境（例如测试实验室）中使用 WDP，你可以禁用“身份验证”选项。 这支持未加密的通信，并允许任何拥有你的计算机 IP 地址的用户连接到通信并控制它。

## <a name="windows-device-portal-content"></a>Windows 设备门户内容

WDP 提供了以下一组页面。

- 应用管理器
- Xbox Live
- 文件资源管理器
- 正在运行的进程
- 性能
- 调试
- ETW（Windows 事件跟踪）日志记录
- 性能跟踪
- 设备管理器
- Bluetooth
- 网络
- 崩溃数据
- 功能
- 混合现实
- 流式安装调试程序
- 位置
- Scratch

## <a name="using-windows-device-portal-to-test-and-debug-msix-apps"></a>使用 Windows 设备门户测试和调试 MSIX 应用


> [!VIDEO https://www.youtube.com/embed/PdgXeOMt4hk]


## <a name="more-windows-device-portal-options"></a>Windows 设备门户的更多选项

### <a name="registry-based-configuration"></a>基于注册表的配置

如果你希望为 WDP 选择端口号（如 80 和 443），你可以设置以下 RegKey：

- （位于 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WebManagement\Service` 下面）
    - `UseDynamicPorts`：一个必需的 DWORD。 将其设置为 0，以便保留你已选择的端口号。
    - `HttpPort`：一个必需的 DWORD。 包含 WDP 用于侦听 HTTP 连接的端口号。
    - `HttpsPort`：一个必需的 DWORD。 包含 WDP 用于侦听 HTTPS 连接的端口号。
    
在相同的 RegKey 路径下，你也可以关闭身份验证要求：
- `UseDefaultAuthorizer` - `0` 为禁用，`1` 为启用。  
    - 这可控制每个连接的基本身份验证要求以及从 HTTP 到 HTTPS 的重定向。  
    
### <a name="command-line-options-for-windows-device-portal"></a>Windows 设备门户的命令行选项

通过管理命令提示符，可以启用和配置 WDP 的部件。 要查看版本上支持的最新命令集，可以运行 `webmanagement /?`

- `sc start webmanagement` 或 `sc stop webmanagement`
    - 打开或关闭该服务。 这仍需要启用开发人员模式。 
- `-Credentials <username> <password>` 
    - 设置 WDP 的用户名和密码。 用户名必须符合基本身份验证标准，因此不得包含冒号 (:) 且应从标准 ASCII 字符（例如 [a-zA-Z0-9]）中构建，因为浏览器不会以标准方式解析全字符集。  
- `-DeleteSSL` 
    - 这将重置用于 HTTPS 连接的 SSL 证书缓存。 如果你遇到无法避免的 TLS 连接错误（而不是预期的证书警告），此选项可为你解决该问题。 
- `-SetCert <pfxPath> <pfxPassword>`
    - 有关详细信息，请参阅[使用自定义的 SSL 证书预配 Windows 设备门户](./device-portal-ssl.md)。  
    - 这将允许你安装自己的 SSL 证书来修复通常显示在 WDP 中的 SSL 警告页面。 
- `-Debug <various options for authentication, port selection, and tracing level>`
    - 运行独立版本的具有特定配置和可见调试消息的 WDP。 这对于生成[打包的插件](./device-portal-plugin.md)最为有用。 
    - 有关如何作为系统运行此项以完全测试你的打包插件的详细信息，请参阅 [MSDN 杂志文章](/archive/msdn-magazine/2017/october/windows-device-portal-write-a-windows-device-portal-packaged-plug-in)。

## <a name="troubleshooting"></a>疑难解答

下面介绍在设置 Windows 设备门户时可能会遇到的一些常见错误。

### <a name="windowsupdatesearch-returns-invalid-number-of-updates-0x800f0950-cbs_e_invalid_windows_update_count"></a>WindowsUpdateSearch 返回无效的更新数 (0x800f0950 CBS_E_INVALID_WINDOWS_UPDATE_COUNT)

尝试在 Windows 10 的预发行版上安装开发人员包时，可能会收到此错误。 这些按需功能 (FoD) 包托管在 Windows 更新上，要在预发行版本上下载它们，则需要选择加入外部测试。 如果安装没有选择加入外部测试以查找正确的版本和环组合，则将无法下载有效负载。 仔细检查以下内容：

1. 导航到“设置”>“更新和安全性”>“Windows 预览体验计划”，并确认“Windows 预览体验成员帐户”部分中包含正确的帐户信息。 如果没有看到该部分，请选择“链接 Windows 预览体验成员帐户”，添加电子邮件帐户，并确认它显示在“Windows 预览体验成员帐户”标题下（可能需要再次选择“链接 Windows 预览体验成员帐户”以实际链接新添加的帐户）。
 
2. 在“要接收哪种类型的内容?”下，确保选中“Windows 积极开发”。
 
3. 在“要以什么进度来获取新版本?”下，确保选中“Windows 预览体验 - 快”。
 
4. 现即可安装 FoDs。 如果确认使用的是 Windows 预览体验 - 快，但仍然无法安装 FoDs，请提供反馈并将日志文件附加在 C:\Windows\Logs\CBS 下。

### <a name="sc-startservice-openservice-failed-1060-the-specified-service-does-not-exist-as-an-installed-service"></a>[SC] StartService：OpenService FAILED 1060：指定的服务不作为已安装的服务存在

如果未安装开发人员包，则可能会收到此错误。 没有开发人员包，就没有 Web 管理服务。 尝试再次安装开发人员包。

### <a name="cbs-cannot-start-download-because-the-system-is-on-metered-network-cbs_e_metered_network"></a>CBS 无法开始下载，因为系统位于按流量计费的网络 (CBS_E_METERED_NETWORK)

如果使用的是按流量计费的 Internet 连接，则可能会收到此错误。 你将无法通过按流量计费的连接下载开发人员包。

## <a name="see-also"></a>请参阅

* [Windows 设备门户概述](device-portal.md)
* [Windows 设备门户核心 API 参考](./device-portal-api-core.md)