---
title: '启用语音的 Shell (Xbox) '
description: 了解如何使用启用了语音的 Shell (VES) ，将语音控件支持添加到通用 Windows 平台 (UWP) Xbox 上的应用。
ms.date: 10/19/2017
ms.topic: article
keywords: windows 10、uwp、xbox、speech、speech enabled shell
ms.openlocfilehash: fa0f56a6821fd8858cab317654cd0ead5d731693
ms.sourcegitcommit: 39fb8c0dff1b98ededca2f12e8ea7977c2eddbce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91750263"
---
# <a name="using-speech-to-invoke-ui-elements"></a>使用语音调用 UI 元素

启用语音的 Shell (VES) 是 Windows 语音平台的扩展，可在应用内实现一流的语音体验，从而允许用户使用语音调用屏幕上的控件并通过听写插入文本。 VES 致力于在所有 Windows Shell 和设备上提供通用的端到端体验，并在应用程序开发人员所需的工作量最小。  为实现此目的，它利用了 Microsoft 语音平台和 UI 自动化 (UIA) framework。

## <a name="user-experience-walkthrough"></a>用户体验演练 ##
下面概述了在 Xbox 上使用 VES 时用户将遇到的情况，并在深入了解 VES 工作原理的详细信息之前，帮助设置上下文。

- 用户打开了 Xbox 控制台并想要浏览其应用，以查找感兴趣的内容：

    > 用户： "你好 Cortana，请打开我的游戏和应用程序"

- 用户处于活动状态的侦听模式 (ALM) ，这意味着，控制台现在正在侦听用户调用屏幕上可见的控件，而无需说 "你好 Cortana"。  用户现在可以切换到查看应用并滚动应用列表：

    > 用户： "应用程序"

- 若要滚动视图，用户可以简单地说：

    > 用户： "向下滚动"

- 用户会看到感兴趣的应用程序的 box 画面，但忘记了名称。  用户要求显示语音提示标签：

    > 用户： "显示标签"

- 现在清楚地说，应用程序可以启动：

    > 用户： "电影和电视"

- 若要退出活动侦听模式，用户会告诉 Xbox 停止侦听：

    > 用户： "停止侦听"

- 稍后，可以通过以下方式启动新的活动侦听会话：

    > 用户： "你好 Cortana，进行选择" 或 "你好 Cortana"，请选择 "

## <a name="ui-automation-dependency"></a>UI 自动化依赖项 ##
VES 是 UI 自动化客户端，它依赖于应用程序的 UI 自动化提供程序公开的信息。 这与 Windows 平台上的 "讲述人" 功能已使用的基础结构相同。  UI 自动化启用对用户界面元素的编程访问，包括控件的名称、控件的类型以及它所实现的控件模式。  当应用程序中的 UI 变化时，VES 将响应 UIA 更新事件，并重新分析更新后的 UI 自动化树，以查找所有可操作的项，并使用此信息来生成语音识别语法。 

所有 UWP 应用都有权访问 UI 自动化框架，并可公开有关 UI 的信息，这些信息独立于在 (XAML、DirectX/Direct3D、Xamarin 等 ) 上构建的图形框架。  在某些情况下，如 XAML，大多数繁重的提升都是通过框架来完成的，这大大减少了支持讲述人和 VES 所需的工作。

有关 UI 自动化的详细信息，请参阅 [Ui 自动化基础知识](/dotnet/framework/ui-automation/ui-automation-fundamentals "UI 自动化基础知识")。

## <a name="control-invocation-name"></a>控件调用名称 ##
VES 采用以下试探法来确定将哪个短语注册到语音识别器作为控件名称 (ie。用户调用控件所需的对话) 。  这也是在语音提示标签中显示的短语。

名称的源（按优先级顺序）：

1. 如果元素具有 `LabeledBy` 附加属性，则 VES 将使用 `AutomationProperties.Name` 此文本标签的。
2. `AutomationProperties.Name` 元素的。  在 XAML 中，将使用控件的文本内容作为的默认值 `AutomationProperties.Name` 。
3. 如果控件是一个用名或按钮，则 VES 将查找具有有效的第一个子元素 `AutomationProperties.Name` 。

