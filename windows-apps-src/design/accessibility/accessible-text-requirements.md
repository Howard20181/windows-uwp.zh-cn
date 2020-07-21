---
Description: 本主题介绍了应用中的文本的最佳辅助功能做法：确保颜色和背景满足必需的对比率。
ms.assetid: BA689C76-FE68-4B5B-9E8D-1E7697F737E6
title: 辅助文本要求
label: Accessible text requirements
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7f794cc6f23cf3f4beaf5bc3c9558ceaf2ccc8a6
ms.sourcegitcommit: 0a319e2e69ef88b55d472b009b3061a7b82e3ab1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521309"
---
# <a name="accessible-text-requirements"></a>辅助文本要求  




本主题介绍了应用中的文本的最佳辅助功能做法：确保颜色和背景满足必需的对比率。 本主题还讨论了通用 Windows 平台 (UWP) 应用中的文本元素可以具有的 Microsoft UI 自动化角色，以及图形中的文本的最佳做法。

<span id="contrast_rations"/>
<span id="CONTRAST_RATIONS"/>

## <a name="contrast-ratios"></a>对比率  
尽管用户始终可以选择切换到高对比度模式，但是你的应用的文本设计应当将该选项视为最后的方法。 更好的做法是确保应用文本满足某些为文本及其背景之间对比度级别制定的指导准则。 对比度级别基于不考虑色调的确定性技术进行评估。 例如，如果文本为红色，而背景为绿色，则具有色盲障碍的用户可能无法读取该文本。 检查和更正对比率可以防止出现这些类型的辅助功能问题。

