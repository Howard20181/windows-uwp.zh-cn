---
title: 启动 Windows 设置应用
description: 了解如何从你的应用启动 Windows 设置应用。 本主题介绍了 ms-settings URI 方案。 使用此 URI 方案将 Windows 设置应用启动到特定设置页面。
ms.assetid: C84D4BEE-1FEE-4648-AD7D-8321EAC70290
ms.date: 04/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: 19H1
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: 1ef32816d04516bf4c8ce8677d7f682d2d59f657
ms.sourcegitcommit: db48036af630f33f0a2f7a908bfdfec945f3c241
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84437168"
---
# <a name="launch-the-windows-settings-app"></a>启动 Windows 设置应用

**重要的 API**

-   [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync)
-   [**PreferredApplicationPackageFamilyName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname)
-   [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview)

了解如何启动 Windows"设置"应用。 本主题介绍 **ms-settings:** URI 方案。 使用此 URI 方案将 Windows 设置应用启动到特定设置页面。

启动为设置应用是编写隐私感知应用的重要组成部分。 如果你的应用无法访问敏感资源，我们建议为用户提供到该资源的隐私设置的方便链接。 有关详细信息，请参阅[隐私感知应用指南](https://docs.microsoft.com/windows/uwp/security/index)。

## <a name="how-to-launch-the-settings-app"></a>如何启动“设置”应用

若要启动 "**设置**" 应用，请使用 `ms-settings:` URI 方案，如以下示例中所示。

在此示例中，超链接 XAML 控件用于使用 `ms-settings:privacy-microphone` URI 启动麦克风的隐私设置页面。

```xml
<!--Set Visibility to Visible when access to the microphone is denied -->
<TextBlock x:Name="LocationDisabledMessage" FontStyle="Italic"
                 Visibility="Collapsed" Margin="0,15,0,0" TextWrapping="Wrap" >
          <Run Text="This app is not able to access the microphone. Go to " />
              <Hyperlink NavigateUri="ms-settings:privacy-microphone">
                  <Run Text="Settings" />
              </Hyperlink>
          <Run Text=" to check the microphone privacy settings."/>
</TextBlock>
```

此外，你的应用可以调用 [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) 方法来启动**设置**应用。 此示例演示如何使用 `ms-settings:privacy-webcam` URI 启动到相机的隐私设置页面。

```cs
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-webcam"));
```
```cppwinrt
bool result = co_await Windows::System::Launcher::LaunchUriAsync(Windows::Foundation::Uri(L"ms-settings:privacy-webcam"));
```

上述代码会启动相机的隐私设置页面：

![相机隐私设置。](images/privacyawarenesssettingsapp.png)

有关启动 URI 的详细信息，请参阅[启动 URI 的默认应用](launch-default-app.md)。

## <a name="ms-settings-uri-scheme-reference"></a>ms-settings: URI 方案引用

使用以下 URI 以打开“设置”应用的各个页面。

> 请注意，设置页面是否可用因 Windows SKU 而异。 并非 Windows 10 桌面版中所有可用的设置在 Windows 10 移动版上也都可用，反之亦然。 备注列中还记录了使页面可用所必须满足的附加要求。

<!-- TODO: 
* ms-settings:controlcenter
* ms-settings:holographic
* ms-settings:keyboard-advanced
* ms-settings:regionlanguage-adddisplaylanguage (crashed)
* ms-settings:regionlanguage-setdisplaylanguage (crashed)
* ms-settings:signinoptions-launchpinenrollment
* ms-settings:storagecleanup
* ms-settings:update-security -->

## <a name="accounts"></a>帐户

|“设置”页| URI |
|-------------|-----|
| 访问工作单位或学校 | ms-settings:workplace |
| 电子邮件和应用帐户  | ms-settings:emailandaccounts |
| 家人和其他人 | ms-settings:otherusers |
| 设置展台 | ms-设置： assignedaccess |
| 登录选项 | ms-settings:signinoptions<br>ms-settings:signinoptions-dynamiclock |
| 同步设置 | ms-settings:sync |
| Windows Hello 设置 | ms-settings:signinoptions-launchfaceenrollment<br>ms-settings:signinoptions-launchfingerprintenrollment |
| 你的信息 | ms-settings:yourinfo |

## <a name="apps"></a>应用

|“设置”页| URI |
|-------------|-----|
| 应用和功能 | ms-settings:appsfeatures |
| 应用功能 | ms-settings:appsfeatures-app（应用的重置、管理加载项和可下载内容等操作）|
| 网站应用 | ms-settings:appsforwebsites |
| 默认应用 | ms-settings:defaultapps |
| 管理可选功能 | ms-settings:optionalfeatures |
| 离线地图 | ms-settings:maps<br/>ms-settings： map-downloadmaps （下载地图） |
| 启动应用 | ms-settings:startupapps |
| 视频播放 | ms-settings:videoplayback |

## <a name="cortana"></a>Cortana

|“设置”页| URI |
|-------------|-----|
| 跨设备的 Cortana | ms-settings:cortana-notifications |
| 更多详细信息 | ms-settings:cortana-moredetails |
| 权限 & 历史记录 | ms-settings:cortana-permissions |
| 搜索窗口 | ms-settings： cortana-windowssearch |
| 与 Cortana 交谈 | ms-settings:cortana-language<br/>ms-设置： cortana<br/>ms-settings： cortana-talktocortana |

> [!NOTE] 
> 如果计算机设置为当前未提供 Cortana 或 Cortana 已禁用的区域，则桌面上的此设置部分将称为 "搜索"。 在这种情况下，将不会列出 cortana 特定页面（在我的设备上 Cortana，并与 Cortana 通信）。 

## <a name="devices"></a>设备

|“设置”页| URI |
|-------------|-----|
| 自动播放 | ms-settings:autoplay |
| Bluetooth | ms-settings:bluetooth |
| 连接的设备 | ms-settings:connecteddevices |
| 默认相机 | ms-设置：照相机（**在 Windows 10 中已弃用，版本1809及更高版本**） |
| 鼠标和触摸板 | ms-settings:mousetouchpad（仅具有触摸板的设备可使用触摸板设置） |
| 触控笔和 Windows Ink | ms-settings:pen |
| 打印机和扫描仪 | ms-settings:printers |
| 触摸板 | ms-settings:devices-touchpad（仅在存在触摸板硬件时可用） |
| Typing | ms-settings:typing |
| USB | ms-settings:usb |
| 滚轮 | ms-settings:wheel（仅在“拨号”配对成功后可用） |
| 你的手机 | ms-settings:mobile-devices  |

## <a name="ease-of-access"></a>轻松访问

|“设置”页| URI |
|-------------|-----|
| 音频 | ms-settings:easeofaccess-audio |
| 隐藏式字幕 | ms-settings:easeofaccess-closedcaptioning |
| 颜色筛选器 | ms-settings： easeofaccess-colorfilter |
| 光标和指针大小 | ms-settings： easeofaccess-cursorandpointersize |
| 显示 | ms-settings:easeofaccess-display |
| 目视控制 | ms-settings:easeofaccess-eyecontrol |
| 字体 | ms-settings:fonts |
| 高对比度 | ms-settings:easeofaccess-highcontrast |
| 键盘 | ms-settings:easeofaccess-keyboard |
| 放大镜 | ms-settings:easeofaccess-magnifier |
| 鼠标 | ms-settings:easeofaccess-mouse |
| 讲述人 | ms-settings:easeofaccess-narrator |
| 其他选项 | ms-settings： easeofaccess-otheroptions （**在 Windows 10 版本1809及更高版本中已弃用**） |
| 语音 | ms-settings:easeofaccess-speechrecognition |

## <a name="extras"></a>附加信息

|“设置”页| URI |
|-------------|-----|
| 附加信息 | ms 设置：附加（仅在安装了 "设置应用" 时可用，例如，由第三方提供） |

## <a name="gaming"></a>游戏

|“设置”页| URI |
|-------------|-----|
| 广播 | ms-settings:gaming-broadcasting |
| 游戏栏 | ms-settings:gaming-gamebar |
| 游戏 DVR | ms-settings:gaming-gamedvr |
| 游戏模式 | ms-settings:gaming-gamemode |
| 全屏玩游戏 | ms-settings:quietmomentsgame |
| TruePlay | ms-设置：游戏-trueplay （**在 Windows 10 版本1809及更高版本中已弃用**） |
| Xbox 网络 | ms-settings:gaming-xboxnetworking |

## <a name="home-page"></a>主页

|“设置”页| URI |
|-------------|-----|
| “设置”主页 | ms-settings: |

## <a name="mixed-reality"></a>混合现实

> [!NOTE]
> 仅当安装了混合现实门户应用时，这些设置才可用。

| “设置”页 | URI |
|---------------|-----|
| 音频和语音 | ms-settings:holographic-audio |
| 环境 | ms 设置：隐私-全息环境 |
| 耳机显示 | ms-设置：全息耳机 |
| 卸载 | ms-设置：全息版-管理 |

## <a name="network--internet"></a>网络和 Internet

|“设置”页| URI |
|-------------|-----|
| 飞行模式 | ms-settings:network-airplanemode<br/>ms-settings:proximity |
| 手机网络和 SIM 卡 | ms-settings:network-cellular |
| 数据用途 | ms-settings:datausage |
| 拨号 | ms-settings:network-dialup |
| DirectAccess | ms-settings:network-directaccess（仅在启用 DirectAccess 后可用） |
| 以太网 | ms-settings:network-ethernet |
| 管理已知网络 | ms-settings:network-wifisettings |
| 移动热点 | ms-settings:network-mobilehotspot |
| NFC | ms-settings:nfctransactions |
| 代理 | ms-settings:network-proxy |
| 状态 | ms-settings:network-status<br/>ms-设置：网络 |
| VPN | ms-settings:network-vpn |
| WLAN | ms-settings:network-wifi（仅当设备具有 WLAN 适配器时可用） |
| WLAN 呼叫 | ms-settings:network-wificalling（仅在启用 WLAN 呼叫后可用） |

## <a name="personalization"></a>个性化设置

|“设置”页| URI |
|-------------|-----|
| 背景 | ms-settings:personalization-background |
| 选择哪些文件夹显示在“开始”菜单上 | ms-settings:personalization-start-places |
| 颜色 | ms-settings:personalization-colors<br/>ms-设置：颜色 |
| 概览 | ms-设置：个性化浏览（**在 Windows 10 中已弃用，版本1809及更高版本**） |
| 锁屏界面 | ms-settings:lockscreen |
| 导航栏 | ms 设置：个性化设置-导航栏（**在 Windows 10 中已弃用，版本1809及更高版本**） |
| 个性化（类别） | ms-settings:personalization |
| 开始 | ms-settings:personalization-start |
| 任务栏 | ms-settings:taskbar |
| 主题 | ms-settings:themes |

## <a name="phone"></a>电话

|“设置”页| URI |
|-------------|-----|
| 你的手机 | ms-settings:mobile-devices<br/>ms-设置：移动设备-addphone<br/>ms 设置：移动设备-addphone-direct （打开**手机**应用） |

## <a name="privacy"></a>隐私

|“设置”页| URI |
|-------------|-----|
| 外部设备应用 | ms-settings： accessoryapps （**在 Windows 10 版本1809及更高版本中已弃用**） |
| 帐户信息 | ms-settings:privacy-accountinfo |
| 活动历史记录 | ms-settings:privacy-activityhistory |
| 广告 ID | ms-settings： advertisingid （**在 Windows 10 版本1809及更高版本中已弃用**） |
| 应用诊断 | ms-settings:privacy-appdiagnostics |
| 自动文件下载 | ms-settings:privacy-automaticfiledownloads |
| 后台应用 | ms-settings:privacy-backgroundapps |
| 日历 | ms-settings:privacy-calendar |
| 呼叫历史记录 | ms-settings:privacy-callhistory |
| 照相机 | ms-settings:privacy-webcam |
| 联系人 | ms-settings:privacy-contacts |
| 文档 | ms-settings:privacy-documents |
| 电子邮件 | ms-settings:privacy-email |
| 眼球跟踪器 | ms-settings:privacy-eyetracker（需要眼球跟踪器硬件） |
| 反馈和诊断 | ms-settings:privacy-feedback |
| 文件系统 | ms-settings:privacy-broadfilesystemaccess |
| 常规 | ms 设置：隐私或 ms 设置：隐私-常规 |
| 墨迹 & 键入 |ms-settings:privacy-speechtyping |
| 位置 | ms-settings:privacy-location |
| 消息传递 | ms-settings:privacy-messaging |
| 麦克风 | ms-settings:privacy-microphone |
| 移动 | ms-settings:privacy-motion |
| 通知 | ms-settings:privacy-notifications |
| 其他设备 | ms-settings:privacy-customdevices |
| 电话呼叫 | ms 设置：隐私-phonecalls |
| 图片 | ms-settings:privacy-pictures |
| 无线电收发器 | ms-settings:privacy-radios |
| 语音 | ms 设置：隐私-语音 |
| 任务 | ms-settings:privacy-tasks |
| 视频 | ms-settings:privacy-videos |
| 语音激活 | ms 设置：隐私-voiceactivation |

## <a name="surface-hub"></a>Surface Hub

|“设置”页| URI |
|-------------|-----|
| 帐户 | ms-settings:surfacehub-accounts |
| 会话清理 | ms-settings:surfacehub-sessioncleanup |
| 团队会议 | ms-settings:surfacehub-calling |
| 团队设备管理 | ms-settings:surfacehub-devicemanagenent |
| 欢迎屏幕 | ms-settings:surfacehub-welcome |

## <a name="system"></a>System

|“设置”页| URI |
|-------------|-----|
| 关于 | ms-settings:about |
| 高级显示设置 | ms-settings:display-advanced（仅适用于支持高级显示选项的设备） |
| 应用卷和设备首选项 | ms-设置：应用-卷（**添加到 Windows 10，版本 1903**）|
| 节电模式 | ms-settings:batterysaver（仅在具有电池的设备[如平板电脑]上可用） |
| “节电模式”设置 | ms-settings:batterysaver-settings（仅在具有电池的设备[如平板电脑]上可用） |
| 电池使用 | ms-settings:batterysaver-usagedetails（仅在具有电池的设备[如平板电脑]上可用） |
| 剪贴板 | ms-设置：剪贴板 |
| 显示 | ms-settings:display |
| 默认保存位置 | ms-settings:savelocations |
| 显示 | ms-settings:screenrotation |
| 复制我的屏幕 | ms-settings:quietmomentspresentation |
| 在这些时间内 | ms-settings:quietmomentsscheduled |
| 加密 | ms-settings:deviceencryption |
| 专注助手 | ms-settings:quiethours <br> ms-settings:quietmomentshome |
| 图形设置 | ms-settings:display-advancedgraphics（仅适用于支持高级图形选项的设备） |
| 消息传递 | ms-settings:messaging |
| 多任务 | ms-settings:multitasking |
| 夜灯设置 | ms-settings:nightlight |
| 电话 | ms-settings:phone-defaultapps |
| 投影到这台电脑 | ms-settings:project |
| 共享体验 | ms-settings:crossdevice |
| 平板模式 | ms-settings:tabletmode |
| 任务栏 | ms-settings:taskbar |
| 通知和操作 | ms-settings:notifications |
| 远程桌面 | ms-settings:remotedesktop |
| 电话 | ms-设置： phone （**在 Windows 10 中已弃用，版本1809及更高版本**） |
| 电源和睡眠 | ms-settings:powersleep |
| 声音 | ms-设置：声音 |
| 存储 | ms-settings:storagesense |
| 存储感知 | ms-settings:storagepolicies |

## <a name="time-and-language"></a>时间和语言

|“设置”页| URI |
|-------------|-----|
| 日期和时间 | ms-settings:dateandtime |
| 日本输入法设置 | ms-settings:regionlanguage-jpnime（在安装了 Microsoft 日本输入法编辑器的情况下可用） |
| 区域 | ms-设置： regionformatting |
| 语言 | ms-设置：键盘<br/>ms-settings:regionlanguage<br/>ms-settings： regionlanguage-bpmfime<br/>ms-settings： regionlanguage-cangjieime<br/>ms-settings： regionlanguage-chsime-domainlexicon<br/>ms-settings： regionlanguage-chsime-keyconfig<br/>ms-settings： regionlanguage-chsime-udp<br/>ms-settings： regionlanguage-chsime-五笔-udp<br/>ms-settings： regionlanguage-quickime |
| 拼音输入法设置 | ms-settings:regionlanguage-chsime-pinyin（在安装了 Microsoft 拼音输入法编辑器的情况下可用） |
| 语音 | ms-settings:speech |
| 五笔输入法设置  | ms-settings:regionlanguage-chsime-wubi（在安装了 Microsoft 五笔输入法编辑器的情况下可用） |

## <a name="update--security"></a>更新和安全

|“设置”页| URI |
|-------------|-----|
| 激活 | ms-settings:activation |
| 备份 | ms-settings:backup |
| 传递优化 | ms-settings:delivery-optimization |
| 查找我的设备 | ms-settings:findmydevice |
| 面向开发人员 | ms-settings:developers |
| 恢复 | ms-settings:recovery |
| 疑难解答 | ms-settings:troubleshoot |
| Windows 安全性 | ms-settings:windowsdefender |
| Windows 预览体验计划 | ms-settings:windowsinsider（仅当用户在 WIP 中注册时显示）<br/>ms-settings： windowsinsider-optin |
| Windows 更新 | ms-settings:windowsupdate<br>ms-settings:windowsupdate-action |
| Windows 更新 - 高级选项 | ms-settings:windowsupdate-options |
| Windows 更新 - 重启选项 | ms-settings:windowsupdate-restartoptions |
| Windows 更新 - 查看更新历史记录 | ms-settings:windowsupdate-history |

## <a name="user-accounts"></a>用户帐户

|“设置”页| URI |
|-------------|-----|
| 设置 | ms-settings:workplace-provisioning（仅在企业部署了预配包后可用） |
| 设置 | ms-settings:workplace-provisioning（仅在移动设备和企业部署了预配包后可用） |
| Windows Anywhere | ms-settings:windowsanywhere（设备必须支持 Windows Anywhere） |