## <a name="actionable-controls"></a>可操作控件 ##
如果控件实现以下某个自动化控件模式，则 VES 会认为该控件可操作：

- **InvokePattern** (例如 Button) -表示启动或执行单个明确操作并且在激活后不维护状态的控件。

- **TogglePattern** (例如 复选框) -表示一个控件，该控件可以循环一组状态并在设置后保持状态。

- **SelectionItemPattern** (例如 组合框) -表示一个控件，该控件充当可选子项集合的容器。

- **ExpandCollapsePattern** (例如 组合框) -表示直观展开以显示内容并折叠以隐藏内容的控件。

- **ScrollPattern** (例如 List) -表示充当子元素集合的可滚动容器的控件。

## <a name="scrollable-containers"></a>可滚动容器 ##
对于支持 ScrollPattern 的可滚动容器，VES 将侦听语音命令，如 "向左滚动"、"向右滚动" 等。当用户触发其中一个命令时，将调用具有相应参数的 Scroll。  根据和属性的值注入滚动命令 `HorizontalScrollPercent` `VerticalScrollPercent` 。  例如，如果 `HorizontalScrollPercent` 大于0，将添加 "向左滚动"，如果小于100，则将添加 "向右滚动" 等等。

## <a name="narrator-overlap"></a>讲述人重叠 ##
"讲述人" 应用程序也是 UI 自动化客户端，并使用 `AutomationProperties.Name` 属性作为其为当前所选 UI 元素读取的文本的源之一。  为了提供更好的可访问性体验，许多应用程序开发人员已尝试使用 `Name` 较长的描述性文本重载该属性，目的是在讲述人阅读时提供详细信息和上下文。  但是，这会导致两个功能之间存在冲突： VES 需要与控件的可见文本匹配或匹配的短短语，而讲述人却受益于更长的描述性短语，以提供更好的上下文。

若要解决此问题，请从 Windows 10 创意者更新开始，更新了讲述人以查看 `AutomationProperties.HelpText` 属性。  如果此属性不为空，则除了外，讲述人还会说出其内容 `AutomationProperties.Name` 。  如果 `HelpText` 为空，则讲述人只会读取名称的内容。  这将允许在需要时使用较长的描述性字符串，但会在属性中保留较短的语音识别短语 `Name` 。

![显示按钮后面的代码的关系图，其中包括 AutomationProperties.Name 和 Automationproperties.livesetting。 HelpText 表明启用了语音的 Shell 侦听名称配置。](images/ves_narrator.jpg)

有关详细信息，请参阅 [UI 中的辅助功能支持的自动化属性](/previous-versions/windows/silverlight/dotnet-windows-silverlight/ff400332(v=vs.95) "UI 中的辅助功能支持的自动化属性")。

## <a name="active-listening-mode-alm"></a>活动侦听模式 (ALM)  ##
### <a name="entering-alm"></a>输入 ALM ###
在 Xbox 上，VES 并不会不断地侦听语音输入。  用户需要通过口述显式进入活动的侦听模式：

- "你好 Cortana，请选择"，或
- "你好 Cortana，进行选择"

还有其他几个 Cortana 命令还会使用户处于活动状态，例如 "你好 Cortana，登录" 或 "你好 Cortana，回家"。 

输入 ALM 将产生以下影响：

- Cortana 覆盖将显示在右上角，告诉用户他们可以说出的内容。  用户讲话时，语音识别器识别的短语片段也会显示在此位置中。
- VES 分析 UIA 树，查找所有可操作的控件，在语音识别语法中注册其文本，并启动持续侦听会话。

    ![显示带的屏幕截图，其中突出显示了 "显示标签" 选项。](images/ves_overlay.png)

### <a name="exiting-alm"></a>正在退出 ALM ###
当用户使用语音与 UI 交互时，系统将保留在 ALM 中。  可以通过两种方式退出 ALM：

- 用户显式显示 "停止侦听"，或
- 如果在开始输入 ALM 或自上次肯定识别后的17秒内没有肯定识别，则会超时

