---
description: 了解如何使用 TemplateSettings 类提供一组定义新控件模板的属性。
title: 模板设置类
ms.assetid: CAE933C6-EF13-465A-9831-AB003AF23907
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 23418233769d893e755ee8981aa9f66aa9404758
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89154961"
---
# <a name="template-settings-classes"></a>模板设置类


## <a name="prerequisites"></a>必备条件

我们假定你可以将控件添加到 UI，设置控件的属性以及连接事件处理程序。 有关将控件添加到应用的说明，请参阅[添加控件和处理事件](../design/controls-and-patterns/controls-and-events-intro.md)。 我们还假设你了解如何通过创建默认模板的副本并对其进行编辑来定义控件的自定义模板的基础知识。 有关详细信息，请参阅[快速入门：控件模板](/previous-versions/windows/apps/hh465374(v=win.10))。

## <a name="the-scenario-for-templatesettings-classes"></a>适用于 **TemplateSettings** 类的方案

**TemplateSettings** 类提供一组可在为控件定义新控件模板时使用的属性。 这些属性具有诸如特定 UI 元素部件大小的像素度量之类的值。 这些值有时是来自于通常难以覆盖甚至访问的控件逻辑的经计算的值。 其中一些属性旨在作为控制部件的过渡和动画的 **From****To** 值，因此也是成对出现的相关 **TemplateSettings** 属性。

有多个 **TemplateSettings** 类。 它们全部都在 [**Windows.UI.Xaml.Controls.Primitives**](/uwp/api/Windows.UI.Xaml.Controls.Primitives) 命名空间中。 下面是类的列表，以及指向相关控件的 **TemplateSettings** 属性的链接。 此 **TemplateSettings** 属性是你访问控件的 **TemplateSettings** 值的方式，并且可以建立对其属性的模板绑定：

-   [**ComboBoxTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ComboBoxTemplateSettings)：[**ComboBox.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.combobox.templatesettings) 的值
-   [**GridViewItemTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.GridViewItemTemplateSettings)：[**GridViewItem.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.gridviewitem.templatesettings) 的值
-   [**ListViewItemTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ListViewItemTemplateSettings)：[**ListViewItem.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.listviewitem.templatesettings) 的值
-   [**ProgressBarTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ProgressBarTemplateSettings)：[**ProgressBar.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.progressbar.templatesettings) 的值
-   [**ProgressRingTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ProgressRingTemplateSettings)：[**ProgressRing.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.progressring.templatesettings) 的值
-   [**SettingsFlyoutTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.SettingsFlyoutTemplateSettings)：[**SettingsFlyout.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.settingsflyout.templatesettings) 的值
-   [**ToggleSwitchTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ToggleSwitchTemplateSettings)：[**ToggleSwitch.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.toggleswitch.templatesettings) 的值
-   [**ToolTipTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ToolTipTemplateSettings)：[**ToolTip.TemplateSettings**](/uwp/api/windows.ui.xaml.controls.tooltip.templatesettings) 的值

**TemplateSettings** 属性始终旨在用于 XAML 中，而不是用于代码。 它们是父控件的只读 **TemplateSettings** 属性的只读子属性。 对于高级自定义控件方案，在此方案中你要创建新的基于 [**Control**](/uwp/api/Windows.UI.Xaml.Controls.Control) 的类并且可以影响控件逻辑，请在控件上定义一个自定义 **TemplateSettings** 属性，以传送可能对任何正在为控件重新创建模板的人有用的信息。 作为该属性的只读值，请定义与你的控件相关的新 **TemplateSettings** 类，此类具有与模板度量、动画位置等相关的每个信息项的只读属性，并且向调用方提供使用你的控件逻辑初始化的该类的运行时实例。 **TemplateSettings** 类派生自 [**DependencyObject**](/uwp/api/Windows.UI.Xaml.DependencyObject)，因此这些属性可以使用依赖属性系统进行更改属性的回调。 但是这些属性的依赖属性标识符将不公开为公共 API，因为 **TemplateSettings** 属性旨在对调用方只读。