此处记录的文本对比度建议基于 Web 辅助功能标准 [G18：确保文本（和文本的图像）和文本后面的背景之间的对比率至少为 4.5:1](https://www.w3.org/TR/WCAG20-TECHS/G18.html)。 该指南在 *WCAG 2.0 的 W3C 技术*规范中有说明。

为了考虑辅助功能，可见文本与背景的发光度对比率必须最低为 4.5:1。 例外情况包括徽标和附带文本，例如作为非活动 UI 组件一部分的文本。

装饰性文本且未传递任何信息的文本除外。 例如，如果使用随机字词创建背景，且这些字词可以在不改变含义的情况下进行重新整理或取代，则会将这些字词视为装饰性文本且无需符合此条件。

使用颜色对比工具验证可见文本的对比度是否可接受。 请参阅 [WCAG 2.0 G18 的技术（“资源”部分）](https://www.w3.org/TR/WCAG20-TECHS/G18.html#G18-resources)了解可以测试对比率的工具。

> [!NOTE]
> “WCAG 2.0 G18 的技术”列出的某些工具无法与 UWP 应用交互使用。 你可能需要在工具中手动输入前景和背景颜色值，或者屏幕捕获应用 UI，然后对屏幕捕获图像运行对比率工具。

<span id="Text_element_roles"/>
<span id="text_element_roles"/>
<span id="TEXT_ELEMENT_ROLES"/>

## <a name="text-element-roles"></a>文本元素角色  
UWP 应用可以使用以下默认元素（通常称为 *text* 元素或 *textedit* 控件）：

* [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock)：角色是[**文本**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)
* [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)：角色为[ **Edit**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)
* [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock) （和溢出类[**RichTextBlockOverflow**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblockoverflow)）：角色是[**文本**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)
* [**RichEditBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox)：角色为[ **Edit**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)

当控件报告它的角色为 [**Edit**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType) 时，辅助技术假设用户可通过多种方法更改这些值。 因此，如果你在 [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) 中放置静态文本，则说明你向辅助功能用户报告的角色和应用结构有误。

在 XAML 的文本模型中，针对静态文本，主要使用 [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) 和 [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock) 两种元素。 这两种元素均非 [**Control**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control) 子类，因此两者均不可通过键盘聚焦，也不可按 Tab 键顺序显示。 但是，这并不意味着辅助技术现在和将来无法读取它们。 屏幕阅读器的设计通常支持多种读取应用内容的模式，包括摆脱聚焦和 Tab 键顺序限制的专用阅读模式或导航模式，例如“虚拟光标”。 因此，不要为能通过 Tab 键顺序向用户显示静态文本而将静态文本输入到可聚焦容器中。 辅助技术用户预期按 Tab 键顺序显示的所有内容均可交互，如果他们在这里看到静态文本，这不但起不到帮助作用，还会令其感到不解。 在使用屏幕阅读器检查应用的静态文本时，你应该通过“讲述人”功能亲自测试，以感受用户在你的应用上获得的体验。

<span id="Auto-suggest_accessibility"/>
<span id="auto-suggest_accessibility"/>
<span id="AUTO-SUGGEST_ACCESSIBILITY"/>

## <a name="auto-suggest-accessibility"></a>自动建议辅助功能  
当用户在输入字段中进行键入后显示可能的建议列表时，此种情形称为“自动建议”。 这在邮件字段中的“收件人:”行、Windows 中的 Cortana 搜索框、Microsoft Edge 中的 URL 输入字段、“天气”应用中的位置输入字段等方面很常见。 如果你使用的是 XAML [**AutosuggestBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) 或 HTML 内部控件，则已默认为你关联了此体验。 若要使此体验可访问输入字段，必须关联列表。 这将在[实现自动建议](#implementing_auto-suggest)一节中介绍。

已更新了讲述人，使此类型的体验可访问特殊建议模式。 在高级别上，当正确连接编辑字段和列表时，最终用户将：

* 了解列表是否存在，以及列表关闭的时间
* 了解可用的建议数目
* 了解选定项（如果有）
* 能够将“讲述人”焦点移动到列表
* 能够使用所有其他阅读模式浏览建议

![建议列表](images/autosuggest-list.png)<br/>
_建议列表示例_

<span id="Implementing_auto-suggest"/>
<span id="implementing_auto-suggest"/>
<span id="IMPLEMENTING_AUTO-SUGGEST"/>

### <a name="implementing-auto-suggest"></a>实现自动建议  
若要使此体验可访问输入字段，必须在 UIA 树中关联列表。 在桌面应用中，通过 [UIA_ControllerForPropertyId](https://msdn.microsoft.com/windows/desktop/ee684017) 属性实现此关联；在 UWP 应用中，通过 [ControlledPeers](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) 属性实现此关联。

在高级别上，存在 2 种类型的自动建议体验。

**默认选择**  
如果在列表中设置了默认选择，则“讲述人”会在桌面应用中查找 [**UIA_SelectionItem_ElementSelectedEventId**](https://docs.microsoft.com/windows/desktop/WinAuto/uiauto-event-ids) 事件或在 UWP 应用中查找要引发的 [**AutomationEvents.SelectionItemPatternOnElementSelected**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) 事件。 每次选择更改时，如果用户键入其他字母并且建议已更新，或者如果用户浏览该列表，则应引发 **ElementSelected** 事件。

默认选择 ![列表](images/autosuggest-default-selection.png)<br/>
_其中存在默认选择的示例_

**无默认选择**  
如果没有默认选择（如在“天气”应用的位置框中），则每次更新列表时，“讲述人”都会在列表上查找 [**UIA_LayoutInvalidatedEventId**](https://docs.microsoft.com/windows/desktop/WinAuto/uiauto-event-ids) 事件（适用于桌面）或查找要引发的 [**LayoutInvalidated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) 事件（适用于 UWP）。

无默认选择的 ![列表](images/autosuggest-no-default-selection.png)<br/>
_没有默认选择的示例_

### <a name="xaml-implementation"></a>XAML 实现  
如果你使用的是默认 XAML [**AutosuggestBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox)，则已为你关联了所有内容。 如果你要使用 [**TextBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox) 和列表自行实现自动建议体验，则需要在 [TextBox**上将列表设置为**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.getcontrolledpeers)AutomationProperties.ControlledPeers。 必须在每次添加或删除ControlledPeers[**属性时触发该属性的**AutomationPropertyChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.getcontrolledpeers) 事件，此外还必须引发你自己的 [**SelectionItemPatternOnElementSelected**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) 事件或 [**LayoutInvalidated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.peers.automationevents) 事件，具体取决于方案类型（已在本文的前面部分介绍过）。

### <a name="html-implementation"></a>HTML 实现  
如果你使用的是 HTML 中的内部控件，则已为你映射了 UIA 实现。 以下是已为你关联的实现示例。

``` HTML
<label>Sites <input id="input1" type="text" list="datalist1" /></label>
<datalist id="datalist1">
        <option value="http://www.google.com/" label="Google"></option>
        <option value="http://www.reddit.com/" label="Reddit"></option>
</datalist>
```

 如果你要创建自己的控件，则必须设置你自己的 ARIA 控件（将在 W3C 标准中进行介绍）。

<span id="Text_in_graphics"/>
<span id="text_in_graphics"/>
<span id="TEXT_IN_GRAPHICS"/>

## <a name="text-in-graphics"></a>图形中的文本

请尽可能避免在图形中包括文本。 例如，图像源文件中所包括的、在应用中显示为 [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) 元素的任何文本不会自动供辅助技术访问或读取。 如果必须在图形中使用文本，确保你提供为“alt text”的等效值的 [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name) 值包括该文本或者该文本的含义汇总。 如果要从矢量创建作为 [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) 一部分的文本字符，或者要使用 [**Glyphs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Glyphs) 创建文本字符，也可以遵循类似的注意事项。

<span id="Text_font_size"/>
<span id="text_font_size"/>
<span id="TEXT_FONT_SIZE"/>

## <a name="text-font-size-and-scale"></a>文本字号和刻度

当字体使用的字体太小时，用户可能很难阅读应用程序中的文本，因此请确保应用程序中的任何文本在第一位置合理。

完成这一显而易见后，Windows 会提供各种辅助功能和设置，用户可利用这些工具和设置，并调整其自身需求和读取文本的首选项。 这些地方包括：

* "放大镜" 工具，用于放大 UI 的选定区域。 你应确保应用中的文本布局不会使使用放大镜进行读取变得困难。
* 设置中的全局缩放和分辨率设置 **-> 系统 > 显示-> 比例和布局**。 确切的可用大小调整选项会有所不同，具体取决于显示设备的功能。
* 设置中的文本大小设置 **-> 轻松访问-> 显示**。 调整 "放大**文本**" 设置，以便仅在所有应用程序和屏幕上指定支持控件中的文本大小（所有 UWP 文本控件都支持没有任何自定义项或模板化的文本缩放体验）。 
> [!NOTE]
> "**使所有内容变得更大**" 设置允许用户仅在主屏幕上指定其首选的文本和应用大小。

各种文本元素和控件都具有 [**IsTextScaleFactorEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.istextscalefactorenabled) 属性。 默认情况下，此属性的值为 **true**。 如果**为 true**，则可以缩放该元素中文本的大小。 缩放会影响具有较小**FontSize**的文本，而不会影响具有大型**FontSize**的文本。 可以通过将元素的**IsTextScaleFactorEnabled**属性设置为**false**来禁用自动调整大小。 

有关更多详细信息，请参阅[文本缩放](https://docs.microsoft.com/windows/uwp/design/input/text-scaling)。

将以下标记添加到应用并运行它。 调整 "**文本大小**" 设置，并查看每个**TextBlock**会发生什么情况。

XAML
```xml
<TextBlock Text="In this case, IsTextScaleFactorEnabled has been left set to its default value of true."
    Style="{StaticResource BodyTextBlockStyle}"/>

<TextBlock Text="In this case, IsTextScaleFactorEnabled has been set to false."
    Style="{StaticResource BodyTextBlockStyle}" IsTextScaleFactorEnabled="False"/>
```  

建议你不要禁用文本缩放，因为在所有应用中全局缩放 UI 文本都是用户的重要辅助功能体验。

你还可以使用 [**TextScaleFactorChanged**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactorchanged) 事件和 [**TextScaleFactor**](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactor) 属性查明对手机上的 **“文本大小”** 设置的更改。 以下是操作方法：

C#
```csharp
{
    ...
    var uiSettings = new Windows.UI.ViewManagement.UISettings();
    uiSettings.TextScaleFactorChanged += UISettings_TextScaleFactorChanged;
    ...
}

private async void UISettings_TextScaleFactorChanged(Windows.UI.ViewManagement.UISettings sender, object args)
{
    var messageDialog = new Windows.UI.Popups.MessageDialog(string.Format("It's now {0}", sender.TextScaleFactor), "The text scale factor has changed");
    await messageDialog.ShowAsync();
}
```

**TextScaleFactor**的值是 \[1，2.25\]范围内的 double。 最小的文本通过此数量放大。 例如，你可以使用此值缩放图像以匹配文本。 但是请记住，并非所有文本都通过相同的系数来缩放。 一般来说，开始时的文本越大，它受缩放的影响越小。

这些类型具有 **IsTextScaleFactorEnabled** 属性：  
* [**System.windows.controls.contentpresenter>** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentPresenter)
* [**控件**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control)和派生类
* [**FontIcon**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.FontIcon)
* [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock)
* [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock)
* [**TextElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.TextElement)和派生类

<span id="related_topics"/>

## <a name="related-topics"></a>相关主题  

* [文本缩放](https://docs.microsoft.com/windows/uwp/design/input/text-scaling)
* [辅助功能](accessibility.md)
* [基本辅助功能信息](basic-accessibility-information.md)
* [XAML 文本显示示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/XAML%20text%20display%20sample%20(Windows%208))
* [XAML 文本编辑示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/XAML%20text%20editing%20sample%20(Windows%208))
* [XAML 辅助功能示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/XAML%20accessibility%20sample) 