## <a name="invoking-controls"></a>调用控件 ##
在 ALM 中，用户可以使用语音与 UI 交互。  如果 UI 配置正确 (名称属性与可见文本) 匹配，则使用语音执行操作应该是一种无缝、自然的体验。  用户应能够只说他们在屏幕上看到的内容。

## <a name="overlay-ui-on-xbox"></a>在 Xbox 上覆盖 UI ##
为控件派生的名称 VES 可能不同于 UI 中的实际可见文本。  这可能是由于控件的 `Name` 属性或 `LabeledBy` 显式设置为不同字符串的附加元素。  或者，控件不具有 GUI 文本，而只包含图标或图像元素。

在这些情况下，用户需要有一种方法来查看调用此类控件所需的方法。  因此，一旦进入活动状态，就可以通过 "显示标签" 显示语音提示。  这会使语音提示标签显示在每个可操作控件的顶部。

存在100个标签的限制，因此，如果应用的 UI 具有比100更具可操作性的控件，将会出现一些不会显示语音提示标签的部分。  在这种情况下选择哪些标签是不确定的，因为它依赖于当前 UI 的结构和组合，因为它是在 UIA 树中首次枚举的。

显示语音提示标签后，没有用于隐藏它们的命令，它们将保持可见，直到发生以下事件之一：

- 用户调用控件
- 用户离开当前场景
- 用户说 "停止侦听"
- 活动侦听模式超时

## <a name="location-of-voice-tip-labels"></a>语音提示标签的位置 ##
语音提示标签在控件的 BoundingRectangle 中水平和垂直居中。  当控件很小且分组紧密时，某些标签可能会重叠，并会被其他人遮盖，并且 VES 会尝试将这些标签分开以分隔它们，确保它们可见。  但是，这不一定会在100% 的时间运行。  如果 UI 非常拥挤，则很可能会导致某些标签被其他人遮盖。 请查看包含 "显示标签" 的 UI，以确保有足够的空间来显示语音提示可见性。

![控件边框内水平和垂直居中的语音提示标签屏幕截图。](images/ves_labels.png)

## <a name="combo-boxes"></a>组合框 ##
展开组合框中的每个项时，组合框中的每个项都将获取其自己的语音提示标签，并且通常会位于下拉列表后面的现有控件的顶部。  若要避免显示混乱的、令人困惑的 muddle 标签 (其中组合框项标签与组合框后的控件标签混合) 在展开组合框时，只会显示其子项的标签; 所有其他语音提示标签都将隐藏。  然后，用户可以选择某个下拉项或 "关闭" 组合框。

- 折叠组合框上的标签：

    ![显示和声音视频输出窗口的屏幕截图，其中的标签位于折叠组合框上。](images/ves_combo_closed.png)

- 展开组合框上的标签：

    ![带有展开组合框中的标签的显示和声音视频输出窗口的屏幕截图。](images/ves_combo_open.png)


## <a name="scrollable-controls"></a>可滚动控件 ##
对于滚动控件，滚动命令的语音提示将在控件的每个边缘上居中。  将仅为可操作的滚动方向显示语音提示。例如，如果垂直滚动不可用，则不会显示 "向上滚动" 和 "向下滚动"。  当存在多个可滚动区域时，VES 将使用序号来区分它们 (例如。 "向右滚动 1"、"向右滚动 2" 等 ) 。

![向左滚动并向右滚动 U I 方向的屏幕截图。](images/ves_scroll.png) 

## <a name="disambiguation"></a>消除歧义 ##
如果多个 UI 元素具有相同的名称，或语音识别器与多个候选项匹配，则 VES 将进入歧义消除模式。  在此模式下，将为所涉及的元素显示语音提示标签，以便用户可以选择正确的标签。 用户可以通过说 "取消" 取消消除歧义模式。

例如：

- 处于活动状态的侦听模式下，消除歧义之前;用户说 "我不明确"：

    ![活动侦听模式的屏幕截图现在可以说出显示的选项，并且按钮上不显示任何标签。](images/ves_disambig1.png) 

- 两个按钮都匹配;消除歧义开始：

    ![活动侦听模式的屏幕截图，其中显示了所需的选项，以及按钮上的项1和项2标签。](images/ves_disambig2.png) 

