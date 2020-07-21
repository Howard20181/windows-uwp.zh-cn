---
description: 通过样式可以设置控件属性，并重复使用这些设置，以便保持多个控件具有一致的外观。
MS-HAID: dev\_ctrl\_layout\_txt.styling\_controls
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
ms.date: 01/03/2019
title: XAML 样式
ms.assetid: AB469A46-FAF5-42D0-9340-948D0EDF4150
label: XAML styles
template: detail.hbs
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5a2f73fe6280d3a86510ce48f47017dd4897139a
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79208982"
---
# <a name="xaml-styles"></a>XAML 样式





可以使用 XAML 框架通过多种方式自定义应用的外观。 通过样式可以设置控件属性，并重复使用这些设置，以便保持多个控件具有一致的外观。

## <a name="style-basics"></a>样式基础知识

使用样式可将属性设置提取到可重复使用的资源中。 下面的示例显示了具有设置 [BorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderbrush)、[BorderThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderthickness) 和 [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) 属性样式的 3 个按钮。 通过应用样式可以使控件具有相同外观，而无需单独为每个控件设置这些属性。

![通过样式设计的按钮](images/styles-rainbow-buttons.png)

你可以定义在 XAML 中为控件嵌入的样式，或者作为可重复使用的资源。 在单个页面的 XAML 文件中、App.xaml 文件中，或者单独的资源词典 XAML 文件中定义资源。 可以在应用之间共享资源词典 XAML 文件，并且可以将多个资源词典合并到单个应用中。 定义资源的位置决定了该资源可以使用的范围。 页面级资源只在定义了它们的页面中可用。 如果在 App.xaml 和页面中同时定义了具有相同关键字的资源，则页面中的资源将覆盖 App.xaml 中的资源。 如果资源在单独的资源词典文件中定义，则它的范围由引用资源词典的位置确定。

