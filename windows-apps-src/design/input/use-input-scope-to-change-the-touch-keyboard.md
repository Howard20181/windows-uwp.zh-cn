---
description: 若要帮助用户使用触摸键盘或软输入面板 (SIP) 输入数据，你可以将文本控件的输入范围设置为与期望用户输入的数据类型匹配。
MS-HAID: dev\_ctrl\_layout\_txt.use\_input\_scope\_to\_change\_the\_touch\_keyboard
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: 使用输入范围更改触摸键盘
ms.assetid: 6E5F55D7-24D6-47CC-B457-B6231EDE2A71
template: detail.hbs
keywords: 键盘、辅助功能、导航、焦点、文本、输入、用户交互
ms.date: 02/08/2017
ms.topic: article
ms.openlocfilehash: 9b274d7cafd179157571a51db1122153a33d846e
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93035070"
---
# <a name="use-input-scope-to-change-the-touch-keyboard"></a>使用输入范围更改触摸键盘

若要帮助用户使用触摸键盘或软输入面板 (SIP) 输入数据，你可以将文本控件的输入范围设置为与期望用户输入的数据类型匹配。

### <a name="important-apis"></a>重要的 API
- [InputScope](/uwp/api/windows.ui.xaml.controls.textbox.inputscope)
- [InputScopeNameValue](/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue)


当应用在具有触摸屏的设备上运行时，触摸键盘可用于文本输入。 当用户点击可编辑的输入字段（如 **[TextBox](/uwp/api/Windows.UI.Xaml.Controls.TextBox)** 或 **[RichEditBox](/uwp/api/Windows.UI.Xaml.Controls.RichEditBox)** ）时，将调用触摸键盘。 通过设置文本控件的 *输入范围* ，使用户能够更快、更轻松地在应用程序中输入数据，以匹配用户输入的数据类型。 输入范围会针对控件所预期的文本输入类型向系统提供提示，以便系统可以为该输入类型提供专用的触摸键盘布局。

例如，如果文本框仅用于输入4位数的 PIN，则将 [**InputScope**](/uwp/api/windows.ui.xaml.controls.textbox.inputscope) 属性设置为 **Number** 。 这将通知系统显示数字键盘布局，以便于用户输入 PIN。

> [!IMPORTANT]
> - 此信息仅适用于 SIP。 它不适用于硬件键盘或 Windows“轻松使用”选项中提供的屏幕键盘。
> - 输入范围不会导致任何输入验证的执行，并且不会阻止用户通过硬件键盘或其他输入设备提供任何输入。 你仍然负责按需在代码中验证输入。

## <a name="changing-the-input-scope-of-a-text-control"></a>更改文本控件的输入范围

可用于你的应用的输入范围是 **[InputScopeNameValue](/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue)** 枚举的成员。 你可以将 ****TextBox**** 或 **[RichEditBox](/uwp/api/Windows.UI.Xaml.Controls.TextBox)** 的 [InputScope](/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) 属性设置为以下值之一。

> [!IMPORTANT]
> **[PasswordBox](/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)** 上的 **[InputScope](/uwp/api/windows.ui.xaml.controls.passwordbox.inputscope)** 属性仅支持 **Password** 和 **NumericPin** 值。 将忽略任何其他值。

在这里，你可以更改多个文本框的输入范围以匹配每个文本框的预期数据范围。

**更改 XAML 中的输入范围**

1.  在页面的 XAML 文件中，找到需要更改的文本标记。
2.  将 [**InputScope**](/uwp/api/windows.ui.xaml.controls.textbox.inputscope) 属性应用到标记并指定与预期输入匹配的 [**InputScopeNameValue**](/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue) 值。

    下面是一些可能会出现在常用客户联系信息表中的文本框。 设置 [**InputScope**](/uwp/api/windows.ui.xaml.controls.textbox.inputscope) 后，将针对每个文本框显示数据布局合理的触摸键盘。

    ```xaml
    <StackPanel Width="300">
        <TextBox Header="Name" InputScope="Default"/>
        <TextBox Header="Email Address" InputScope="EmailSmtpAddress"/>
        <TextBox Header="Telephone Number" InputScope="TelephoneNumber"/>
        <TextBox Header="Web site" InputScope="Url"/>
    </StackPanel>
    ```

**更改代码中的输入范围**

1.  在页面的 XAML 文件中，找到需要更改的文本标记。 如果未设置，请设置 [x:Name 属性](../../xaml-platform/x-name-attribute.md)以便可以在代码中引用控件。

    ```csharp
    <TextBox Header="Telephone Number" x:Name="phoneNumberTextBox"/>
    ```

2.  实例化新的 [**InputScope**](/uwp/api/Windows.UI.Xaml.Input.InputScope) 对象。

    ```csharp
    InputScope scope = new InputScope();
    ```

3.  实例化新的 [**InputScopeName**](/uwp/api/Windows.UI.Xaml.Input.InputScopeName) 对象。
    
    ```csharp
    InputScopeName scopeName = new InputScopeName();
    ```

