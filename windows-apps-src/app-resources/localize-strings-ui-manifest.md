---
Description: 如果你希望应用支持其他显示语言，并且你的代码或 XAML 标记或应用包清单中有字符串文本，则将这些字符串移到资源文件 (.resw) 中。 然后，你可以针对应用支持的每种语言制作该资源文件的翻译副本。
title: 对 UI 和应用包清单中的字符串进行本地化
ms.assetid: E420B9BB-C0F6-4EC0-BA3A-BA2875B69722
label: Localize strings in your UI and app package manifest
template: detail.hbs
ms.date: 11/01/2017
ms.topic: article
keywords: windows 10, uwp, 资源, 图像, 资产, MRT, 限定符
ms.localizationpriority: medium
ms.openlocfilehash: 411ecb63189084ba83f9971ded2bbe02d899aabd
ms.sourcegitcommit: a30808f38583f7c88fb5f54cd7b7e0b604db9ba6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762871"
---
# <a name="localize-strings-in-your-ui-and-app-package-manifest"></a>对 UI 和应用包清单中的字符串进行本地化

有关对应用进行本地化的价值主张的详细信息，请参阅[全球化和本地化](../design/globalizing/globalizing-portal.md)。

如果你希望应用支持其他显示语言，并且你的代码或 XAML 标记或应用包清单中有字符串文本，则将这些字符串移到资源文件 (.resw) 中。 然后，你可以针对应用支持的每种语言制作该资源文件的翻译副本。

硬编码字符串文本可以出现在强制性代码或 XAML 标记中，例如作为 **TextBlock** 的 **Text** 属性。 它们还可以显示在应用包清单源文件（`Package.appxmanifest` 文件）中，例如，作为 Visual Studio 清单设计器的“应用程序”选项卡上的显示名称的值。 将这些字符串移到资源文件 (.resw) 中，并参考资源标识符替换应用和清单中的硬编码字符串文本。

与图像资源文件中仅包含一个图像资源的图像资源不同，字符串资源文件中包含*多个*字符串资源。 字符串资源是资源文件 (.resw)，并且你通常在项目的 \Strings 文件夹中创建此类资源文件。 有关任何在资源文件 (.resw) 的名称中使用限定符的背景，请参阅[定制语言、比例和其他限定符的资源](tailor-resources-lang-scale-contrast.md)。

## <a name="store-strings-in-a-resources-file"></a>将字符串存储在资源文件中

1. 设置应用的默认语言。
    1. 在 Visual Studio 中打开你的解决方案后，打开 `Package.appxmanifest`。
    2. 在应用程序选项卡上，确认已设置相应的默认语言（如“en”或“en-US”）。 其余步骤将假设你已经将默认语言设置为“en-US”。
    <br>**注意**  至少需要为此默认语言提供本地化的字符串资源。 如果没有找到与用户的首选语言或显示语言设置更好的匹配，将加载这些资源。