- 选择 "选择 2" 时显示单击操作：

    ![活动侦听模式的屏幕截图现在，您可以说到 "显示的内容" 选项，而在第一个按钮上，"我的标签" 不明确。](images/ves_disambig3.png) 
 
## <a name="sample-ui"></a>示例 UI ##
下面是基于 XAML 的 UI 的示例，以各种方式设置 AutomationProperties.Name：

```xaml
<Page
    x:Class="VESSampleCSharp.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:VESSampleCSharp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Button x:Name="button1" Content="Hello World" HorizontalAlignment="Left" Margin="44,56,0,0" VerticalAlignment="Top"/>
        <Button x:Name="button2" AutomationProperties.Name="Launch Game" Content="Launch" HorizontalAlignment="Left" Margin="44,106,0,0" VerticalAlignment="Top" Width="99"/>
        <TextBlock AutomationProperties.Name="Day of Week" x:Name="label1" HorizontalAlignment="Left" Height="22" Margin="168,62,0,0" TextWrapping="Wrap" Text="Select Day of Week:" VerticalAlignment="Top" Width="137"/>
        <ComboBox AutomationProperties.LabeledBy="{Binding ElementName=label1}" x:Name="comboBox" HorizontalAlignment="Left" Margin="310,57,0,0" VerticalAlignment="Top" Width="120">
            <ComboBoxItem Content="Monday" IsSelected="True"/>
            <ComboBoxItem Content="Tuesday"/>
            <ComboBoxItem Content="Wednesday"/>
            <ComboBoxItem Content="Thursday"/>
            <ComboBoxItem Content="Friday"/>
            <ComboBoxItem Content="Saturday"/>
            <ComboBoxItem Content="Sunday"/>
        </ComboBox>
        <Button x:Name="button3" HorizontalAlignment="Left" Margin="44,156,0,0" VerticalAlignment="Top" Width="213">
            <Grid>
                <TextBlock AutomationProperties.Name="Accept">Accept Offer</TextBlock>
                <TextBlock Margin="0,25,0,0" Foreground="#FF5A5A5A">Exclusive offer just for you</TextBlock>
            </Grid>
        </Button>
    </Grid>
</Page>
```

在此示例中，用户界面的外观将类似于，无需使用语音提示标签。
 
- 处于活动状态的侦听模式下，不显示标签：

    ![活动侦听模式的屏幕截图，其中包含以查看标签，即显示 "显示标签" 选项且不显示标签。](images/ves_alm_nolabels.png) 

- 在活动监听模式下，在用户显示 "显示标签" 后：

    ![活动侦听模式的屏幕截图（如果已完成），假设显示 "停止侦听" 选项和在 U I 控件上显示的标签。](images/ves_alm_labels.png) 

对于 `button1` ，XAML 自动 `AutomationProperties.Name` 使用控件的可视文本内容中的文本填充该属性。  这就是为什么即使没有显式集，也会出现语音提示标签 `AutomationProperties.Name` 。

对于 `button2` ，我们将显式设置 `AutomationProperties.Name` 为控件文本以外的内容。

使用 `comboBox` ，我们使用 `LabeledBy` 属性 `label1` 作为自动化的源进行引用 `Name` ，而在中， `label1` 我们将设置 `AutomationProperties.Name` 为比屏幕上呈现的内容更自然的短语 ( "星期的日期"，而不是 "选择星期几" ) 。

最后，使用时 `button3` ，VES `Name` 从第一个子元素中获取，因为 `button3` 本身没有 `AutomationProperties.Name` 集。

## <a name="see-also"></a>另请参阅
- [UI 自动化基础知识](/dotnet/framework/ui-automation/ui-automation-fundamentals "UI 自动化基础知识")
- [UI 中的辅助功能支持的自动化属性](/previous-versions/windows/silverlight/dotnet-windows-silverlight/ff400332(v=vs.95) "UI 中的辅助功能支持的自动化属性")
- [常见问题解答](frequently-asked-questions.md)
- [Xbox One 上的 UWP](index.md)