在 [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) 定义中，你需要一个 [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) 属性和由一个或多个 [Setter](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 元素组成的集合。 **TargetType** 属性是一个指定要应用样式的 [FrameworkElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) 类型的字符串。 **TargetType** 值必须指定由 Windows 运行时定义的派生的 **FrameworkElement** 类型或引用的程序集中提供的自定义类型。 如果你试图将某个样式应用到某控件，而此控件的类型与你试图应用的样式的 **TargetType** 属性不匹配，则会发生异常。

每个 [Setter](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) 元素都需要一个 [Property](https://docs.microsoft.com/uwp/api/windows.ui.xaml.setter.property) 和一个 [Value](https://docs.microsoft.com/uwp/api/windows.ui.xaml.setter.value)。 这些属性设置用于指示该设置将应用于哪个控件属性，以及为该属性设置的值。 你可以使用特性或属性元素语法设置 **Setter.Value**。 下面的 XAML 显示了应用于前面显示的按钮的样式。 在此 XAML 中，前两个 **Setter** 元素使用特性语法，但是最后一个 **Setter**（用于设置 [BorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderbrush) 属性）使用属性元素语法。 该示例不使用 [x:Key 特性](../../xaml-platform/x-key-attribute.md)这一特性，因此该样式已隐式应用到按钮。 隐式或显式应用样式在下一节进行介绍。

```XAML
<Page.Resources>
    <Style TargetType="Button">
        <Setter Property="BorderThickness" Value="5" />
        <Setter Property="Foreground" Value="Black" />
        <Setter Property="BorderBrush" >
            <Setter.Value>
                <LinearGradientBrush StartPoint="0.5,0" EndPoint="0.5,1">
                    <GradientStop Color="Yellow" Offset="0.0" />
                    <GradientStop Color="Red" Offset="0.25" />
                    <GradientStop Color="Blue" Offset="0.75" />
                    <GradientStop Color="LimeGreen" Offset="1.0" />
                </LinearGradientBrush>
            </Setter.Value>
        </Setter>
    </Style>
</Page.Resources>

<StackPanel Orientation="Horizontal">
    <Button Content="Button"/>
    <Button Content="Button"/>
    <Button Content="Button"/>
</StackPanel>
```

## <a name="apply-an-implicit-or-explicit-style"></a>应用隐式或显式样式

如果你将样式定义为资源，有两种方法可将其应用到控件：

-   隐式方法，通过仅指定 [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) 的 [TargetType](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style)。
-   显式方法，通过指定 [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) 的 [TargetType](../../xaml-platform/x-key-attribute.md) 和 [x:Key 特性](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style)这一特性，然后通过使用显式键的 [{StaticResource} 标记扩展](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style)引用设置目标控件的 [Style](https://docs.microsoft.com/windows/uwp/xaml-platform/staticresource-markup-extension) 属性。

如果样式包含 [x:Key 特性](../../xaml-platform/x-key-attribute.md)，则只能通过将控件的 [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style) 属性设置为键控样式，从而将其应用到控件。 相反，没有 x:Key 特性的样式会自动应用到其目标类型的每个控件，这些控件没有显示样式设置。

下面两个按钮演示了隐式和显示样式。

![隐式和显示样式按钮。](images/styles-buttons-implicit-explicit.png)

在本示例中，第一个样式具有 [x:Key 特性](../../xaml-platform/x-key-attribute.md)，其目标类型为 [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button)。 第一个按钮的 [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style) 属性设置为此关键字，所以显示应用该样式。 第二个样式会隐式应用到第二个按钮，因为该按钮的目标类型为 **Button**，并且该样式没有 x:Key 特性。

```XAML
<Page.Resources>
    <Style x:Key="PurpleStyle" TargetType="Button">
        <Setter Property="FontFamily" Value="Segoe UI"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="Foreground" Value="Purple"/>
    </Style>

    <Style TargetType="Button">
        <Setter Property="FontFamily" Value="Segoe UI"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="RenderTransform">
            <Setter.Value>
                <RotateTransform Angle="25"/>
            </Setter.Value>
        </Setter>
        <Setter Property="BorderBrush" Value="Green"/>
        <Setter Property="BorderThickness" Value="2"/>
        <Setter Property="Foreground" Value="Green"/>
    </Style>
</Page.Resources>

<Grid x:Name="LayoutRoot">
    <Button Content="Button" Style="{StaticResource PurpleStyle}"/>
    <Button Content="Button"/>
</Grid>
```

## <a name="use-based-on-styles"></a>基于样式使用

为了使样式便于维护以及优化样式的重复使用，你可以创建从其他样式继承的样式。 使用 [BasedOn](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.basedon) 属性可创建继承的样式。 从其他样式继承的样式必须应用到同一类型的控件，或者从基本样式的目标类型派生出来的控件。 例如，如果基本样式的目标类型为 [ContentControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentControl)，则基于此样式的样式可应用到 **ContentControl** 或从 **ContentControl** 派生的类型（如 [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) 和 [ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer)）。 如果基于样式的值没有设置，则从基本样式继承。 要从基本样式更改值，基于样式会覆盖该值。 下一个示例演示了具有从同一基本样式继承的样式的 **Button** 和 [CheckBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox)。

![使用基于样式的样式设计按钮。](images/styles-buttons-based-on.png)

基本样式应用到 [ContentControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentControl)，并设置 [Height](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) 和 [Width](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) 属性。 基于此样式的样式应用到 [CheckBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) 和 [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button)，这些类型从 **ContentControl** 派生而来。 基于样式为 [BorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderbrush) 和 [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) 属性设置不同的颜色。 （通常不在 **CheckBox** 的周围加边框。 我们在此处执行此操作，来显示样式的效果。）

```XAML
<Page.Resources>
    <Style x:Key="BasicStyle" TargetType="ContentControl">
        <Setter Property="Width" Value="130" />
        <Setter Property="Height" Value="30" />
    </Style>

    <Style x:Key="ButtonStyle" TargetType="Button"
           BasedOn="{StaticResource BasicStyle}">
        <Setter Property="BorderBrush" Value="Orange" />
        <Setter Property="BorderThickness" Value="2" />
        <Setter Property="Foreground" Value="Red" />
    </Style>

    <Style x:Key="CheckBoxStyle" TargetType="CheckBox"
           BasedOn="{StaticResource BasicStyle}">
        <Setter Property="BorderBrush" Value="Blue" />
        <Setter Property="BorderThickness" Value="2" />
        <Setter Property="Foreground" Value="Green" />
    </Style>
</Page.Resources>

<StackPanel>
    <Button Content="Button" Style="{StaticResource ButtonStyle}" Margin="0,10"/>
    <CheckBox Content="CheckBox" Style="{StaticResource CheckBoxStyle}"/>
</StackPanel>
```

## <a name="use-tools-to-work-with-styles-easily"></a>使用工具轻松处理样式

将样式应用到控件的一种快捷方式是，在 Microsoft Visual Studio XAML 设计界面上，右键单击控件并选择“编辑样式”  或“编辑模板”  （取决于右键单击的控件）。 然后，通过选择“应用资源”  来应用现有样式，或通过选择“创建空项”  来定义一个新样式。 如果创建空白样式，则可以使用相应的选项在该页面、App.xaml 文件或一个单独的资源字典中进行定义。

## <a name="lightweight-styling"></a>轻型样式设置

通常在应用级别或页面级别上替代系统画笔，在任意情况下，颜色替代都将影响引用该画笔的所有控件；在 XAML 中，许多控件可以引用相同的系统画笔。

![通过样式设计的按钮](images/LightweightStyling_ButtonStatesExample.png)

```XAML
<Page.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <ResourceDictionary x:Key="Light">
                 <SolidColorBrush x:Key="ButtonBackground" Color="Transparent"/>
                 <SolidColorBrush x:Key="ButtonForeground" Color="MediumSlateBlue"/>
                 <SolidColorBrush x:Key="ButtonBorderBrush" Color="MediumSlateBlue"/>
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Page.Resources>
```

对于诸如 PointerOver（鼠标悬停在按钮上）、**PointerPressed**（按钮已调用）或 Disabled（按钮不可交互）的状态。 这些后缀将追加到原始轻型样式设置名称：**ButtonBackgroundPointerOver**、**ButtonForegroundPointerPressed**、**ButtonBorderBrushDisabled** 等。修改这些画笔还将确保控件的配色与应用的主题保持一致。

在 **App.Resources** 级别上放置这些画笔替代将更改整个应用（而不是单个页面）内的所有按钮。

### <a name="per-control-styling"></a>每个控件的样式设置

在其他情况下，需要仅以特定方式更改一个页面上的单个控件，而不更改该控件的任何其他版本：

![通过样式设计的按钮](images/LightweightStyling_CheckboxExample.png)

```XAML
<CheckBox Content="Normal CheckBox" Margin="5"/>
<CheckBox Content="Special CheckBox" Margin="5">
    <CheckBox.Resources>
        <ResourceDictionary>
            <ResourceDictionary.ThemeDictionaries>
                <ResourceDictionary x:Key="Light">
                    <SolidColorBrush x:Key="CheckBoxForegroundUnchecked"
                        Color="Purple"/>
                    <SolidColorBrush x:Key="CheckBoxForegroundChecked"
                        Color="Purple"/>
                    <SolidColorBrush x:Key="CheckBoxCheckGlyphForegroundChecked"
                        Color="White"/>
                    <SolidColorBrush x:Key="CheckBoxCheckBackgroundStrokeChecked"  
                        Color="Purple"/>
                    <SolidColorBrush x:Key="CheckBoxCheckBackgroundFillChecked"
                        Color="Purple"/>
                </ResourceDictionary>
            </ResourceDictionary.ThemeDictionaries>
        </ResourceDictionary>
    </CheckBox.Resources>
</CheckBox>
<CheckBox Content="Normal CheckBox" Margin="5"/>
```

这仅影响存在该控件的页面上的一个“特殊复选框”。

## <a name="modify-the-default-system-styles"></a>修改默认系统样式

在可以使用来自 Windows 运行时默认 XAML 资源的样式时，应使用这些样式。 在必须定义自己的样式时，如果可能，请尝试以默认样式为基础（如前所述，使用基于样式，或先编辑最初默认样式的副本）。

## <a name="the-template-property"></a>template 属性

样式 setter 可用于 [Control](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template) 的 [Template](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control) 属性，实际上，此样式构成了多数典型的 XAML 样式和应用的 XAML 资源。 此内容将在主题[控件模板](control-templates.md)中更详细地讨论。
