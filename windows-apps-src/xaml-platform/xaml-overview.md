---
description: 向 Windows 运行时应用开发人员群体介绍了 XAML 语言和 XAML 概念，并介绍了在 XAML 中声明对象和设置属性的不同方式，因为它用于创建 Windows 运行时应用。
title: XAML 概述
ms.assetid: 48041B37-F1A8-44A4-BB8E-1D4DE30E7823
ms.date: 07/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: 6d45e70afa5b0dc6e903dbd253c09042bb2046c2
ms.sourcegitcommit: f7ef7e894d7b7fc24483b4485605686abf8f2e93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679213"
---
# <a name="xaml-overview"></a>XAML 概述

本文介绍了 Windows 运行时应用开发人员群体的 XAML 语言和 XAML 概念，并介绍了在 XAML 中声明对象和设置属性的不同方式，因为它用于创建 Windows 运行时应用。

## <a name="what-is-xaml"></a>什么是 XAML？

Extensible Application Markup Language (Extensible Application Markup Language, XAML) 是一种声明性语言。 具体而言，XAML 可以使用语言结构初始化对象并设置对象的属性，该结构显示多个对象之间的层次结构关系和支持类型扩展的支持类型约定。 你可以在声明性的 XAML 标记中创建可视的 UI 元素。 然后你可以为每个 XAML 文件关联一个独立的代码隐藏文件，以响应事件和处理最初在 XAML 中声明的对象。

XAML 语言支持在开发过程中的不同工具和角色之间交换源，例如在设计工具和交互式开发环境（IDE）之间交换 XAML 源，或在主要开发人员和本地化之间交换 XAML 源商. 通过将 XAML 用作交换格式，可以分开或整合设计人员角色和开发人员角色，并且设计人员和开发人员可以在开发应用期间迭代。

如果将它们视为 Windows 运行时应用项目的一部分，则 XAML 文件即是带 .xaml 扩展名的 XML 文件。

## <a name="basic-xaml-syntax"></a>基本 XAML 语法

XAML 的基本语法基于 XML。 依照定义，有效的 XAML 必须也是有效的 XML。 但 XAML 还具有语法概念，这些概念是根据 XML 1.0 规范，赋予了不同的和更完整的含义，但在 XML 中仍有效。 例如，XAML 支持*属性元素语法*，其中属性值可在元素中设置，而不是在属性中作为字符串值或内容进行设置。 对于常规 XML 而言，XAML 属性元素是名称中带点号的元素，因此它对于纯 XML 有效，但具有不同的含义。

## <a name="xaml-and-visual-studio"></a>XAML 和 Visual Studio

无论是在 XAML 文本编辑器中，还是在更为图形化的 XAML 设计界面中，Microsoft Visual Studio 都可以帮助你生成有效的 XAML 语法。 使用 Visual Studio 为应用编写 XAML 时，不要担心每个击键的语法太多。 IDE 通过提供自动完成提示来鼓励有效的 XAML 语法，其中显示了 Microsoft IntelliSense 列表和下拉列表中的建议，并在 "**工具箱**" 窗口中显示了 UI 元素库或其他技术。 如果这是您第一次使用 XAML，则了解语法规则可能仍有用，尤其是在描述 XAML 语法引用或其他主题时，有时用于描述限制或选择的术语。 Xaml 语法的精细点在一个单独的主题[xaml 语法指南](xaml-syntax-guide.md)中介绍。

## <a name="xaml-namespaces"></a>XAML 命名空间

在常规编程中，命名空间是一种组织概念，用于确定如何解释编程实体的标识符。 通过使用命名空间，编程框架可将用户声明的标识符与框架声明的标识符区分开、通过命名空间限定消除标识符的歧义、强制执行范围名称规则等。 XAML 具有为 XAML 语言实现此用途的其自己的 XAML 命名空间概念。 下面是 XAML 应用并扩展 XML 语言命名空间概念的方式：

