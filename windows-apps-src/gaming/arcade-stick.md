---
title: 街机摇杆
description: 使用 Windows.Gaming.Input 街机摇杆 API 检测并读取街机摇杆。
ms.assetid: 2E52232F-3014-4C8C-B39D-FAC478BA3E01
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, 游戏, 街机摇杆, 输入
ms.localizationpriority: medium
ms.openlocfilehash: e9a9a2b3622169b99a1b3a0c3607329c85c5785f
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89159411"
---
# <a name="arcade-stick"></a>街机摇杆

此页介绍使用 [Windows.Gaming.Input.ArcadeStick][arcadestick] 和通用 Windows 平台 (UWP) 的相关 API 进行 Xbox One 街机摇杆编程的基本知识。

在本页中，你将了解如下内容：

* 如何收集相连街机摇杆及其用户的列表
* 如何检测是否已添加或删除某街机摇杆
* 如何读取一个或多个街机摇杆的输入
* 拱廊类棒如何表现为 UI 导航设备

## <a name="arcade-stick-overview"></a>街机摇杆概述

要重现单独街机的感觉并实现其高精度数字控制，街机摇杆是尤为重要的输入设备。 街机摇杆对于肉搏战和其他街机风格的游戏来说是完美的输入设备，并适合所有能正常使用全数字控件的游戏。 街机摇杆在 Windows 10 和 Xbox One UWP 应用中受 [Windows.Gaming.Input][] 命名空间支持。

Xbox One 拱廊类棒配有一个8向数字游戏杆，六个 **操作** 按钮 (表示为下图中的 A1-A6) ，两个 **特殊** 按钮 (表示为 S1 和 S2) ;它们是不支持模拟控件或振动的所有数字输入设备。 Xbox One 拱廊类棒还配备了用于支持 UI 导航的 **视图** 和 **菜单** 按钮，但它们并不用于支持游戏命令，也不能作为游戏杆按钮轻松访问。

![带4个拱廊类的游戏杆，6个动作按钮 (A1-A6) ，2个特殊按钮 (S1 和 S2) ](images/arcade-stick-1.png)

### <a name="ui-navigation"></a>UI 导航

为了减轻支持很多适用于用户界面导航的不同输入设备的负担并促进游戏和设备之间的一致性，大多数_物理_输入设备同时充当单独的称为 [UI 导航控制器](ui-navigation-controller.md) 的_逻辑_输入设备。 UI 导航控制器可跨各种输入设备提供通用的 UI 导航命令词汇。

