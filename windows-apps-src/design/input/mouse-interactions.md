---
description: 通过处理用于触摸和笔输入的相同基本指针事件在应用中响应鼠标输入。
title: 鼠标交互
ms.assetid: C8A158EF-70A9-4BA2-A270-7D08125700AC
label: Mouse
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dfefda0e09159c398334ae77bc29dcd8fc3f628a
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93034720"
---
# <a name="mouse-interactions"></a>鼠标交互

为触摸输入优化 Windows 应用设计，并默认获取基本的鼠标支持。 

![鼠标](images/input-patterns/input-mouse.jpg)

鼠标输入最适合那些需要精确指向和单击的用户交互。 由于 Windows 的 UI 针对触摸的不精确特性进行了优化，因此它自然支持这种固有的精确度。

鼠标输入和触摸输入的不同之处在于，触摸可以通过对这些对象执行物理手势（如轻扫、滑动、拖动、旋转等）更逼真地模拟直接操作 UI 元素。 操作鼠标通常要求提供其他一些 UI，例如可调整对象大小或对其进行旋转的句柄的使用。

本主题介绍鼠标交互的设计注意事项。

## <a name="the-uwp-app-mouse-language"></a>UWP 应用鼠标语言

一组在整个系统中通用的简单鼠标交互功能。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">术语</th>
<th align="left">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>悬停以了解</p></td>
<td align="left"><p>悬停在元素上可以显示更详细的信息或指导性可视化内容（如工具提示），而无需提交操作。</p></td>
</tr>
<tr class="even">
<td align="left"><p>单击以进行主操作</p></td>
<td align="left"><p>单击某个元素可以调用它的主操作（如启动应用或执行命令）。</p></td>
</tr>
<tr class="odd">
<td align="left"><p>滚动以更改视图</p></td>
<td align="left"><p>显示滚动条以在内容区域中向上、向下、向左和向右移动。 用户可以通过单击滚动条或者旋转鼠标滚轮来滚动。 滚动条可以指示当前视图在内容区域中的位置（一边触摸一边平移会显示类似的 UI）。</p></td>
</tr>
<tr class="even">
<td align="left"><p>右键单击以选定和进行命令操作</p></td>
<td align="left"><p>右键单击以使用全局命令显示导航栏（如果有的话）与应用栏。 右键单击某个元素可将其选定并显示带有所选元素的上下文命令的应用栏。</p>
<div class="alert">
<strong>注意</strong>  如果选择或应用栏命令不是适合的 UI 行为，右键单击可显示上下文菜单。 但是，我们强烈建议你针对所有的命令行为使用应用栏。
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>使用 UI 命令进行缩放</p></td>
<td align="left"><p>显示应用栏中的 UI 命令（如 + 和 -），或者在按住 Ctrl 的情况下旋转鼠标滚轮模拟通过收缩和拉伸手势进行缩放。</p></td>
</tr>
<tr class="even">
<td align="left"><p>使用 UI 命令进行旋转</p></td>
<td align="left"><p>显示应用栏中的 UI 命令，或者在按住 Ctrl+Shift 的情况下旋转鼠标滚轮模拟旋转手势来进行旋转。 旋转设备本身会旋转整个屏幕。</p></td>
</tr>
<tr class="odd">
<td align="left"><p>单击并拖动以重新排列</p></td>
<td align="left"><p>单击并拖动元素可移动它。</p></td>
</tr>
<tr class="even">
<td align="left"><p>单击并拖动以选择文本</p></td>
<td align="left"><p>在可选择的文本内单击并拖动可选择它。 双击可选择一个单词。</p></td>
</tr>
</tbody>
</table>

## <a name="mouse-input-events"></a>鼠标输入事件

大多数鼠标输入都可以通过所有 [**UIElement**](/uwp/api/Windows.UI.Xaml.UIElement) 对象支持的常见路由输入事件进行处理。 这些方法包括：