- XAML 可将保留的 XML 属性 **xmlns** 用于命名空间声明。 该属性的值通常是一个统一资源标识符 (URI)，它是从 XML 继承的约定。
- XAML 使用声明性前缀来声明非默认命名空间，并声明元素和属性中使用的前缀引用该命名空间。
- XAML 有一个叫做默认命名空间的概念，当不使用或声明任何现有前缀时，便会使用该命名空间。 针对每一个 XAML 编程框架，可以对默认命名空间进行不同的定义。
- 在 XAML 文件或构造中，命名空间定义从父元素继承到子元素。 例如，如果在 XAML 文件的根元素中定义命名空间，则该文件中的所有元素都将继承该命名空间定义。 如果以后添加到该页面中的某个元素重新定义了命名空间，则该元素的后代将继承新定义。
- 一个元素的属性继承该元素的命名空间。 XAML 属性很少有前缀。

XAML 文件几乎总是在其根元素中声明一个默认 XAML 命名空间。 默认 XAML 命名空间定义了无需使用前缀来限定即可声明哪些元素。 对于典型的 Windows 运行时应用项目，此默认命名空间包含用于 UI 定义的所有 Windows 运行时的内置 XAML 词汇：默认控件、文本元素、XAML 图形和动画、数据绑定和样式支持类型等。 因此，为 Windows 运行时应用编写的大部分 XAML 在引用常见 UI 元素时都将避免使用 XAML 命名空间和前缀。

下面的代码段显示了模板为应用程序的初始页面创建的 <xref:Windows.UI.Xaml.Controls.Page> 根（仅显示开始标记和简化）。 它声明了默认命名空间，还有 **x** 命名空间（将在下面加以介绍）。

```xml
<Page
    x:Class="Application1.BlankPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
>
```

## <a name="the-xaml-language-xaml-namespace"></a>XAML 语言 XAML 命名空间

一种在几乎每个 Windows 运行时 XAML 文件中都会声明的特定 XAML 命名空间即为 XAML 语言命名空间。 此命名空间包括 XAML 语言规范定义的元素和概念。 根据约定，XAML 语言 XAML 命名空间会映射到前缀“x”。 Windows 运行时应用项目的默认项目和文件模板始终会将默认 XAML 命名空间（没有前缀，只有 `xmlns=`）和 XAML 语言 XAML 命名空间（前缀为“x”）定义为根元素的一部分。

“x”前缀/XAML 语言 XAML 命名空间包含你在 XAML 中经常使用的多种编程结构。 下面是一些最常见的结构：

| 术语 | 描述 |
|------|-------------|
| [x：Key](x-key-attribute.md) | 为 XAML <xref:Windows.UI.Xaml.ResourceDictionary>中的每个资源设置唯一的用户定义的键。 该密钥的令牌字符串是 **StaticResource** 标记扩展的参数，你可以在以后使用此密钥在应用 XAML 的其他位置检索其他 XAML 用法中的 XAML 资源。 |
| [x：Class](x-class-attribute.md) | 为 XAML 页面提供代码隐藏的类指定代码命名空间和代码类名称。 这可为构建你的应用时通过构建操作创建或加入的类命名。 这些构建操作支持 XAML 标记编译器，并在编译应用时，将你的标记和代码隐藏文件组合到一起。 必须具有此类才能支持对 XAML 页面实现代码隐藏。 默认 Windows 运行时激活模式下 <xref:Windows.UI.Xaml.Window.Content%2A?displayProperty=nameWithType>。 |
| [x：Name](x-name-attribute.md) | 在处理 XAML 中定义的对象元素后，为运行时代码中存在的实例指定一个运行时对象名。 你可以将在 XAML 中设置 **x:Name** 看作是在代码中声明命名变量。 稍后你会了解，这是将 XAML 加载为 Windows 运行时应用的一个组件时发生的实际情况。 <br/><div class="alert">**请注意**<xref:Windows.UI.Xaml.FrameworkElement.Name%2A> 是框架中的类似属性，但并不是所有元素都支持它。 每当该元素类型不支持**FrameworkElement.Name**时，请使用**x：Name**进行元素标识。 |
| [x：Uid](x-uid-directive.md) | 标识某些元素，应将本地化后的资源用于该元素的一些属性值。 有关如何使用 **x:Uid** 的详细信息，请参阅[快速入门：翻译 UI 资源](/previous-versions/windows/apps/hh965329(v=win.10))。 |
| [XAML 固有数据类型](xaml-intrinsic-data-types.md) | 当属性或资源需要值时，这些类型可以为简单值类型指定值。 这些固有类型与通常定义为每个编程语言固有定义的一部分的简单值类型相对应。 例如，你可能需要一个对象，该对象表示要在 <xref:Windows.UI.Xaml.Media.Animation.ObjectAnimationUsingKeyFrames> storyboarded 可视状态中使用的**true**布尔值。 对于 XAML 中的该值，您将使用**x:Boolean**内部类型作为 object 元素，如下所示： <code>&lt;x:Boolean&gt;True&lt;/x:Boolean&gt;</code> |

