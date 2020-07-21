---
title: 分配托管的 Windows 运行时组件
description: 你可以通过文件副本分配 Windows 运行时组件。
ms.assetid: 80262992-89FC-42FC-8298-5AABF58F8212
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1d306c02d4fd99acaa49ec59230181ac0a20c9f3
ms.sourcegitcommit: f561efbda5c1d47b85601d91d70d86c5332bbf8c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690387"
---
# <a name="distributing-a-managed-windows-runtime-component"></a>分配托管的 Windows 运行时组件

你可以通过文件副本分配 Windows 运行时组件。 但是，如果组件包括许多文件，安装对用户而言会很繁琐。 另外，放置文件错误或无法设置引用可能会给他们造成问题。 为了便于安装和使用，你可以将复杂的组件打包为 Visual Studio 扩展 SDK。 用户仅需为整个程序包设置一个引用即可。 使用 "**扩展和更新**" 对话框，可以轻松找到并安装组件，如[查找和使用 Visual Studio 扩展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions?view=vs-2015)中所述。

## <a name="planning-a-distributable-windows-runtime-component"></a>计划可分配的 Windows 运行时组件

为二进制文件（例如 .winmd 文件）选择唯一的名称。 我们建议使用以下格式以确保唯一性：

``` syntax
company.product.purpose.extension
For example: Microsoft.Cpp.Build.dll
```

你的二进制文件可能会和其他开发人员的二进制文件一起安装在应用包中。 请参阅[如何：创建软件开发工具包](https://docs.microsoft.com/visualstudio/extensibility/creating-a-software-development-kit?view=vs-2015)中的 "扩展 sdk"。

若要决定分配组件的方式，请考虑其复杂性。 在以下情况下，建议使用扩展 SDK 或类似的程序包管理器：

-   组件包含多个文件。
-   你为多个平台（例如，x86 和 ARM）提供各种版本的组件。
-   提供组件的调试版本和发布版本。
-   组件的文件和程序集仅在设计时使用。

如果出现上述多种情况，扩展 SDK 会极其有用。

> **注意**  对于复杂的组件，NuGet 程序包管理系统提供扩展 sdk 的开放源替代项。 像扩展 SDK 一样，NuGet 支持创建可简化复杂组件安装的程序包。 有关 NuGet 包与 Visual Studio 扩展 Sdk 的比较，请参阅[使用 NuGet 和扩展 SDK 添加引用](https://docs.microsoft.com/visualstudio/ide/adding-references-using-nuget-versus-an-extension-sdk?view=vs-2015)。

## <a name="distribution-by-file-copy"></a>通过文件副本进行分配

如果组件包括单个 .winmd 文件或 .winmd 文件和资源索引 (.pri) 文件，你只需使 .winmd 文件可供用户复制即可。 用户可以将文件放置在项目中的任何所需位置、使用“添加现有项”对话框将 .winmd 文件添加到项目，然后使用“引用管理器”对话框创建引用。 如果包括 .pri 文件或 .xml 文件，请指示用户使用 .winmd 文件放置这些文件。

> **请注意**，当你生成 Windows 运行时组件时，即使你的项目不包含任何资源，Visual Studio  始终会生成一个 pri 文件。 如果你的组件具有测试应用，则可以通过检查 bin\\调试\\AppX 文件夹中的应用包的内容来确定是否使用该 pri 文件。 如果此处未显示组件中的 .pri 文件，则无需分配它。 或者，你可以使用 [MakePRI.exe](https://docs.microsoft.com/previous-versions/windows/apps/jj552945(v=win.10)) 工具从 Windows 运行时组件项目中转储资源文件。 例如，在“Visual Studio 命令提示符”窗口中，键入： makepri dump /if MyComponent.pri /of MyComponent.pri.xml 你可以在[资源管理系统 (Windows)](https://docs.microsoft.com/previous-versions/windows/apps/jj552947(v=win.10)) 中阅读有关 .pri 文件的详细信息。

## <a name="distribution-by-extension-sdk"></a>通过扩展 SDK 进行分配

复杂组件通常包括 Windows 资源，但请查看之前部分中关于检测空 .pri 文件的说明。

**创建扩展 SDK**

1.  请确保已安装 Visual Studio SDK。 你可以从 [Visual Studio 下载](https://visualstudio.microsoft.com/downloads/download-visual-studio-vs)页面下载 Visual Studio SDK。
2.  使用 VSIX 项目模板创建新项目。 你可以在“扩展性”类别的 Visual C# 或 Visual Basic 下找到该模板。 此模板作为 Visual Studio SDK 的一部分进行安装。 （[演练：使用 C# 或 Visual Basic 创建 SDK](https://docs.microsoft.com/visualstudio/extensibility/walkthrough-creating-an-sdk-using-csharp-or-visual-basic?view=vs-2015) 或[演练：使用 C++ 创建 SDK](https://docs.microsoft.com/visualstudio/extensibility/walkthrough-creating-an-sdk-using-cpp?view=vs-2015)，通过非常简单的方案展示此模板的用法。 )
3.  确定 SDK 的文件夹结构。 该文件夹结构和“引用”、“Redist”以及DesignTime文件夹开始于 VSIX 项目的根级别。

    -   “引用”是用户可据以进行编程的二进制文件的位置。 扩展 SDK 在用户的 Visual Studio 项目中创建对这些文件的引用。
    -   “Redist”是其他必须使用二进制文件分配的文件的位置，位于使用组件创建的应用内。
    -   DesignTime是仅在开发人员创建使用组件的应用时使用的文件的位置。

    在其中每个文件夹中，你都可以创建配置文件夹。 允许的名称为调试、零售和 CommonConfiguration。 CommonConfiguration 文件夹适用于无论是由零售版本还是由调试版本使用都相同的文件。 如果仅分配组件的零售版本，你可以将所有内容置于 CommonConfiguration 内并忽略其他两个文件夹。

    在每个配置文件夹中，你可以为特定于平台的文件提供体系结构文件夹。 如果将相同文件用于所有平台，可以提供一个名为“neutral”的文件夹。 可以在[如何：创建软件开发工具包](https://docs.microsoft.com/visualstudio/extensibility/creating-a-software-development-kit?view=vs-2015)中查找文件夹结构的详细信息，包括其他体系结构文件夹名称。 （该文讨论平台 SDK 和扩展 SDK。 若要折叠有关平台 SDK 的部分以避免混淆，你会发现该文很有用。 )

4.  创建 SDK 清单文件。 该清单指定名称和版本信息、SDK 支持的体系结构、.NET 版本以及有关 Visual Studio 使用 SDK 的方式的其他信息。 你可以在[如何：创建软件开发工具包](https://docs.microsoft.com/visualstudio/extensibility/creating-a-software-development-kit?view=vs-2015)中找到详细信息和示例。
5.  生成和分配扩展 SDK。 有关详细信息，包括对 VSIX 包进行本地化和签名，请参阅[Vsix 部署](https://docs.microsoft.com/visualstudio/misc/how-to-manually-package-an-extension-vsix-deployment?view=vs-2015)。

## <a name="related-topics"></a>相关主题

* [创建软件开发工具包](https://docs.microsoft.com/visualstudio/extensibility/creating-a-software-development-kit?view=vs-2015)
* [NuGet 程序包管理系统](https://github.com/NuGet/Home)
* [资源管理系统（Windows）](https://docs.microsoft.com/previous-versions/windows/apps/jj552947(v=win.10))
* [查找和使用 Visual Studio 扩展](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions?view=vs-2015)
* [Makepri.exe 命令选项](https://docs.microsoft.com/previous-versions/windows/apps/jj552945(v=win.10))
