---
title: Windows 运行时 8.x 到 UWP 案例研究：Bookstore1
ms.assetid: e4582717-afb5-4cde-86bb-31fb1c5fc8f3
description: 本主题介绍将一个非常简单的通用 8.1 应用移植到 Windows 10 通用 Windows 平台 (UWP) 应用的案例研究。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ff8e095fc46d2c08771f3e2e002fe2d8b602ab71
ms.sourcegitcommit: 53c00939b20d4b0a294936df3d395adb0c13e231
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91933058"
---
# <a name="windows-runtime-8x-to-uwp-case-study-bookstore1"></a>Windows 运行时 8.x 到 UWP 案例研究：Bookstore1


本主题介绍将一个非常简单的通用 8.1 应用移植到 Windows 10 通用 Windows 平台 (UWP) 应用的案例研究。 通用 8.1 应用是为 Windows 8.1 生成一个应用包，并为 Windows Phone 8.1 生成另一个应用包的应用。 在 Windows 10 中，你可以创建可供客户安装到种类广泛的设备上的单个应用包，而这正是我们要在此案例研究中实现的目标。 请参阅 [UWP 应用指南](../get-started/universal-application-platform-guide.md)。

我们将移植的应用包含绑定到视图模型的 **ListBox**。 该视图模型具有显示标题、作者和书籍封面的书籍列表。 书籍封面图像将 " **生成操作** " 设置为 " **内容** "，并将 " **复制到输出目录** " 设置为 "不 **复制**"。

本部分中之前的主题介绍了平台之间的差异，并且提供有关将应用的各个方面从 XAML 标记移植到访问数据（通过绑定到视图模型）这一过程的详细信息和指南。 案例研究旨在通过在真实示例中实际显示指南来补充该指南。 案例研究假设你已阅读该指南，因此不会重复该指南。

**注意**   在 \_ Visual studio 中打开 Bookstore1Universal 10 时，如果看到消息 "需要 Visual studio 更新"，请按照[TargetPlatformVersion](w8x-to-uwp-troubleshooting.md)中的步骤操作。

## <a name="downloads"></a>下载

