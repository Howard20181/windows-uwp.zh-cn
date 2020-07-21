---
description: XBind 标记扩展允许在标记中使用函数。
title: x:Bind 中的函数
ms.date: 02/06/2019
ms.topic: article
keywords: windows 10, uwp, xBind
ms.localizationpriority: medium
ms.openlocfilehash: 879be9591bae36a1dbcd485387fbb4ac7f502fea
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "66360076"
---
# <a name="functions-in-xbind"></a>x:Bind 中的函数

> [!NOTE]
> 有关将应用中的数据绑定与 **{x:Bind}** 结合使用的常规信息（以及有关 **{x:Bind}** 和 **{Binding}** 之间的全方位比较），请参阅[深入了解数据绑定](data-binding-in-depth.md)。

从 Windows 10 版本 1607 开始， **{x:Bind}** 支持使用某个函数作为绑定路径的叶步。 这样做可以实现以下操作：

- 实现值转换的更简单方法
- 依赖多个参数适用于绑定的方法

> [!NOTE]
> 若要使用 **{x:Bind}** 的函数，你的应用的最低目标 SDK 版本必须为 14393 或更高版本。 当你的应用面向较早版本的 Windows 10 时，无法使用这些函数。 有关目标版本的详细信息，请参阅[版本自适应代码](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code)。

在以下示例中，项目的背景和前景会绑定到这些函数，根据颜色参数执行转换。

```xaml
<DataTemplate x:DataType="local:ColorEntry">
    <Grid Background="{x:Bind local:ColorEntry.Brushify(Color), Mode=OneWay}" Width="240">
        <TextBlock Text="{x:Bind ColorName}" Foreground="{x:Bind TextColor(Color)}" Margin="10,5" />
    </Grid>
</DataTemplate>
```

```csharp
class ColorEntry
{
    public string ColorName { get; set; }
    public Color Color { get; set; }

    public static SolidColorBrush Brushify(Color c)
    {
        return new SolidColorBrush(c);
    }

    public SolidColorBrush TextColor(Color c)
    {
        return new SolidColorBrush(((c.R * 0.299 + c.G * 0.587 + c.B * 0.114) > 150) ? Colors.Black : Colors.White);
    }
}
```

## <a name="xaml-attribute-usage"></a>XAML 属性使用方法

```xaml
<object property="{x:Bind pathToFunction.FunctionName(functionParameter1, functionParameter2, ...), bindingProperties}" ... />
```

## <a name="path-to-the-function"></a>函数的路径

与其他属性路径一样指定该函数的路径，可以包含用于定位该函数的点 (.)、索引器或强制转换。

可以使用 XMLNamespace:ClassName.MethodName 语法指定静态函数。 例如，使用以下语法在代码隐藏中绑定到静态函数。

```xaml
<Page 
     xmlns:local="using:MyNamespace">
     ...
    <StackPanel>
        <TextBlock x:Name="BigTextBlock" FontSize="20" Text="Big text" />
        <TextBlock FontSize="{x:Bind local:MyHelpers.Half(BigTextBlock.FontSize)}" 
                   Text="Small text" />
    </StackPanel>
</Page>
```

```csharp
namespace MyNamespace
{
    static public class MyHelpers
    {
        public static double Half(double value) => value / 2.0;
    }
}
```

还可以直接在标记中使用系统函数来完成简单方案，如日期格式设置、文本格式设置、文本串联等。例如：

```xaml
<Page 
     xmlns:sys="using:System"
     xmlns:local="using:MyNamespace">
     ...
     <CalendarDatePicker Date="{x:Bind sys:DateTime.Parse(TextBlock1.Text)}" />
     <TextBlock Text="{x:Bind sys:String.Format('{0} is now available in {1}', local:MyPage.personName, local:MyPage.location)}" />
</Page>
```

如果模式为单向/双向，函数路径将对它执行更改检测，并且将重新计算绑定（如果对这些对象进行了更改）。

要绑定的函数需要：

- 可以访问代码和元数据 - 因此 internal / private 在 C# 中有效，但 C++/CX 需要方法为公有 WinRT 方法。
- 重载基于参数数目，而不是基于参数类型，并且它将尝试匹配第一个带有许多参数的重载。
- 参数类型需要匹配将传入的数据 - 我们不执行收缩转换
- 函数的返回类型需要匹配正使用绑定的属性的类型

绑定引擎会对用函数名称触发的属性更改通知做出反应，并在必要时重新计算绑定。 例如：

```xaml
<DataTemplate x:DataType="local:Person">
   <StackPanel>
      <TextBlock Text="{x:Bind FullName}" />
      <Image Source="{x:Bind IconToBitmap(Icon, CancellationToken), Mode=OneWay}" />
   </StackPanel>
</DataTemplate>
```

```csharp
public class Person:INotifyPropertyChanged
{
    //Implementation for an Icon property and a CancellationToken property with PropertyChanged notifications
    ...

    //IconToBitmap function is essentially a multi binding converter between several options.
    public Uri IconToBitmap (Uri icon, Uri cancellationToken)
    {
        Uri foo = new Uri(...);        
        if (isCancelled)
        {
            foo = cancellationToken;
        }
        else 
        {
            if (this.fullName.Contains("Sr"))
            {
               //pass a different Uri back
               foo = new Uri(...);
            }
            else
            {
                foo = icon;
            }
        }
        return foo;
    }

    //Ensure FullName property handles change notification on itself as well as IconToBitmap since the function uses it
    public string FullName
    {
        get { return this.fullName; }
        set
        {
            this.fullName = value;
            this.OnPropertyChanged ();
            this.OnPropertyChanged ("IconToBitmap"); 
            //this ensures Image.Source binding re-evaluates when FullName changes in addition to Icon and CancellationToken
        }
    }
}
```

> [!TIP]
> 可以使用 x:Bind 中的函数来实现特定的方案，这些方案与通过 WPF 中的转换器和 MultiBinding 实现的方案相同。

## <a name="function-arguments"></a>函数参数

可以指定多个函数参数，用逗号 (,) 分隔

- 绑定路径 - 类似直接绑定该对象的语法。
  - 如果模式为单向/双向，将执行更改检查，并在更改对象后重新计算绑定
- 使用引号括起来的常量字符串 - 需要使用引号将它指定为一个字符串。 乘幂号 (^) 可用于转义字符串中的引号
- 常数 - 例如，-123.456
- 布尔值 – 指定为“x:True”或“x:False”

### <a name="two-way-function-bindings"></a>双向函数绑定

在双向绑定方案中，必须针对绑定的相反方向指定第二个函数。 这是使用 **BindBack** 绑定属性完成的。 在以下示例中，函数应该有一个参数，该参数是需要返回给模型的值。

```xaml
<TextBlock Text="{x:Bind a.MyFunc(b), BindBack=a.MyFunc2, Mode=TwoWay}" />
```