在 XAML 语言 XAML 命名空间中还存在其他编程结构，但没有这么常用。

## <a name="mapping-custom-types-to-xaml-namespaces"></a>将自定义类型映射到 XAML 命名空间

作为一种语言，XAML 功能最强大的方面之一在于，可以轻松地扩展 Windows 运行时应用的 XAML 词汇。 你可以使用应用的编程语言定义自己的自定义类型，然后在 XAML 标记中引用你的自定义类型。 从根本上讲，通过自定义类型提供的扩展支持已经内置于 XAML 语言的工作原理。 框架或应用开发人员负责创建 XAML 引用的支持对象。 框架和应用开发人员都不会按照其词汇中的对象表示的规范或超出基本的 XAML 语法规则。 （XAML 语言 XAML 命名空间类型应做些什么，但 Windows 运行时提供了所有必要的支持。）

如果你为来自非 Windows 运行时核心库和元数据的类型使用 XAML，则必须声明和映射带前缀的 XAML 命名空间。 在元素用法中使用该前缀以引用已在你的库中定义的类型。 通常，你可以在根元素以及其他 XAML 命名空间定义中将前缀映射声明为 **xmlns** 属性。

要使你自己的命名空间定义引用自定义类型，你首先需要指定关键字 **xmlns:** ，然后指定所需的前缀。 该属性的值必须包含关键字 **using:** ，将其作为该值的第一部分。 该值的其余部分为字符串令牌，它按名称引用特定的代码支持的命名空间，并且其中还包含你的自定义类型。

该前缀定义的标记令牌用于在该 XAML 文件中标记的剩余部分中引用该 XAML 命名空间。 前缀与要在 XAML 命名空间中引用的实体之间有一个冒号字符 (:)。

例如，用于将前缀 `myTypes` 映射到命名空间 `myCompany.myTypes` 的特性语法为： `    xmlns:myTypes="using:myCompany.myTypes"`，具有代表性的元素用法为： `<myTypes:CustomButton/>`

有关为自定义类型映射 XAML 命名空间的详细信息，包括 Visual C++ 组件扩展 (C++/CX) 的特殊注意事项，请参阅 [XAML 命名空间和命名空间映射](xaml-namespaces-and-namespace-mapping.md)。

## <a name="other-xaml-namespaces"></a>其他 XAML 命名空间