2. 创建默认语言的资源文件 (.resw)。
    1. 在你的项目节点下，创建一个新文件夹，并将其命名为“字符串”。
    2. 在 `Strings` 下，创建一个新的子文件夹，并将其命名为“en-US”。
    3. 在 `en-US` 下，创建一个新的资源文件 (.resw)，并确认其名称为“Resources.resw”。
    <br>**注意**  如果你想要移植 .NET 资源文件 ( .resx) ，请参阅[移植 XAML 和 UI](../porting/wpsl-to-uwp-porting-xaml-and-ui.md#localization-and-globalization)。
3. 打开 `Resources.resw` 并添加这些字符串资源。

    `Strings/en-US/Resources.resw`

    ![> E N U S > 的字符串的添加资源表的屏幕截图 .resw 文件。](images/addresource-en-us.png)

    在此示例中，“Greeting”是你可以从你的标记中引用的字符串资源标识符，我们将向你进行演示。 对于标识符“Greeting”，将为文本属性提供一个字符串，为宽度属性提供一个字符串。 “Greeting.Text”是属性标识符的一个示例，因为它对应 UI 元素的属性。 例如，你还可以在名称列中添加“Greeting.Foreground”，并将它的值设置为“红色”。 “Farewell”标识符是一个简单的字符串资源标识符；它没有子属性，并且可以从强制性代码加载，我们将向你进行演示。 “备注”列是向翻译提供任何特殊说明的一个好地方。

    在此示例中，我们有一个名称为“Farewell”的简单字符串资源标识符条目，因此我们不能*同时*具有基于该相同标识符的属性标识符。 因此，添加“Farewell.Text”将导致生成 `Resources.resw` 时出现重复条目错误。

    资源标识符区分大小写，并且对于每个资源文件应是唯一的。 请确保使用有意义的资源标识符，为翻译人员提供额外的上下文。 请勿在字符串资源送去翻译后更改资源标识符。 本地化团队使用资源标识符来跟踪资源内的添加、删除和更新。 资源标识符中的更改（也称为“资源标识符转换”）需要重新翻译字符串，因为尽管字符串可能会删除并且会添加其他字符串，但会显示这些字符串。

## <a name="refer-to-a-string-resource-identifier-from-xaml"></a>从 XAML 引用字符串资源标识符

使用 [x:Uid 指令](../xaml-platform/x-uid-directive.md)将标记中的控件或其他元素与字符串资源标识符关联。

```xaml
<TextBlock x:Uid="Greeting"/>
```

在运行时加载 `\Strings\en-US\Resources.resw`（因为现在这是项目中唯一的资源文件）。 **TextBlock** 上的 **x:Uid** 指令会引发查找，在 `Resources.resw` 中查找包含字符串资源标识符“Greeting”的属性标识符。 找到“Greeting.Text”和“Greeting.Width”属性标识符，并将它们的值应用到 **TextBlock**，覆盖在标记中本地设置的任何值。 如果你添加了“Greeting.Foreground”值，也会应用该值。 但只有属性标识符用来设置 XAML 标记元素上的属性，因此在此 TextBlock 上将 **x:Uid** 设置为“Farewell”不会产生影响。 `Resources.resw`*确实*包含字符串资源标识符 "告别"，但它不包含它的属性标识符。

将字符串资源标识符分配给 XAML 元素时，确保该标识符的*所有*属性标识符适合 XAML 元素。 例如，如果你在 **TextBlock** 上设置了 `x:Uid="Greeting"`，“Greeting.Text”将解析，因此 **TextBlock** 类型具有文本属性。 但如果你在**按钮**上设置了 `x:Uid="Greeting"`，“Greeting.Text”将引发运行时错误，因为**按钮**类型不具有文本属性。 适用于此案例的一个解决方案是创作名称为“ButtonGreeting.Content”的属性标识符，并在**按钮**上设置 `x:Uid="ButtonGreeting"`。

你可能不会想要从资源文件设置**宽度**，而是想要允许控件根据内容动态调整大小。

**注意**  对于[附加属性](../xaml-platform/attached-properties-overview.md)，需要在 .resw 文件的 "名称" 列中使用特殊的语法。 例如，要设置“Greeting”标识符的 [**AutomationProperties.Name**](/uwp/api/windows.ui.xaml.automation.automationproperties.NameProperty) 附加属性的值，下面是需要在“名称”列中输入的内容。

```xml
Greeting.[using:Windows.UI.Xaml.Automation]AutomationProperties.Name
```

## <a name="refer-to-a-string-resource-identifier-from-code"></a>引用代码中的字符串资源标识符

可以基于简单字符串资源标识符显式地加载字符串资源。

> [!NOTE]
> 如果调用了任何*可能*在后台/工作线程上执行的 **GetForCurrentView** 方法，则可使用 `if (Windows.UI.Core.CoreWindow.GetForCurrentThread() != null)` 测试保护该调用。 从背景/工作线程调用 **GetForCurrentView** 导致“*&lt;typename&gt; 无法在不具有 CoreWindow 的线程上创建*”的异常。

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Farewell");
```

```cppwinrt
auto resourceLoader{ Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView() };
myXAMLTextBlockElement().Text(resourceLoader.GetString(L"Farewell"));
```

```cpp
auto resourceLoader = Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView();
this->myXAMLTextBlockElement->Text = resourceLoader->GetString("Farewell");
```

可以使用来自类库（通用 Windows）或 [Windows 运行时库（通用 Windows）](../winrt-components/index.md)项目内的相同代码。 在运行时，加载托管库的应用的资源。 我们建议库从托管库的应用加载资源，因为应用的本地化程度可能更高。 如果库确实需要提供资源，该库应向其托管应用提供将这些资源替换为输入的选项。

如果资源名称被分段 (包含 "." 个字符) ，则在资源名称中使用正斜杠替换句点 ( "/" ) 字符。 例如，属性标识符包含点;因此，需要执行此操作，以便从代码中加载其中的一个 substition。

```csharp
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Fare/Well"); // <data name="Fare.Well" ...> ...
```

如果有疑问，可以使用 [MakePri.exe](makepri-exe-command-options.md) 转储应用的 PRI 文件。 每个资源 `uri` 都显示在转储的文件中。

```xml
<ResourceMapSubtree name="Fare"><NamedResource name="Well" uri="ms-resource://<GUID>/Resources/Fare/Well">...
```

## <a name="refer-to-a-string-resource-identifier-from-your-app-package-manifest"></a>引用来自应用包清单的字符串资源标识符

1. 打开应用程序包清单源文件 (`Package.appxmanifest` 文件) ，默认情况下，你的应用程序 `Display name` 表示为字符串文字。

   ![Appxmanifest.xml 文件的屏幕截图，其中显示了 "应用程序" 选项卡，显示名称设置为 "艾德工作周期"。](images/display-name-before.png)

2. 要制作此字符串的本地化版本，请打开 `Resources.resw` 并使用名称“AppDisplayName”和值“Adventure Works Cycles”添加新的字符串资源。

3. 使用你刚创建的字符串资源标识符引用（“AppDisplayName”）替换显示名称字符串参数。 你使用 `ms-resource` URI（统一资源标识符）方案来执行此操作。

   ![Appxmanifest.xml 文件的屏幕截图，显示 "应用程序" 选项卡，显示名称设置为 "M S 资源应用程序显示名称"。](images/display-name-after.png)

4. 对清单中的每个需要本地化的字符串重复此过程。 例如，你的应用的短名称（你可以将其配置为显示在“开始”菜单的应用磁贴上）。 有关你可以本地化的应用包清单中的所有项目的列表，请参阅[可本地化清单项目](/uwp/schemas/appxpackage/uapmanifestschema/localizable-manifest-items-win10?branch=live)。

## <a name="localize-the-string-resources"></a>本地化字符串资源

1. 复制你的资源文件 (.resw) 用于另一种语言。
    1. 在“字符串”下，创建一个新的子文件夹并将其命名为“de-DE”，表示德语（德国）。
   <br>**注意**  对于文件夹名称，可以使用任何[BCP-47 语言标记](https://tools.ietf.org/html/bcp47)。 请参阅[定制语言、比例和其他限定符的资源](tailor-resources-lang-scale-contrast.md)获取有关语言限定符和常用语言标记列表的详细信息。
   2. 在 `Strings/de-DE` 文件夹中复制 `Strings/en-US/Resources.resw`。
2. 翻译字符串。
    1. 打开 `Strings/de-DE/Resources.resw` 并翻译“值”列中的值。 你无需翻译评论。

    `Strings/de-DE/Resources.resw`

    ![添加资源，德语](images/addresource-de-de.png)

如果你愿意，你可以对其他语言重复步骤 1 和 2。

`Strings/fr-FR/Resources.resw`

![添加资源，法语](images/addresource-fr-fr.png)

## <a name="test-your-app"></a>测试应用程序

测试应用的默认显示语言。 然后，你可以在 "**设置**时间" 中更改显示语言  >  **& 语言**  >  **区域 & 语言**  >  **语言**并重新测试应用。 在你的 UI 和 shell 中查看字符串（例如，你的标题栏，即你的显示语言，以及磁贴上的短名称）。

**注意**如果可以找到匹配显示语言设置的文件夹名称，则加载该文件夹中的资源文件。 否则将发生回退，以应用的默认语言的资源结束。

## <a name="factoring-strings-into-multiple-resources-files"></a>将字符串构建到多个资源文件中

你可以将所有字符串保留在一个单独的资源文件 (resw) 中，也可以将它们构建到多个资源文件中。 例如，你可能想要将错误消息保留在一个资源文件中，将你的应用包清单保留在另一个资源文件中，将你的 UI 字符串保留在第三个资源文件中。 这是你在此案例中的文件夹结构外观。

!["解决方案" 面板的屏幕截图，其中显示了艾德公司的工作周期 > 带有德语、U 和法语的字符串文件夹以及法语区域设置文件夹和文件。](images/manifest-resources.png)

要将字符串资源标识符引用的范围设定在一个特定的文件，你只需在标识符前面添加 `/<resources-file-name>/`。 下面的标记示例假定 `ErrorMessages.resw` 包含名称为“PasswordTooWeak.Text”且其值对错误进行描述的资源。

```xaml
<TextBlock x:Uid="/ErrorMessages/PasswordTooWeak"/>
```

你只需要在字符串资源标识符前面为资源文件添加*除* `Resources.resw` 以外的 `/<resources-file-name>/`。 这是因为“Resources.resw”是默认文件名称，因此如果你省略文件名称，将假定使用该名称（正如我们在本主题前面的示例中所示）。

下面的代码示例假定 `ErrorMessages.resw` 包含名称为“MismatchedPasswords”且其值对错误进行描述的资源。

> [!NOTE]
> 如果调用了任何*可能*在后台/工作线程上执行的 **GetForCurrentView** 方法，则可使用 `if (Windows.UI.Core.CoreWindow.GetForCurrentThread() != null)` 测试保护该调用。 从背景/工作线程调用 **GetForCurrentView** 导致“*&lt;typename&gt; 无法在不具有 CoreWindow 的线程上创建*”的异常。

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("ErrorMessages");
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("MismatchedPasswords");
```

```cppwinrt
auto resourceLoader{ Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView(L"ErrorMessages") };
myXAMLTextBlockElement().Text(resourceLoader.GetString(L"MismatchedPasswords"));
```

```cpp
auto resourceLoader = Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView("ErrorMessages");
this->myXAMLTextBlockElement->Text = resourceLoader->GetString("MismatchedPasswords");
```

如果要将“AppDisplayName”资源从 `Resources.resw` 移动到 `ManifestResources.resw`，在应用包清单中，需将 `ms-resource:AppDisplayName` 更改为 `ms-resource:/ManifestResources/AppDisplayName`。

如果资源文件名被分段 (包含 "." 个字符) ，则在引用该名称时，请在名称中保留该名称。 **请勿** 使用正斜杠 ( "/" 替换点 ) 字符，就像为资源名称所做的那样。

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("Err.Msgs");
```

如果有疑问，可以使用 [MakePri.exe](makepri-exe-command-options.md) 转储应用的 PRI 文件。 每个资源 `uri` 都显示在转储的文件中。

```xml
<ResourceMapSubtree name="Err.Msgs"><NamedResource name="MismatchedPasswords" uri="ms-resource://<GUID>/Err.Msgs/MismatchedPasswords">...
```

## <a name="load-a-string-for-a-specific-language-or-other-context"></a>为特定语言或其他上下文加载字符串

默认 [**ResourceContext**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext?branch=live)（从 [**ResourceContext.GetForCurrentView**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.GetForCurrentView) 获取）包含每个限定符名称的限定符值，表示默认运行时上下文（换言之，即当前用户和计算机的设置）。 根据该运行时上下文中的限定符值匹配资源文件 (.resw) - 基于其名称中的限定符。

但在有些时候，你可能想要让你的应用覆盖系统设置，并明确当查找要加载的匹配资源文件时要使用的语言、比例或其他限定符值。 例如，你可能希望用户能够选择一种替代语言用于工具提示或错误消息。

为此，你可以构建一个新的 **ResourceContext**（而不使用默认值），覆盖它的值，然后在你的字符串查找中使用该上下文对象。

```csharp
var resourceContext = new Windows.ApplicationModel.Resources.Core.ResourceContext(); // not using ResourceContext.GetForCurrentView
resourceContext.QualifierValues["Language"] = "de-DE";
var resourceMap = Windows.ApplicationModel.Resources.Core.ResourceManager.Current.MainResourceMap.GetSubtree("Resources");
this.myXAMLTextBlockElement.Text = resourceMap.GetValue("Farewell", resourceContext).ValueAsString;
```

按照上述示例使用 **QualifierValues** 适用于任何限定符。 对于特殊的语言案例，你可以使用此替代方法。

```csharp
resourceContext.Languages = new string[] { "de-DE" };
```

为了在全局级别达到相同的效果，*可以* 覆盖默认 **ResourceContext** 中的限定符值。 但我们建议你调用 [**ResourceContext.SetGlobalQualifierValue**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.setglobalqualifiervalue?branch=live#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_)。 你通过调用 **SetGlobalQualifierValue** 一次性设置值，则每次当你使用默认 **ResourceContext** 进行查找时，这些值都会对它产生影响。

```csharp
Windows.ApplicationModel.Resources.Core.ResourceContext.SetGlobalQualifierValue("Language", "de-DE");
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Farewell");
```

某些限定符有系统数据提供程序。 因此，你无需调用 **SetGlobalQualifierValue**，可以改为通过提供程序自己的 API 调整提供程序。 例如，此代码显示如何设置 [**PrimaryLanguageOverride**](/uwp/api/Windows.Globalization.ApplicationLanguages.PrimaryLanguageOverride)。

```csharp
Windows.Globalization.ApplicationLanguages.PrimaryLanguageOverride = "de-DE";
```

## <a name="updating-strings-in-response-to-qualifier-value-change-events"></a>响应限定符值更改事件更新字符串

你运行的应用可以响应影响默认 **ResourceContext** 中的限定符值的系统设置中的更改。 其中任何系统设置在 [**ResourceContext.QualifierValues**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues) 上调用 [**MapChanged**](/uwp/api/windows.foundation.collections.iobservablemap-2.mapchanged?branch=live) 事件。

为了响应此事件，可以从默认的 **ResourceContext** 重新加载字符串。

```csharp
public MainPage()
{
    this.InitializeComponent();

    ...

    // Subscribe to the event that's raised when a qualifier value changes.
    var qualifierValues = Windows.ApplicationModel.Resources.Core.ResourceContext.GetForCurrentView().QualifierValues;
    qualifierValues.MapChanged += new Windows.Foundation.Collections.MapChangedEventHandler<string, string>(QualifierValues_MapChanged);
}

private async void QualifierValues_MapChanged(IObservableMap<string, string> sender, IMapChangedEventArgs<string> @event)
{
    var dispatcher = this.myXAMLTextBlockElement.Dispatcher;
    if (dispatcher.HasThreadAccess)
    {
        this.RefreshUIText();
    }
    else
    {
        await dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => this.RefreshUIText());
    }
}

