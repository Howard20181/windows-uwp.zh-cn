---
description: 标量或数组值需要先封装到引用类对象内，然后再传递到需要 IInspectable 的函数。 该封装过程称为对值进行 *装箱*。
title: 通过 C++/WinRT 将值装箱到 IInspectable 和从中取消装箱
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, 标准, c++, cpp, winrt, 投影, XAML, 控件, 装箱, 标量, 值
ms.localizationpriority: medium
ms.openlocfilehash: 08c36c735b319bb1658b2d4ce745ae0fdb7bdeee
ms.sourcegitcommit: b89d3bc42713fbe4c0ada99d6f514f1304821221
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107466417"
---
# <a name="boxing-and-unboxing-values-to-iinspectable-with-cwinrt"></a>通过 C++/WinRT 将值装箱到 IInspectable 和对其取消装箱

> [!NOTE]
> 不仅可对标量值进行装箱和取消装箱，而可使用 [winrt::box_value](/uwp/cpp-ref-for-winrt/box-value) 和 [winrt::unbox_value](/uwp/cpp-ref-for-winrt/unbox-value) 函数对大多数类型的数组进行这类操作（枚举数组除外） 。 只能使用 [winrt::unbox_value_or](/uwp/cpp-ref-for-winrt/unbox-value-or) 函数对标量值取消装箱。

[IInspectable 接口](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)是 Windows 运行时 (WinRT) 中每个运行时类的根接口。 这类似于位于每个 COM 接口和类的根处的 [IUnknown](/windows/desktop/api/unknwn/nn-unknwn-iunknown)；而且类似于位于每个[通用类型系统](/dotnet/standard/base-types/common-type-system)类的根处的 System.Object。

换言之，可向任何运行时类的实例传递需要 IInspectable 的函数。 但是你无法将标量值（如数值或文本值）直接传递到此类函数，也不能直接传递数组。 相反，标量或数组值需要封装到引用类对象内。 该封装过程称为对值进行 *装箱*。

> [!IMPORTANT]
> 不管将什么类型传递给 Windows 运行时 API，都可以对该类型进行装箱和取消装箱操作。 换言之，可以对 Windows 运行时类型这样做。 上面给出的一些示例包括数字值、文本值（字符串）和数组。 另一个示例是在 IDL 中定义的 `struct`。 如果尝试对常规 C++ `struct`（未在 IDL 中定义）执行装箱操作，编译器会提醒你只能将 Windows 运行时类型装箱。 运行时类是 Windows 运行时类型，不过，我们当然可以将运行时类传递到 Windows 运行时 API，无需将其装箱。

[C++/WinRT](./intro-to-using-cpp-with-winrt.md) 提供了 [winrt::box_value](/uwp/cpp-ref-for-winrt/box-value) 函数，该函数采用标量或数组值，并将装箱的值返回到 IInspectable中 。 对于取消 IInspectable 装箱并返回到标量或数组值，提供 [winrt::unbox_value](/uwp/cpp-ref-for-winrt/unbox-value) 函数 。 对于取消 IInspectable 装箱并返回到标量值，还提供 [winrt::unbox_value_or](/uwp/cpp-ref-for-winrt/unbox-value-or) 函数 。

## <a name="examples-of-boxing-a-value"></a>取消值装箱的示例
[LaunchActivatedEventArgs::Arguments](/uwp/api/windows.applicationmodel.activation.launchactivatedeventargs.Arguments) 访问器函数返回 [winrt::hstring](/uwp/cpp-ref-for-winrt/hstring)，这是一个标量值。 我们可以将该 hstring 值进行装箱并将其传递到需要 IInspectable 的函数，如下所示。

```cppwinrt
void App::OnLaunched(LaunchActivatedEventArgs const& e)
{
    ...
    rootFrame.Navigate(winrt::xaml_typename<BlankApp1::MainPage>(), winrt::box_value(e.Arguments()));
    ...
}
```

要设置 XAML [按钮](/uwp/api/windows.ui.xaml.controls.button)的内容属性，请调用 [Button::Content](/uwp/api/windows.ui.xaml.controls.contentcontrol.content?) 转变器函数。 要将内容属性设置为字符串值，可以使用此代码。

```cppwinrt
Button().Content(winrt::box_value(L"Clicked"));
```

首先，[hstring](/uwp/cpp-ref-for-winrt/hstring) 转换构造函数将此字符串参数转换为 hstring。 然后，调用采用 hstring 的 winrt::box_value 的重载。

## <a name="examples-of-unboxing-an-iinspectable"></a>取消 IInspectable 装箱的示例
在自己的需要 IInspectable 的函数中，可以使用 [winrt::unbox_value](/uwp/cpp-ref-for-winrt/unbox-value) 取消装箱，也可以使用 [winrt::unbox_value_or](/uwp/cpp-ref-for-winrt/unbox-value-or) 通过默认值取消装箱。

```cppwinrt
void Unbox(winrt::Windows::Foundation::IInspectable const& object)
{
    hstring hstringValue = unbox_value<hstring>(object); // Throws if object is not a boxed string.
    hstringValue = unbox_value_or<hstring>(object, L"Default"); // Returns L"Default" if object is not a boxed string.
    float floatValue = unbox_value_or<float>(object, 0.f); // Returns 0.0 if object is not a boxed float.
}
```

## <a name="determine-the-type-of-a-boxed-value"></a>确定装箱值的类型
如果收到装箱值但不确定它所包含的类型（需要知道类型以便取消装箱），可以查询装箱值的 [IPropertyValue](/uwp/api/windows.foundation.ipropertyvalue) 接口，然后对其调用 Type。 下面是代码示例。

`WINRT_ASSERT` 是宏定义，并且扩展到 [_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros)。

```cppwinrt
float pi = 3.14f;
auto piInspectable = winrt::box_value(pi);
auto piPropertyValue = piInspectable.as<winrt::Windows::Foundation::IPropertyValue>();
WINRT_ASSERT(piPropertyValue.Type() == winrt::Windows::Foundation::PropertyType::Single);
```

## <a name="important-apis"></a>重要的 API
* [IInspectable 接口](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)
* [winrt::box_value 函数模板](/uwp/cpp-ref-for-winrt/box-value)
* [winrt::hstring 结构](/uwp/cpp-ref-for-winrt/hstring)
* [winrt::unbox_value 函数模板](/uwp/cpp-ref-for-winrt/unbox-value)
* [winrt::unbox_value_or 函数模板](/uwp/cpp-ref-for-winrt/unbox-value-or)
