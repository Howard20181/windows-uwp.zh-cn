---
description: 本文介绍如何使用 C# 创建适用于 WinUI 3 的 XAML 模板化控件。
title: 使用 C# 将 WinUI 3 应用的 XAML 控件模板化
ms.date: 03/05/2021
ms.topic: article
keywords: windows 10, uwp, 自定义控件, 模板化控件, winui
ms.author: drewbat
author: drewbatgit
ms.localizationpriority: high
ms.custom: 19H1
ms.openlocfilehash: b0616219b1950d476a1b5dd281281399196aad3e
ms.sourcegitcommit: 4b4ace01ab130f0f9784fa0be0dcce28bdfc422f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107265702"
---
# <a name="templated-xaml-controls-for-winui-3-apps-with-c"></a>使用 C# 将 WinUI 3 应用的 XAML 控件模板化

本文介绍如何使用 C# 创建适用于 WinUI 3 的模板化 XAML 控件。 模板化控件继承自 Microsoft.UI.Xaml.Controls.Control，并且具有可使用 XAML 控件模板自定义的可视结构和可视行为。

在执行本文的操作步骤之前，你应确保已配置开发环境以创建 WinUI 3 应用。 有关设置信息，请参阅[适用于桌面应用的 WinUI 3 入门](./get-started-winui3-for-desktop.md)。

## <a name="create-a-blank-app-bglabelcontrolapp"></a>创建空白应用 (BgLabelControlApp)

首先在 Microsoft Visual Studio 中创建新项目。 在“新建项目”对话框中，选择“空白应用(UWP 版 WinUI)”项目模板，并确保选择 C# 语言版本 。 将项目名称设置为“BgLabelControlApp”，使文件名与以下示例中的代码保持一致。 将“目标版本”设置为 Windows 10 版本 1903（内部版本 18362），并将“最低版本”设置为 Windows 10 版本 1803（内部版本 17134） 。 本演练还适用于使用“打包的空白应用(桌面版 WinUI)”项目模板创建的桌面应用，只需确保执行“BgLabelControlApp (桌面)”项目中的所有步骤即可 。

![空白应用项目模板](images/winui-csharp-new-project-uwp.png)

## <a name="add-a-templated-control-to-your-app"></a>向应用添加模板化控件

若要添加模板化控件，请单击工具栏中的“项目”菜单，或在“解决方案资源管理器”中右键单击项目，然后选择“添加新项”  。 在“Visual C#”->“WinUI”下，选择“自定义控件(WinUI)”模板 。 将新控件命名为“BgLabelControl”，然后单击“添加”。 

## <a name="update-the-custom-control-c-file"></a>更新自定义控件 C# 文件

在 C# 文件 BgLabelControl.cs 中，请注意，构造函数定义了控件的 DefaultStyleKey 属性。 此键标识默认模板，如果控件的使用者未显式指定模板，则使用该模版。 键值是控件的类型。 稍后，在实现通用模板文件时，我们将看到使用此键。

```csharp
public BgLabelControl()
{
    this.DefaultStyleKey = typeof(BgLabelControl);
}
```

我们的模板化控件将具有一个文本标签，该标签可通过代码、XAML 或通过数据绑定以编程方式设置。 为了使系统保持控件标签的文本为最新，需要将其实现为 [DependencyPropety](/uwp/api/Windows.UI.Xaml.DependencyProperty)。 为此，我们首先声明一个字符串属性，并将其命名为“Label”。 我们不使用后备变量，而是通过调用 [GetValue](/uwp/api/windows.ui.xaml.dependencyobject.getvalue) 和 [SetValue](/uwp/api/windows.ui.xaml.dependencyobject.setvalue) 来设置和获取依赖项属性的值。 这些方法由 Microsoft.UI.Xaml.Controls.Control 继承的 [DependencyObject](/uwp/api/windows.ui.xaml.dependencyobject) 提供。

```csharp
public string Label
{
    get => (string)GetValue(LabelProperty);
    set => SetValue(LabelProperty, value);
}
```
接下来，声明该依赖项属性，并通过调用 [DependencyProperty.Register](/uwp/api/windows.ui.xaml.dependencyproperty.register) 向系统注册它。 此方法指定“Label”属性的名称和类型、该属性所有者的类型、BgLabelControl 类以及该属性的默认值 。

```csharp
DependencyProperty LabelProperty = DependencyProperty.Register(
    nameof(Label), 
    typeof(string),
    typeof(BgLabelControl), 
    new PropertyMetadata(default(string), new PropertyChangedCallback(OnLabelChanged)));
```

实现依赖项属性只需要这两个步骤，但在此示例中，我们将为 OnLabelChanged 事件添加一个可选处理程序。 每当该属性值更新时，系统都会引发此事件。 在这种情况下，我们将检查新标签文本是否为空字符串，并相应地更新类变量。

```csharp
public bool HasLabelValue { get; set; }

private static void OnLabelChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
{
    BgLabelControl labelControl = d as BgLabelControl; //null checks omitted
    String s = e.NewValue as String; //null checks omitted
    if (s == String.Empty)
    {
        labelControl.HasLabelValue = false;
    }
    else
    {
        labelControl.HasLabelValue = true;
    }
}
```
有关依赖项属性工作原理的详细信息，请参阅[依赖项属性概述](/windows/uwp/xaml-platform/dependency-properties-overview)。

## <a name="define-the-default-style-for-bglabelcontrol"></a>定义 BgLabelControl 的默认样式
模板化控件必须提供默认样式模板，如果控件的用户未显示设置样式，则使用该样式模板。 在此步骤中，我们将修改控件的通用模板文件。

当你将自定义控件 (WinUI) 添加到应用时，就会生成通用模板文件。 此文件命名为“Generic.xaml”，并且在解决方案资源管理器的“Themes”文件夹中生成。 为了让 XAML 框架能够找到模板化控件的默认样式，文件夹和文件名是必须的。 删除 Generic.xaml 的默认内容，然后粘贴下方的标记。



```xaml
<!-- \Themes\Generic.xaml -->
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:BgLabelControlApp">

    <Style TargetType="local:BgLabelControl" >
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="local:BgLabelControl">
                    <Grid Width="100" Height="100" Background="{TemplateBinding Background}">
                        <TextBlock HorizontalAlignment="Center" VerticalAlignment="Center" Text="{TemplateBinding Label}"/>
                    </Grid>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
```

在此示例中，可以看到“Style”元素的“TargetType”属性设置为“BgLabelControlApp”命名空间中的“BgLabelControl”类型   。 此类型的值与前面为控件的构造函数中的“DefaultStyleKey”属性指定的值相同，该构造函数将此值标识为控件的默认样式。

控件模板中“TextBlock”的“Text”属性绑定到控件的“Label”依赖项属性  。 该属性使用 [TemplateBinding](/windows/uwp/xaml-platform/templatebinding-markup-extension) 标记扩展进行绑定。 此示例还将“Grid”背景绑定到继承自“Control”类的“Background”依赖项属性  。

## <a name="add-an-instance-of-bglabelcontrol-to-the-main-ui-page"></a>将 BgLabelControl 的实例添加到主 UI 页面

打开 `MainPage.xaml`，其中包含主 UI 页面的 XAML 标记。 紧接在 Button 元素（StackPanel 内）之后，添加以下标记 。

```xaml
<local:BgLabelControl Background="Red" Label="Hello, World!"/>
```

生成并运行该应用，你将看到模板化控件具有我们指定的背景颜色和标签。

![模板化控件结果](images/winui-templated-control-result.png)


