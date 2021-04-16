---
title: 将 Clipboard 示例从 C# 移植到 C++/WinRT（案例研究）
description: 本主题提供了一个有关将[通用 Windows 平台 (UWP) 应用示例](https://github.com/microsoft/Windows-universal-samples)之一从 [C#](/visualstudio/get-started/csharp) 移植到 [C++/WinRT](./intro-to-using-cpp-with-winrt.md) 的案例研究。
ms.date: 04/13/2020
ms.topic: article
keywords: windows 10, uwp, 标准, c++, cpp, winrt, 投影, 端口, 迁移, C#, 示例, 剪贴板, 案例, 研究
ms.localizationpriority: medium
ms.openlocfilehash: cef005e00302efe850e19fff27318495efb275e9
ms.sourcegitcommit: b89d3bc42713fbe4c0ada99d6f514f1304821221
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107466437"
---
# <a name="porting-the-clipboard-sample-to-cwinrt-from-cmdasha-case-study"></a>将 Clipboard 示例从 C# 移植到 C++/WinRT&mdash;案例研究

本主题提供了一个有关将[通用 Windows 平台 (UWP) 应用示例](https://github.com/microsoft/Windows-universal-samples)之一从 [C#](/visualstudio/get-started/csharp) 移植到 [C++/WinRT](./intro-to-using-cpp-with-winrt.md) 的案例研究。 可以通过按照本演练并自行移植示例，获取移植实践和体验。

有关对从 C# 移植到 C++/WinRT 所涉及的技术详细信息的全面分类，请参阅对应的主题[从 C# 迁移到 C++/WinRT](./move-to-winrt-from-csharp.md)。

## <a name="a-brief-preface-about-c-and-c-source-code-files"></a>简要介绍 C# 和 C++ 源代码文件

在 C# 项目中，源代码文件主要是 `.cs` 文件。 迁移到 C++ 后，你会注意到有更多类型的源代码文件可供使用。 原因与编译器之间的差异、重用 C++ 源代码的方式，以及声明和定义类型及其函数（其方法）的概念有关 。

函数 declaration 仅描述函数的签名（其返回类型和名称及其参数类型和名称） 。 函数 definition 包括函数的正文（其实现） 。

涉及到类型时稍有不同。 通过提供类型的名称并（至少）仅声明其所有成员函数（以及其他成员）来定义类型 。 就是这样，即使未定义类型的成员函数，也可以定义类型。

- 常见的 C++ 源代码文件是 `.h` 和 `.cpp` 文件。 `.h` 文件是标头文件，它定义了一个或多个类型。 虽然可以在标头中定义成员函数，但这通常是 `cpp` 文件的用途。 因此，对于假想 C++ 类型 MyClass，应在 `MyClass.h` 中定义 MyClass，并在 `MyClass.cpp` 中定义其成员函数。 要让其他开发人员重复使用你的类，只需共享 `.h` 文件和对象代码。 应将 `.cpp` 文件保密，因为其实现构成了知识产权。
- 预编译标头 (`pch.h`)。 通常，应用程序中包含一组标头文件，这组文件很少变化。 因此无需在每次编译时处理这组标头的内容，即可将这些标头聚合为一个，编译一次，然后在每次生成时使用预编译步骤的输出。 可以通过预编译标头文件（通常名为 `pch.h`）来执行此操作。
- `.idl` 文件。 这些文件包含接口定义语言 (IDL)。 可以将 IDL 视为 Windows 运行时类型的标头文件。 我们将在 [MainPage 类型的 IDL](#idl-for-the-mainpage-type) 部分中详细地讨论 IDL。

## <a name="download-and-test-the-clipboard-sample"></a>下载和测试 Clipboard 示例

请访问 [Clipboard 示例](/samples/microsoft/windows-universal-samples/clipboard/)网页，然后单击“下载 ZIP”。 解压缩下载的文件，并查看文件夹结构。

- C# 版本的示例源代码包含在名为 `cs` 的文件夹中。
- C++/WinRT 版本的示例源代码包含在名为 `cppwinrt` 的文件夹中。
- C# 版本和 C++/WinRT 版本使用的其他文件都可以在 `shared` 和 `SharedContent` 文件夹中找到。

本主题中的演练演示如何重新创建 C++/WinRT 版本的 Clipboard 示例，具体方法是从 C# 源代码移植该示例。 这样，你就可以了解如何将你自己 C# 的项目移植到 C++/WinRT。

若要了解示例的作用，请打开 C# 解决方案 (`\Clipboard_sample\cs\Clipboard.sln`)，根据需要更改配置（可能更改为 x64），然后生成并运行。 示例本身的用户界面 (UI) 会逐步指导你了解各种功能。

## <a name="create-a-blank-app-cwinrt-named-clipboard"></a>创建名为“Clipboard”的空白应用 (C++/WinRT)

> [!NOTE]
> 有关安装和使用 C++/WinRT Visual Studio 扩展 (VSIX) 和 NuGet 包（两者共同提供项目模板，并生成支持）的信息，请参阅[适用于 C++/WinRT 的 Visual Studio 支持](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)。

通过在 Microsoft Visual Studio 中创建新的 C++/WinRT 项目，开始移植过程。 使用“空白应用(C++/WinRT)”项目模板创建新项目。 将其名称设置为 Clipboard，然后确保取消选中“将解决方案和项目放在同一目录中”（使文件夹结构与此演练匹配）。

若是为了获取基线，请确保这一新的空项目生成并运行。

## <a name="packageappxmanifest-and-asset-files"></a>Package.appxmanifest 和资产文件

如果不需要在同一台计算机上并行安装此示例的 C# 和 C++/WinRT 版本，则两个项目的应用包清单源文件 (`Package.appxmanifest`) 可能相同。 在这种情况下，只需将 `Package.appxmanifest` 从 C# 项目复制到 C++/WinRT 项目，即可完成。

为了使示例的两个版本共存，它们需要不同的标识符。 在这种情况下，在 C++/WinRT 项目中，在 XML 编辑器中打开 `Package.appxmanifest` 文件，并记下这三个值。

- 在 /Package/Identity 元素中，记下 Name 属性的值 。 这就是“程序包名称”。 对于新创建的项目，该项目将为其提供唯一 GUID 的初始值。
- 在 /Package/Applications/Application 元素中，记下 ID 属性的值 。 这就是“应用程序 ID”。
- 在 /Package/mp:PhoneIdentity 元素中，记下“PhoneProductId”属性的值 。 同样，对于新创建的项目，这将设置为与包名称相同的 GUID。

然后，将 `Package.appxmanifest` 从 C# 项目复制到 C++/WinRT 项目。 最后，可以还原记下的三个值。 或者，你可以编辑复制的值，使其成为唯一的和/或适用于应用程序和组织（通常对于新项目也是执行此操作）。 例如，在这种情况下，可以将复制的值从 Microsoft.SDKSamples.Clipboard.CS 改为 Microsoft.SDKSamples.Clipboard.CppWinRT，而不是还原包名称的值 。 并且，我们可以将应用程序 ID 设置保留为“App”。 只要包名称或应用程序 ID 不同，这两个应用程序就会有不同的应用程序用户模型 ID (AUMID)。

在本演练中，需要对 `Package.appxmanifest` 进行一些其他更改。 字符串“Clipboard C# Sample”出现三次。 将其更改为“Clipboard C++/WinRT Sample”。

在 C++/WinRT 项目中，`Package.appxmanifest` 文件和项目现在与它们引用的资产文件不同步。 若要解决此问题，请先选择 `Assets` 文件夹中的所有文件（在 Visual Studio 的解决方案资源管理器中）并删除这些文件（选择对话框中的“删除”），从 C++/WinRT 项目中删除这些资产。

C# 项目引用共享文件夹中的资产文件。 可以在 C++/WinRT 项目中执行相同的操作，也可以按照本演练中的操作来复制文件。

导航到 `\Clipboard_sample\SharedContent\media` 文件夹。 选择 C# 项目包含的七个文件（`microsoft-sdk.png` 到 `windows-sdk.png`），复制这些文件，然后将它们粘贴到新项目的 `\Clipboard\Clipboard\Assets` 文件夹中。

右键单击 `Assets` 文件夹（在 C++/WinRT 项目的解决方案资源管理器中）>“添加” > “现有项…”，然后导航到 `\Clipboard\Clipboard\Assets` 。 在文件选取器中选择这七个文件，然后单击“添加”。

`Package.appxmanifest` 现在与项目的资产文件重新同步。

## <a name="mainpage-including-the-functionality-that-configures-the-sample"></a>MainPage，包括用于配置示例的功能

&mdash;类似于所有[通用 Windows 平台 (UWP) 应用示例](https://github.com/microsoft/Windows-universal-samples)&mdash;，Clipboard 示例包含一个方案集合，用户一次可以单步执行一个方案。 给定示例中的方案集合在示例的源代码中进行配置。 集合中的每个方案都是一个数据项，其中存储了标题以及项目中实现该方案的类的类型。

在示例的 C# 版本中，如果你查看 `SampleConfiguration.cs` 源代码文件，将会看到两个类。 大多数配置逻辑位于 MainPage 类中，该类是一个分部类（在与 `MainPage.xaml` 中的标记和 `MainPage.xaml.cs` 中的强制性代码结合使用时，它会形成完整的类）。 此源代码文件中的另一个类是 Scenario，具有 Title 和 ClassType 属性  。

在接下来的几个子部分中，我们将介绍如何移植 MainPage 和 Scenario 。

### <a name="idl-for-the-mainpage-type"></a>MainPage 类型的 IDL

我们先来简要介绍一下接口定义语言 (IDL)，以及它如何帮助我们使用 C++/WinRT 进行编程。 IDL 是用于描述 Windows 运行时类型的可调用图面的一种源代码。 类型的可调用（或公共）图面已投影到世界各地区，以便可以使用该类型。 该类型的投影部分与该类型的实际内部实现（当然是不可调用的，并且不是公共的）进行比较。 这只是我们在 IDL 中定义的投影部分。

使用编写的 IDL 源代码（在 `.idl` 文件中）即可将 IDL 编译为计算机可读的元数据文件（也称为 Windows 元数据）。 这些元数据文件的扩展名为 `.winmd`，以下是其部分用途。

- `.winmd` 可以描述组件中的 Windows 运行时类型。 从某个应用程序项目引用 Windows 运行时组件 (WRC) 时，该应用程序项目将读取属于 WRC 的 Windows 元数据（该元数据可能位于单独的文件中，也可能会打包到与 WRC 本身相同的文件中），以便你可以从应用程序内使用 WRC 的类型。
- `.winmd` 可以描述应用程序某一部分中的 Windows 运行时类型，以便它们可由同一应用程序的不同部分使用。 例如，在同一应用中 XAML 页使用的 Windows 运行时类型。
- 为了更轻松地使用 Windows 运行时类型（内置或第三方），C++/WinRT 生成系统使用 `.winmd` 文件来生成包装类型，以表示这些 Windows 运行时类型的投影部分。
- 为了让你更轻松地实现 Windows 运行时类型，C++/WinRT 生成系统会将 IDL 转换为 `.winmd` 文件，然后使用该文件为投影生成包装，并生成作为实现基础的存根（我们稍后会在本主题中详细介绍这些存根）。

与 C++/WinRT 一起使用的 IDL 的特定版本是 [Microsoft 接口定义语言 3.0](/uwp/midl-3/intro)。 在本节的其余部分中，我们将详细介绍 C# MainPage 类型。 我们会确定哪些部分需要在 C++/WinRT MainPage 类型的投影中（即，在其可调用或公共的图面中），以及哪些部分只能是其实现的一部分。 这一区别很重要，因为当我们开始编写 IDL（我们将在下一部分中执行此操作）时，我们在此处将仅定义可调用的部分。

同时实现 MainPage 类型的 C# 源代码文件包括：`MainPage.xaml`（我们将通过复制它来快速移植）、`MainPage.xaml.cs` 和 `SampleConfiguration.cs`。

在 C++/WinRT 版本中，我们以类似的方式将 MainPage 类型纳入源代码文件中。 我们将在 `MainPage.xaml.cs` 中采用逻辑，并将大部分转换为 `MainPage.h` 和 `MainPage.cpp`。 对于 `SampleConfiguration.cs` 中的逻辑，我们会将其转换为 `SampleConfiguration.h` 和 `SampleConfiguration.cpp`。

C# 通用 Windows 平台 (UWP) 应用程序中的类当然是 Windows 运行时类型。 但在 C++/WinRT 应用程序中创作类型时，可以选择将该类型设为 Windows 运行时类型还是常规 C++ 类/结构/枚举。

项目中的任何 XAML 页都必须是 Windows 运行时类型，因此 MainPage 必须是 Windows 运行时类型。 在 C++/WinRT 项目中，MainPage 已是 Windows 运行时类型，因此我们不需要更改这部分。 具体而言，它是运行时类。

- 若要更详细地了解是否应针对给定类型创作运行时类，请参阅主题：[使用 C++/WinRT 创作 API](./author-apis.md)。
- 在 C++/WinRT 中，运行时类的内部实现及其投影（公共）部分以两个不同的类的形式存在。 这些称为实现类型和投影类型 。 可以在上面提到的主题和[通过 C++/WinRT 使用 API](./consume-apis.md) 中了解详细信息。
- 有关运行时类和 IDL（`.idl` 文件）之间的连接的详细信息，可以参阅主题 [XAML 控件；绑定到 C++/WinRT 属性](./binding-property.md)。 本主题分步介绍创作新运行时类的过程，第一步是将新的 Midl 文件 (.idl) 项添加到项目。

对于 MainPage，在 C++/WinRT 项目中实际已有必要的 `MainPage.idl` 文件。 这是因为项目模板为我们创建了它。 但稍后在本演练中，我们会将更多 `.idl` 文件添加到项目。

我们很快就会看到一个列表，其中列出了需要添加到现有 `MainPage.idl` 文件的 IDL。 在此之前，我们要确定需要在 IDL 中添加哪些项以及不需要在其中添加哪些项。

为了确定 MainPage 的哪些成员需要在 `MainPage.idl` 中声明（以便它们成为 MainPage 运行时类的一部分），以及哪些成员可以是 MainPage 实现类型的成员，应创建 C# MainPage 类的成员列表   。 通过在 `MainPage.xaml.cs` 和 `SampleConfiguration.cs` 中查找，找到这些成员。

我们总共找到了十二个 `protected` 和 `private` 字段和方法。 我们发现以下 `public` 成员。

- 默认构造函数 `MainPage()`。
- 静态字段 Current 和 FEATURE_NAME 。
- 属性 IsClipboardContentChangedEnabled 和 Scenarios 。
- 方法 BuildClipboardFormatsOutputString、DisplayToast、EnableClipboardContentChangedNotifications 和 NotifyUser   。

那些 `public` 成员，是在 `MainPage.idl` 中声明的候选人。 接下来，让我们逐一检查，看它们是否需要成为 MainPage 运行时类的一部分，或者是否只需成为其实现的一部分。

- 默认构造函数 `MainPage()`。 对于 XAML 页面，通常在其 IDL 中声明默认构造函数。 这样，XAML UI 框架便可以激活该类型。
- 从各个方案 XAML 页面中，使用静态字段 Current 来访问应用程序的 MainPage 实例 。 由于 Current 未用于与 XAML 框架进行互操作（也没有跨编译单元使用），因此我们可以将其保留为仅为实现类型的成员。 在此类情况下，对自己的项目可以选择执行此操作。 但由于字段是投影类型的实例，因此在 IDL 中声明它是合乎逻辑的。 这就是我们在此要执行的操作（这样做还会使代码略微整洁一些）。
- 这种情况类似于静态 FEATURE_NAME 字段，可在 MainPage 类型中进行访问 。 同样，选择在 IDL 中声明它会使代码略微整洁。
- IsClipboardContentChangedEnabled 属性仅在 OtherScenarios 类中使用 。 因此，在移植过程中，我们将稍微简化操作，并使其成为 OtherScenarios 运行时类的专用字段。 这样一来，就不会传入 IDL。
- Scenarios 属性是 Scenario 类型（我们前面提到的类型）的对象集合 。 我们将在下一子部分讨论 Scenario，因此，让我们也暂时保留 Scenarios 属性 。
- BuildClipboardFormatsOutputString、DisplayToast 和 EnableClipboardContentChangedNotifications 方法是实用工具函数，与主页相比，这些函数与示例的常规状态更相关  。 因此，在移植过程中，我们会将这三个方法重构到名为 SampleState 的新实用工具类型（无需是 Windows 运行时类型）。 因此，这三种方法不会进入 IDL。
- 从各个方案 XAML 页面中，对从静态 Current 字段返回的 MainPage 实例调用 NotifyUser 方法 。 因为 Current 是投影类型的实例（如前面所述），所以需要在 IDL 中声明 NotifyUser 。 NotifyUser 采用 NotifyType 类型的参数 。 在下一子部分中，我们将对此进行讨论。

要对其进行数据绑定的任何成员也需要在 IDL 中进行声明（不管是使用 `{x:Bind}` 还是 `{Binding}`）。 有关详细信息，请参阅[数据绑定](../data-binding/index.md)。

当前进度：我们正在生成一个列表，其中列出哪些成员添加到 `MainPage.idl` 文件，以及哪些成员不添加到该文件。 但我们仍必须讨论 Scenarios 属性和 NotifyType 类型 。 接下来让我们开始讨论吧。

### <a name="idl-for-the-scenario-and-notifytype-types"></a>Scenario 和 NotifyType 类型的 IDL 

Scenario 类是在 `SampleConfiguration.cs` 中定义的。 我们需要决定如何将该类移植到 C++/WinRT。 默认情况下，我们可能会将其设为普通的 C++ `struct`。 但如果在二进制文件中使用 Scenario，或与 XAML 框架进行互操作，则需要在 IDL 中将其声明为 Windows 运行时类型。

研究 C# 源代码，我们发现此上下文中使用 Scenario。

```xaml
<ListBox x:Name="ScenarioControl" ... >
```

```csharp
var itemCollection = new List<Scenario>();
int i = 1;
foreach (Scenario s in scenarios)
{
    itemCollection.Add(new Scenario { Title = $"{i++}) {s.Title}", ClassType = s.ClassType });
}
ScenarioControl.ItemsSource = itemCollection;
```

Scenario 对象的集合将分配给 **ListBox**（这是项目控件）的 [ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) 属性 。 由于 Scenario 需要与 XAML 进行互操作，因此它需要是 Windows 运行时类型。 因此需要在 IDL 中定义它。 定义 IDL 中的 Scenario 类型会导致 C++/WinRT 生成系统在后台头文件（该文件的名称和位置对于本演练不重要）中为你生成 Scenario 的源代码定义 。

你应该记得 MainPage.Scenarios 是 Scenario 对象的集合，我们刚才说过这些对象需要位于 IDL 中 。 因此，也需要在 IDL 中声明 MainPage.Scenarios。

NotifyType 是在 C# 的 `MainPage.xaml.cs` 中声明的 `enum`。 由于我们将 NotifyType 传递到属于 MainPage 运行时类的方法，因此 NotifyType 也需要是 Windows 运行时类型，并且需要在 `MainPage.idl` 中进行定义  。

现在，让我们向 `MainPage.idl` 文件添加已决定在 IDL 中声明的 Mainpage 的新类型和新成员。 同时，我们将从 IDL 中删除 Visual Studio 项目模板提供的 Mainpage 的占位符成员。

因此，在 C++/WinRT 项目中，打开 `MainPage.idl`，并对其进行编辑，使其类似于下面的列表。 请注意，其中一项编辑是将命名空间名称从 Clipboard 更改为 SDKTemplate 。 如果需要，可以将 `MainPage.idl` 的全部内容替换为以下代码。 需要注意的另一个调整是，我们要将名称 Scenario::ClassType 更改为 Scenario::ClassName 。

```idl
// MainPage.idl
namespace SDKTemplate
{
    struct Scenario
    {
        String Title;
        Windows.UI.Xaml.Interop.TypeName ClassName;
    };

    enum NotifyType
    {
        StatusMessage,
        ErrorMessage
    };

    [default_interface]
    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();

        static MainPage Current{ get; };
        static String FEATURE_NAME{ get; };

        static Windows.Foundation.Collections.IVector<Scenario> scenarios{ get; };

        void NotifyUser(String strMessage, NotifyType type);
    };
}
```

> [!NOTE]
> 有关 C++/WinRT 项目中 `.idl` 文件内容的详细信息，请参阅 [Microsoft 接口定义语言 3.0](/uwp/midl-3/)。

对于自己的移植工作，你可能不希望也不需要像上述操作一样更改命名空间名称。 我们在此处执行此操作，只是因为我们正在移植的 C# 项目的默认命名空间是 SDKTemplate，而项目和程序集的名称为 Clipboard 。

但在本演练中继续进行迁移操作时，我们会将 Clipboard 命名空间名称的源代码中的每个匹配项更改为 SDKTemplate 。 在 C++/WinRT 项目属性中，还有一个地方会出现 Clipboard 命名空间名称，因此我们现在借此机会进行更改。

在 Visual Studio 中，对于 C++/WinRT 项目，将项目属性“公共属性”\>“C++/WinRT”\>“根命名空间”设置为值“SDKTemplate”  。

### <a name="save-the-idl-and-re-generate-stub-files"></a>保存 IDL 并重新生成存根文件

[XAML 控件；绑定到 C++/WinRT 属性](./binding-property.md)主题引入了存根文件的概念，并向你进行了操作演示。 我们还在本主题前面的内容中提到了存根，即 C++/WinRT 生成系统将 `.idl` 文件的内容转换为 Windows 元数据，然后名为 `cppwinrt.exe` 的工具从该元数据生成实现所基于的存根。

每次在 IDL 中添加、删除或更改内容时，生成系统都会更新这些存根文件中的存根实现。 因此，每次更改 IDL 和生成时，我们建议你查看这些存根文件，复制任何已更改的签名，并将其粘贴到项目中。 稍后我们将介绍如何执行此操作的更多具体信息和示例。 这样做的优点是，随时都可以准确无误地知道实现类型的形状及其方法的签名。

此时，我们暂时完成了对 `MainPage.idl` 文件的编辑，因此应立即进行保存。 此时，项目目前无法完成生成，但现在执行生成是一项有用的操作，因为它会为 MainPage 重新生成存根文件。

对于此 C++/WinRT 项目，这些存根文件将在 `\Clipboard\Clipboard\Generated Files\sources` 文件夹中生成。 在部分生成完成后，你会在此处找到它们（同样，生成不会完全成功。 但我们关注的&mdash;生成存根&mdash;步骤会成功）。 我们需要的文件是 `MainPage.h` 和 `MainPage.cpp`。

在这两个存根文件中，你将看到添加到 IDL 的 MainPage 的成员的存根实现（例如 Current 和 FEATURE_NAME）  。 你需要将这些存根实现复制到项目中已存在的 `MainPage.h` 和 `MainPage.cpp` 文件。 同时，就像我们对 IDL 进行的操作一样，我们将从这些现有的文件中删除 Visual Studio 项目模板提供的 Mainpage 的占位符成员（名为 MyProperty 的虚拟属性和名为 ClickHandler 的事件处理程序）  。

事实上，我们要保留的 MainPage 当前版本的唯一成员是构造函数。

从存根文件复制新成员、删除不需要的成员并更新命名空间后，项目中的 `MainPage.h` 和 `MainPage.cpp` 文件应如下面列出的代码所示。 请注意，有两个 MainPage 类型。 一个在 implementation 命名空间中，另一个在 factory_implementation 命名空间中。 我们对 factory_implementation 类型的唯一更改是向其命名空间添加了 SDKTemplate 。

```cppwinrt
// MainPage.h
#pragma once
#include "MainPage.g.h"

namespace winrt::SDKTemplate::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage();

        static SDKTemplate::MainPage Current();
        static hstring FEATURE_NAME();
        static Windows::Foundation::Collections::IVector<SDKTemplate::Scenario> scenarios();
        void NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type);
    };
}
namespace winrt::SDKTemplate::factory_implementation
{
    struct MainPage : MainPageT<MainPage, implementation::MainPage>
    {
    };
}
```

```cppwinrt
// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "MainPage.g.cpp"

namespace winrt::SDKTemplate::implementation
{
    MainPage::MainPage()
    {
        InitializeComponent();
    }
    SDKTemplate::MainPage MainPage::Current()
    {
        throw hresult_not_implemented();
    }
    hstring MainPage::FEATURE_NAME()
    {
        throw hresult_not_implemented();
    }
    Windows::Foundation::Collections::IVector<SDKTemplate::Scenario> MainPage::scenarios()
    {
        throw hresult_not_implemented();
    }
    void MainPage::NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type)
    {
        throw hresult_not_implemented();
    }
}
```

对于字符串，C# 将使用 System.String。 有关示例，请参阅 MainPage.NotifyUser 方法。 在 IDL 中，我们使用 String 声明一个字符串，当 `cppwinrt.exe` 工具为我们生成 C++/WinRT 代码时，它将使用 [winrt::hstring](/uw/cpp-ref-for-winrt/hstring) 类型 。 每当在 C# 代码中遇到字符串时，我们会将其移植到 winrt::hstring。 有关详细信息，请参阅 [C++/WinRT 中的字符串处理](./strings.md)。

有关方法签名中 `const&` 参数的说明，请参阅[参数传递](./concurrency.md#parameter-passing)。

### <a name="update-all-remaining-namespace-declarationsreferences-and-build"></a>更新所有剩余的命名空间声明/引用，然后生成

在生成 C++/WinRT 项目之前，请查找 Clipboard 命名空间的任何声明（和引用），然后将其更改为 SDKTemplate 。

- `MainPage.xaml` 和 `App.xaml`。 命名空间显示在 `x:Class` 和 `xmlns:local` 属性的值中。
- `App.idl`.
- `App.h`.
- `App.cpp`. 有两个 `using namespace` 指令（搜索子字符串 `using namespace Clipboard`）以及两个 MainPage 类型的限定（搜索 `Clipboard::MainPage`）。 这些都需要更改。

由于我们从 MainPage 中删除了事件处理程序，因此还需要进入 `MainPage.xaml`，并从标记中删除 Button 元素 。

保存所有文件。 清理解决方案（“生成” > “清理剪贴板”），然后进行生成 。 如果目前所做的更改有效，则生成将成功。

### <a name="implement-the-mainpage-members-that-we-declared-in-idl"></a>实现在 IDL 中声明的 MainPage 成员

#### <a name="the-constructor-current-and-feature_name"></a>构造函数 Current 和 FEATURE_NAME 

下面是我们需要移植的相关代码（来自 C# 项目）。

```xaml
<!-- MainPage.xaml -->
...
<TextBlock x:Name="SampleTitle" ... />
...
```

```csharp
// MainPage.xaml.cs
...
public sealed partial class MainPage : Page
{
    public static MainPage Current;

    public MainPage()
    {
        InitializeComponent();
        Current = this;
        SampleTitle.Text = FEATURE_NAME;
    }
...
}
...

// SampleConfiguration.cs
...
public partial class MainPage : Page
{
    public const string FEATURE_NAME = "Clipboard C# sample";
...
}
...
```

不久，我们将完全重复使用 `MainPage.xaml`（通过复制）。 现在，我们将使用适当的名称将 TextBlock 元素临时添加到 C++/WinRT 项目的 `MainPage.xaml` 中。

FEATURE_NAME 是 MainPage 的静态字段（在其行为中，C# `const` 字段实质上是静态的），在 `SampleConfiguration.cs` 中定义 。 对于 C++/WinRT（而不是（静态）字段），我们会将其设置为（静态）只读属性的 C++/WinRT 表达式。 表示属性 getter 的 C++/WinRT 方式是使用返回属性值的函数，不使用任何参数（访问器）。 因此，C# FEATURE_NAME 静态字段变成 C++/WinRT FEATURE_NAME 静态访问器函数（在这种情况下，返回字符串文本） 。

我们偶尔会在移植 C# 只读属性时执行相同的操作。 对于 C# 可写属性，表示属性 setter 的 C++/WinRT 方法是使用 `void` 函数，该函数将属性值作为参数（转变器）。 无论哪种情况，如果 C# 字段或属性是静态的，则 C++/WinRT 访问器和/或转变器也是静态的。

Current 是 MainPage 的静态（不是常量）字段 。 同样，我们将它设置为只读属性（的 C++/WinRT 表达式），并再次将其设置为静态。 其中 FEATURE_NAME 是常量，Current 不是常量 。 因此在 C++/WinRT 中，我们将需要一个支持字段，并且我们的访问器将返回该字段。 因此，在 C++/WinRT 项目中，我们将在 `MainPage.h` 中声明名为“current”的专用静态字段，在 `MainPage.cpp` 中定义/初始化 current（因为它具有静态存储持续时间），并且通过名为“Current”的公共静态访问器函数进行访问  。

构造函数本身执行几个赋值，这些赋值可以直接移植。

在 C++/WinRT 项目中，添加一个名称为 `SampleConfiguration.cpp` 的新的“Visual C++” > “代码” > “C++ 文件 (.cpp)”项目  。

编辑 `MainPage.xaml`、`MainPage.h`、`MainPage.cpp` 和 `SampleConfiguration.cpp` 以与以下列表相匹配。

```xaml
<!-- MainPage.xaml -->
...
<StackPanel ...>
    <TextBlock x:Name="SampleTitle" />
</StackPanel>
...
```

```cppwinrt
// MainPage.h
...
namespace winrt::SDKTemplate::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
...
        static SDKTemplate::MainPage Current() { return current; }
...
    private:
        static SDKTemplate::MainPage current;
...
    };
...
}

// MainPage.cpp
...
namespace winrt::SDKTemplate::implementation
{
    SDKTemplate::MainPage MainPage::current{ nullptr };
...
    MainPage::MainPage()
    {
        InitializeComponent();
        MainPage::current = *this;
        SampleTitle().Text(FEATURE_NAME());
    }
...
}

// SampleConfiguration.cpp
#include "pch.h"
#include "MainPage.h"

using namespace winrt;
using namespace SDKTemplate;

hstring implementation::MainPage::FEATURE_NAME()
{
    return L"Clipboard C++/WinRT Sample";
}
```

此外，请务必从 `MainPage.cpp` 中删除 MainPage::Current() 和 MainPage::FEATURE_NAME() 的现有函数主体，因为现在我们在其他位置定义这些方法 。

正如你所看到的，MainPage::current 声明为 SDKTemplate::MainPage 类型（投影类型） 。 不是 SDKTemplate::implementation::MainPage 类型（实现类型）。 投影类型是设计为在 XAML 互操作的项目中或跨二进制文件使用的类型。 实现类型是用于实现对投影类型公开的设施的类型。 由于 MainPage::current 的声明（在 `MainPage.h` 中）显示在实现命名空间 (winrt::SDKTemplate::implementation) 中，因此非限定的 MainPage 将引用实现类型  。 因此，我们使用 SDKTemplate:: 进行限定，以清楚地表明，我们希望 MainPage::current 成为投影类型 winrt::SDKTemplate::MainPage 的实例  。

在构造函数中，有一些与 `MainPage::current = *this;` 相关的点，需要对这些点进行说明。
- 当你在实现类型的成员内使用 `this` 指针时，`this` 指针当然是指向实现类型的指针。
- 若要将 `this` 指针转换为相应的投影类型，请将其取消引用。 如果从 IDL 生成实现类型（如下所示），则实现类型具有转换为投影类型的转换运算符。 这就是此处赋值有效的原因。

有关这些细节的详细信息，请参阅[实例化和返回实现类型和接口](./author-apis.md#instantiating-and-returning-implementation-types-and-interfaces)。

`SampleTitle().Text(FEATURE_NAME());` 也在构造函数中。 `SampleTitle()` 部分是对名为 SampleTitle 的简单访问器函数的调用，它将返回添加到 XAML 的 TextBlock 。 每当 `x:Name` XAML 元素，XAML 编译器就会生成一个以该元素命名的访问器。 `.Text(...)` 部分对 SampleTitle 访问器返回的 TextBlock 对象调用 Text 赋值函数  。 而且 `FEATURE_NAME()` 调用静态 MainPage::FEATURE_NAME 访问器函数以返回字符串文本。 总而言之，这一行代码将设置名为 SampleTitle 的 TextBlock 的 Text 属性 。

请注意，由于 Windows 运行时中的字符串很宽，因此若要移植字符串文本，请使用宽字符编码前缀 `L` 作为前缀。 因此，我们将（例如）“字符串文本”更改为 L“字符串文本”。 另请参阅[宽字符串文本](/cpp/cpp/string-and-character-literals-cpp#wide-string-literals)。

#### <a name="scenarios"></a>**方案**

下面是我们需要移植的相关 C# 代码。

```csharp
// MainPage.xaml.cs
...
public sealed partial class MainPage : Page
{
...
    public List<Scenario> Scenarios
    {
        get { return this.scenarios; }
    }
...
}
...

// SampleConfiguration.cs
...
public partial class MainPage : Page
{
...
    List<Scenario> scenarios = new List<Scenario>
    {
        new Scenario() { Title = "Copy and paste text", ClassType = typeof(CopyText) },
        new Scenario() { Title = "Copy and paste an image", ClassType = typeof(CopyImage) },
        new Scenario() { Title = "Copy and paste files", ClassType = typeof(CopyFiles) },
        new Scenario() { Title = "Other Clipboard operations", ClassType = typeof(OtherScenarios) }
    };
...
}
...
```

从我们以前的调查中，我们知道 Scenario 对象的此集合将显示在 ListBox 中 。 在 C++/WinRT 中，针对可分配给项目控件 ItemsSource 属性的此类集合类型有一些限制。 集合必须为矢量或可观察的矢量，而且其元素必须为以下其中之一。

- 运行时类或
- [IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)。

对于 IInspectable 情况，如果元素本身不是运行时类，则这些元素的类型需为可以装箱和取消装箱，并来自 [IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) 的类型 。 这意味着它们必须是 Windows 运行时类型（请参阅[将值装箱到 IInspectable 和对其取消装箱](./boxing.md)）。

对于此案例研究，我们不会将 Scenario 设置为运行时类。 但这仍是一个合理的选择。 在你自己的移植过程中，有时必须使用运行时类。 例如，如果需要使元素类型为“可观察”（请参阅 [XAML 控件；绑定到 C++/WinRT 属性](./binding-property.md)），或者，如果元素出于任何其他原因而需要使用方法，则该元素不仅仅是一组数据成员。

由于在本演练中，我们不打算对 Scenario 类型使用运行时类，因此，我们需要考虑装箱。 如果 Scenario 为常规 C++ `struct`，那么我们就无法对其进行装箱。 但如果将 Scenario 声明为 IDL 中的 `struct`，那就可以对其进行装箱。

我们可以选择提前对 Scenario 进行装箱，或等到我们将要分配到 ItemsSource 时，实时对它们进行装箱 。 下面是有关这两个选项的一些注意事项。

- 提前装箱。 对于此选项，数据成员是可分配给 UI 的 IInspectable 集合。 初始化时，将 Scenario 对象装箱到该数据成员中。 我们只需要该集合的一个副本，但每次需要读取其字段时，都必须对元素进行取消装箱。
- 实时装箱。 对于此选项，数据成员是 Scenario 的集合。 当需要分配给 UI 时，我们会将 Scenario 对象从数据成员装箱到 IInspectable 的新集合中 。 我们可以在不取消装箱的情况下读取数据成员中的元素字段，但需要集合的两个副本。

正如你所看到的，对于这样的小型集合，优点和缺点参半。 因此，对于此案例研究，我们将选择实时选项。

scenarios 成员是在 `SampleConfiguration.cs` 中定义和初始化的 MainPage 字段 。 Scenarios 是 MainPage 的只读属性，在 `MainPage.xaml.cs` 中定义（并且实现为仅返回 scenarios 字段）  。 我们将在 C++/WinRT 项目中执行类似操作；但我们会将这两个成员设为静态成员（因为我们只需要在应用程序中使用一个实例，因此，无需类实例即可访问这些成员）。 我们会将它们分别命名为 scenariosInner 和 scenarios 。 我们将在 `MainPage.h` 中声明 scenariosInner。 而且，因为它具有静态存储持续时间，我们将在 `.cpp` 文件（在本例中为 `SampleConfiguration.cpp`）中进行定义/初始化。

编辑 `MainPage.h` 和 `SampleConfiguration.cpp` 以与以下列表相匹配。

```cppwinrt
// MainPage.h
...
struct MainPage : MainPageT<MainPage>
{
...
    static Windows::Foundation::Collections::IVector<Scenario> scenarios() { return scenariosInner; }
...
private:
    static winrt::Windows::Foundation::Collections::IVector<Scenario> scenariosInner;
...
};

// SampleConfiguration.cpp
...
using namespace Windows::Foundation::Collections;
...
IVector<Scenario> implementation::MainPage::scenariosInner = winrt::single_threaded_observable_vector<Scenario>(
{
    Scenario{ L"Copy and paste text", xaml_typename<SDKTemplate::CopyText>() },
    Scenario{ L"Copy and paste an image", xaml_typename<SDKTemplate::CopyImage>() },
    Scenario{ L"Copy and paste files", xaml_typename<SDKTemplate::CopyFiles>() },
    Scenario{ L"History and roaming", xaml_typename<SDKTemplate::HistoryAndRoaming>() },
    Scenario{ L"Other Clipboard operations", xaml_typename<SDKTemplate::OtherScenarios>() },
});
```

此外，请务必从 `MainPage.cpp` 中删除 MainPage::scenarios() 的现有函数主体，因为我们现在正在头文件中定义该方法。

正如你所看到的，在 `SampleConfiguration.cpp` 中，我们通过调用名为 [winrt::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) 的 C++/WinRT 帮助程序函数，初始化静态数据成员 scenariosInner。 该函数为我们创建一个新的 Windows 运行时集合对象，并将其作为 [IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_) 接口返回。 由于在此示例中，集合不是“可观察的”（不需要，因为它不会在初始化后添加或删除元素），因此可以改为选择调用 [winrt::single_threaded_vector](/uwp/cpp-ref-for-winrt/single-threaded-vector)。 该函数返回作为 [IVector](/uwp/api/windows.foundation.collections.ivector_t_) 接口的集合。

有关集合以及对它们进行绑定的详细信息，请参阅 [XAML 项目控件；绑定到 C++/WinRT 集合](./binding-collection.md)以及[使用 C++/WinRT 的集合](./collections.md)。

刚添加的初始化代码引用项目中尚未使用的类型（例如 winrt::SDKTemplate::CopyText。 为了解决此情况，让我们继续，并向项目添加五个新的空白 XAML 页。

#### <a name="add-five-new-blank-xaml-pages"></a>添加五个新的空白 XAML 页

向项目添加新的“XAML” > “空白页(C++/WinRT)”项（确保它是“空白页(C++/WinRT)”项模板，而不是“空白页”）   。 将它命名为 `CopyText`。 新的 XAML 页在 SDKTemplate 命名空间中定义，这正是我们所需要的。

再重复上述步骤四次，并将 XAML 页命名为 `CopyImage`、`CopyFiles`、`HistoryAndRoaming` 和 `OtherScenarios`。

如果需要，现在可以重新生成。

#### <a name="notifyuser"></a>**NotifyUser**

在 C# 项目中，你将在 `MainPage.xaml.cs` 中找到 MainPage.NotifyUser 方法的实现。 MainPage.NotifyUser 依赖于 MainPage.UpdateStatus，而该方法反过来又依赖于尚未移植的 XAML 元素 。 现在，我们将只是在 C++/WinRT 项目中剔除 UpdateStatus 方法，稍后会进行移植。

下面是我们需要移植的相关 C# 代码。

```csharp
// MainPage.xaml.cs
...
public void NotifyUser(string strMessage, NotifyType type)
if (Dispatcher.HasThreadAccess)
{
    UpdateStatus(strMessage, type);
}
else
{
    var task = Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
}
private void UpdateStatus(string strMessage, NotifyType type) { ... }{
...
```

NotifyUser 使用 [Windows.UI.Core.CoreDispatcherPriority](/uwp/api/windows.ui.core.coredispatcherpriority) 枚举 。 在 C++/WinRT 中，每当需要使用 Windows 命名空间中的类型时，都需要包含相应的 C++/WinRT Windows 命名空间头文件（有关详细信息，请参阅 [C++/WinRT 入门](./get-started.md)）。 在这种情况下，你将在下面的代码清单中看到该标头是 `winrt/Windows.UI.Core.h`，并且我们会将其包含在 `pch.h` 中。

UpdateStatus 是专用的。 因此，我们会将其设置为针对 MainPage 实现类型的专用方法。 不应对运行时类调用 UpdateStatus，因此我们不会在 IDL 中声明它。

移植 MainPage.NotifyUser 并剔除 MainPage.UpdateStatus 后，这就是 C++/WinRT 项目中的内容 。 在此代码清单后，我们将查看一些详细信息。

```cppwinrt
// pch.h
...
#include <winrt/Windows.UI.Core.h>
...

// MainPage.h
...
struct MainPage : MainPageT<MainPage>
{
...
    void NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type);
...
private:
    void UpdateStatus(hstring const& strMessage, SDKTemplate::NotifyType const& type);
...
};

// MainPage.cpp
...
void MainPage::NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type)
{
    if (Dispatcher().HasThreadAccess())
    {
        UpdateStatus(strMessage, type);
    }
    else
    {
        Dispatcher().RunAsync(Windows::UI::Core::CoreDispatcherPriority::Normal, [strMessage, type, this]()
            {
                UpdateStatus(strMessage, type);
            });
    }
}
void MainPage::UpdateStatus(hstring const& strMessage, SDKTemplate::NotifyType const& type)
{
    throw hresult_not_implemented();
}
...
```

在 C# 中，可以使用点表示法“点入到”嵌套属性中。 因此，C# MainPage 类型可使用语法 `Dispatcher` 访问其自己的 Dispatcher 属性 。 C# 可以通过语法（比如 `Dispatcher.HasThreadAccess`）进一步点入该值。 在 C++/WinRT 中，属性是作为访问器函数实现的，因此，语法的不同之处仅在于为每个函数调用添加括号。

|C#|C++/WinRT|
|-|-|
|`Dispatcher.HasThreadAccess`|`Dispatcher().HasThreadAccess()`|

当 NotifyUser 的 C# 版本调用 [CoreDispatcher.RunAsync](/uwp/api/windows.ui.core.coredispatcher.runasync) 时，会将异步回调委托实现为 lambda 函数 。 C++/WinRT 版本具有相同的功能，但语法略有不同。 在 C++/WinRT 中，捕获要使用的两个参数以及 `this` 指针（因为我们将调用成员函数）。 有关将委托实现为 lambda 的详细信息和代码示例，请参阅[在 C++/WinRT 中使用委托处理事件](./handle-events.md)主题。 此外，在这种特定情况下，我们可以忽略 `var task =` 部分。 我们不会等待返回的异步对象，因此无需存储它。 

### <a name="implement-the-remaining-mainpage-members"></a>实现其余的 MainPage 成员

接下来，我们将完整列出 MainPage 的成员（跨 `MainPage.xaml.cs` 和 `SampleConfiguration.cs` 实现），以便我们可以看到目前移植了哪些成员，以及哪些成员尚未移植。

|成员|访问|状态|
|-|-|-|
|MainPage 构造函数|`public`|已移植|
|Current 属性|`public`|已移植|
|FEATURE_NAME 属性|`public`|已移植|
|IsClipboardContentChangedEnabled 属性|`public`|未开始|
|Scenarios 属性|`public`|已移植|
|BuildClipboardFormatsOutputString 方法|`public`|未开始|
|DisplayToast 方法|`public`|未开始|
|EnableClipboardContentChangedNotifications 方法|`public`|未开始|
|NotifyUser 方法|`public`|已移植|
|OnNavigatedTo 方法|`protected`|未开始|
|isApplicationWindowActive 字段|`private`|未开始|
|needToPrintClipboardFormat 字段|`private`|未开始|
|scenarios 字段|`private`|已移植|
|Button_Click 方法|`private`|未开始|
|DisplayChangedFormats 方法|`private`|未开始|
|Footer_Click 方法|`private`|未开始|
|HandleClipboardChanged 方法|`private`|未开始|
|OnClipboardChanged 方法|`private`|未开始|
|OnWindowActivated 方法|`private`|未开始|
|ScenarioControl_SelectionChanged 方法|`private`|未开始|
|UpdateStatus 方法|`private`|已剔除|

然后在接下来的几个子部分，我们将讨论目前尚未移植的成员。

> [!NOTE]
> 有时，我们将在源代码中遇到 XAML 标记（在 `MainPage.xaml` 中）中 UI 元素的引用。 对于这些引用，我们将通过向 XAML 添加简单的占位符元素暂时绕过这些问题。 这样一来，项目将在每个子部分后继续生成。 另一种方法是通过将 `MainPage.xaml` 的整个内容从 C# 项目复制到 C++/WinRT 项目来解析引用。 但如果这样做，则可能需要很长一段时间才能暂时停止并再次生成（因此可能会隐藏在此过程中所产生的拼写错误或其他错误）。
>
> 完成 MainPage 类的强制性代码的移植后，需要复制 XAML 文件的内容，确保项目仍会生成。

#### <a name="isclipboardcontentchangedenabled"></a>**IsClipboardContentChangedEnabled**

这是默认为 `false` 的 get-set C# 属性。 它是 MainPage 的成员，在 `SampleConfiguration.cs` 中定义。

对于 C++/WinRT，我们将需要一个访问器函数、一个赋值函数和一个字段形式的支持数据成员。 由于 IsClipboardContentChangedEnabled 表示示例中某个方案的状态，而不是 MainPage 本身的状态，因此，我们将针对名为 SampleState 的新实用工具类型创建新成员  。 我们将在 `SampleConfiguration.cpp` 源代码文件中实现该操作，并将成员设置为 `static`（因为我们只需要在应用程序中使用一个实例，因此，无需类实例即可访问这些成员）。

若要在 C++/WinRT 项目中附带 `SampleConfiguration.cpp`，请添加一个名称为 `SampleConfiguration.h` 的新的“Visual C++” > “代码” > “头文件 (.h)”项目  。 编辑 `SampleConfiguration.h` 和 `SampleConfiguration.cpp` 以与以下列表相匹配。

```cppwinrt
// SampleConfiguration.h
#pragma once 
#include "pch.h"

namespace winrt::SDKTemplate
{
    struct SampleState
    {
        static bool IsClipboardContentChangedEnabled();
        static void IsClipboardContentChangedEnabled(bool checked);
    private:
        static bool isClipboardContentChangedEnabled;
    };
}

// SampleConfiguration.cpp
...
#include "SampleConfiguration.h"
...
bool SampleState::isClipboardContentChangedEnabled = false;
...
bool SampleState::IsClipboardContentChangedEnabled()
{
    return isClipboardContentChangedEnabled;
}
void SampleState::IsClipboardContentChangedEnabled(bool checked)
{
    if (isClipboardContentChangedEnabled != checked)
    {
        isClipboardContentChangedEnabled = checked;
    }
}
```

同样，必须在应用程序中定义一次带有 `static` 存储的字段（如 SampleState::isClipboardContentChangedEnabled），并且 `.cpp` 文件是一个好地方（在本例中为 `SampleConfiguration.cpp`）。

#### <a name="buildclipboardformatsoutputstring"></a>**BuildClipboardFormatsOutputString**

此方法是 MainPage 的公共成员，并且在 `SampleConfiguration.cs` 中进行定义。

```csharp
// SampleConfiguration.cs
...
public string BuildClipboardFormatsOutputString()
{
    DataPackageView clipboardContent = Windows.ApplicationModel.DataTransfer.Clipboard.GetContent();
    StringBuilder output = new StringBuilder();

    if (clipboardContent != null && clipboardContent.AvailableFormats.Count > 0)
    {
        output.Append("Available formats in the clipboard:");
        foreach (var format in clipboardContent.AvailableFormats)
        {
            output.Append(Environment.NewLine + " * " + format);
        }
    }
    else
    {
        output.Append("The clipboard is empty");
    }
    return output.ToString();
}
...
```

在 C++/WinRT 中，我们将 BuildClipboardFormatsOutputString 设置为 SampleState 的公共静态方法 。 我们可以将其设置为 `static`，因为它不会访问任何实例成员。

若要在 C++/WinRT 中使用 Clipboard 和 DataPackageView 类型，需要包含 C++/WinRT Windows 命名空间头文件 `winrt/Windows.ApplicationModel.DataTransfer.h` 。

在 C# 中，DataPackageView.AvailableFormats 属性是 IReadOnlyList，因此我们可以访问它的 Count 属性  。 在 C++/WinRT 中，DataPackageView::AvailableFormats 访问器函数返回 IVectorView，该函数具有可调用的 Size 访问器函数  。

若要移植 C# System.Text.StringBuilder 类型的使用，我们将使用标准 C++类型 [std::wostringstream](/cpp/standard-library/sstream-typedefs#wostringstream) 。 该类型是宽字符串的输出流（若要使用它，则需要包含 `sstream` 头文件）。 不要像对 StringBuilder 那样使用 Append 方法，应该对输出流（如 wostringstream）使用[插入运算符](/cpp/standard-library/using-insertion-operators-and-controlling-format) (`<<`)  。 有关详细信息，请参阅 [iostream 编程](/cpp/standard-library/iostream-programming)和[设置 C++/WinRT 字符串格式](./strings.md#formatting-strings)。

C# 代码使用 `new` 关键字构造 StringBuilder。 在 C# 中，对象默认为引用类型，使用 `new` 在堆上声明。 在新式标准 C++ 中，对象默认为值类型，在堆栈上声明（不使用 `new`）。 这样，我们就可以将 `StringBuilder output = new StringBuilder();` 移植到 C++/WinRT，仅作为 `std::wostringstream output;`。

C# `var` 关键字要求编译器对类型进行推断。 在 C++/WinRT 中，我们将 `var` 移植到 `auto`。 但在 C++/WinRT 中，有时候为了避免复制，需要使用对某个推断（或推导）类型的引用，并且使用 `auto&` 来表示对推导类型的左值引用。 在另外一些时候，需要使用可以正确绑定（不管是使用左值还是右值进行初始化）的特殊类型的引用。  我们使用 `auto&&` 来表示它。 这就是我们看到的在以下已移植代码的 `for` 循环中使用的形式。 有关左值和右值的简介，请参阅[值类别以及对它们的引用](./cpp-value-categories.md)。 

编辑 `pch.h`、`SampleConfiguration.h` 和 `SampleConfiguration.cpp` 以与以下列表相匹配。

```cppwinrt
// pch.h
...
#include <sstream>
#include "winrt/Windows.ApplicationModel.DataTransfer.h"
...

// SampleConfiguration.h
...
static hstring BuildClipboardFormatsOutputString();
...

// SampleConfiguration.cpp
...
using namespace Windows::ApplicationModel::DataTransfer;
...
hstring SampleState::BuildClipboardFormatsOutputString()
{
    DataPackageView clipboardContent{ Clipboard::GetContent() };
    std::wostringstream output;

    if (clipboardContent && clipboardContent.AvailableFormats().Size() > 0)
    {
        output << L"Available formats in the clipboard:";
        for (auto&& format : clipboardContent.AvailableFormats())
        {
            output << std::endl << L" * " << std::wstring_view(format);
        }
    }
    else
    {
        output << L"The clipboard is empty";
    }

    return hstring{ output.str() };
}
```

> [!NOTE]
> 代码 `DataPackageView clipboardContent{ Clipboard::GetContent() };` 所在行中的语法使用现代标准 C++ 的一项称为“统一初始化”的功能，特征性地使用花括号而不是 `=` 符号。 该语法清楚地表明正在进行的是初始化而不是赋值。 如果你偏好那种看起来像赋值（但实际上不是）的语法形式，则可将上面的语法替换为等效的 `DataPackageView clipboardContent = Clipboard::GetContent();`。 不过，最好是这两种表示初始化的方式你都习惯，因为你可能会发现，这两种方式在你遇到的代码中的使用频率都很高。

#### <a name="displaytoast"></a>**DisplayToast**

DisplayToast 是 C# MainPage 类的公共静态方法，你会发现它在 `SampleConfiguration.cs` 中进行定义 。 在 C++/WinRT 中，我们将其设置为 SampleState 的公共静态方法。

我们已了解与移植此方法相关的大部分细节和技术。 要注意的一个新项是，将 C# 逐字字符串文本 (`@`) 移植到标准 C++ [原始字符串文本](/cpp/cpp/string-and-character-literals-cpp#raw-string-literals-c11) (`LR`)。

此外，当你在 C++/WinRT 中引用 [ToastNotification](/uwp/api/windows.ui.notifications.toastnotification) 和 [XmlDocument](/uwp/api/windows.data.xml.dom.xmldocument) 类型时，可以按命名空间名称限定它们，也可以编辑 `SampleConfiguration.cpp` 并添加 `using namespace` 指令，如以下示例 。

```cppwinrt
using namespace Windows::UI::Notifications;
```

当引用 [XmlDocument](/uwp/api/windows.data.xml.dom.xmldocument) 类型以及引用任何其他 Windows 运行时类型时，可以选择相同的选项。

除了这些项目外，只需遵循与之前相同的指导，即可完成以下步骤。

- 在 `SampleConfiguration.h` 中声明方法，并在 `SampleConfiguration.cpp` 中进行定义。
- 编辑 `pch.h` 以包含任何必需的 C++/WinRT Windows 命名空间头文件。
- 在堆栈上构造 C++/WinRT 对象，而不是在堆上构造。
- 将对属性 get 访问器的调用替换为函数调用语法 (`()`)。

编译器/链接器错误的一个常见原因是忘记包含所需的 C++/WinRT Windows 命名空间头文件。 有关一个可能的错误的详细信息，请参阅[为什么链接器出现“LNK2019:未解析的外部符号”错误？](./faq.yml#why-is-the-linker-giving-me-a--lnk2019--unresolved-external-symbol--error-)。

如果想要按照本演练自行移植 DisplayToast，则可以将结果与下载的 [Clipboard 示例](/samples/microsoft/windows-universal-samples/clipboard/)源代码的 ZIP 的 C++/WinRT 版本中的代码进行比较。

#### <a name="enableclipboardcontentchangednotifications"></a>**EnableClipboardContentChangedNotifications**

EnableClipboardContentChangedNotifications 是 C# MainPage 类的公共静态方法，它在 `SampleConfiguration.cs` 中定义 。

```csharp
// SampleConfiguration.cs
...
public bool EnableClipboardContentChangedNotifications(bool enable)
{
    if (IsClipboardContentChangedEnabled == enable)
    {
        return false;
    }

    IsClipboardContentChangedEnabled = enable;
    if (enable)
    {
        Clipboard.ContentChanged += OnClipboardChanged;
        Window.Current.Activated += OnWindowActivated;
    }
    else
    {
        Clipboard.ContentChanged -= OnClipboardChanged;
        Window.Current.Activated -= OnWindowActivated;
    }
    return true;
}
...
private void OnClipboardChanged(object sender, object e) { ... }
private void OnWindowActivated(object sender, WindowActivatedEventArgs e) { ... }
...
```

在 C++/WinRT 中，我们将其设置为 SampleState 的公共静态方法。

在 C# 中，可以使用 `+=` 和 `-=` 运算符语法来注册和撤销事件处理委托。 在 C++/WinRT 中，可以使用多个语法选项注册/撤销委托，如[在 C++/WinRT 中使用委托处理事件](./handle-events.md)中所述。 但通常是通过调用以事件命名的一对函数进行注册和撤销操作。 若要注册，请将委托传递给注册函数，然后检索返回的撤销令牌 ([**winrt::event_token**](/uwp/cpp-ref-for-winrt/event-token))。 若要撤销，请将该令牌传递给撤销函数。 在这种情况下，处理程序是静态的，而且（如下面的代码清单中所示）函数调用语法非常简单。

在 C# 中，实际上会在幕后使用类似的令牌。 但是，此语言将该详细信息设为隐式信息。 C++/WinRT 将其设为显式信息。

object 类型将显示在 C# 事件处理程序签名中。 在 C# 语言中，object 是 .NET [System.Object](/dotnet/api/system.object) 类型的[别名](/dotnet/csharp/language-reference/builtin-types/reference-types) 。 C++/WinRT 中的等效项是 [winrt::Windows::Foundation::IInspectable](/windows/win32/api/inspectable/nn-inspectable-iinspectable)。 因此，你会在 C++/WinRT 事件处理程序中看到 IInspectable。

编辑 `SampleConfiguration.h` 和 `SampleConfiguration.cpp` 以与以下列表相匹配。

```cppwinrt
// SampleConfiguration.h
...
private:
    static event_token clipboardContentChangedToken;
    static event_token activatedToken;
    static void OnClipboardChanged(Windows::Foundation::IInspectable const& sender, Windows::Foundation::IInspectable const& e);
    static void OnWindowActivated(Windows::Foundation::IInspectable const& sender, Windows::UI::Core::WindowActivatedEventArgs const& e);
...

// SampleConfiguration.cpp
...
using namespace Windows::Foundation;
using namespace Windows::UI::Core;
using namespace Windows::UI::Xaml;
...
event_token SampleState::clipboardContentChangedToken;
event_token SampleState::activatedToken;
...
bool SampleState::EnableClipboardContentChangedNotifications(bool enable)
{
    if (isClipboardContentChangedEnabled == enable)
    {
        return false;
    }

    IsClipboardContentChangedEnabled(enable);
    if (enable)
    {
        clipboardContentChangedToken = Clipboard::ContentChanged(OnClipboardChanged);
        activatedToken = Window::Current().Activated(OnWindowActivated);
    }
    else
    {
        Clipboard::ContentChanged(clipboardContentChangedToken);
        Window::Current().Activated(activatedToken);
    }
    return true;
}
void SampleState::OnClipboardChanged(IInspectable const&, IInspectable const&){}
void SampleState::OnWindowActivated(IInspectable const&, WindowActivatedEventArgs const& e){}
```

暂时将事件处理委托本身（OnClipboardChanged 和 OnWindowActivated）保留为存根 。 它们已在要移植的成员列表中，我们将在后面的子部分中介绍它们。

#### <a name="onnavigatedto"></a>**OnNavigatedTo**

OnNavigatedTo 是 C# MainPage 类的受保护的方法，并且在 `MainPage.xaml.cs` 中进行定义 。 示例如下所示，另外还有它所引用的 XAML ListBox。

```xaml
<!-- MainPage.xaml -->
...
<ListBox x:Name="ScenarioControl" ... />
...
```

```csharp
// MainPage.xaml.cs
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    // Populate the scenario list from the SampleConfiguration.cs file
    var itemCollection = new List<Scenario>();
    int i = 1;
    foreach (Scenario s in scenarios)
    {
        itemCollection.Add(new Scenario { Title = $"{i++}) {s.Title}", ClassType = s.ClassType });
    }
    ScenarioControl.ItemsSource = itemCollection;

    if (Window.Current.Bounds.Width < 640)
    {
        ScenarioControl.SelectedIndex = -1;
    }
    else
    {
        ScenarioControl.SelectedIndex = 0;
    }
}
```

这是一种重要且有趣的方法，因为我们在其中将 Scenario 对象的集合分配给 UI。 此 C# 代码将生成 Scenario 对象的 [System.Collections.Generic.List](/dotnet/api/system.collections.generic.list-1)，并将其分配给 ListBox（它是项目控件）的 [ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) 属性   。 在 C# 中，我们使用[字符串内插](/dotnet/csharp/language-reference/tokens/interpolated)为每个 Scenario 对象生成标题（请注意 `$` 特殊字符的使用）。

在 C++/WinRT 中，我们将 OnNavigatedTo 设置为 MainPage的公共方法 。 接下来，我们会将一个存根 ListBox 元素添加到 XAML 中，使生成成功。 在代码清单后，我们将查看一些详细信息。

```xaml
<!-- MainPage.xaml -->
...
<StackPanel ...>
    ...
    <ListBox x:Name="ScenarioControl" />
</StackPanel>
...
```

```cppwinrt
// MainPage.h
...
void OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e);
...

// MainPage.cpp
...
using namespace winrt::Windows::UI::Xaml;
using namespace winrt::Windows::UI::Xaml::Navigation;
...
void MainPage::OnNavigatedTo(NavigationEventArgs const& /* e */)
{
    auto itemCollection = winrt::single_threaded_observable_vector<IInspectable>();
    int i = 1;
    for (auto s : MainPage::scenarios())
    {
        s.Title = winrt::to_hstring(i++) + L") " + s.Title;
        itemCollection.Append(winrt::box_value(s));
    }
    ScenarioControl().ItemsSource(itemCollection);

    if (Window::Current().Bounds().Width < 640)
    {
        ScenarioControl().SelectedIndex(-1);
    }
    else
    {
        ScenarioControl().SelectedIndex(0);
    }
}
...
```

同样，我们将调用 [winrt::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) 函数，但这一次是创建 [IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) 的集合。 这就是对 Scenario 对象执行实时装箱的这一决定的一部分。

这里，我们将 [**to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring) 函数和 **winrt::hstring** 的 [串联运算符](/uwp/cpp-ref-for-winrt/hstring#operator-concatenation-operator)组合使用，而不是像 C# 那样使用 [字符串内插](/dotnet/csharp/language-reference/tokens/interpolated)。

#### <a name="isapplicationwindowactive"></a>**isApplicationWindowActive**

在 C# 中，isApplicationWindowActive 是一个简单的专用 `bool` 字段，属于 MainPage 类，并且在 `SampleConfiguration.cs` 中进行定义 。 默认为 `false`。 在 C++/WinRT 中，我们在 `SampleConfiguration.h` 和 `SampleConfiguration.cpp` 文件中将其设为 SampleState 的公共静态字段（原因已描述），并且默认值相同。

我们已了解如何对静态字段进行声明、定义和初始化。 若要复习，请回顾我们对 isClipboardContentChangedEnabled 字段执行的操作，对 isApplicationWindowActive 亦是如此 。

#### <a name="needtoprintclipboardformat"></a>**needToPrintClipboardFormat**

与 isApplicationWindowActive 的模式相同（请参阅上一标题）。

#### <a name="button_click"></a>**Button_Click**

Button_Click 是 C# MainPage 类的专用（事件处理）方法，并且在 `MainPage.xaml.cs` 中进行定义 。 示例如下所示，其中还包含该方法引用的 XAML SplitView，以及用于注册该方法的 ToggleButton。

```xaml
<!-- MainPage.xaml -->
...
<SplitView x:Name="Splitter" ... />
...
<ToggleButton Click="Button_Click" .../>
...
```

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    Splitter.IsPaneOpen = !Splitter.IsPaneOpen;
}
```

下面是已移植到 C++/WinRT 的等效项。 请注意，在 C++/WinRT 版本中，事件处理程序是 `public`（如你所见，可在 `private:` 声明之前进行声明）。 这是因为在 XAML 标记中注册的事件处理程序（例如此处理程序）需要在 C++/WinRT 中设置为 `public`，以便 XAML 标记访问它。 如果在强制性代码中注册事件处理程序（如我们在此前的 **MainPage::EnableClipboardContentChangedNotifications** 中所做的那样），则不需将事件处理程序设置为 `public`。

```xaml
<!-- MainPage.xaml -->
...
<StackPanel ...>
    ...
    <SplitView x:Name="Splitter" />
</StackPanel>
...
```

```cppwinrt
// MainPage.h
...
    void Button_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& e);
private:
...

// MainPage.cpp
void MainPage::Button_Click(Windows::Foundation::IInspectable const& /* sender */, Windows::UI::Xaml::RoutedEventArgs const& /* e */)
{
    Splitter().IsPaneOpen(!Splitter().IsPaneOpen());
}
```

#### <a name="displaychangedformats"></a>**DisplayChangedFormats**

在 C# 中，DisplayChangedFormats 是属于 MainPage 类的专用方法，在 `SampleConfiguration.cs` 中进行定义 。

```csharp
private void DisplayChangedFormats()
{
    string output = "Clipboard content has changed!" + Environment.NewLine;
    output += BuildClipboardFormatsOutputString();
    NotifyUser(output, NotifyType.StatusMessage);
}
```

在 C++/WinRT 中，我们在 `SampleConfiguration.h` 和 `SampleConfiguration.cpp` 文件中将其设置为 SampleState 的专用静态字段（它不会访问任何实例成员）。 此方法的 C# 代码不使用 System.Text.StringBuilder；但它可以进行足够的字符串格式设置，对于 C++/WinRT 版本，这是使用 std::wostringstream 的另一个好地方 。

我们将在输出流中插入标准 C++ `std::endl`（一个换行符），而不是在 C# 代码中使用静态 [System.Environment.NewLine](/dotnet/api/system.environment.newline) 属性。

```cppwinrt
// SampleConfiguration.h
...
private:
    static void DisplayChangedFormats();
...

// SampleConfiguration.cpp
void SampleState::DisplayChangedFormats()
{
    std::wostringstream output;
    output << L"Clipboard content has changed!" << std::endl;
    output << BuildClipboardFormatsOutputString().c_str();
    MainPage::Current().NotifyUser(output.str(), NotifyType::StatusMessage);
}
```

上面的 C++/WinRT 版本的设计效率有点低。 首先，我们创建一个 std::wostringstream。 但我们也会调用 BuildClipboardFormatsOutputString 方法（前面已移植）。 该方法将创建自己的 std::wostringstream。 它会将流转换为 winrt::hstring 并返回。 我们调用 [hstring::c_str](/uwp/cpp-ref-for-winrt/hstring#hstringc_str-function) 函数将返回的 hstring 返回到 C 样式字符串，然后将其插入到流中 。 更高效的方式是只创建一个 std::wostringstream 并进行传递（引用），以便方法可以直接将字符串插入其中。

这就是我们在 [Clipboard 示例](/samples/microsoft/windows-universal-samples/clipboard/)源代码（在你下载的 ZIP 中）的 C++/WinRT 版本中所执行的操作。 在该源代码中，有一个名为 SampleState::AddClipboardFormatsOutputString 的新专用静态方法，该方法采用对输出流的引用并对其进行操作。 然后，重构 SampleState::DisplayChangedFormats 和 SampleState::BuildClipboardFormatsOutputString 方法，以调用该新方法 。 它在功能上等效于本主题中的代码清单，但效率更高。

#### <a name="footer_click"></a>**Footer_Click**

Footer_Click 是属于 C# MainPage 类的异步事件处理程序，在 `MainPage.xaml.cs` 中进行定义 。 下面的代码清单在功能上等效于你下载的源代码中的方法。 但在这里，我们将其从一行解压缩到了四行，以便更轻松地查看它的功能，进而找到它的移植方式。

```csharp
async void Footer_Click(object sender, RoutedEventArgs e)
{
    var hyperlinkButton = (HyperlinkButton)sender;
    string tagUrl = hyperlinkButton.Tag.ToString();
    Uri uri = new Uri(tagUrl);
    await Windows.System.Launcher.LaunchUriAsync(uri);
}
```

技术上讲，方法是异步的，但它不会在 `await` 后执行任何操作，因此不需要 `await`（也不需要 `async` 关键字）。 它可能会使用它们，以避免在 Visual Studio 中使用 IntelliSense 消息。

等效的 C++/WinRT 方法也将是异步的（因为它调用 [Launcher.LaunchUriAsync](/uwp/api/windows.system.launcher.launchuriasync)）。 但不需要 `co_await`，也不需要返回异步对象。 有关 `co_await` 和异步对象的信息，请参阅[利用 C++/WinRT 实现的并发和异步操作](./concurrency.md)。

现在，让我们讨论一下该方法的作用。 由于这是 HyperlinkButton 的 Click 事件的事件处理程序，因此名为 sender 的对象实际上是 HyperlinkButton 。 因此，类型转换是安全的（也可以将此转换表示为 `sender as HyperlinkButton`）。 接下来，我们将检索 Tag 属性的值（如果查看 C# 项目中的 XAML 标记，你会看到此项设置为表示 web url 的字符串）。 尽管 FrameworkElement.Tag 属性（HyperlinkButton 是 FrameworkElement）的类型为 object，但在 C# 中，我们可以使用 [Object.ToString](/dotnet/api/system.object.tostring) 对其进行字符串化    。 在生成的字符串中，构造 Uri 对象。 最后（通过 Shell 的帮助），启动一个浏览器并导航到该 url。

下面是移植到 C++/WinRT 的方法（同样，为清楚起见进行了扩展），之后是详细信息的说明。

```cppwinrt
// pch.h
...
#include "winrt/Windows.System.h"
...

// MainPage.h
...
    void Footer_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& e);
private:
...

// MainPage.cpp
...
using namespace winrt::Windows::Foundation;
using namespace winrt::Windows::UI::Xaml::Controls;
...
void MainPage::Footer_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const&)
{
    auto hyperlinkButton{ sender.as<HyperlinkButton>() };
    hstring tagUrl{ winrt::unbox_value<hstring>(hyperlinkButton.Tag()) };
    Uri uri{ tagUrl };
    Windows::System::Launcher::LaunchUriAsync(uri);
}
```

与往常一样，我们将事件处理程序设置为 `public`。 对 sender 对象使用 [as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) 函数以将其转换为 HyperlinkButton。 在 C++/WinRT 中，Tag 属性为 [IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)（等效于 [Object](/dotnet/api/system.object)）  。 但 IInspectable 上没有 Tostring 。 我们必须将 IInspectable 取消装箱为标量值（在本例中为字符串）。 同样，有关装箱和取消装箱的详细信息，请参阅[将值装箱到 IInspectable 和对其取消装箱](./boxing.md)。

最后两行重复我们之前看到过的移植模式，它们几乎与 C# 版本相对应。

#### <a name="handleclipboardchanged"></a>**HandleClipboardChanged**

移植此方法并不涉及任何新内容。 你可以在下载的 [Clipboard 示例](/samples/microsoft/windows-universal-samples/clipboard/)源代码的 ZIP 中比较 C# 和 C++/WinRT 版本。

#### <a name="onclipboardchanged-and-onwindowactivated"></a>**OnClipboardChanged** 和 **OnWindowActivated**

目前只有这两个事件处理程序的空存根。 但移植它们非常简单，无需讨论新内容。

#### <a name="scenariocontrol_selectionchanged"></a>**ScenarioControl_SelectionChanged**

这是属于 C# MainPage 类的另一个专用事件处理程序，并在 `MainPage.xaml.cs` 中进行定义。 在 C++/WinRT 中，将其设置为公共，并在 `MainPage.h` 和 `MainPage.cpp` 中实现。

对于此方法，我们需要 MainPage::navigating，它是一个初始化为 `false` 的专用布尔字段。 需要在 `MainPage.xaml` 中有一个名为 ScenarioFrame 的 Frame。 但除这些细节外，移植此方法无需任何新技术。

#### <a name="updatestatus"></a>**UpdateStatus**

目前只有 MainPage.UpdateStatus 的存根。 再次移植其实现涉及的很多是旧技术。 需要注意的一点是，在 C# 中，我们可以将 string 与 String.Empty 进行比较，而在 C++/WinRT 中，我们改为调用 [winrt::hstring::empty](/uwp/cpp-ref-for-winrt/hstring#hstringempty-function) 函数  。 另一种情况是 `nullptr` 是 C# 的 `null` 的标准 C++ 等效项。

你可以通过已介绍的技术来执行其余的移植。 下面列出了在此方法的移植版本编译之前需要执行的操作类型。

- 对于 `MainPage.xaml`，请添加名为 StatusBorder 的 Border。
- 对于 `MainPage.xaml`，请添加名为 StatusBlock 的 TextBlock。
- 对于 `MainPage.xaml`，请添加名为 StatusPanel 的 StackPanel。
- 对于 `pch.h`，请添加 `#include "winrt/Windows.UI.Xaml.Media.h"`。
- 对于 `pch.h`，请添加 `#include "winrt/Windows.UI.Xaml.Automation.Peers.h"`。
- 对于 `MainPage.cpp`，请添加 `using namespace winrt::Windows::UI::Xaml::Media;`。
- 对于 `MainPage.cpp`，请添加 `using namespace winrt::Windows::UI::Xaml::Automation::Peers;`。

### <a name="copy-the-xaml-and-styles-necessary-to-finish-up-porting-mainpage"></a>复制完成移植 MainPage所需的 XAML 和样式

对于 XAML，理想情况是可以在 C# 和 C++/WinRT 项目中使用相同的 XAML 标记。 Clipboard 示例是其中一种情况。

在其 `Styles.xaml` 文件中，Clipboard 示例具有一个 XAML ResourceDictionary 样式，该样式应用于应用程序 UI 上的按钮、菜单和其他 UI 元素。 `Styles.xaml` 页面将合并到 `App.xaml` 中。 接下来是 UI 的标准 `MainPage.xaml` 起点，我们已经对其进行了简要说明。 现在，在项目的 C++/WinRT 版本中，我们可以重复使用这三个不变的 `.xaml` 文件。

与资产文件一样，你可以选择从应用程序的多个版本中引用相同的共享 XAML 文件。 在本演练中，为了简单起见，我们将文件复制到 C++/WinRT 项目中并以这种方式添加它们。

导航到 `\Clipboard_sample\SharedContent\xaml` 文件夹，选择并复制 `App.xaml` 和 `MainPage.xaml`，然后将这两个文件粘贴到 C++/WinRT 项目的 `\Clipboard\Clipboard` 文件夹中，并在出现提示时选择替换文件。

在项目节点下向 C++/WinRT 项目添加新文件夹，并将其命名为 `Styles`。 导航到 `\Clipboard_sample\SharedContent\xaml` 文件夹，选择并复制 `Styles.xaml`，然后将其粘贴到 C++/WinRT 项目的 `\Clipboard\Clipboard\Styles` 文件夹中。 右键单击 `Styles` 文件夹（在 C++/WinRT 项目的解决方案资源管理器中）>“添加” > “现有项…”，然后导航到 `\Clipboard\Clipboard\Styles` 。 在文件选取器中选择 `Styles`，然后单击“添加”。

现在，我们已完成 MainPage 的移植，如果你按照步骤进行操作，则 C++/WinRT 项目现在将生成并运行。

## <a name="consolidate-your-idl-files"></a>合并 `.idl` 文件

除了 UI 的标准 `MainPage.xaml` 起点，Clipboard 示例还有五个其他的特定于方案的 XAML 页及其相应的代码隐藏文件。 在 C++/WinRT 版项目中，我们将重复使用所有这些页面的实际 XAML 标记，不做更改。 我们会在随后的几个主要部分探讨如何移植代码隐藏。 但在那样做之前，让我们先讨论 IDL。

将多个运行时类合并成单个 IDL 文件很有意义（请参阅[将运行时类重构到 Midl 文件 (.idl) 中](./author-apis.md#factoring-runtime-classes-into-midl-files-idl)）。 因此，接下来我们将合并 `CopyFiles.idl`、`CopyImage.idl`、`CopyText.idl`、`HistoryAndRoaming.idl` 和 `OtherScenarios.idl` 的内容，方法是：将该 IDL 移到名为 `Project.idl` 的单个文件中（然后删除原始文件）。

在这样做的同时，让我们也从所有五个 XAML 页面类型中删除自动生成的虚拟属性（`Int32 MyProperty;` 及其实现）。

首先，向 C++/WinRT 项目添加新的 Midl 文件 (.idl) 项。 将其命名为 `Project.idl`。 将 `Project.idl` 的全部内容替换为以下代码。

```idl
// Project.idl
namespace SDKTemplate
{
    [default_interface]
    runtimeclass CopyFiles : Windows.UI.Xaml.Controls.Page
    {
        CopyFiles();
    }

    [default_interface]
    runtimeclass CopyImage : Windows.UI.Xaml.Controls.Page
    {
        CopyImage();
    }

    [default_interface]
    runtimeclass CopyText : Windows.UI.Xaml.Controls.Page
    {
        CopyText();
    }

    [default_interface]
    runtimeclass HistoryAndRoaming : Windows.UI.Xaml.Controls.Page
    {
        HistoryAndRoaming();
    }

    [default_interface]
    runtimeclass OtherScenarios : Windows.UI.Xaml.Controls.Page
    {
        OtherScenarios();
    }
}
```

可以看到，这只是复制单个 `.idl` 文件的内容（全都在一个命名空间内，并从每个运行时类中删除了 `MyProperty`）。

在 Visual Studio 的解决方案资源管理器中，通过多选方式选择所有的原始 IDL 文件（`CopyFiles.idl`、`CopyImage.idl`、`CopyText.idl`、`HistoryAndRoaming.idl` 和 `OtherScenarios.idl`），并对其执行“编辑” > “删除”操作（在对话框中选择“删除”）。

最后，若要在所有五个 XAML 页面类型的 `.h` 和 `.cpp` 文件中删除 `MyProperty`，请删除 `int32_t MyProperty()` 访问器和 `void MyProperty(int32_t)` 赋值函数的声明和定义。

顺便说一下，最好让 XAML 文件的名称与其所表示的类的名称相匹配。 例如，如果 XAML 标记文件中有 `x:Class="MyNamespace.MyPage"`，则应将该文件命名为 `MyPage.xaml`。 虽然这不是一项技术要求，但如果不必为同一项目的不同名称费尽心思，则项目会更易于理解和维护，且更易于使用。

## <a name="copyfiles"></a>**CopyFiles**

在 C# 项目中，**CopyFiles** XAML 页面类型在 `CopyFiles.xaml` 和 `CopyFiles.xaml.cs` 源代码文件中实现。 下面依次介绍 **CopyFiles** 的每个成员。

### <a name="rootpage"></a>**rootPage**

这是专用字段。

```csharp
// CopyFiles.xaml.cs
...
public sealed partial class CopyFiles : Page
{
    MainPage rootPage = MainPage.Current;
    ...
}
...
```

我们可以在 C++/WinRT 中定义并初始化它，如下所示。

```cppwinrt
// CopyFiles.h
...
struct CopyFiles : CopyFilesT<CopyFiles>
{
    ...
private:
    SDKTemplate::MainPage rootPage{ MainPage::Current() };
};
...
```

同样（与使用 **MainPage::current** 一样），**CopyFiles::rootPage** 被声明的类型为 **SDKTemplate::MainPage**（投影类型，而不是实现类型）。

### <a name="copyfiles-the-constructor"></a>**CopyFiles**（构造函数）

在 C++/WinRT 项目中，**CopyFiles** 类型已经有一个包含我们需要的代码的构造函数（它只是调用 **InitializeComponent**）。

### <a name="copybutton_click"></a>**CopyButton_Click**

C# **CopyButton_Click** 方法是一个事件处理程序，我们可以从其签名的 `async` 关键字中了解到该方法执行异步工作。 在 C++/WinRT 中，我们以协同例程的形式实现异步方法。 有关如何使用 C++/WinRT 执行并发的简介以及对协同例程的介绍，请参阅[使用 C++/WinRT 执行并发和异步操作](./concurrency.md)。

通常需要在协同例程完成后计划进一步的工作。在这种情况下，协同例程会返回某个可等待的异步对象类型，该类型可以选择报告进度 但这些注意事项通常不适用于事件处理程序。 因此，当事件处理程序执行异步操作时，可以将其作为一个返回 **winrt::fire_and_forget** 的协同例程来实现。 有关详细信息，请参阅[发后不理](./concurrency-2.md#fire-and-forget)。

尽管“发后不理”协同例程的理念是你不需操心其何时完成，但工作仍会在后台继续（或者处于挂起状态，等待恢复）。 可以从 C# 实现中看出，**CopyButton_Click** 依赖于 `this` 指针（它访问实例数据成员 `rootPage`）。 因此，必须确保 `this` 指针（指向 **CopyFiles** 对象的指针）的生存期长于 **CopyButton_Click** 协同例程。 在类似于使用此示例应用程序的情况下，用户在 UI 页面之间导航，我们无法直接控制这些页面的生存期。 如果将 **CopyFiles** 页销毁（通过离开该页面），而此时 **CopyButton_Click** 仍在后台线程上运行，则不能安全地访问 `rootPage`。 若要使协同例程正确，需要获取对 `this` 指针的强引用，并在协同例程存在期间保留该引用。 有关详细信息，请参阅 [C++/WinRT 中的强引用和弱引用](./weak-references.md)。

如果在 C++/WinRT 版示例中查看 **CopyFiles::CopyButton_Click**，你会发现它是在堆栈上使用简单的声明来完成的。

```cppwinrt
fire_and_forget CopyFiles::CopyButton_Click(IInspectable const&, RoutedEventArgs const&)
{
    auto lifetime{ get_strong() };
    ...
}
```

让我们看看已移植代码的其他一些值得注意的方面。

在代码中，我们将 [**FileOpenPicker**](/uwp/api/windows.storage.pickers.fileopenpicker) 对象实例化，并在两行后访问该对象的 [**FileTypeFilter**](/uwp/api/windows.storage.pickers.fileopenpicker.filetypefilter) 属性。 该属性的返回类型实现字符串的 **IVector**。 在该 **IVector** 上，我们调用 [IVector<T>.ReplaceAll(T[])](/uwp/api/windows.foundation.collections.ivector-1.replaceall) 方法。 令人感兴趣的方面是我们要传递给该方法的值，其中应有一个数组。 下面是代码行。

```cppwinrt
filePicker.FileTypeFilter().ReplaceAll({ L"*" });
```

我们要传递的值 (`{ L"*" }`) 是标准 C++ 初始值设定项列表。 在此示例中，它包含单个对象，但初始值设定项列表可以包含以逗号分隔的任意数量的对象。 通过 C++/WinRT 代码片段可以轻松地将初始值设定项列表传递给方法，如[标准初始值设定项列表](./std-cpp-data-types.md#standard-initializer-lists)中所述。

我们将 C# `await` 关键字移植到 C++/WinRT 中的 `co_await`。 下面是代码中的示例。

```cppwinrt
auto storageItems{ co_await filePicker.PickMultipleFilesAsync() };
```

接下来，请考虑这行 C# 代码。

```csharp
dataPackage.SetStorageItems(storageItems);
```

C# 能够将 *storageItems* 所表示的 **IReadOnlyList<StorageFile>** 隐式转换为 [**DataPackage.SetStorageItems**](/uwp/api/windows.applicationmodel.datatransfer.datapackage.setstorageitems) 所需的 **IEnumerable<IStorageItem>** 。 但在 C++/WinRT 中，我们需要从 **IVectorView<StorageFile>** 显式转换为 **IIterable<IStorageItem>** 。 因此，我们通过另一个示例来了解 [**as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) 函数如何起作用。

```cppwinrt
dataPackage.SetStorageItems(storageItems.as<IVectorView<IStorageItem>>());
```

如果在 C# 中使用 `null` 关键字（例如 `Clipboard.SetContentWithOptions(dataPackage, null)`），则在 C++/WinRT 中使用 `nullptr`（例如 `Clipboard::SetContentWithOptions(dataPackage, nullptr)`）。

### <a name="pastebutton_click"></a>**PasteButton_Click**

这是另一个事件处理程序，采用“发后不理”协同例程的形式。 让我们看看已移植代码的一些值得注意的方面。

在 C# 版示例中，我们使用 `catch (Exception ex)` 来捕获异常。 在移植的 C++/WinRT 代码中，会看到表达式 `catch (winrt::hresult_error const& ex)`。 若要详细了解 [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) 及其使用方式，请参阅 [C++/WinRT 的错误处理](./error-handling.md)。

`if (storageItems != null)` 是一个示例，用于测试 C# 对象是否为 `null`。 在 C++/WinRT 中，我们可以依赖一个将对象转换为 `bool` 的转换运算符在内部针对 `nullptr` 进行测试。

下面是移植的 C++/WinRT 版示例中代码片段的稍微简化的版本。

```cppwinrt
std::wostringstream output;
output << std::wstring_view(ApplicationData::Current().LocalFolder().Path());
```

像这样通过 **winrt::hstring** 构造 **std::wstring_view**，其所演示的是一种替代方法，替代对 [**hstring::c_str**](/uwp/cpp-ref-for-winrt/hstring#hstringc_str-function) 函数的调用（目的是将 **winrt::hstring** 转换为 C 样式的字符串）。 此替代方法有效是因为 hstring 的 [ 转换运算符可以将对象转换为 **std::wstring_view**](/uwp/cpp-ref-for-winrt/hstring#hstringoperator-stdwstring_view)。

请考虑以下 C# 代码片段。

```csharp
var file = storageItem as StorageFile;
if (file != null)
...
```

为了将 C# 的 `as` 关键字移植到 C++/WinRT，到目前为止，我们已经看到 [**as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) 函数使用了多次。 如果类型转换失败，该函数会引发异常。 但是，如果希望此转换在失败时返回 `nullptr`（以便我们在代码中处理该条件），则改用 [**try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function) 函数。

```cppwinrt
auto file{ storageItem.try_as<StorageFile>() };
if (file)
...
```

### <a name="copy-the-xaml-necessary-to-finish-up-porting-copyfiles"></a>复制完成移植 CopyFiles 所需的 XAML

现在可以从 C# 项目中选择 `CopyFiles.xaml` 文件的全部内容，将其粘贴到 C++/WinRT 项目的 `CopyFiles.xaml` 文件中（替换 C++/WinRT 项目中该文件的现有内容）。

最后，请编辑 `CopyFiles.h` 和 `.cpp` 并删除虚拟 **ClickHandler** 函数，因为我们只是覆盖了相应的 XAML 标记。

现在，我们已完成 **CopyFiles** 的移植。如果你按照步骤进行操作，则现在会生成并运行 C++/WinRT 项目，**CopyFiles** 方案会正常运行。

## <a name="copyimage"></a>**CopyImage**

若要移植 **CopyImage** XAML 页类型，请完成与 **CopyFiles** 相同的过程。 移植 **CopyImage** 时，会遇到使用 C# [using 语句](/dotnet/csharp/language-reference/keywords/using-statement)的情况。该语句可确保正确释放那些实现 [**IDisposable**](/dotnet/api/system.idisposable) 接口的对象。

```csharp
if (imageReceived != null)
{
    using (var imageStream = await imageReceived.OpenReadAsync())
    {
        ... // Pass imageStream to other APIs, and do other work.
    }
}
```

C++/WinRT 中的等效接口是 [**IClosable**](/uwp/api/windows.foundation.iclosable)（带有其单一 **Close** 方法）。 下面是上面的 C# 代码的 C++/WinRT 等效代码。

```cppwinrt
if (imageReceived)
{
    auto imageStream{ co_await imageReceived.OpenReadAsync() };
    ... // Pass imageStream to other APIs, and do other work.
    imageStream.Close();
}
```

C++/WinRT 对象实现 **IClosable** 主要有益于那些缺乏确定性终止操作的语言。 C++/WinRT 有确定性终止操作，因此我们在编写 C++/WinRT 时通常不需要调用 **IClosable::Close**。 但有些情况下可以调用它，这就是其中一种情况。 在这里，*imageStream* 标识符是围绕基础 Windows 运行时对象（在本例中是一个用于实现 [**IRandomAccessStreamWithContentType**](/uwp/api/windows.storage.streams.irandomaccessstreamwithcontenttype) 的对象）的引用计数包装器。 虽然可以确定 *imageStream* 的终结器（其析构函数）会在封闭范围（花括号）末尾运行，但无法确定该终结器是否会调用 **Close**。 这是因为我们已将 *imageStream* 传递给其他 API，这些 API 仍可能归到基础 Windows 运行时对象的引用计数中。 因此，在这种情况下，可以显式调用 **Close**。 有关详细信息，请参阅[我是否需要对所使用的运行时类调用 IClosable::Close？](./faq.yml#do-i-need-to-call-iclosable--close-on-runtime-classes-that-i-consume-)。

接下来考虑 C# 表达式 `(uint)(imageDecoder.OrientedPixelWidth * 0.5)`，它可以在 **OnDeferredImageRequestedHandler** 事件处理程序中找到。 该表达式将 `uint` 与 `double` 相乘，得出的结果为 `double`， 然后将其强制转换为 `uint`。 在 C++/WinRT 中，我们可以使用外观类似的 C 样式强制转换 (`(uint32_t)(imageDecoder.OrientedPixelWidth() * 0.5)`)，但最好是明确体现我们要使用的强制转换类型。在此示例中，我们会使用 `static_cast<uint32_t>(imageDecoder.OrientedPixelWidth() * 0.5)` 来这样做。

C# 版 **CopyImage.OnDeferredImageRequestedHandler** 有 `finally` 子句，但没有 `catch` 子句。 我们已对 C++/WinRT 版本进行了更深入的了解，并实现了一个 `catch` 子句，用于报告延迟的渲染是否成功。

移植此 XAML 页面的其余部分没有什么可供讨论的新内容。 请记住删除虚拟 ClickHandler 函数。 另外，与使用 CopyFiles 一样，移植的最后一步是选择 `CopyImage.xaml` 的全部内容，将其粘贴到 C++/WinRT 项目的同一文件中。

## <a name="copytext"></a>**CopyText**

可以使用我们已介绍的技术来移植 `CopyText.xaml` 和 `CopyText.xaml.cs`。

## <a name="historyandroaming"></a>**HistoryAndRoaming**

在移植 **HistoryAndRoaming** XAML 页面类型时，有一些方面值得关注。

首先，请查看 C# 源代码，按照控制流完成从 **OnNavigatedTo** 到 **OnHistoryEnabledChanged** 事件处理程序再到异步函数 **CheckHistoryAndRoaming** 的整个过程（不需等待，因此它实质上是一种“发后不理”协同例程）。 由于 **CheckHistoryAndRoaming** 是异步的，因此在 C++/WinRT 中需谨慎对待 `this` 指针的生存期。 如果在 `HistoryAndRoaming.cpp` 源代码文件中查看实现，则可以看到结果。 首先，将委托附加到 **Clipboard::HistoryEnabledChanged** 和 **Clipboard::RoamingEnabledChanged** 事件时，我们只会获得对 **HistoryAndRoaming** 页面对象的弱引用。 为此，请创建委托，该委托依赖于从 [**winrt::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function) 返回的值，而不依赖于 `this` 指针。 这意味着最终会调用异步代码的委托本身不会使 **HistoryAndRoaming** 页保持活动状态（如果我们离开该页面）。

其次，当我们最终访问“发后不理”**CheckHistoryAndRoaming** 协同例程时，首先要做的就是对 `this` 进行强引用，确保 **HistoryAndRoaming** 页至少生存到协同例程最后完成的时间。 若要详细了解所描述的两个方面，请参阅 [C++/WinRT 中的强引用和弱引用](./weak-references.md)。

在移植 **CheckHistoryAndRoaming** 时，我们发现另一值得关注的方面。 它包含用于更新 UI 的代码，因此我们需要确保在主 UI 线程上执行该操作。 最初调用事件处理程序的线程是主 UI 线程。 但通常情况下，异步方法可在任意线程上执行和/或恢复。 在 C# 中，解决方案是调用 [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync)，在 lambda 函数中更新 UI。 在 C++/WinRT 中，我们可以将 [**winrt::resume_foreground**](/uwp/cpp-ref-for-winrt/resume-foreground) 函数与 `this` 指针的 [**Dispatcher**](/uwp/api/windows.ui.xaml.dependencyobject.dispatcher) 配合使用，以便挂起协同例程并立即在主 UI 线程上恢复。

相关表达式为 `co_await winrt::resume_foreground(Dispatcher());`。 也可直接将其表示为 `co_await Dispatcher();`（虽然不太清晰）。 较短的版本基于 C++/WinRT 提供的转换运算符。

移植此 XAML 页面的其余部分没有什么可供讨论的新内容。 请记住删除虚拟 ClickHandler 函数，并通过复制的方式覆盖 XAML 标记。

## <a name="otherscenarios"></a>**OtherScenarios**

可以使用我们已介绍的技术来移植 `OtherScenarios.xaml` 和 `OtherScenarios.xaml.cs`。

## <a name="conclusion"></a>结论

希望本演练为你提供了充足的移植信息和技术。现在，你可以将自己的 C# 应用程序移植到 C++/WinRT 了。 可以通过刷新器来继续参考 Cliboard 示例中源代码的旧版本 (C#) 和新版本 (C++/WinRT)，并将它们并排比较以查看对应项。

## <a name="related-topics"></a>相关主题
* [从 C# 移动到 C++/WinRT](./move-to-winrt-from-csharp.md)
