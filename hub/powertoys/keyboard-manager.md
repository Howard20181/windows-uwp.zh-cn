---
title: 适用于 Windows 10 的 PowerToys 键盘管理器实用工具
description: 允许用户重新定义键盘上的键的实用工具
ms.date: 12/02/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a8ffd782a1b23d1e439be0462300ebdf20593913
ms.sourcegitcommit: 375cf20e0583335805ec246d65819dc1674a2e32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98240997"
---
# <a name="keyboard-manager-utility"></a>键盘管理器实用工具

PowerToys 键盘管理器使你能够重新定义键盘上的键。

例如，你可以在键盘上为字母<kbd>D</kbd>交换字母<kbd>A</kbd> 。 当你选择 <kbd>某个</kbd> 键时，将显示一个 <kbd>D</kbd> 。

![PowerToys 键盘管理器重新映射密钥屏幕截图](../images/powertoys-keyboard-remap.png)

还可以交换快捷键组合。 例如，快捷键（ <kbd>Ctrl +</kbd> + <kbd></kbd>）将在 Microsoft Word 中复制文本。 通过 PowerToys 键盘管理器实用工具，可以交换该快捷方式，使其<kbd>⊞ Win</kbd> + <kbd>C</kbd>) 。 现在， <kbd>⊞ Win</kbd> + <kbd>C</kbd>) 会复制文本。 如果未在 PowerToys 键盘管理器中指定目标应用程序，则快捷方式交换将跨 Windows 全局应用。

必须启用 PowerToys 键盘管理器 (，并在后台) 运行，以便重新映射要应用的密钥和快捷方式。 如果 PowerToys 未运行，将不再应用密钥重新映射。

![PowerToys 键盘管理器重新映射快捷方式屏幕截图](../images/powertoys-keyboard-shortcuts.png)

> [!NOTE]
> 某些快捷键是为操作系统保留的，无法替换。 不能重新映射的密钥包括：
> - `⊞ Win`+`L`和 `Ctrl`  +  `Alt`  +  `Del` 不能重新映射，因为它们由 Windows OS 保留。
> - `Fn`（功能）键（在大多数情况下）不能重新映射。`F1` - `F12`（和`F13` - `F24`）可以重新映射。
> - `Pause` 将仅发送 sngle 的 keydown 事件。 例如，将其映射到 backspace 键，并按 + 保存仅会删除单个字符。

## <a name="settings"></a>设置

若要创建与键盘管理器的映射，你将需要打开 PowerToys 设置 (在 Windows "开始" 菜单中搜索 "PowerToys" 应用程序，选择它将打开 "PowerToys 设置" 窗口) 。 在 PowerToys 设置中，在 "键盘管理器" 选项卡上，你将看到以下选项：

- 通过选择 "重新<kbd>映射密钥</kbd>" 启动重新映射键盘设置窗口
- 通过选择重新<kbd>映射快捷方式</kbd>来启动重新映射快捷方式设置窗口

## <a name="remap-keys"></a>重新映射密钥

若要重新映射某个密钥，将其更改为新值，请使用 "重新 <kbd>映射密钥</kbd> " 按钮启动 "重新映射键盘设置" 窗口。 首次启动时，不会显示预定义的映射。 您必须选择 <kbd>+</kbd> 按钮以添加新的重新映射。

出现新的重新映射行后，请在 "键" 列中选择要对其输出进行 ***更改** 的键。 选择要在 "映射到" 列中分配的新密钥值。

例如，如果要按 <kbd>A</kbd> 并显示 <kbd>B</kbd>  ：

- 键： "A"
- 映射到： "B"

若要在 "A" 和 "B" 键之间交换密钥位置，请使用以下项添加另一个重映射：

- 键： "B"
- 映射到： "A"

![键盘重新映射密钥屏幕截图](../images/powertoys-keyboard-remap-a-b.png)

## <a name="key-to-shortcut"></a>快捷键到快捷方式

若要将密钥重新映射到快捷方式 (键组合) ，请在 "映射到" 列中输入快捷键组合。

例如，如果想要选择 "C" 键，并使其产生 "Ctrl + V"：

- 键： "C"
- 映射到： "Ctrl + V"

> [!NOTE]
> 即使在另一个快捷方式中使用了重新映射的密钥，也会保留密钥重新映射。 例如，输入 "Alt + C" 快捷方式将导致 "Alt + Ctrl + V"，因为 C 键已重新映射到 "Ctrl + V"。

