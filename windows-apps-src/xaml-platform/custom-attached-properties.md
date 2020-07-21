---
description: 介绍如何将一个 XAML 附加属性实现为依赖属性，以及如何定义让附加属性可用于 XAML 所必需的访问器约定。
title: 自定义附加属性
ms.assetid: E9C0C57E-6098-4875-AA3E-9D7B36E160E0
ms.date: 07/18/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: f23d66acc9371fd7b23b6770a0c7be6d16f86be4
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71340612"
---
# <a name="custom-attached-properties"></a>自定义附加属性

*附加属性*是一种 XAML 概念。 附加属性通常定义为一种特殊形式的依赖属性。 本主题介绍如何将一个 XAML 附加属性实现为依赖属性，如何定义让附加属性可用于 XAML 所必需的访问器约定。

## <a name="prerequisites"></a>必备条件

我们假设你能从现有依赖属性的客户角度理解依赖属性，并且已阅读了[依赖属性概述](dependency-properties-overview.md)。 你还应该阅读了[附加属性概述](attached-properties-overview.md)。 要理解本主题中的示例，你还应该理解 XAML，知道如何编写使用 C++、C# 或 Visual Basic 的基本 Windows 运行时应用。

## <a name="scenarios-for-attached-properties"></a>附加属性的使用场景

除了定义类，如果有理由提供其他属性设置机制，则可以创建一个附加属性。 最常见的情况是布局和服务支持。 现有布局属性的示例包括 [**Canvas.ZIndex**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc190397(v=vs.95)) 和 [**Canvas.Top**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.top)。 在布局场景中，以布局控制元素的子元素形式存在的元素可单独向其父元素表达布局需求，每个元素设置一个被其父元素定义为附加属性的属性值。 Windows 运行时 API 中服务支持方案的一个示例是 [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) 的一组附加属性，例如 [**ScrollViewer.IsZoomChainingEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.iszoomchainingenabled)。

> [!WARNING]
> Windows 运行时 XAML 实现的现有限制是，无法对自定义附加属性进行动画处理。

## <a name="registering-a-custom-attached-property"></a>注册自定义附加属性

如果将附加属性严格定义为在其他类型上使用，在其中注册该属性的类不必派生自 [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject)。 但是，如果你采用将附加属性也用作依赖属性的典型模型，则需要让访问器的目标参数使用 **DependencyObject**，以便你可以使用支持属性存储。

通过声明类型为[**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty)的**公共** **静态** **readonly**属性，将附加属性定义为依赖属性。 你可以使用 [**RegisterAttached**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.registerattached) 方法的返回值来定义此属性。 属性名称必须与指定为**system.windows.dependencyproperty.registerattached** *name*参数的附加属性名称匹配，并将字符串 "property" 添加到末尾。 这是相对于其表示的属性来命名依赖属性标识符的既有约定。

定义自定义附加属性与自定义依赖属性的主要区别在定义访问器或包装器的方式上。 并不使用[自定义依赖属性](custom-dependency-properties.md)中介绍的包装器技术，你还必须提供静态的 **Get**_PropertyName_ 和 **Set**_PropertyName_ 方法作为附加属性的访问器。 访问器多数供 XAML 分析器使用，但任何其他调用方也可以使用它们来设置非 XAML 场景中的值。

> [!IMPORTANT]
> 如果未正确定义访问器，则 XAML 处理器无法访问附加属性，尝试使用它的任何人都可能会收到 XAML 分析器错误。 此外，设计和编码工具通常依赖于在引用的程序集中遇到自定义依赖属性时命名标识符的 "\*属性" 约定。

## <a name="accessors"></a>访问器

**Get**_PropertyName_ 访问器的签名必须如下所示。

`public static` _valueType_ **Get**_PropertyName_ `(DependencyObject target)`

对于 Microsoft Visual Basic，应如下所示。

`Public Shared Function Get`_PropertyName_`(ByVal target As DependencyObject) As `_valueType_`)`

*target* 对象可以是实现中的一种更为具体的类型，但必须派生自 [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject)。 *valueType* 返回值也可以是你的实现中一种更为具体的类型。 基本的 **Object** 类型也可接受，但通常希望附加属性执行类型安全性。 getter 和 setter 签名中对类型的使用是一种推荐的类型安全技术。

**Set**_PropertyName_ 访问器的签名必须如下所示。

`public static void Set`_PropertyName_` (DependencyObject target , `_valueType_` value)`

对于 Visual Basic，应如下所示。

`Public Shared Sub Set`_PropertyName_` (ByVal target As DependencyObject, ByVal value As `_valueType_`)`