4.  将 [**InputScopeName**](/uwp/api/windows.ui.xaml.input.inputscopename.namevalue) 对象的 [**NameValue**](/uwp/api/Windows.UI.Xaml.Input.InputScopeName) 属性设置为 [**InputScopeNameValue**](/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue) 枚举的值。

    ```csharp
    scopeName.NameValue = InputScopeNameValue.TelephoneNumber;
    ```

5.  将 [**InputScopeName**](/uwp/api/Windows.UI.Xaml.Input.InputScopeName) 对象添加到 [**InputScope**](/uwp/api/windows.ui.xaml.input.inputscope.names) 对象的 [**Names**](/uwp/api/Windows.UI.Xaml.Input.InputScope) 集合。

    ```csharp
    scope.Names.Add(scopeName);
    ```

6.  将 [**InputScope**](/uwp/api/Windows.UI.Xaml.Input.InputScope) 对象设置为文本控件的 [**InputScope**](/uwp/api/windows.ui.xaml.controls.textbox.inputscope) 属性的值。

    ```csharp
    phoneNumberTextBox.InputScope = scope;
    ```

下面是全部代码。

```CSharp
InputScope scope = new InputScope();
InputScopeName scopeName = new InputScopeName();
scopeName.NameValue = InputScopeNameValue.TelephoneNumber;
scope.Names.Add(scopeName);
phoneNumberTextBox.InputScope = scope;
```

相同的步骤可以压缩为此简写代码。

```CSharp
phoneNumberTextBox.InputScope = new InputScope() 
{
    Names = {new InputScopeName(InputScopeNameValue.TelephoneNumber)}
};
```

## <a name="text-prediction-spell-checking-and-auto-correction"></a>文本预测、拼写检查和自动更正

[**TextBox**](/uwp/api/Windows.UI.Xaml.Controls.TextBox) 和 [**RichEditBox**](/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) 控件具有多个属性，这些属性会影响 SIP 的行为。 若要为你的用户提供最佳体验，则了解在使用触摸键盘时这些属性如何影响文本输入非常重要。

-   [**IsSpellCheckEnabled**](/uwp/api/windows.ui.xaml.controls.textbox.isspellcheckenabled)：为文本控件启用拼写检查后，该控件将与系统的拼写检查引擎进行交互，以标记无法识别的单词。 点击一个单词即可查看建议的更正单词列表。 默认情况下，拼写检查处于启用状态。

    在 **Default** 输入范围中，使用此属性还可以使句子中第一个单词的首字母自动大写，并自动更正所键入的单词。 在其他输入范围中可能会禁用这些自动更正功能。 有关详细信息，请参阅本主题后面的表格。

-   [**IsTextPredictionEnabled**](/uwp/api/windows.ui.xaml.controls.textbox.istextpredictionenabled)：为文本控件启用文本预测后，系统将显示你可能会键入的单词的列表。 你可以从该列表中进行选择，因此你无需键入整个单词。 默认情况下，文本预测处于启用状态。

    如果输入范围的属性不为 **Default** ，将禁用文本预测，即使 [**IsTextPredictionEnabled**](/uwp/api/windows.ui.xaml.controls.textbox.istextpredictionenabled) 属性为 **true** 也是如此。 有关详细信息，请参阅本主题后面的表格。

-   [**PreventKeyboardDisplayOnProgrammaticFocus**](/uwp/api/windows.ui.xaml.controls.textbox.preventkeyboarddisplayonprogrammaticfocus)：若此属性为 **true** ，则系统在采用编程方式在文本控件上设置焦点时，不显示 SIP。 而仅在用户与控件交互时才显示该键盘。

## <a name="touch-keyboard-index-for-windows"></a>适用于 Windows 的触摸键盘索引

这些表显示了适用于公用输入范围值 (SIP) 布局的 Windows 软输入面板。 针对每个输入范围列出了输入范围对 **IsSpellCheckEnabled** 和 **IsTextPredictionEnabled** 属性所启用的功能的影响。 这并非是可用输入范围的完整列表。

> [!Tip] 
> 通过按 " **&123** 键更改为" 数字 "和" 符号 "布局，并按" **abcd** "键更改为字母布局，可以在字母布局和数字和符号布局之间切换大多数触摸键盘。

### <a name="default"></a>默认

`<TextBox InputScope="Default"/>`

默认的 Windows touch 键盘。

![默认 Windows 触摸键盘](images/input-scopes/default.png)
- 拼写检查：如果 **IsSpellCheckEnabled**  =  **为 true** ，则启用，如果 **IsSpellCheckEnabled**  =  **为 false** ，则禁用
- 自动更正：如果 **IsSpellCheckEnabled**  =  **为 true** ，则启用，如果 **IsSpellCheckEnabled**  =  **为 false** ，则禁用
- 自动大写：如果 **IsSpellCheckEnabled**  =  **为 true** ，则启用，如果 **IsSpellCheckEnabled**  =  **为 false** ，则禁用
- 文本预测：如果 **IsTextPredictionEnabled**  =  **为 true** ，则启用，如果 **IsTextPredictionEnabled**  =  **为 false** ，则禁用

### <a name="currencyamountandsymbol"></a>CurrencyAmountAndSymbol

