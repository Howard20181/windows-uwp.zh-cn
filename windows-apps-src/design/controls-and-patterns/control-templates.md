---
Description: 通过在 XAML 框架中创建控件模板，你可以自定义控件的可视结构和可视行为。
MS-HAID: dev\_ctrl\_layout\_txt.control\_templates
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: 控件模板
ms.assetid: 6E642626-A1D6-482F-9F7E-DBBA7A071DAD
label: Control templates
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8ed4515a90df50a1ff8c0d6b82eb8cda5a4a78c9
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82968742"
---
# <a name="control-templates"></a>控件模板

通过在 XAML 框架中创建控件模板，你可以自定义控件的可视结构和可视行为。 控件有多个属性，如 [**Background**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background)、[**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) 以及 [**FontFamily**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.fontfamily)，可以设置这些属性以指定控件外观的多个方面。 但是可以通过设置这些属性所做的更改有限。 你可以通过使用 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 类创建模板来指定其他自定义。 我们在此处介绍如何创建 **ControlTemplate** 以自定义 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 控件的外观。

> **重要的 API**：[**ControlTemplate 类**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate)、[**Control.Template 属性**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template)

## <a name="custom-control-template-example"></a>自定义控件模板示例

在默认情况下，[**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 控件将其内容（字符串或 **CheckBox** 旁的对象）放在选择框的右侧，并使用复选标记来表示用户已选中 **CheckBox**。 这些特性表示 **CheckBox** 的可视结构和可视行为。

下面是分别在 `Unchecked`、`Checked` 和 `Indeterminate` 状态下使用默认 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 的 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate)。

![默认 CheckBox 模板](images/templates-checkbox-states-default.png)

你可以通过为 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 创建 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 来更改这些特性。 例如，如果你想要让复选框的内容显示在选择框下方，并且你想要用 **X** 来表示用户已选中复选框。 你可以在 **CheckBox** 的 **ControlTemplate** 中指定这些特性。

若要将自定义模板与控件一起使用，请将 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 分配给控件的 [**Template**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template) 属性。 下面是使用称为 `CheckBoxTemplate1` 的 **ControlTemplate** 的 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox)。 我们在下一节介绍 **ControlTemplate** 的 Extensible Application Markup Language (XAML)。

```XAML
<CheckBox Content="CheckBox" Template="{StaticResource CheckBoxTemplate1}" IsThreeState="True" Margin="20"/>
```

下面是在应用模板后，[**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 在 `Unchecked`、`Checked` 和 `Indeterminate` 状态下的外观。

![自定义 CheckBox 模板](images/templates-checkbox-states.png)

## <a name="specify-the-visual-structure-of-a-control"></a>指定控件的可视结构

当你创建 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 时，结合 [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) 对象来生成一个单一的控件。 **ControlTemplate** 只能有一个 **FrameworkElement** 作为其根元素。 该根元素通常包含其他 **FrameworkElement** 对象。 这些对象的组合组成控件的可视结构。

此 XAML 为 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 创建了 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox)，可指定控件的内容显示在选择框的下方。 根元素为 [**Border**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border)。 该示例指定 [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) 来创建 **X**，表示用户已选定 **CheckBox**，并用 [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) 表示不确定状态。 请注意，[**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) 在 **Path** 和 **Ellipse** 上均设置为 0，因此在默认情况下，两者都不会显示。

[TemplateBinding](../../xaml-platform/templatebinding-markup-extension.md) 是一种特殊的绑定，将一个控件模板中的属性值链接到模板控件上的某个其他公开的属性的值。 TemplateBinding 只能在 XAML 中的 ControlTemplate 定义中使用。 有关详细信息，请参阅 [TemplateBinding 标记扩展](../../xaml-platform/templatebinding-markup-extension.md)。

> [!NOTE]
> 从 Windows 10 版本 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) 开始，可以在使用 [TemplateBinding](../../xaml-platform/templatebinding-markup-extension.md) 的位置使用 [**x:Bind**](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) 标记扩展。 有关详细信息，请参阅 [TemplateBinding 标记扩展](../../xaml-platform/templatebinding-markup-extension.md)。

```XAML
<ControlTemplate x:Key="CheckBoxTemplate1" TargetType="CheckBox">
    <Border BorderBrush="{TemplateBinding BorderBrush}"
            BorderThickness="{TemplateBinding BorderThickness}"
            Background="{TemplateBinding Background}">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="25"/>
            </Grid.RowDefinitions>
            <Rectangle x:Name="NormalRectangle" Fill="Transparent" Height="20" Width="20"
                       Stroke="{ThemeResource SystemControlForegroundBaseMediumHighBrush}"
                       StrokeThickness="{ThemeResource CheckBoxBorderThemeThickness}"
                       UseLayoutRounding="False"/>
            <!-- Create an X to indicate that the CheckBox is selected. -->
            <Path x:Name="CheckGlyph"
                  Data="M103,240 L111,240 119,248 127,240 135,240 123,252 135,264 127,264 119,257 111,264 103,264 114,252 z"
                  Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                  FlowDirection="LeftToRight"
                  Height="14" Width="16" Opacity="0" Stretch="Fill"/>
            <Ellipse x:Name="IndeterminateGlyph"
                     Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                     Height="8" Width="8" Opacity="0" UseLayoutRounding="False" />
            <ContentPresenter x:Name="ContentPresenter"
                              ContentTemplate="{TemplateBinding ContentTemplate}"
                              Content="{TemplateBinding Content}"
                              Margin="{TemplateBinding Padding}" Grid.Row="1"
                              HorizontalAlignment="Center"
                              VerticalAlignment="{TemplateBinding VerticalContentAlignment}"/>
        </Grid>
    </Border>
</ControlTemplate>
```

