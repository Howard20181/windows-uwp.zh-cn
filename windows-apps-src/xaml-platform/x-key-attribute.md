---
description: 唯一标识作为资源被创建和引用的元素，这些资源存在于一个 ResourceDictionary 中。
title: xKey 属性
ms.assetid: 141FC5AF-80EE-4401-8A1B-17CB22C2277A
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 45cdc3bcf766cd110498e357052da150ea96fa21
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89161691"
---
# <a name="xkey-attribute"></a>x:Key 特性


唯一标识作为资源被创建和引用的元素，这些资源存在于一个 [**ResourceDictionary**](/uwp/api/Windows.UI.Xaml.ResourceDictionary) 中。

## <a name="xaml-attribute-usage"></a>XAML 属性使用方法

``` syntax
<ResourceDictionary>
  <object x:Key="stringKeyValue".../>
</ResourceDictionary>
```

## <a name="xaml-attribute-usage-implicit-resourcedictionary"></a>XAML 属性使用方法（隐式 **ResourceDictionary**）

``` syntax
<object.Resources>
  <object x:Key="stringKeyValue".../>
</object.Resources>
```

## <a name="xaml-values"></a>XAML 值

| 术语 | 描述 |
|------|-------------|
| 对象 (object) | 任何可共享的对象。 请参阅 [ResourceDictionary 和 XAML 资源引用](../design/controls-and-patterns/resourcedictionary-and-xaml-resource-references.md)。 |
| stringKeyValue | 一个用作键的真实字符串，它必须遵守 _XamlName_&gt; 语法。 请参阅下面的“XamlName 语法”。 | 

##  <a name="xamlname-grammar"></a>XamlName 语法

以下是在通用 Windows 平台 (UWP) XAML 实现中作为键使用的字符串的规范语法：

``` syntax
XamlName ::= NameStartChar (NameChar)*
NameStartChar ::= LetterCharacter | '_'
NameChar ::= NameStartChar | DecimalDigit
LetterCharacter ::= ('a'-'z') | ('A'-'Z')
DecimalDigit ::= '0'-'9'
CombiningCharacter::= none
```

-   字符限制为较小的 ASCII 范围，更专用于罗马字母大写和小写字母、数字和下划线 (\_) 字符。
-   不支持 Unicode 字符范围。
-   名称不能以数字开头。

## <a name="remarks"></a>备注

[**ResourceDictionary**](/uwp/api/Windows.UI.Xaml.ResourceDictionary) 的子元素一般包含一个 **x:Key** 属性，该属性在该词典中指定一个唯一的键值。 键唯一性在加载时由 XAML 处理器执行。 非唯一的 **x:Key** 值将导致 XAML 分析异常。 如果 [{StaticResource} 标记扩展](staticresource-markup-extension.md)发出请求，任何未解析的键也会导致 XAML 分析异常。

**x:Key** 和 [x:Name](x-name-attribute.md) 不是同一概念。 **x:Key** 仅用于资源词典中。 x:Name 适用于 XAML 的所有区域。 一个使用键值的 [**FindName**](/uwp/api/windows.ui.xaml.frameworkelement.findname) 调用不会检索键资源。 在资源字典中定义的对象可能有一个 **x:Key**、一个 **x:Name** 或两者皆有。 不要求键和名称互相匹配。

请注意，在给出的隐式语法中，[**ResourceDictionary**](/uwp/api/Windows.UI.Xaml.ResourceDictionary) 对象在 XAML 处理器生成一个新对象来填充 [**Resources**](/uwp/api/windows.ui.xaml.frameworkelement.resources) 集合的方式上是隐式的。

指定 **x:Key** 的代码等效于任何结合使用一个键和基础 [**ResourceDictionary**](/uwp/api/Windows.UI.Xaml.ResourceDictionary) 的操作。 例如，当向一个 **ResourceDictionary** 添加资源时，一个应用于资源标记中的 **x:Key** 等效于 **Insert** 的 *key* 参数的值。

如果资源字典中的某个项是目标 [**Style**](/uwp/api/Windows.UI.Xaml.Style) 或 [**ControlTemplate**](/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate)，则它可以省略 **x:Key** 的值；在每一种情况下，该资源项的隐式键都是解释为字符串的 **TargetType** 值。 有关详细信息，请参阅[快速入门：设置控件的样式](/previous-versions/windows/apps/hh465498(v=win.10))和 [ResourceDictionary 和 XAML 资源引用](../design/controls-and-patterns/resourcedictionary-and-xaml-resource-references.md)。