`<TextBox InputScope="CurrencyAmountAndSymbol"/>`

默认数字和符号键盘布局。

![可输入货币字符的 Windows 触摸键盘](images/input-scopes/currencyamountandsymbol.png)

- 包含 page left/right 键以显示更多符号
- 拼写检查：默认情况下处于启用状态，可以禁用
- 自动更正：默认情况下处于启用状态，可以禁用
- 首字母自动大写：始终处于禁用状态
- 文本预测：默认情况下处于启用状态，可以禁用
 
### <a name="url"></a>Url

`<TextBox InputScope="Url"/>`

![可输入 URL 的 Windows 触摸键盘](images/input-scopes/url.png)

- 包括 **“.com”** 键和![“转到”](images/input-scopes/kbdgokey.png)键。 按住 **.com** 键以显示 (的其他选项，如 **org** 、 **.net** 和特定于区域的后缀) 
- 包括 **：** 、 **-** 和 **/** 键
- 拼写检查：默认情况下处于禁用状态，可以启用
- 自动更正：默认情况下处于禁用状态，可以启用
- 首字母自动大写：默认情况下处于禁用状态，可以启用
- 文本预测：默认情况下处于禁用状态，可以启用


### <a name="emailsmtpaddress"></a>EmailSmtpAddress

`<TextBox InputScope="EmailSmtpAddress"/>`

![可输入电子邮件地址的 Windows 触摸键盘](images/input-scopes/emailsmtpaddress.png)
- 包括 **@** 和 **.com** 项。 按住 **.com** 键以显示 (的其他选项，如 **org** 、 **.net** 和特定于区域的后缀) 
- 包括 **_** 和 **-** 键
- 拼写检查：默认情况下处于禁用状态，可以启用
- 自动更正：默认情况下处于禁用状态，可以启用
- 首字母自动大写：默认情况下处于禁用状态，可以启用
- 文本预测：默认情况下处于禁用状态，可以启用


### <a name="number"></a>数字

`<TextBox InputScope="Number"/>`

![可输入数字的 Windows 触摸键盘](images/input-scopes/number.png)
- 拼写检查：默认情况下处于启用状态，可以禁用
- 自动更正：默认情况下处于启用状态，可以禁用
- 首字母自动大写：始终处于禁用状态
- 文本预测：默认情况下处于启用状态，可以禁用

### <a name="telephonenumber"></a>TelephoneNumber

`<TextBox InputScope="TelephoneNumber"/>`

![可输入电话号码的 Windows 触摸键盘](images/input-scopes/telephonenumber.png)
- 拼写检查：默认情况下处于启用状态，可以禁用
- 自动更正：默认情况下处于启用状态，可以禁用
- 首字母自动大写：始终处于禁用状态
- 文本预测：默认情况下处于启用状态，可以禁用

### <a name="search"></a>搜索

`<TextBox InputScope="Search"/>`

![用于搜索的 Windows 触摸键盘](images/input-scopes/search.png)
- 包含 **搜索** 键，而不是 **Enter** 键
- 拼写检查：默认情况下处于启用状态，可以禁用
- 自动更正：默认情况下处于启用状态，可以禁用
- 首字母自动大写：始终处于禁用状态
- 文本预测：默认情况下处于启用状态，可以禁用

### <a name="searchincremental"></a>SearchIncremental

`<TextBox InputScope="SearchIncremental"/>`

![用于渐进式搜索的 Windows 触摸键盘](images/input-scopes/searchincremental.png)
- 与 **默认** 布局相同
- 拼写检查：默认情况下处于禁用状态，可以启用
- 自动更正：始终处于禁用状态
- 首字母自动大写：始终处于禁用状态
- 文本预测：始终处于禁用状态

### <a name="formula"></a>公式

`<TextBox InputScope="Formula"/>`

![用于公式的 Windows 触控键盘](images/input-scopes/formula.png)
- 包含 **=** 密钥
- 还包括 **%** 、 **$** 和 **+** 键
- 拼写检查：默认情况下处于启用状态，可以禁用
- 自动更正：默认情况下处于启用状态，可以禁用
- 首字母自动大写：始终处于禁用状态
- 文本预测：默认情况下处于启用状态，可以禁用

### <a name="chat"></a>聊天

`<TextBox InputScope="Chat"/>`

![默认 Windows 触摸键盘](images/input-scopes/default.png)
- 与 **默认** 布局相同
- 拼写检查：默认情况下处于启用状态，可以禁用
- 自动更正：默认情况下处于启用状态，可以禁用
- 首字母自动大写：默认情况下处于启用状态，可以禁用
- 文本预测：默认情况下处于启用状态，可以禁用

### <a name="nameorphonenumber"></a>NameOrPhoneNumber

`<TextBox InputScope="NameOrPhoneNumber"/>`

![默认 Windows 触摸键盘](images/input-scopes/default.png)
- 与 **默认** 布局相同
- 拼写检查：默认情况下处于禁用状态，可以启用
- 自动更正：默认情况下处于禁用状态，可以启用
- 自动大写：默认情况下，可以启用 (每个单词的首字母大写) 
- 文本预测：默认情况下处于禁用状态，可以启用