## <a name="remap-shortcuts"></a>重新映射快捷方式

若要重新映射快捷组合键（如 "Ctrl + v"），请选择 "重新 <kbd>映射快捷方式</kbd> " 以启动 "重新映射快捷方式设置" 窗口。

首次启动时，不会显示预定义的映射。 您必须选择 <kbd>+</kbd> 按钮以添加新的重新映射。

出现新的重新映射行后，在 "快捷方式" 列中选择要 _*_更改_*_ 其输出的键。 选择要在 "映射到" 列中分配的新快捷键值。

例如，快捷方式<kbd>Ctrl +</kbd> + <kbd></kbd>将复制所选文本。 重新映射该快捷方式以使用 <kbd>Alt</kbd> 键，而不是 <kbd>Ctrl</kbd> 键：

- 快捷方式： "Ctrl" + "C"
- 映射到： "Alt" + "C"

![键盘重新映射快捷屏幕截图](../images/powertoys-keyboard-remap-shortcut.png)

重新映射快捷方式时要遵循的规则 (这些规则仅适用于) 的 "快捷方式" 列：

- 快捷方式必须以修改键开头： <kbd>Ctrl</kbd>、 <kbd>Shift</kbd>、 <kbd>Alt</kbd>或 <kbd>⊞ Win</kbd>
- 快捷方式必须以操作密钥结束， (所有非修改键) ： A、B、C、1、2、3等。
- 快捷方式的长度不能超过3个键  

## <a name="remap-a-shortcut-to-a-single-key"></a>将快捷方式重映射到单个键

可以将快捷方式 (组合键) 重新映射到单个按键。

例如，若要将快捷键<kbd>⊞ Win</kbd>  +  <kbd>D</kbd> (显示/隐藏 Windows 桌面应用) 只需要按一键，请按<kbd>Alt</kbd>：

- 快捷方式： "⊞ Win" (Windows 键) + "D"
- 映射到： "Alt"

> [!NOTE]
> 即使在另一个快捷方式中使用了重新映射的密钥，也会保留快捷方式重映射。 例如，在重新映射如上所示的 "Alt" 键后输入快捷方式 "Alt" + "Tab" 将导致 "⊞ Win" + "D" + "Tab"。

## <a name="app-specific-shortcuts"></a>应用特定的快捷方式

利用键盘管理器，你可以在 (而不是跨 Windows) 全局映射特定应用的快捷方式。

例如，在 Outlook 电子邮件应用程序中，快捷方式 "Ctrl + E" 默认设置为搜索电子邮件。 如果希望改为将 "Ctrl + F" 设置为搜索电子邮件 (而不是转发默认情况下) 设置的电子邮件，可以重新映射快捷方式，将 "Outlook" 设置为 "目标应用"。

键盘管理器使用进程名称，而不是应用程序名称 () 目标应用。 例如，Microsoft Edge 设置为 "msedge" (进程名称) ，而不是 "Microsoft Edge" (应用程序名称) 。 若要查找应用程序的进程名称，请打开 PowerShell 并输入命令 `get-process` 或打开命令提示符，然后输入命令 `tasklist` 。 这会生成当前打开的所有应用程序的进程名称列表。 下面是一些常用应用程序进程名称的列表。

  | _ *应用程序**   | **进程名**|
  | ------------------| --------------|
  | Microsoft Edge    |  msedge.exe   |
  | OneNote           |  onenote.exe  |
  | Outlook           |  outlook.exe  |
  | Teams             |  Teams.exe    |
  | Adobe Photoshop   |  Photoshop.exe|
  | 文件资源浏览器     |  explorer.exe |
  | Spotify 音乐     |  spotify.exe  |
  | Google Chrome     |  chrome.exe   |
  | Excel             |  excel.exe    |
  | Word              |  winword.exe  |
  | Powerpoint        |  powerpnt.exe |

### <a name="keys-that-cannot-be-remapped"></a>不能重新映射的密钥

某些快捷键不允许重新映射。 其中包括：

- <kbd>Ctrl</kbd> +<kbd>Alt</kbd> + <kbd>Del</kbd> (interupt 命令) 
- <kbd>⊞ Win</kbd> +<kbd>L</kbd> (锁定您的计算机) 
- 在大多数情况下，不能重新映射函数密钥<kbd>Fn</kbd> () 但可以映射<kbd>F1</kbd> - <kbd>F12</kbd> 。