- [**BringIntoViewRequested**](/uwp/api/windows.ui.xaml.uielement.bringintoviewrequested)
- [**CharacterReceived**](/uwp/api/windows.ui.xaml.uielement.characterreceived)
- [**ContextCanceled**](/uwp/api/windows.ui.xaml.uielement.contextcanceled)
- [**ContextRequested**](/uwp/api/windows.ui.xaml.uielement.contextrequested)
- [**DoubleTapped**](/uwp/api/windows.ui.xaml.uielement.doubletapped)
- [**DragEnter**](/uwp/api/windows.ui.xaml.uielement.dragenter)
- [**DragLeave**](/uwp/api/windows.ui.xaml.uielement.dragleave)
- [**DragOver**](/uwp/api/windows.ui.xaml.uielement.dragover)
- [**DragStarting**](/uwp/api/windows.ui.xaml.uielement.dragstarting)
- [**击落**](/uwp/api/windows.ui.xaml.uielement.drop)
- [**DropCompleted**](/uwp/api/windows.ui.xaml.uielement.dropcompleted)
- [**GettingFocus**](/uwp/api/windows.ui.xaml.uielement.gettingfocus)
- [**GotFocus**](/uwp/api/windows.ui.xaml.uielement.gotfocus)
- [**Holding**](/uwp/api/windows.ui.xaml.uielement.holding)
- [**KeyDown**](/uwp/api/windows.ui.xaml.uielement.keydown)
- [**KeyUp**](/uwp/api/windows.ui.xaml.uielement.keyup)
- [**LosingFocus**](/uwp/api/windows.ui.xaml.uielement.losingfocus)
- [**LostFocus**](/uwp/api/windows.ui.xaml.uielement.lostfocus)
- [**ManipulationCompleted**](/uwp/api/windows.ui.xaml.uielement.manipulationcompleted)
- [**ManipulationDelta**](/uwp/api/windows.ui.xaml.uielement.manipulationdelta)
- [**ManipulationInertiaStarting**](/uwp/api/windows.ui.xaml.uielement.manipulationinertiastarting)
- [**ManipulationStarted**](/uwp/api/windows.ui.xaml.uielement.manipulationstarted)
- [**ManipulationStarting**](/uwp/api/windows.ui.xaml.uielement.manipulationstarting)
- [**NoFocusCandidateFound**](/uwp/api/windows.ui.xaml.uielement.nofocuscandidatefound)
- [**PointerCanceled**](/uwp/api/windows.ui.xaml.uielement.pointercanceled)
- [**PointerCaptureLost**](/uwp/api/windows.ui.xaml.uielement.pointercapturelost)
- [**PointerEntered**](/uwp/api/windows.ui.xaml.uielement.pointerentered)
- [**PointerExited**](/uwp/api/windows.ui.xaml.uielement.pointerexited)
- [**PointerMoved**](/uwp/api/windows.ui.xaml.uielement.pointermoved)
- [**PointerPressed**](/uwp/api/windows.ui.xaml.uielement.pointerpressed)
- [**PointerReleased**](/uwp/api/windows.ui.xaml.uielement.pointerreleased)
- [**PointerWheelChanged**](/uwp/api/windows.ui.xaml.uielement.pointerwheelchanged)
- [**System.windows.forms.control.previewkeydown>**](/uwp/api/windows.ui.xaml.uielement.previewkeydown)
- [**PreviewKeyUp**](/uwp/api/windows.ui.xaml.uielement.previewkeyup)
- [**RightTapped**](/uwp/api/windows.ui.xaml.uielement.righttapped)
- [**Tapped**](/uwp/api/windows.ui.xaml.uielement.tapped)

但是，你可以利用每个设备的特定功能 (例如鼠标滚轮事件) 使用 [Windows](/uwp/api/windows.ui.input)中的指针、笔势和操作事件。

**示例：** 请参阅 [BasicInput 示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)。

## <a name="guidelines-for-visual-feedback"></a>视觉反馈指南

- 当（通过移动或悬停事件）检测到鼠标时，显示特定于鼠标的 UI 以指示元素显示的功能。 如果鼠标在一定的时间段内没有移动，或者如果用户启动了触摸交互，则让鼠标 UI 逐渐淡出。 这会使 UI 干净整洁。
- 不要使用鼠标获取悬停反馈，由元素提供的反馈是足够的（请参阅下面的“光标”）。
- 如果元素不支持交互（如静态文本），请勿显示视觉反馈。
- 不要将焦点矩形与鼠标交互结合使用。 保留焦点矩形是为了进行键盘交互。
- 对于所有代表相同输入目标的元素，同时显示视觉反馈。
- 提供用来模拟基于触摸的操作 （如平移、旋转、缩放等）提供按钮（如 + 和 -）。

有关视觉反馈的更一般指南，请参阅[视觉反馈指南](guidelines-for-visualfeedback.md)。

## <a name="cursors"></a>游标

为鼠标指针提供了一组标准光标。 它们用来表示元素的主要操作。

每个标准光标都有一个与它相关联的默认图像。 用户或应用可以随时替换与任何标准光标相关联的默认图像。 通过 [**PointerCursor**](/uwp/api/windows.ui.core.corewindow.pointercursor) 函数指定光标图像。

如果你需要自定义鼠标光标：

- 对于可单击元素，始终使用箭头光标（![箭头光标](images/cursor-arrow.png)）。 对于链接或其他交互元素，不使用指向手光标（![指向手光标](images/cursor-pointinghand.png)）。 而应使用悬停效果（上文中有介绍）。
- 对于可选择文本，使用文本光标（![文本光标](images/cursor-text.png)）。
- 当主要操作是移动（如拖动或裁剪）时，使用移动光标（![移动光标](images/cursor-move.png)）。 对于主要操作是导航的元素（如“开始”菜单磁贴），不使用移动光标。
- 当对象的大小可调整时，使用水平、垂直和对角调整大小光标（![垂直调整光标](images/cursor-vertical.png), ![水平调整光标](images/cursor-horizontal.png), ![对角调整光标（左下和右上）](images/cursor-diagonal2.png), ![对角调整光标（左上和右下）](images/cursor-diagonal1.png)）。
- 当在固定画布（如地图）内平移内容时，使用手掌型光标（![手掌型光标（张开）](images/cursor-pan1.png), ![手掌型光标（闭合）](images/cursor-pan2.png)当在固定画布（例如地图）中平移内容时。

## <a name="related-articles"></a>相关文章

- [处理指针输入](handle-pointer-input.md)
- [标识输入设备](identify-input-devices.md)
- [事件和路由事件概述](../../xaml-platform/events-and-routed-events-overview.md)

### <a name="samples"></a>示例

- [基本输入示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [低延迟输入示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [用户交互模式示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [焦点视觉对象示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)