[下载 Bookstore1 \_ 81 通用8.1 应用](https://codeload.github.com/MicrosoftDocs/windows-topic-specific-samples/zip/Bookstore1_81)。

[下载 Bookstore1Universal \_ 10 Windows 10 应用](https://codeload.github.com/MicrosoftDocs/windows-topic-specific-samples/zip/Bookstore1Universal_10)。

## <a name="the-universal-81-app"></a>通用 8.1 应用

下面是 \_ 我们要移植的应用的 Bookstore1 81，如所示。 它只是一个垂直滚动的书籍列表框，位于应用名称和页面标题下。

![bookstore1 \- 81 如何在 windows 上查找](images/w8x-to-uwp-case-studies/c01-01-win81-how-the-app-looks.png)

\_Windows 上的 Bookstore1 81

![bookstore1 \- 81 如何在 windows phone 上查找](images/w8x-to-uwp-case-studies/c01-02-wp81-how-the-app-looks.png)

\_Windows Phone 上的 Bookstore1 81

##  <a name="porting-to-a-windows10-project"></a>移植到 Windows 10 项目

Bookstore1 \_ 81 解决方案是一个8.1 通用应用项目，其中包含这些项目。

-   Bookstore1 \_ 81. Windows。 这是为 Windows 8.1 生成应用包的项目。
-   Bookstore1 \_ 81. WindowsPhone。 这是为 Windows Phone 8.1 生成应用包的项目。
-   Bookstore1 \_ 81. Shared。 这是包含由其他两个项目同时使用的源代码、标记文件以及其他资源的项目。

对于此案例研究，我们针对要支持的设备提供[如果你有通用 8.1 应用](w8x-to-uwp-root.md)中所述的常用选项。 此处的决策很简单：此应用在其 Windows 8.1 和 Windows Phone 8.1 形式中具有相同的功能，并且具有大部分相同的代码。 因此，我们将“共享”项目的内容（以及其他项目中我们所需要的一切其他内容）移植到面向通用设备系列的 Windows 10（可安装到种类最广泛的设备）。

在 Visual Studio 中创建一个新项目、将文件复制到 Bookstore1 \_ 81，然后将复制的文件包含在新项目中，这是一个非常快速的任务。 首先创建一个新的空白应用程序（Windows 通用）项目。 将其命名为 Bookstore1Universal \_ 10。 要从 Bookstore1 \_ 81 复制到 Bookstore1Universal 10 的文件 \_ 。

**从“共享项目”中**

-   复制包含书籍封面图像 PNG 文件的文件夹 (该文件夹为 \\ 资产 \\ CoverImages) 。 复制该文件夹后，在 **“解决方案资源管理器”** 中，请确保将 **“显示所有文件”** 切换为打开。 右键单击已复制的文件夹，然后单击 " **包括在项目中**"。 该命令的意思是将文件或文件夹“包括”在某个项目中。 每次复制文件或文件夹时，每复制一次**解决方案资源管理器**单击 "**刷新**"，然后将文件或文件夹包含在项目中。 无需为你将在目标位置替换的文件执行此操作。
-   复制包含视图模型源文件的文件夹， (文件夹为 \\ ViewModel) 。
-   复制 MainPage.xaml 并替换目标位置中的文件。

**从 Windows 项目中**

-   复制 BookstoreStyles.xaml。 我们将使用此文件作为一个合适的起始点，因为此文件中的所有资源键都将在 Windows 10 应用中解析；而等效的 WindowsPhone 文件中的某些资源键不会解析。

编辑刚刚复制的源代码和标记文件，并将对 Bookstore1 81 命名空间的所有引用更改 \_ 为 Bookstore1Universal \_ 10。 执行此操作的一种快速方法是使用 " **在文件中替换** " 功能。 视图模型中和任何其他强制性代码中都不需要更改任何代码。 但是，为了更轻松地查看正在运行的应用程序版本，请将 **Bookstore1Universal \_ BookstoreViewModel** 属性返回的值从 "BOOKSTORE1 \_ 81" 更改为 "Bookstore1Universal \_ 10"。

现在，你可以执行生成和运行操作。 新的 UWP 应用的外观如下，当前尚未完成任何明确的工作以将其移植到 Windows 10。

![Windows 10 应用程序的屏幕截图，无明确更改。](images/w8x-to-uwp-case-studies/c01-03-desk10-initial-source-code-changes.png)

在桌面设备上运行的初始源代码发生更改的 Windows 10 应用

![Windows 10 应用程序的屏幕截图，其中包含最初的源代码更改。](images/w8x-to-uwp-case-studies/c01-04-mob10-initial-source-code-changes.png)

在移动设备上运行的初始源代码发生更改的 Windows 10 应用

视图和视图模型正确地协同工作，并且 **ListBox** 处于运行状态。 我们只需修复样式。 在浅色主题的移动设备中，我们可以看到列表框的边框，但是该边框很容易处于隐藏状态。 并且版式太大，因此我们将更改我们正在使用的样式。 此外，如果我们希望应用看起来像默认状态，应用在桌面设备上运行时应使用浅色。 因此，我们将更改该设置。

## <a name="universal-styling"></a>通用样式设置

Bookstore1 \_ 81 应用程序使用了两个不同的资源字典 (BookstoreStyles) 为 Windows 8.1 和 Windows Phone 8.1 操作系统定制其样式。 这两个 BookstoreStyles.xaml 文件均未包含我们在 Windows 10 应用中所需的样式。 但好消息是，我们所需要的实际上比它们中的任意一个都要简单得多。 因此接下来的步骤将主要涉及到删除和简化我们的项目文件和标记。 具体步骤如下。 并且你可以使用本主题顶部的链接下载这些项目，并查看此处和案例研究末尾之间的所有更改结果。

-   若要缩短项目之间的间距，请在 MainPage.xaml 中查找 `BookTemplate` 数据模板并从根 **Grid** 中删除 `Margin="0,0,0,8"`。
-   同样在 `BookTemplate` 中，存在对 `BookTemplateTitleTextBlockStyle` 和 `BookTemplateAuthorTextBlockStyle` 的引用。 Bookstore1 \_ 81 将这些密钥用作间接寻址，以便单个密钥在两个应用中具有不同的实现。 我们不再需要该间接寻址；我们可以直接引用系统样式。 因此使用 `TitleTextBlockStyle` 和 `SubtitleTextBlockStyle` 分别替换这些引用。
-   现在，我们需要将 `LayoutRoot` 的 Background 设置为正确的默认值，以便无论使用何种主题，应用在所有设备上运行时都具有合适的外观。 将其从 `"Transparent"` 更改为 `"{ThemeResource ApplicationPageBackgroundThemeBrush}"`。
-   在 `TitlePanel` 中，将对 `TitleTextBlockStyle` 的引用（现在有点过大）更改为对 `CaptionTextBlockStyle` 的引用。 `PageTitleTextBlockStyle` 是另一 Bookstore1 \_ 81 间接寻址，我们不再需要此间接寻址。 将其更改为引用 `HeaderTextBlockStyle`。
-   我们不再需要在 **ListBox** 上设置任何特殊的 Background、Style 或 ItemContainerStyle，因此只需从标记中删除这三个属性及其值即可。 但是我们需要隐藏 **ListBox** 的边框，以便将 `BorderBrush="{x:Null}"` 添加到其中。
-   我们将不再引用 BookstoreStyles.xaml **ResourceDictionary** 文件中的任何资源。 你可以删除所有这些资源。 但不要删除 BookstoreStyles.xaml 文件本身：我们仍需要最后一次使用它，你将在下一部分中看到。

在样式设置操作的最后一步中，应用的外观如下所示。

![带有样式操作最后一序列的应用屏幕截图。](images/w8x-to-uwp-case-studies/c01-05-desk10-almost-ported.png)

在桌面设备上运行的即将完成移植的 Windows 10 应用

![几乎端口 windows 10 应用程序的屏幕截图。](images/w8x-to-uwp-case-studies/c01-06-mob10-almost-ported.png)

在移动设备上运行的即将移植完成的 Windows 10 应用

## <a name="an-optional-adjustment-to-the-list-box-for-mobile-devices"></a>对移动设备的列表框的可选调整

当应用在移动设备上运行时，列表框的背景在两种主题下都默认为浅色。 这可能是你偏好的样式，如果确实如此，则除了以下整理工作之外，没有其他要执行的操作：从项目中删除 BookstoreStyles.xaml 资源字典文件，并删除将其合并到 MainPage.xaml 的标记。

但控件的设计目的是你可以自定义它们的外观，同时使其行为不受影响。 因此，如果你希望列表框在深色主题下显示为深色（这是原始应用的外观），本部分介绍了一种可执行该操作的方法。

我们所进行的更改只需要在应用在移动设备上运行时，影响该应用即可。 因此，在移动设备系列上运行时，我们将使用略微自定义的列表框样式，然后我们将在任何其他设备上运行时继续使用默认样式。 为此，我们将创建 BookstoreStyles.xaml 的副本，并且为其分配一个特殊的 MRT 限定名称，这将导致它仅在移动设备上进行加载。

添加一个新的 **ResourceDictionary** 项目项并将其命名为 BookstoreStyles.DeviceFamily-Mobile.xaml。 你现在有两个逻辑名称均为 BookstoreStyles.xaml 的文件（这是你在标记和代码中使用的名称）。 但是，这两个文件具有不同的物理名称，因此它们可以包含不同的标记。 你可以对任何 xaml 文件使用此 MRT 限定的命名方案，但是请注意所有具有相同逻辑名称的 xaml 文件都共享单个 xaml.cs 代码隐藏文件（其中一个适用）。

为该列表框编辑控件模板的副本，并将它与 `BookstoreListBoxStyle` 的键一起存储在新的资源词典 BookstoreStyles.DeviceFamily-Mobile.xaml 中。 现在我们将对三个资源库进行简单的更改。

-   在前台资源库中，将该值更改为 `"{x:Null}"`。 请注意，在元素上将属性直接设置为 `"{x:Null}"` 与使用代码将其设置为 `null` 相同。 但是在资源库中使用 `"{x:Null}"` 的值具有独特的效果：它将以默认样式（对于相同属性）替代资源库并在目标元素上还原属性的默认值。
-   在后台资源库中，将该值更改为 `"Transparent"` 以删除该浅色背景。
-   在模板资源库中，找到名为 `Focused` 的视觉状态并删除其情节提要，使其成为空的标记。
-   从标记中删除所有其他资源库。

最后，将 `BookstoreListBoxStyle` 复制到 BookstoreStyles.xaml 并删除它的三个资源库，使其成为空的标记。 我们执行此操作的效果是，在除了移动设备之外的设备上，我们对 BookstoreStyles.xaml 和 `BookstoreListBoxStyle` 的引用将解析，但不会产生任何影响。

![已移植的 Windows 10 应用](images/w8x-to-uwp-case-studies/c01-07-mob10-ported.png)

在移动设备上运行的已移植的 Windows 10 应用

## <a name="conclusion"></a>结束语

此案例研究介绍了移植非常简单的应用（可以认为是一个过分简单的应用）的过程。 例如，列表框可用于选择或者用于建立导航的上下文；应用导航到具有有关所点击项的更多详细信息的页面。 根据用户的选择，此特定应用不执行任何操作，并且它没有导航。 即便如此，案例研究仍可用于打破僵局、介绍移植过程以及演示可在真实的 UWP 应用中使用的重要技术。

我们还看到了移植视图模型过程通常很顺利的证据。 用户界面和外形规格支持是我们在移植时可能更需要注意的方面。

下一个案例研究是 [Bookstore2](w8x-to-uwp-case-study-bookstore2.md)，我们将从中了解访问和显示分组数据。