## <a name="specify-the-visual-behavior-of-a-control"></a>指定控件的可视行为

可视行为指定控件在确定状态下的外观。 [  **CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 控件具有 3 种复选状态：`Checked`、`Unchecked` 和 `Indeterminate`。 [  **IsChecked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.ischecked) 属性的值确定 **CheckBox** 的状态，其状态确定框中显示的内容。

下表列出了 [**IsChecked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.ischecked) 的可能值、[**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 的相应状态，以及 **CheckBox** 的外观。

|                     |                    |                         |
|---------------------|--------------------|-------------------------|
| **IsChecked** 值 | **CheckBox** 状态 | **CheckBox** 外观 |
| **true**            | `Checked`          | 包含“X”。        |
| **false**           | `Unchecked`        | 空白。                  |
| **null**            | `Indeterminate`    | 包含一个圆形。      |


使用 [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) 对象可指定控件在某种状态下的外观。 **VisualState** 包含可更改 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 中元素外观的 [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.BeginStoryboard) 或 [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate)。 当控件进入 [**VisualState.Name**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.visualstate.name) 属性指定的状态时，将应用 **Setter** 或 [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) 中的属性更改。 当控件退出该状态时，这些更改将会删除。 你可以将 **VisualState** 对象添加到 [**VisualStateGroup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualStateGroup) 对象。 还可以将 **VisualStateGroup** 对象添加到 [**VisualStateManager.VisualStateGroups**](https://docs.microsoft.com/dotnet/api/system.windows.visualstatemanager) 附加属性，这些对象在 **ControlTemplate** 的根 [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) 上设置。

以下 XAML 介绍在 `Checked`、`Unchecked` 和 `Indeterminate` 状态下的 [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) 对象。 该示例在 [**Border**](https://docs.microsoft.com/dotnet/api/system.windows.visualstatemanager) 上设置 [**VisualStateManager.VisualStateGroups**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border) 附加属性，它是 [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 的根元素。 `Checked` **VisualState** 指定名为 `CheckGlyph` 的 [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path)（已在前面的示例中介绍）的 [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) 为 1。 `Indeterminate` **VisualState** 指定名为 `IndeterminateGlyph` 的 [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) 的 **Opacity** 为 1。 `Unchecked` **VisualState** 没有 [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 或 [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard)，因此 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 将返回到其默认外观。

```XAML
<ControlTemplate x:Key="CheckBoxTemplate1" TargetType="CheckBox">
    <Border BorderBrush="{TemplateBinding BorderBrush}"
            BorderThickness="{TemplateBinding BorderThickness}"
            Background="{TemplateBinding Background}">

        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup x:Name="CheckStates">
                <VisualState x:Name="Checked">
                    <VisualState.Setters>
                        <Setter Target="CheckGlyph.Opacity" Value="1"/>
                    </VisualState.Setters>
                    <!-- This Storyboard is equivalent to the Setter. -->
                    <!--<Storyboard>
                        <DoubleAnimation Duration="0" To="1"
                         Storyboard.TargetName="CheckGlyph" Storyboard.TargetProperty="Opacity"/>
                    </Storyboard>-->
                </VisualState>
                <VisualState x:Name="Unchecked"/>
                <VisualState x:Name="Indeterminate">
                    <VisualState.Setters>
                        <Setter Target="IndeterminateGlyph.Opacity" Value="1"/>
                    </VisualState.Setters>
                    <!-- This Storyboard is equivalent to the Setter. -->
                    <!--<Storyboard>
                        <DoubleAnimation Duration="0" To="1"
                         Storyboard.TargetName="IndeterminateGlyph" Storyboard.TargetProperty="Opacity"/>
                    </Storyboard>-->
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="25"/>
            </Grid.RowDefinitions>
            <Rectangle x:Name="NormalRectangle" Fill="Transparent" Height="20" Width="20"
                       Stroke="{ThemeResource SystemControlForegroundBaseMediumHighBrush}"
                       StrokeThickness="{ThemeResource CheckBoxBorderThemeThickness}"
                       UseLayoutRounding="False"/>
            <!-- Create an X to indicate that the CheckBox is selected. -->
            <Path x:Name="CheckGlyph"
                  Data="M103,240 L111,240 119,248 127,240 135,240 123,252 135,264 127,264 119,257 111,264 103,264 114,252 z"
                  Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                  FlowDirection="LeftToRight"
                  Height="14" Width="16" Opacity="0" Stretch="Fill"/>
            <Ellipse x:Name="IndeterminateGlyph"
                     Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                     Height="8" Width="8" Opacity="0" UseLayoutRounding="False" />
            <ContentPresenter x:Name="ContentPresenter"
                              ContentTemplate="{TemplateBinding ContentTemplate}"
                              Content="{TemplateBinding Content}"
                              Margin="{TemplateBinding Padding}" Grid.Row="1"
                              HorizontalAlignment="Center"
                              VerticalAlignment="{TemplateBinding VerticalContentAlignment}"/>
        </Grid>
    </Border>
</ControlTemplate>
```

为了更深入地了解 [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) 对象的工作原理，请考虑当 [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 从 `Unchecked` 状态切换到 `Checked` 状态，然后切换到 `Indeterminate` 状态，然后又恢复为 `Unchecked` 状态时，会发生什么。 下表介绍了这些转换。

|                                      |                                                                                                                                                                                                                                                                                                                                                |                                                   |
|--------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| 状态转换                     | 引发的结果                                                                                                                                                                                                                                                                                                                                   | 转换完成时的 CheckBox 外观 |
| 从 `Unchecked` 到 `Checked`。       | `Checked` [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) 的 [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 值已应用，因此 `CheckGlyph` 的 [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) 为 1。                                                                                                                                                         | 显示 X。                                |
| 从 `Checked` 到 `Indeterminate`。   | `Indeterminate` [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) 的 [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 值已应用，因此 `IndeterminateGlyph` 的 [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) 为 1。 `Checked` **VisualState** 的 **Setter** 值已删除，因此 `CheckGlyph` 的 [**Opacity**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.opacity) 为 0。 | 显示一个圆形。                            |
| 从 `Indeterminate` 到 `Unchecked`。 | `Indeterminate` [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) 的 [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 值已删除，因此 `IndeterminateGlyph` 的 [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) 为 0。                                                                                                                                           | 不显示任何符号。                             |

 
有关如何创建控件的视觉状态（尤其是如何使用 [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) 类和动画类型）的详细信息，请参阅[视觉状态的情节提要动画](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10))。

## <a name="use-tools-to-work-with-themes-easily"></a>使用工具轻松处理主题

将主题应用到控件的一种快捷方式是，在 Microsoft Visual Studio XAML“文档大纲”  上右键单击控件，然后选择“编辑主题”  或“编辑样式”  （具体取决于你所右键单击的控件）。 然后，通过选择“应用资源”  来应用现有主题，或通过选择“创建空项”  来定义一个新主题。

## <a name="controls-and-accessibility"></a>控件和辅助功能

当为控件创建新模板时，除了可能会更改控件的行为和视觉外观外，还可能会更改控件自行代表辅助功能框架的方式。 Windows 应用支持 Microsoft UI 自动化框架用于辅助功能。 所有默认控件及其模板都支持适用于控件的用途和功能的常见 UI 自动化控件类型和模式。 这些控件类型和模式由 UI 自动化客户端（如辅助技术）进行解释，这样允许控件作为较大辅助应用 UI 的一部分进行访问。

若要分离基本控件逻辑以及符合 UI 自动化的某些体系结构要求，控件类在独立类（自动化对等）中包含辅助功能支持。 有时自动化对等会与控件模板有交互，因为对等预期某些命名部件存在于模板中，以便可能会使用诸如允许辅助技术调用按钮操作的功能。

创建全新的自定义控件时，有时还希望随之一起新建自动化对等。 有关详细信息，请参阅[自定义的自动化对等](../accessibility/custom-automation-peers.md)。

## <a name="learn-more-about-a-controls-default-template"></a>了解有关控件默认模板的详细信息

记录了 XAML 控件样式和模板的主题向你显示的起始 XAML 摘录与使用之前介绍的“编辑主题”  或“编辑样式”  技术时看到的相同。 每个主题都将列出视觉状态的名称、使用的主题资源，以及包含该模板的样式的完整 XAML。 如果你已开始修改模板并要查看原始模板的外观，或者想要验证你的新模板是否具有所有所需的命名视觉状态，这些主题将是非常有用的指南。

## <a name="theme-resources-in-control-templates"></a>控件模板中的主题资源

对于 XAML 模板中的某些属性，你可能已注意到使用 [{ThemeResource} 标记扩展](../../xaml-platform/themeresource-markup-extension.md)的资源引用。 这是一种可使单个控件模板使用资源的技术，这些资源可能采用不同的值，具体取决于当前处于活动状态的主题。 这对于画笔和颜色尤其重要，因为主题的主要目的是使用户选择应用于整个系统的是深色主题、浅色主题，还是高对比度主题。 使用 XAML 资源系统的应用可以使用适合该主题的资源集，以便应用 UI 中的主题选择可以反映用户的整个系统的主题选择。

## <a name="get-the-sample-code"></a>获取示例代码

* [XAML 控件库示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics)
* [自定义文本编辑控件示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl)
