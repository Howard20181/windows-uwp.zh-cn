---
title: Windows 运行时组件中的自定义事件和事件访问器
description: .NET 支持 Windows 运行时组件通过隐藏通用 Windows 平台（UWP）事件模式与 .NET 事件模式之间的差异，可以轻松地声明事件组件。
ms.assetid: 6A66D80A-5481-47F8-9499-42AC8FDA0EB4
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1891a89d83ea8a193d5c0fcedf939101323981e6
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71340537"
---
# <a name="custom-events-and-event-accessors-in-windows-runtime-components"></a>Windows 运行时组件中的自定义事件和事件访问器

.NET 支持 Windows 运行时组件通过隐藏通用 Windows 平台（UWP）事件模式与 .NET 事件模式之间的差异，可以轻松地声明事件组件。 但是，在 Windows 运行时组件中声明自定义事件访问器时，必须遵循 UWP 中使用的模式。

## <a name="registering-events"></a>正在注册事件

当你注册以在 UWP 中处理事件时，添加访问器将返回令牌。 若要取消注册，请将此令牌传递到删除访问器。 这意味着 UWP 事件的添加和删除访问器具有不同于你习惯使用的访问器的签名。

幸运的是，Visual Basic C#和编译器可简化此过程：在 Windows 运行时组件中使用自定义访问器声明事件时，编译器会自动使用 UWP 模式。 例如，如果添加访问器没有返回令牌，你将收到编译器错误。 .NET 提供了两种支持实现的类型：