private void RefreshUIText()
{
    var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();
    this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Farewell");
}
```

## <a name="load-strings-from-a-class-library-or-a-windows-runtime-library"></a>从类库或 Windows 运行时库加载字符串

引用的类库（通用 Windows）或 [Windows 运行时库（通用 Windows）](../winrt-components/index.md)的字符串资源通常被添加到在生成过程中要将其包括在其中的包的子文件夹中。 此类字符串的资源标识符通常采用 *LibraryName/ResourcesFileName/ResourceIdentifier* 的形式。

库可以为自己的资源获取 ResourceLoader。 例如，下面的代码演示了一个库或引用它的应用程序如何获取库的字符串资源的 Windows.applicationmodel.resources.resourceloader。

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("ContosoControl/Resources");
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("exampleResourceName");
```

对于 (通用 Windows) 的 Windows 运行时库，如果默认命名空间为分段 (其中包含 "." 个字符) ，则在资源结构图名称中使用点。

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("Contoso.Control/Resources");
```

对于类库 (通用 Windows) ，无需执行此操作。 如果有疑问，可以指定 [MakePri.exe 命令行选项](makepri-exe-command-options.md) 来转储组件或库的 PRI 文件。 每个资源 `uri` 都显示在转储的文件中。

```xml
<NamedResource name="exampleResourceName" uri="ms-resource://Contoso.Control/Contoso.Control/ReswFileName/exampleResourceName">...
```

## <a name="loading-strings-from-other-packages"></a>从其他包加载字符串

应用包的资源通过包自己的顶级 [**windows.applicationmodel.resources.core.resourcemap**](/uwp/api/windows.applicationmodel.resources.core.resourcemap?branch=live) 进行管理和访问，该文件可从当前 [**ResourceManager**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live)访问。 在每一个包中，各种组件可以具有自己的 ResourceMap 子树，你可以通过 [**ResourceMap.GetSubtree**](/uwp/api/windows.applicationmodel.resources.core.resourcemap.getsubtree?branch=live) 进行访问。

框架包可以使用绝对资源标识符 URI 访问自己的资源。 另请参阅 [URI 方案](uri-schemes.md)。

## <a name="loading-strings-in-non-packaged-applications"></a>在非打包应用程序中加载字符串

从 Windows 版本 1903 (2019 更新) ，非打包的应用程序也可以利用资源管理系统。

只需创建 UWP 用户控件/库，然后 [将任何字符串存储在资源文件中](#store-strings-in-a-resources-file)。 然后，可以 [引用 XAML 中的字符串资源标识符](#refer-to-a-string-resource-identifier-from-xaml)，从 [代码中引用字符串资源标识符](#refer-to-a-string-resource-identifier-from-code)，或者 [从类库或 Windows 运行时库加载字符串](#load-strings-from-a-class-library-or-a-windows-runtime-library)。

若要在非打包的应用程序中使用资源，应执行以下操作：

1. 当从代码解析资源时，请使用 [GetForViewIndependentUse](/uwp/api/windows.applicationmodel.resources.resourceloader.getforviewindependentuse) 而不是 [GetForCurrentView](/uwp/api/windows.applicationmodel.resources.resourceloader.getforcurrentview) ，因为未打包方案中没有 *当前视图* 。 如果在未打包的情况下调用 [GetForCurrentView](/uwp/api/windows.applicationmodel.resources.resourceloader.getforcurrentview) ，则会发生以下异常：无法 *在没有 CoreWindow 的线程上创建资源上下文。*
1. 使用 [MakePri.exe](./compile-resources-manually-with-makepri.md) 手动生成应用的资源 pri 文件。
    - `makepri new /pr <PROJECTROOT> /cf <PRICONFIG> /of resources.pri`运行
    - &lt;Priconfig.default.xml &gt; 必须省略 " &lt; 打包 &gt; " 部分，以便将所有资源捆绑在单个资源的 pri 文件中。 如果使用[createconfig](./makepri-exe-command-options.md#createconfig-command)创建的默认[MakePri.exe 配置文件](./makepri-exe-configuration.md)，则需要在 &lt; &gt; 创建后手动删除 "打包" 部分。
    - &lt;Priconfig.default.xml &gt; 必须包含将项目中的所有资源合并为单个资源 pri 文件所需的所有相关索引器。 [Createconfig](./makepri-exe-command-options.md#createconfig-command)创建的默认[MakePri.exe 配置文件](./makepri-exe-configuration.md)包含所有索引器。
    - 如果不使用默认配置，请确保已启用 PRI 索引器 (查看默认配置 "如何执行此操作") 合并位于项目根目录中的 UWP 项目引用、NuGet 引用等的 PRIs。
        > [!NOTE]
        > 通过省略 `/IndexName` 并且项目没有应用程序清单，PRI 文件的 IndexName/root 命名空间会自动设置为 *应用程序*，运行时识别非打包应用程序 (这将删除对包 ID) 之前的硬依赖项。 指定资源 Uri 时，将忽略根命名空间的 ms 资源：//引用会将 *应用程序* 推断为非打包应用程序的根命名空间 (或者，可以将 *应用程序* 明确指定为 ms 资源：//Application/) 。
1. 将 PRI 文件复制到 .exe 的生成输出目录
1. 运行 .exe 
    > [!NOTE]
    > 基于非封装应用中的语言解析资源时，资源管理系统使用系统显示语言而不是用户首选语言列表。 用户首选语言列表仅用于 UWP 应用。

> [!Important]
> 修改资源时，必须手动重新生成 PRI 文件。 建议使用可处理 [MakePri.exe](./compile-resources-manually-with-makepri.md) 命令的后期生成脚本，并将 resources 输出输出到 .exe 目录。

## <a name="important-apis"></a>重要的 API
* [ApplicationModel.Resources.ResourceLoader](/uwp/api/Windows.ApplicationModel.Resources.ResourceLoader)
* [ResourceContext.SetGlobalQualifierValue](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.setglobalqualifiervalue?branch=live#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_)
* [MapChanged](/uwp/api/windows.foundation.collections.iobservablemap-2.mapchanged?branch=live)

## <a name="related-topics"></a>相关主题
* [移植 XAML 和 UI](../porting/wpsl-to-uwp-porting-xaml-and-ui.md#localization-and-globalization)
* [x:Uid 指令](../xaml-platform/x-uid-directive.md)
* [附加属性](../xaml-platform/attached-properties-overview.md)
* [可本地化的清单项](/uwp/schemas/appxpackage/uapmanifestschema/localizable-manifest-items-win10?branch=live)
* [BCP-47 语言标记](https://tools.ietf.org/html/bcp47)
* [定制语言、比例和其他限定符的资源](tailor-resources-lang-scale-contrast.md)
* [如何加载字符串资源](/previous-versions/windows/apps/hh965323(v=win.10))