## <a name="how-to-use-templatesettings-in-a-control-template"></a>如何在控件模板中使用 **TemplateSettings**

下面是来自初始默认 XAML 控件模板的示例。 此特定的示例来自 [**ProgressRing**](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) 的默认模板：

```xml
<Ellipse
    x:Name="E1"
    Style="{StaticResource ProgressRingEllipseStyle}"
    Width="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Height="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Margin="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseOffset}"
    Fill="{TemplateBinding Foreground}"/>
```

[**ProgressRing**](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) 模板的完整 XAML 有数百行，因此这只是一个小小的摘要。 此 XAML 定义作为 6 个 [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) 元素之一的控件部件，此元素描绘不确定进度的旋转动画。 作为开发人员，你可能不喜欢圆形，而且可能针对动画处理方式使用不同的图形基元或不同的基本形状。 例如，你可能撰写一个改用一组以方形排列的[**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) 元素的 **ProgressRing**。 如果是这样，则你的新模板的单个 **Rectangle** 组件可能如下所示：

```xml
<Rectangle
    x:Name="R1"
    Width="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Height="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Margin="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseOffset}"
    Fill="{TemplateBinding Foreground}"/>
```

**TemplateSettings** 属性在此处有用的原因是，它们是来自 [**ProgressRing**](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) 的基本控件逻辑的经计算的值。 计算划分 **ProgressRing** 的总体 [**ActualWidth**](/uwp/api/windows.ui.xaml.frameworkelement.actualwidth) 和 [**ActualHeight**](/uwp/api/windows.ui.xaml.frameworkelement.actualheight)，并且为其模板中的每个动作元素分配经计算的度量，以便模板部件可以调整内容大小。

下面是另一个来自默认 XAML 控件模板的示例用法，这次显示作为动画的 **From** 和 **To** 的属性集之一。 它来自 [**ComboBox**](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) 默认模板：

```xml
<VisualStateGroup x:Name="DropDownStates">
    <VisualState x:Name="Opened">
        <Storyboard>
            <SplitOpenThemeAnimation
               OpenedTargetName="PopupBorder"
               ContentTargetName="ScrollViewer"
               ClosedTargetName="ContentPresenter"
               ContentTranslationOffset="0"
               OffsetFromCenter="{Binding RelativeSource={RelativeSource TemplatedParent}, 
                 Path=TemplateSettings.DropDownOffset}"
               OpenedLength="{Binding RelativeSource={RelativeSource TemplatedParent}, 
                 Path=TemplateSettings.DropDownOpenedHeight}"
               ClosedLength="{Binding RelativeSource={RelativeSource TemplatedParent},
                 Path=TemplateSettings.DropDownClosedHeight}" />
        </Storyboard>
   </VisualState>
...
</VisualStateGroup>
```

同样，模板中有许多 XAML，因此我们只显示摘要。 而且这仅仅是分别使用相同 [**ComboBoxTemplateSettings**](/uwp/api/Windows.UI.Xaml.Controls.Primitives.ComboBoxTemplateSettings) 属性的多个状态和主题动画之一。 对于 [**ComboBox**](/uwp/api/Windows.UI.Xaml.Controls.ComboBox)，通过绑定使用 **ComboBoxTemplateSettings** 值强制模板中的相关动画停止，并在基于共享值的位置上开始，以便平稳地过渡。

**注意**   使用**TemplateSettings**值作为控件模板的一部分时，请确保设置的属性与值的类型相匹配。 如果不是，你可能需要为绑定创建一个值转换器，以便可以从 **TemplateSettings** 值的不同源类型转换为绑定的目标类型。 有关详细信息，请参阅 [**IValueConverter**](/uwp/api/Windows.UI.Xaml.Data.IValueConverter)。

## <a name="related-topics"></a>相关主题

* [快速入门：控件模板](/previous-versions/windows/apps/hh465374(v=win.10))