-   [EventRegistrationToken](https://docs.microsoft.com/uwp/api/windows.foundation.eventregistrationtoken) 结构表示令牌。
-   [EventRegistrationTokenTable&lt;T&gt;](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1) 类创建令牌并维护令牌和事件处理程序之间的映射。 泛型类型参数是事件参数类型。 你为每个事件创建此类的实例，并且首次为该事件注册事件处理程序。

NumberChanged 事件的以下代码显示了 UWP 事件的基本模式。 在本示例中，事件参数对象 NumberChangedEventArgs 的构造函数接受代表更改的数字值的单精度整数参数。

> **请注意**  此模式与编译器用于在 Windows 运行时组件中声明的普通事件的模式相同。

 
> [!div class="tabbedCodeSnippets"]
> ```csharp
> private EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>     m_NumberChangedTokenTable = null;
>
> public event EventHandler<NumberChangedEventArgs> NumberChanged
> {
>     add
>     {
>         return EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>             .GetOrCreateEventRegistrationTokenTable(ref m_NumberChangedTokenTable)
>             .AddEventHandler(value);
>     }
>     remove
>     {
>         EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>             .GetOrCreateEventRegistrationTokenTable(ref m_NumberChangedTokenTable)
>             .RemoveEventHandler(value);
>     }
> }
>
> internal void OnNumberChanged(int newValue)
> {
>     EventHandler<NumberChangedEventArgs> temp =
>         EventRegistrationTokenTable<EventHandler<NumberChangedEventArgs>>
>         .GetOrCreateEventRegistrationTokenTable(ref m_NumberChangedTokenTable)
>         .InvocationList;
>     if (temp != null)
>     {
>         temp(this, new NumberChangedEventArgs(newValue));
>     }
> }
> ```
> ```vb
> Private m_NumberChangedTokenTable As  _
>     EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs))
>
> Public Custom Event NumberChanged As EventHandler(Of NumberChangedEventArgs)
>
>     AddHandler(ByVal handler As EventHandler(Of NumberChangedEventArgs))
>         Return EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs)).
>             GetOrCreateEventRegistrationTokenTable(m_NumberChangedTokenTable).
>             AddEventHandler(handler)
>     End AddHandler
>
>     RemoveHandler(ByVal token As EventRegistrationToken)
>         EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs)).
>             GetOrCreateEventRegistrationTokenTable(m_NumberChangedTokenTable).
>             RemoveEventHandler(token)
>     End RemoveHandler
>
>     RaiseEvent(ByVal sender As Class1, ByVal args As NumberChangedEventArgs)
>         Dim temp As EventHandler(Of NumberChangedEventArgs) = _
>             EventRegistrationTokenTable(Of EventHandler(Of NumberChangedEventArgs)).
>             GetOrCreateEventRegistrationTokenTable(m_NumberChangedTokenTable).
>             InvocationList
>         If temp IsNot Nothing Then
>             temp(sender, args)
>         End If
>     End RaiseEvent
> End Event
> ```

静态（在 Visual Basic 中为 Shared）GetOrCreateEventRegistrationTokenTable 方法延迟创建 EventRegistrationTokenTable&lt;T&gt; 对象的事件实例。 将保留令牌表实例的类级别字段传递到此方法。 如果该字段为空，该方法会创建表、在字段中存储对表的引用并返回对表的引用。 如果该字段已经包含令牌表引用，该方法将仅返回该引用。

> **重要**  若要确保线程安全，则&lt;t&gt; 包含事件的实例的字段必须是类级别的字段。 如果它是类级别字段，GetOrCreateEventRegistrationTokenTable 方法会确保在多个线程尝试创建令牌表时，所有线程均会获取相同的表实例。 对于指定事件，对 GetOrCreateEventRegistrationTokenTable 方法的所有调用都必须使用相同的类级别字段。

在删除访问器和 [RaiseEvent](https://docs.microsoft.com/dotnet/articles/visual-basic/language-reference/statements/raiseevent-statement) 方法（在 C# 中是 OnRaiseEvent 方法）中调用 GetOrCreateEventRegistrationTokenTable 方法可确保在以下情况下不会发生任何异常：在添加任何事件处理程序委托之前调用这些方法。

其他在 UWP 事件模式中使用的 EventRegistrationTokenTable&lt;T&gt; 类的成员包括：

-   [AddEventHandler](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1.addeventhandler#System_Runtime_InteropServices_WindowsRuntime_EventRegistrationTokenTable_1_AddEventHandler__0_) 方法生成事件处理程序委托的令牌、在表中存储委托、将其添加到调用列表以及返回该令牌。
-   [RemoveEventHandler(EventRegistrationToken)](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1.removeeventhandler#System_Runtime_InteropServices_WindowsRuntime_EventRegistrationTokenTable_1_RemoveEventHandler_System_Runtime_InteropServices_WindowsRuntime_EventRegistrationToken_) 方法重载会从表和调用列表中删除委托。

    >**请注意**  AddEventHandler 和 RemoveEventHandler （EventRegistrationToken）方法锁定表，以帮助确保线程安全。

-   [InvocationList](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.windowsruntime.eventregistrationtokentable-1.invocationlist#System_Runtime_InteropServices_WindowsRuntime_EventRegistrationTokenTable_1_InvocationList) 属性返回包括所有事件处理程序的委托，并且这些事件处理程序当前已注册为处理该事件。 使用此委派引发事件，或使用委派类的方法单独调用处理程序。

    >**请注意**  建议遵循本文前面提供的示例中所示的模式，并在调用前将委托复制到临时变量。 这可避免一个线程删除最后的处理程序的争用条件，从而在其他线程尝试调用委派之前将其减少为 null。 委派不可变动，因此副本仍然有效。

根据情况将自己的代码置于访问器中。 如果线程安全出现问题，必须自行锁定代码。

C# 用户：当你在 UWP 事件模式中编写自定义事件访问器时，编译器将不会提供常用的语法快捷方式。 如果你使用代码中的事件的名称，它将生成错误。

Visual Basic 用户：在 .NET 中，事件只是表示所有已注册事件处理程序的多路广播委托。 引发事件即表明调用委派。 Visual Basic 语法通常隐藏与委派的交互，而编译器会在调用委派前复制它，如有关线程安全的注释中所述。 在 Windows 运行时组件中创建自定义事件时，必须直接处理委托。 这也意味着你可以使用 [MulticastDelegate.GetInvocationList](https://docs.microsoft.com/dotnet/api/system.multicastdelegate.getinvocationlist#System_MulticastDelegate_GetInvocationList) 方法（例如）为每个事件处理程序获取包含独立委托的数组，条件是你想要单独调用处理程序。

## <a name="related-topics"></a>相关主题

* [事件（Visual Basic）](https://docs.microsoft.com/dotnet/articles/visual-basic/programming-guide/language-features/events/index)
* [事件（C#编程指南）](https://docs.microsoft.com/dotnet/articles/csharp/programming-guide/events/index)
* [适用于 UWP 应用的 .NET 概述](https://docs.microsoft.com/previous-versions/windows/apps/br230302(v=vs.140))
* [适用于 UWP 应用的 .NET](https://docs.microsoft.com/dotnet/api/index?view=dotnet-uwp-10.0)
* [创建 C# 或 Visual Basic Windows 运行时组件并通过 JavaScript 调用此组件的演练](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md)