你常常会看到定义前缀“d”（设计器命名空间）和“mc”（用于实现标记兼容性）的 XAML 文件。 通常，这些是为了支持基础结构，或在设计时工具中启用方案。 有关详细信息，请参阅 [XAML 命名空间主题中的“其他 XAML 命名空间”部分](xaml-namespaces-and-namespace-mapping.md#other-XAML-namespaces)。

## <a name="markup-extensions"></a>标记扩展

标记扩展是一个 XAML 语言概念，常常用在 Windows 运行时 XAML 实现中。 标记扩展通常表示某种“快捷方式”，它允许 XAML 文件访问无法只根据支持类型声明元素的值或行为。 某些标记可通过纯字符串或其他嵌套的元素设置属性，目的是简化不同 XAML 文件之间的语法或因素处理。

在 XAML 属性语法中，花括号“{”和“}”表示 XAML 标记扩展用法。 此用法指示 XAML 处理从对属性值的一般处理转义为处理文字字符串，或者处理一个可直接进行字符串转换的值。 相反，XAML 分析器将调用提供该特定标记扩展行为的代码，以及提供 XAML 分析器所需的备用对象或行为结果的代码。 标记扩展可以具有参数，这些参数遵循标记扩展名称，也包含在花括号中。 通常，评估的标记扩展可提供对象返回值。 分析期间，该返回值将插入到对象树的某个位置中，在该位置中标记扩展用法已位于源 XAML 中。

Windows 运行时 XAML 支持在默认 XAML 命名空间下定义且 Windows 运行时 XAML 分析器可以理解的以下标记扩展：

- [{x:Bind}](x-bind-markup-extension.md)：支持数据绑定，它在运行时通过执行在编译时生成的特殊用途代码，将属性计算延迟到运行时。 此标记扩展支持广泛的参数。
- [{Binding}](binding-markup-extension.md)：支持数据绑定，用于通过执行特殊用途的运行时对象检查，延迟属性评估直至运行时。 此标记扩展支持广泛的参数。
- [{StaticResource}](staticresource-markup-extension.md)：支持引用 <xref:Windows.UI.Xaml.ResourceDictionary>中定义的资源值。 这些资源可以位于不同的 XAML 文件中，但最终必须在加载时可供 XAML 分析器查找。 `{StaticResource}` 使用的参数将标识 <xref:Windows.UI.Xaml.ResourceDictionary>中的键控资源的键（名称）。
- [{ThemeResource}](themeresource-markup-extension.md)：类似于 [{StaticResource}](staticresource-markup-extension.md)，但可响应运行时主题变更。 {ThemeResource} 经常显示在 Windows 运行时的默认 XAML 模板中，因为其中的大部分模板专门用于用户在应用运行时切换主题的兼容性。
- [{TemplateBinding}](templatebinding-markup-extension.md)：[{Binding}](binding-markup-extension.md) 的一种特殊情况，支持 XAML 中的控制模板及其在运行时的最终用法。
- [{RelativeSource}](relativesource-markup-extension.md)：支持一种特定形式的模板绑定，其中的值来自于父模板。
- [{CustomResource}](customresource-markup-extension.md)：适用于资源查找的高级场景。

Windows 运行时还支持 [{x:Null} 标记扩展](x-null-markup-extension.md)。 你可以使用它在 XAML 中将 [**Nullable**](/dotnet/api/system.nullable-1) 值设置为 **null**。 例如，你可以在 <xref:Windows.UI.Xaml.Controls.CheckBox>的控件模板中使用此项，这会将**null**解释为不确定的检查状态（触发 "不确定" 可视状态）。

标记扩展通常返回应用程序的其他部分的现有实例，或将值推迟到运行时。 因为可以将标记扩展用作属性值，并且这是典型用法，所以你经常可以看到标记扩展被用来为引用类型的属性提供值，而采用其他方式时，这些属性可能需要使用属性元素语法。

例如，下面是从 <xref:Windows.UI.Xaml.ResourceDictionary>中引用可重用 <xref:Windows.UI.Xaml.Style> 的语法： `<Button Style="{StaticResource SearchButtonStyle}"/>`。 <xref:Windows.UI.Xaml.Style> 是引用类型，而不是简单值，因此，如果没有 `{StaticResource}` 用法，则需要一个 `<Button.Style>` 属性元素，并在其中 `<Style>` 定义来设置 <xref:Windows.UI.Xaml.FrameworkElement.Style%2A?displayProperty=nameWithType> 属性。

通过使用标记扩展，在 XAML 中可设置的所有属性都可能采用属性语法进行设置。 你可以使用属性语法提供一个属性的引用值，即使它不支持通过其他方法用于直接对象实例化的属性语法也是如此。 或者可以实现一种特定的行为，通过值类型或新创建的引用类型来延迟需填充 XAML 属性的一般需求。

为举例说明，下一个 XAML 示例使用特性语法来设置 <xref:Windows.UI.Xaml.Controls.Border> 的 <xref:Windows.UI.Xaml.FrameworkElement.Style%2A?displayProperty=nameWithType> 属性的值。 <xref:Windows.UI.Xaml.FrameworkElement.Style%2A?displayProperty=nameWithType> 属性采用 <xref:Windows.UI.Xaml.Style?displayProperty=nameWithType> 类的实例，默认情况下，无法使用特性语法字符串创建引用类型。 但在本例中，该属性引用一个特定的标记扩展 [StaticResource](staticresource-markup-extension.md)。 当处理该标记扩展时，它返回对之前已被定义为资源字典中的键控资源的一个 **Style** 元素的引用。

```xml
<Canvas.Resources>
  <Style TargetType="Border" x:Key="PageBackground">
    <Setter Property="BorderBrush" Value="Blue"/>
    <Setter Property="BorderThickness" Value="5"/>
  </Style>
</Canvas.Resources>
...
<Border Style="{StaticResource PageBackground}">
  ...
</Border>
```

你可以嵌套使用标记扩展。 会首先计算最里层的标记扩展。

由于标记扩展的缘故，你需要使用特殊语法来表示属性中的“{”文本值。 有关详细信息，请参阅 [XAML 语法指南](xaml-syntax-guide.md)。

## <a name="events"></a>事件

XAML 是一种用于对象及其属性的声明性语言，但它也包含向标记中的对象附加事件处理程序的语法。 XAML 事件语法可通过 Windows 运行时编程模型集成 XAML 声明的事件。 在处理事件的对象上，将事件的名称指定为一个属性名称。 对于属性值，指定你在代码中定义的一个事件处理函数的名称。 XAML 处理器使用此名称在加载的对象树中创建一个委托表示，将指定的处理程序添加到内部处理程序列表中。 几乎所有 Windows 运行时应用都是同时通过标记和代码隐藏源文件定义的。

下面是一个简单示例。 <xref:Windows.UI.Xaml.Controls.Button> 类支持名为 <xref:Windows.UI.Xaml.Controls.Primitives.ButtonBase.Click>的事件。 你可以为 **Click** 编写处理程序，用于运行在用户单击 **Button** 之后应当调用的代码。 在 XAML 中，你可以将 **Click** 指定为 **Button** 上的一个属性。 对于属性值，请提供一个表示你的处理程序的方法名称的字符串。

```xml
<Button Click="showUpdatesButton_Click">Show updates</Button>
```

当你进行编译时，编译器现在预期在代码隐藏文件中将有一个已定义的名为 `showUpdatesButton_Click` 的方法，位于 XAML 页的 [x:Class](x-class-attribute.md) 值中声明的命名空间中。 此外，该方法必须满足 <xref:Windows.UI.Xaml.Controls.Primitives.ButtonBase.Click> 事件的委托协定。 例如：

```csharp
namespace App1
{
    public sealed partial class MainPage: Page {
        ...
        private void showUpdatesButton_Click (object sender, RoutedEventArgs e) {
            //your code
        }
    }
}
```

```vb
' Namespace included at project level
Public NotInheritable Class MainPage
    Inherits Page
        ...
        Private Sub showUpdatesButton_Click (sender As Object, e As RoutedEventArgs e)
            ' your code
        End Sub
    ...
End Class
```

```cppwinrt
namespace winrt::App1::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        ...
        void showUpdatesButton_Click(Windows::Foundation::IInspectable const&, Windows::UI::Xaml::RoutedEventArgs const&);
    };
}
```

```cpp
// .h
namespace App1
{
    public ref class MainPage sealed {
        ...
    private:
        void showUpdatesButton_Click(Object^ sender, RoutedEventArgs^ e);
    };
}
```

在一个项目中，将 XAML 编写为 .xaml 文件，使用你最喜欢的语言（C#、Visual Basic、C++/CX）编写一个代码隐藏文件。 当在项目的生成操作中对 XAML 文件进行标记编译时，可以通过将一个命名空间和类指定为 XAML 页面根元素的 [x:Class](x-class-attribute.md) 属性来标识每个 XAML 页面的 XAML 代码隐藏文件的位置。 有关这些机制在 XAML 中如何工作以及它们与编程和应用程序模型之间的关系的详细信息，请参阅[事件和路由事件概述](events-and-routed-events-overview.md)。

> [!NOTE]
> 对于C++/cx，有两个代码隐藏文件：一个是标头（.h），另一个是实现（.xaml）。 该实现引用标头，从技术上讲，标头表示代码隐藏连接的入口点。

## <a name="resource-dictionaries"></a>资源字典

创建 <xref:Windows.UI.Xaml.ResourceDictionary> 通常是通过创作资源字典作为 XAML 页或单独的 XAML 文件的区域来完成的。 资源字典以及如何使用资源字典是一个非常大的概念区域，不在本主题的讨论范围内。 有关详细信息，请参阅 [ResourceDictionary 和 XAML 资源引用](../design/controls-and-patterns/resourcedictionary-and-xaml-resource-references.md)。

## <a name="xaml-and-xml"></a>XAML 和 XML

XAML 语言基本上基于 XML 语言。 但 XAML 显著扩展了 XML。 具体来讲，由于它与支持类型概念之间的关系，它处理架构概念的方式显著不同，并且它添加了很多语言元素，例如附加成员和标记扩展。 **xml:lang** 在 XAML 中是有效的，但会影响运行时而不是分析行为，通常又称为一种框架级属性。 有关详细信息，请参阅<xref:Windows.UI.Xaml.FrameworkElement.Language%2A?displayProperty=nameWithType>。 **xml:base** 在标记中是有效的，但分析器会忽略它。 **xml:space** 是有效的，但仅与 [XAML 和空格](xaml-and-whitespace.md)主题中介绍的场景相关。 **encoding** 属性在 XAML 中是有效的。 仅支持 UTF-8 和 UTF-16 编码。 不支持 UTF-32 编码。

###  <a name="case-sensitivity-in-xaml"></a>XAML 中区分大小写

XAML 中是区分大小写的。 这是 XAML 基于 XML 的另一个后果，XML 是区分大小写的。 XAML 元素和属性的名称是区分大小写的。 属性的值可能区分大小写，这取决于为特定属性处理属性值的方式。 例如，如果属性值声明一个枚举的成员名称，将成员名称字符串进行类型转换，以返回枚举成员值的内部行为是不区分大小写的。 相反，**Name** 属性的值和基于 **Name** 属性声明的名称来处理对象的实用程序方法会对名称字符串区分大小写。

## <a name="xaml-namescopes"></a>XAML 名称范围

XAML 语言定义了一个 XAML 名称范围的概念。 XAML 名称范围概念会影响 XAML 处理器在处理可用于 XAML 的 **x:Name** 或 **Name** 值时的方式，特别是依赖名称成为唯一标识符的领域。 XAML 名称范围将在一个单独的主题中详细介绍；请参阅 [XAML 名称范围](xaml-namescopes.md)。

## <a name="the-role-of-xaml-in-the-development-process"></a>XAML 在开发过程中的作用

XAML 在应用开发过程中发挥着多项重要作用。

- 如果使用 C#、Visual Basic 或 C++/CX 编程，XAML 是声明应用的 UI 和该 UI 中各个元素的主要格式。 通常，项目中至少有一个 XAML 文件表示应用中最初显示的 UI 的页面隐喻。 更多的 XAML 文件可以为导航 UI 声明更多页面。 其他 XAML 文件可声明资源，例如模板或样式。
- 使用 XAML 格式为应用的控件和 UI 声明样式和模板。
- 你可以使用样式和模板将现有控件模板化，或者定义一个控件，在一个控件包中提供默认模板。 当你使用它来定义样式和模板时，通常会将相关 XAML 声明为带有 <xref:Windows.UI.Xaml.ResourceDictionary> 根的离散 XAML 文件。
- XAML 是设计器用于支持创建应用 UI 和在不同设计器应用之间交换 UI 设计的通用格式。 最重要的是，应用的 XAML 可在不同的 XAML 设计工具（或工具中的设计窗口）之间交换。
- 其他一些技术也使用 XAML 定义基本 UI。 相对于 Windows Presentation Foundation (WPF) XAML 和 Microsoft Silverlight XAML，Windows 运行时的 XAML 使用同一个适用于其共享的默认 XAML 命名空间的 URI。 Windows 运行时的 XAML 词汇表与 Silverlight 使用的 XAML-for-UI 词汇表具有大量重复内容，与 WPF 所使用的词汇表的重复内容稍微少一些。 因此，XAML 为最初为也使用 XAML 的早期技术定义的 UI 提供了一种有效的迁移路径。
- XAML 定义 UI 的可视外观，一个关联的代码隐藏文件定义逻辑。 无需更改代码隐藏文件中的逻辑，即可调整 UI 设计。 XAML 简化了设计人员与开发人员之间的工作流。
- 得益于丰富的可视设计器和对 XAML 语言的设计图面的支持，XAML 支持在早期开发阶段快速设计 UI 原型。

取决于你自己在开发流程中的角色，你可能不会与 XAML 进行太多交互。 你与 XAML 文件的交互程度还取决于你使用哪个开发环境、是否使用交互式设计环境功能（例如工具箱和属性编辑器），以及 Windows 运行时应用的范围和用途。 但是可能在应用开发期间，你将在元素级别使用文本或 XML 编辑器来编辑 XAML 文件。 使用此信息，你可以以文本或 XML 表示形式自信地编辑 XAML，并在工具、标记编译操作或 Windows 运行时应用的运行时阶段使用 XAML 文件时保持该文件的声明和用途的有效性。

## <a name="optimize-your-xaml-for-load-performance"></a>优化你的 XAML 以提高加载性能

在使用 XAML 定义 UI 元素时，对于使用有利于性能的最佳做法，有一些技巧。 这些技巧中有许多与使用 XAML 资源有关，但为了方便起见，在此一般 XAML 概述中还是将它们列了出来。 有关 XAML 资源的详细信息，请参阅 [ResourceDictionary 和 XAML 资源引用](../design/controls-and-patterns/resourcedictionary-and-xaml-resource-references.md)。 要了解更多有关性能的提示，包括特意展示在 XAML 中应避免的某些欠佳性能做法的 XAML，请参阅[优化你的 XAML 标记](../debug-test-perf/optimize-xaml-loading.md)。

- 如果在 XAML 中经常使用相同的颜色画笔，请将 <xref:Windows.UI.Xaml.Media.SolidColorBrush> 定义为资源，而不是每次使用命名颜色作为特性值。
- 如果在多个 UI 页上使用相同的资源，请考虑在 <xref:Windows.UI.Xaml.Application.Resources%2A> 而不是在每个页面上定义它。 反之，如果只有一个页面使用某个资源，则不要在 **Application.Resources** 中定义该资源，而只为需要该资源的页面定义它。 这对设计应用时的 XAML 构造和 XAML 分析过程中的性能都有好处。
- 对于你的应用打包的资源，请检查是否有未使用的资源（这类资源具有键，但在你的应用中没有 [StaticResource](staticresource-markup-extension.md) 引用使用该资源）。 在发布你的应用之前，请从 XAML 中删除这些资源。
- 如果要使用提供设计资源（<xref:Windows.UI.Xaml.ResourceDictionary.MergedDictionaries%2A>）的单独 XAML 文件，请考虑注释或从这些文件中删除未使用的资源。 即使你有在多个应用中使用的共享 XAML 起始点或者有为你的所有应用提供共用资源的共享 XAML 起始点，你的应用仍然每次都会打包 XAML 资源，并可能需要加载它们。
- 请不要定义组合所不需要的 UI 元素，并尽可能地使用默认的控件模板（这些模板已经过加载性能方面的测试和验证）。
- 使用 <xref:Windows.UI.Xaml.Controls.Border> 容器，而不是特意 overdraws UI 元素。 一般情况下，请不要多次绘制同一像素。 有关过度绘制及其测试方法的详细信息，请参阅 <xref:Windows.UI.Xaml.DebugSettings.IsOverdrawHeatMapEnabled?displayProperty=nameWithType>。
- 为 <xref:Windows.UI.Xaml.Controls.ListView> 或 <xref:Windows.UI.Xaml.Controls.GridView>使用默认项模板;它们具有特殊的**演示者**逻辑，可在为大量列表项生成可视化树时解决性能问题。

## <a name="debug-xaml"></a>调试 XAML

因为 XAML 是一种标记语言，所以用于在 Microsoft Visual Studio 中调试的一些典型策略不可用。 例如，无法在 XAML 文件中设置断点。 不过，有其他的技术可帮助你调试与 UI 定义或其他 XAML 标记相关的问题，与此同时，你仍然可开发你的应用。

当 XAML 文件存在问题时，最典型的结果是某些系统或你的应用不会引发 XAML 分析异常。 只要存在 XAML 分析异常，由 XAML 分析程序加载的 XAML 就无法创建有效的对象树。 在某些情况下，比如当 XAML 声明作为根视觉对象加载的应用程序的第一“页”时，XAML 分析异常不可恢复。

通常在 IDE（例如 Visual Studio 及其某一个 XAML 设计图面）中编辑 XAML。 Visual Studio 通常可提供设计时的验证，以及在编辑 XAML 源时的错误检查。 例如，只要你键入无效的属性值，它就可能在 XAML 文本编辑器中显示“曲线”，而且你甚至不必等待 XAML 编译通过，就可看到 UI 定义中出现错误。

一旦应用真正运行，如果任何 XAML 分析错误在设计时未经检测，公共语言运行时 (CLR) 就会报告这些错误作为 [**XamlParseException**](https://docs.microsoft.com/dotnet/api/Windows.UI.Xaml.markup.xamlparseexception?view=dotnet-uwp-10.0)。 有关可以对运行时 **XamlParseException** 执行的操作的详细信息，请参阅[采用 C# 或 Visual Basic 的 Windows 运行时应用的异常处理](https://docs.microsoft.com/previous-versions/windows/apps/dn532194(v=win.10))。

> [!NOTE]
> 使用C++/cx 代码的应用不会获得特定的[**system.windows.markup.xamlparseexception**](https://docs.microsoft.com/dotnet/api/Windows.UI.Xaml.markup.xamlparseexception?view=dotnet-uwp-10.0)。 但是该异常与 **XamlParseException** 一样，包含的消息可阐明错误源与 XAML 相关，并且在 XAML 文件中包括上下文信息（例如行数）。

有关调试 Windows 运行时应用的详细信息，请参阅[启动调试会话](/visualstudio/debugger/start-a-debugging-session-for-a-store-app-in-visual-studio-vb-csharp-cpp-and-xaml?view=vs-2015)。