*target* 对象可以是实现中的一种更为具体的类型，但必须派生自 [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject)。 *value* 对象和它的 *valueType* 可以是你的实现中一种更为具体的类型。 请记住，此方法的值是 XAML 处理器在标记中遇到你的附加属性时提供的输入。 你使用的类型必须具有类型转换或现有的标记扩展支持，这样才能通过该特性值（最终是一个字符串）创建合适的类型。 基本的 **Object** 类型也可接受，但通常希望进一步增强类型安全性。 为此，请在取值函数中增加类型增强措施。

> [!NOTE]
> 还可以通过属性元素语法定义要使用的附加属性。 在此情况下，你不需要对值进行类型转换，但需要确保所需的值可采用 XAML 构造。 [**VisualStateManager。 system.windows.visualstatemanager.visualstategroups**](https://docs.microsoft.com/dotnet/api/system.windows.visualstatemanager)是一个仅支持属性元素用法的现有附加属性的示例。

## <a name="code-example"></a>代码示例

此示例展示了依赖属性注册（使用 [**RegisterAttached**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.registerattached) 方法），以及一个自定义附加属性的 **Get** 和 **Set** 访问器。 在此示例中，附加属性名称为 `IsMovable`。 因此，访问器必须命名为 `GetIsMovable` 和 `SetIsMovable`。 附加属性的所有者是自身不具有 UI 的名为 `GameService` 服务类；其目的只是在使用 **GameService.IsMovable** 附加属性时提供附加属性服务。

在/Cx 中C++定义附加属性会稍微复杂一些。 必须决定如何协调标头文件和代码文件。 另外，应该将标识符公开为只有一个 **get** 访问器的属性，原因如[自定义依赖属性](custom-dependency-properties.md)中所述。 在C++/cx 中，必须显式定义此属性字段关系，而不是依赖于 .net **readonly** keywording 和简单属性的隐式支持。 你还需要在首次启动应用时，在加载需要附加属性的任何 XAML 页面之前，在仅运行一次的帮助程序函数中执行附加属性的注册操作。 为所有依赖属性或附加属性调用属性注册帮助程序函数的典型位置是 app.xaml 文件代码的 **App** / [**Application**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.-ctor) 构造函数内。

```csharp
public class GameService : DependencyObject
{
    public static readonly DependencyProperty IsMovableProperty = 
    DependencyProperty.RegisterAttached(
      "IsMovable",
      typeof(Boolean),
      typeof(GameService),
      new PropertyMetadata(false)
    );
    public static void SetIsMovable(UIElement element, Boolean value)
    {
        element.SetValue(IsMovableProperty, value);
    }
    public static Boolean GetIsMovable(UIElement element)
    {
        return (Boolean)element.GetValue(IsMovableProperty);
    }
}
```

```vb
Public Class GameService
    Inherits DependencyObject

    Public Shared ReadOnly IsMovableProperty As DependencyProperty = 
        DependencyProperty.RegisterAttached("IsMovable",  
        GetType(Boolean), 
        GetType(GameService), 
        New PropertyMetadata(False))

    Public Shared Sub SetIsMovable(ByRef element As UIElement, value As Boolean)
        element.SetValue(IsMovableProperty, value)
    End Sub

    Public Shared Function GetIsMovable(ByRef element As UIElement) As Boolean
        GetIsMovable = CBool(element.GetValue(IsMovableProperty))
    End Function
End Class
```

```cppwinrt
// GameService.idl
namespace UserAndCustomControls
{
    [default_interface]
    runtimeclass GameService : Windows.UI.Xaml.DependencyObject
    {
        GameService();
        static Windows.UI.Xaml.DependencyProperty IsMovableProperty{ get; };
        static Boolean GetIsMovable(Windows.UI.Xaml.DependencyObject target);
        static void SetIsMovable(Windows.UI.Xaml.DependencyObject target, Boolean value);
    }
}

// GameService.h
...
    static Windows::UI::Xaml::DependencyProperty IsMovableProperty() { return m_IsMovableProperty; }
    static bool GetIsMovable(Windows::UI::Xaml::DependencyObject const& target) { return winrt::unbox_value<bool>(target.GetValue(m_IsMovableProperty)); }
    static void SetIsMovable(Windows::UI::Xaml::DependencyObject const& target, bool value) { target.SetValue(m_IsMovableProperty, winrt::box_value(value)); }

private:
    static Windows::UI::Xaml::DependencyProperty m_IsMovableProperty;
...

// GameService.cpp
...
Windows::UI::Xaml::DependencyProperty GameService::m_IsMovableProperty =
    Windows::UI::Xaml::DependencyProperty::RegisterAttached(
        L"IsMovable",
        winrt::xaml_typename<bool>(),
        winrt::xaml_typename<UserAndCustomControls::GameService>(),
        Windows::UI::Xaml::PropertyMetadata{ winrt::box_value(false) }
);
...
```

```cpp
// GameService.h
#pragma once

#include "pch.h"
//namespace WUX = Windows::UI::Xaml;

namespace UserAndCustomControls {
    public ref class GameService sealed : public WUX::DependencyObject {
    private:
        static WUX::DependencyProperty^ _IsMovableProperty;
    public:
        GameService::GameService();
        void GameService::RegisterDependencyProperties();
        static property WUX::DependencyProperty^ IsMovableProperty
        {
            WUX::DependencyProperty^ get() {
                return _IsMovableProperty;
            }
        };
        static bool GameService::GetIsMovable(WUX::UIElement^ element) {
            return (bool)element->GetValue(_IsMovableProperty);
        };
        static void GameService::SetIsMovable(WUX::UIElement^ element, bool value) {
            element->SetValue(_IsMovableProperty,value);
        }
    };
}

// GameService.cpp
#include "pch.h"
#include "GameService.h"

using namespace UserAndCustomControls;

using namespace Platform;
using namespace Windows::Foundation;
using namespace Windows::Foundation::Collections;
using namespace Windows::UI::Xaml;
using namespace Windows::UI::Xaml::Controls;
using namespace Windows::UI::Xaml::Data;
using namespace Windows::UI::Xaml::Documents;
using namespace Windows::UI::Xaml::Input;
using namespace Windows::UI::Xaml::Interop;
using namespace Windows::UI::Xaml::Media;

GameService::GameService() {};

GameService::RegisterDependencyProperties() {
    DependencyProperty^ GameService::_IsMovableProperty = DependencyProperty::RegisterAttached(
         "IsMovable", Platform::Boolean::typeid, GameService::typeid, ref new PropertyMetadata(false));
}
```

## <a name="setting-your-custom-attached-property-from-xaml-markup"></a>从 XAML 标记设置自定义附加属性

> [!NOTE]
> 如果使用C++的是/WinRT，请跳到下一节（[ C++使用/WinRT 强制设置自定义附加属性](#setting-your-custom-attached-property-imperatively-with-cwinrt)）。

定义附加属性并将它的支持成员包含在一个自定义类型中后，你必须让这些定义可供 XAML 使用。 为此，你必须映射一个 XAML 命名空间，它将引用其中包含相关类的代码命名空间。 如果在一个库中定义了附加属性，必须将该库包含在应用的应用程序包中。

XAML 的 XML 命名空间映射通常位于一个 XAML 页面的根元素中。 例如，对于命名空间 `GameService` 中有一个名为 `UserAndCustomControls` 的类，它包含前面代码段中所示的附加属性定义，映射类似于这样。

```xaml
<UserControl
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:uc="using:UserAndCustomControls"
  ... >
```

使用映射，你可以在任何与目标定义相匹配的元素（包括 Windows 运行时定义的一种现有类型）上设置 `GameService.IsMovable` 附加属性。

```xaml
<Image uc:GameService.IsMovable="True" .../>
```

如果在一个元素上设置附加属性并且该元素包含在同一个映射的 XML 命名空间中，则必须在附加属性名称中包含该前缀。 这是因为该前缀限定了所有者类型。 不能假设附加属性的特性与包含该特性的元素位于相同的 XML 命名空间中，尽管按照正常的 XML 规则，特性可从元素继承命名空间。 例如，如果在一种自定义类型 `GameService.IsMovable`（未给出定义）上设置 `ImageWithLabelControl`，即使二者都在映射到同一个前缀的同一个代码命名空间中定义，XAML 仍将是这样的。

```xaml
<uc:ImageWithLabelControl uc:GameService.IsMovable="True" .../>
```

> [!NOTE]
> 如果要使用C++/CX 编写 XAML UI，则必须包含定义附加属性的自定义类型的标头，无论 XAML 页何时使用该类型。 每个 XAML 页都具有关联的代码隐藏标头（.xaml .h）。 你应在此中包括（使用 **\#包含**）附加属性的所有者类型定义的标头。

## <a name="setting-your-custom-attached-property-imperatively-with-cwinrt"></a>通过C++/WinRT 强制设置自定义附加属性

如果你使用C++的是/WinRT，则可以从命令性代码（而不是 XAML 标记）访问自定义附加属性。 下面的代码演示了如何操作。

```xaml
<Image x:Name="gameServiceImage"/>
```

```cppwinrt
// MainPage.h
...
#include "GameService.h"
...

// MainPage.cpp
...
MainPage::MainPage()
{
    InitializeComponent();

    GameService::SetIsMovable(gameServiceImage(), true);
}
...
```

## <a name="value-type-of-a-custom-attached-property"></a>自定义附加属性的值类型

用作自定义附加属性的值类型的类型会影响附加属性使用、定义或同时影响二者。 附加属性的值类型在多个位置声明：在 **Get** 和 **Set** 访问器方法的签名中，以及作为RegisterAttached[**调用的**propertyType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.registerattached) 参数。

附加属性（无论是否为自定义的）最常用的值类型是一个简单字符串。 这是因为附加属性一般用作 XAML 特性，并且使用一个字符串作为值类型可保持属性的简单性。 可本地转换为字符串方法的其他原语（例如整型、双精度或枚举值）也是常用的附加属性值类型。 你可以使用其他值类型，不支持本地字符串转换的类型作为附加属性值。 但是，这需要选择是使用还是实现：

- 你可以将附加属性保持不变，但附加属性只能在它是一个属性元素并且它的值声明为对象元素时才支持使用。 在此情况下，属性类型必须支持用作对象元素的 XAML 用法。 对于现有的 Windows 运行时引用类，请检查 XAML 语法，确保该类型支持 XAML 对象元素用法。
- 你可以将附加属性保持不变，但只能通过一种 XAML 引用技术和特性用法来使用它，例如可表示为一个字符串的 **Binding** 或 **StaticResource**。

## <a name="more-about-the-canvasleft-example"></a>有关 **Canvas.Left** 示例的详细信息

在先前的附加属性用法示例中，我们显示了几种用来设置 [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left) 附加属性的方法。 但是，这对于 [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) 与你的对象的交互方式有何更改，这是在何时发生的？ 我们将在这个特定示例中深入介绍，原因在于：如果你实现了一个附加属性，则会发现一个有趣的情况，那就是当典型的附加属性所有者类在其他对象上发现了它的附加属性值时，它应当会对这些值进行处理。

[  **Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) 的主要功能是成为 UI 中具有绝对位置的布局容器。 **Canvas** 的子项存储在由基类定义的 [**Children**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.panel.children) 属性中。 在所有的面板中，**Canvas** 是唯一一个使用绝对位置的面板。 如果在添加属性时仅关注 [Canvas **，或者在** UIElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) 作为 **UIElement** 的子元素的特定情况下，该面板中会充斥着常见UIElement 类的对象模型。 将 **Canvas** 的布局控件属性定义为可由任何 **UIElement** 用来使对象模型更简洁的附加属性。

为了成为实际的面板，[**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) 具有可替代框架级 [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) 和 [**Arrange**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) 方法的行为。 **Canvas** 就是在这里实际检查其子项上的附加属性值。 **Measure** 和 **Arrange** 模式的一部分就是一个遍历任何内容的循环，一个面板具有 [**Children**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.panel.children) 属性，通过该属性可以明确假设被视为面板子项的内容。 因此，**Canvas** 布局行为会循环访问这些子项，并针对每个子项进行静态 [**Canvas.GetLeft**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.canvas.getleft) 和 [**Canvas.GetTop**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.canvas.gettop) 调用，查看这些附加属性是否包含非默认值（默认值为 0）。 之后，系统将使用这些值，按照由每个子项提供的特定值将每个子项以绝对位置方式放置到 **Canvas** 中的可用布局空间中。然后系统将使用 **Arrange** 提交这些值。

此代码类似于此伪代码。

```syntax
protected override Size ArrangeOverride(Size finalSize)
{
    foreach (UIElement child in Children)
    {
        double x = (double) Canvas.GetLeft(child);
        double y = (double) Canvas.GetTop(child);
        child.Arrange(new Rect(new Point(x, y), child.DesiredSize));
    }
    return base.ArrangeOverride(finalSize); 
    // real Canvas has more sophisticated sizing
}
```

> [!NOTE]
> 有关面板工作方式的详细信息，请参阅[XAML 自定义面板概述](https://docs.microsoft.com/windows/uwp/layout/custom-panels-overview)。

## <a name="related-topics"></a>相关主题

* [**System.windows.dependencyproperty.registerattached**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.registerattached)
* [附加属性概述](attached-properties-overview.md)
* [自定义依赖属性](custom-dependency-properties.md)
* [XAML概述](xaml-overview.md)