## <a name="how-to-select-a-key"></a>如何选择密钥

若要选择要重映射的密钥或快捷方式，可以执行以下操作：

- 使用 " <kbd>键入密钥</kbd> " 按钮。
- 使用下拉菜单。

选择 " <kbd>类型键/快捷方式</kbd> " 按钮后，将弹出一个对话框，可在其中使用键盘输入键或快捷方式。 对输出满意后，按 <kbd>Enter</kbd> 继续。 如果要离开对话框，请按住 <kbd>Esc</kbd> 按钮。

使用下拉菜单，您可以使用项名称进行搜索，而其他下拉值将在您执行时显示。 但是，当下拉菜单处于打开状态时，不能使用类型键功能。

## <a name="orphaning-keys"></a>Orphaning 键

Orphaning 密钥意味着已将其映射到另一个密钥，不再有任何映射到它的键。

例如，如果从-> B 重新映射密钥，则键盘上不再存在导致的键。

若要解决此问题，请使用 "+" 创建另一个映射的密钥，并将其映射到以生成。若要确保不会发生这种情况，则会为任何孤立密钥显示警告。

![PowerToys 键盘管理器孤立密钥](../images/powertoys-keyboard-remap-orphaned.png)

## <a name="frequently-asked-questions"></a>常见问题

### <a name="i-remapped-the-wrong-keys-how-can-i-stop-it-quickly"></a>我重新映射了错误的密钥，如何快速停止？

要使密钥重映射正常工作，必须在后台运行 PowerToys，并且必须启用键盘管理器。 若要停止重新映射的密钥，请在 "PowerToys" 设置中关闭 PowerToys 或禁用键盘管理器。

### <a name="can-i-use-keyboard-manager-at-my-log-in-screen"></a>我能在登录屏幕上使用键盘管理器吗？

不能，仅当 PowerToys 正在运行时，键盘管理器才可用，并且不能在任何密码屏幕上运行，包括 "以管理员身份运行"。

### <a name="do-i-have-to-turn-off-my-computer-for-the-remapping-to-take-effect"></a>是否需要关闭计算机才能使重新映射生效？

不能，按 **Apply** 后应立即进行重新映射。

### <a name="where-are-the-maclinux-profiles"></a>Mac/Linux 配置文件在哪里？

当前不包含 Mac 和 Linux 配置文件。

### <a name="will-this-work-on-video-games"></a>这是否适用于视频游戏？

这取决于游戏访问密钥的方式。 某些键盘 Api 不适用于键盘管理器。

### <a name="will-remapping-work-if-i-change-my-input-language"></a>如果更改输入语言，将重新映射工作？

是的。 现在，如果您在英语 (我们) 键盘 <kbd>重新映射到</kbd> <kbd>B</kbd> ，然后将语言设置更改为 "法语"，请在法语键盘上键入 <kbd>一个</kbd> 在美国英语键盘上 (<kbd>Q</kbd>) 将导致 <kbd>B</kbd>，这与 Windows 处理多语言输入的方式一致。

## <a name="troubleshooting"></a>故障排除

如果尝试重新映射键或快捷方式，但遇到问题，则可能是以下问题之一：

- 以 **管理员身份运行：** 如果该窗口正在管理员 (提升的) 模式下运行，并且 PowerToys 未以管理员身份运行，则无法重新映射。 请尝试以管理员身份运行 PowerToys。

- **不拦截密钥：** 键盘管理器会截获键盘挂钩以重新映射密钥。 某些应用程序也会执行此操作，并且可能会干扰键盘管理器。 若要解决此问题，请切换到设置并禁用，然后重新启用键盘管理器。

## <a name="known-issues"></a>已知问题

- [Caps light 指示器无法正确切换](https://github.com/microsoft/PowerToys/issues/1692)

- [重新映射不适用于 FancyZones 和快捷方式指南](https://github.com/microsoft/PowerToys/issues/3079)

- [重新映射键（如 Win、Ctrl、Alt 或 Shift）可能会中断手势和某些特殊按钮](https://github.com/microsoft/PowerToys/issues/3703)

请参阅 [打开键盘管理器问题](https://github.com/microsoft/PowerToys/issues?q=is%3Aopen+is%3Aissue+label%3A%22Product-Keyboard+Shortcut+Manager%22)列表。