作为 UI 导航控制器，拱廊类棒将 [所需的一组](ui-navigation-controller.md#required-set) 导航命令映射到游戏杆和 **视图**、 **菜单**、 **操作 1**和 **操作 2** 按钮。

| 导航命令 | 街机摇杆输入  |
| ------------------:| ------------------- |
|                 向上 | 上摇杆            |
|               向下 | 下摇杆          |
|               Left | 左摇杆          |
|              Right | 右摇杆         |
|               查看 | “视图”按钮         |
|               菜单 | “菜单”按钮         |
|             接受 | “操作 1”按钮     |
|             取消 | “操作 2”按钮     |

街机摇杆不会映射导航命令的任何 [可选组](ui-navigation-controller.md#optional-set)。

## <a name="detect-and-track-arcade-sticks"></a>检测和跟踪街机摇杆

检测和跟踪拱廊类棒的工作方式与用于 gamepads 的方式完全相同，只是 [ArcadeStick][] 类，而不是 [游戏板](/uwp/api/Windows.Gaming.Input.Gamepad) 类。 有关详细信息，请参阅[游戏板和振动](gamepad-and-vibration.md)。

<!-- Arcade sticks are managed by the system, therefore you don't have to create or initialize them. The system provides a list of connected arcades sticks and events to notify you when an arcade stick is added or removed.

### The arcade sticks list

The [ArcadeStick][] class provides a static property, [ArcadeSticks][], which is a read-only list of arcade sticks that are currently connected. Because you might only be interested in some of the connected arcade sticks, it's recommended that you maintain your own collection instead of accessing them through the `ArcadeSticks` property.

The following example copies all connected arcade sticks into a new collection. Note that because other threads in the background will be accessing this collection (in the [ArcadeStickAdded][] and [ArcadeStickRemoved][] events), you need to place a lock around any code that reads or updates the collection.

```cpp
auto myArcadeSticks = ref new Vector<ArcadeStick^>();
critical_section myLock{};

for (auto arcadeStick : ArcadeStick::ArcadeSticks)
{
    // Check if the arcade stick is already in myArcadeSticks; if it isn't, add
    // it.
    critical_section::scoped_lock lock{ myLock };
    auto it = std::find(begin(myArcadeSticks), end(myArcadeSticks), arcadeStick);

    if (it == end(myArcadeSticks))
    {
        // This code assumes that you're interested in all arcade sticks.
        myArcadeSticks->Append(arcadeStick);
    }
}
```

### Adding and removing arcade sticks

When an arcade stick is added or removed the [ArcadeStickAdded][] and [ArcadeStickRemoved][] events are raised. You can register handlers for these events to keep track of the arcade sticks that are currently connected.

The following example starts tracking an arcade stick that's been added.

```cpp
ArcadeStick::ArcadeStickAdded += ref new EventHandler<ArcadeStick^>(Platform::Object^, ArcadeStick^ args)
{
    // Check if the just-added arcade stick is already in myArcadeSticks; if it
    // isn't, add it.
    critical_section::scoped_lock lock{ myLock };
    auto it = std::find(begin(myGamepads), end(myGamepads), args);

    // This code assumes that you're interested in all new arcade sticks.
    myArcadeSticks->Append(args);
}
```

The following example stops tracking an arcade stick that's been removed.

```cpp
ArcadeStick::ArcadeStickRemoved += ref new EventHandler<ArcadeStick^>(Platform::Object^, ArcadeStick^ args)
{
    unsigned int indexRemoved;

    if(myArcadeSticks->IndexOf(args, &indexRemoved))
    {
        myArcadeSticks->RemoveAt(indexRemoved);
    }
}
```

### Users and headsets

Each arcade stick can be associated with a user account to link their identity to their gameplay, and can have a headset attached to facilitate voice chat or in-game features. To learn more about working with users and headsets, see [Tracking users and their devices](input-practices-for-games.md#tracking-users-and-their-devices) and [Headset](headset.md). -->

## <a name="reading-the-arcade-stick"></a>读取街机摇杆

确定感兴趣的街机摇杆之后，便可以从街机摇杆收集输入了。 但是，与你可能已习惯的某些其他输入类型不同，街机摇杆不会通过触发事件来表达状态更改。 相反，你需要通过对它们进行“轮询”__ 来定期读取其当前状态。

### <a name="polling-the-arcade-stick"></a>轮询街机摇杆

轮询会捕获街机摇杆在某个精确时间点的快照。 此输入收集方法对于大多数游戏都非常适合，因为其逻辑通常按确定的循环运行，而不是受事件驱动；而且，相对于随着时间逐个收集许多单个输入解释游戏命令，从一次性收集的输入解释游戏命令，通常会更为简单。

你通过调用 [GetCurrentReading][] 来轮询街机摇杆；此功能会返回包含街机摇杆状态的 [ArcadeStickReading][]。

下面是轮询街机摇杆来获取其当前状态的示例。

```cpp
auto arcadestick = myArcadeSticks[0];

ArcadeStickReading reading = arcadestick->GetCurrentReading();
```

除了街机摇杆状态之外，每个读数还包括一个时间戳，准确指出检索状态的时间。 该时间戳对于关联之前读数的时间或者游戏模拟的时间非常有用。

### <a name="reading-the-buttons"></a>读取按钮

每个 "拱廊类棒" 按钮 &mdash; 的四个游戏杆方向、六个 **操作** 按钮和两个 **特殊** 按钮都 &mdash; 提供数字读数，用于指示是 (按) 还是释放 () 。 为提高效率，按钮读数并不表示为单个布尔值;相反，它们全部打包为 [ArcadeStickButtons][] 枚举表示的单个位域。

> [!NOTE]
> 拱廊类棒配有用于 UI 导航的其他按钮，如 " **视图** " 和 " **菜单** " 按钮。 这些按钮不是 `ArcadeStickButtons` 枚举的一部分，只能作为 UI 导航设备通过访问街机摇杆进行读取。 有关详细信息，请参阅 [UI 导航设备](ui-navigation-controller.md)。

从 [ArcadeStickReading][] 结构的 `Buttons` 属性中读取按钮值。 因为此属性是位域，所以使用按位掩码隔离你感兴趣的按钮值。 设置相应位时按钮为按下（向下）；否则，按钮为释放（向上）。

下面的示例确定是否按下了 " **操作 1** " 按钮。

```cpp
if (ArcadeStickButtons::Action1 == (reading.Buttons & ArcadeStickButtons::Action1))
{
    // Action 1 is pressed
}
```

下面的示例确定是否释放 " **操作 1** " 按钮。

```cpp
if (ArcadeStickButtons::None == (reading.Buttons & ArcadeStickButtons::Action1))
{
    // Action 1 is released (not pressed)
}
```

有时你可能需要确定：何时将按钮从按下转换为释放或从释放转换为按下，是按下还是释放多个按钮，或者是否按特定方式安排一组按钮（按下一些按钮，释放一些按钮）。 有关如何检测这些状态的详细信息，请参阅[检测按钮转换](input-practices-for-games.md#detecting-button-transitions) 和[检测复杂按钮安排](input-practices-for-games.md#detecting-complex-button-arrangements)。

## <a name="run-the-inputinterfacing-sample"></a>运行 InputInterfacing 示例

[InputInterfacingUWP 示例 _(github)_](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/System/InputInterfacingUWP) 演示了如何将街机摇杆和不同类型的输入设备配合使用，以及这些输入设备如何像 UI 导航控制器那样运行。

## <a name="see-also"></a>另请参阅

* [Windows.Gaming.Input.UINavigationController][]
* [Windows.Gaming.Input.IGameController][]
* [游戏输入实践](input-practices-for-games.md)

[Windows。]: /uwp/api/Windows.Gaming.Input
[Windows.Gaming.Input.IGameController]: /uwp/api/Windows.Gaming.Input.IGameController
[Windows.Gaming.Input.UINavigationController]: /uwp/api/Windows.Gaming.Input.UINavigationController
[arcadestick]: /uwp/api/Windows.Gaming.Input.ArcadeStick
[arcadesticks]: /uwp/api/Windows.Gaming.Input.ArcadeStick
[arcadestickadded]: /uwp/api/Windows.Gaming.Input.ArcadeStick
[arcadestickremoved]: /uwp/api/Windows.Gaming.Input.ArcadeStick
[getcurrentreading]: /uwp/api/Windows.Gaming.Input.ArcadeStick
[arcadestickreading]: /uwp/api/Windows.Gaming.Input.ArcadeStickReading
[arcadestickbuttons]: /uwp/api/Windows.Gaming.Input.ArcadeStickButtons