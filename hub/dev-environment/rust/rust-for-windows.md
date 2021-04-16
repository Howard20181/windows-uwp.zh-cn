---
title: Rust for Windows 和 windows crate
description: 使用 windows crate 和调用 Windows API。
author: stevewhims
ms.author: stwhi
manager: jken
ms.topic: article
keywords: Rust, Windows 10, Microsoft, 了解 Rust, 初学者在 Windows 上使用 Rust 进行开发, 结合使用 Rust 与 VS Code, Rust for Windows
ms.localizationpriority: medium
ms.date: 03/04/2021
ms.openlocfilehash: 1ba0acf298a3bd492ceac9adccafcac857f67cba
ms.sourcegitcommit: f7c7a2ae6367e114a8b9d438963082440cd24043
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315060"
---
# <a name="rust-for-windows-and-the-windows-crate"></a>Rust for Windows 和 windows crate

&nbsp;
> [!VIDEO https://www.youtube.com/embed/LajquCjHXK4]

## <a name="introducing-rust-for-windows"></a>Rust for Windows 简介

[关于在 Windows 上使用 Rust 进行开发的概述](overview.md)主题展示了一个简单的应用，用于输出“Hello, world!” 消息。 但是，不仅可以在 Windows 上使用 Rust，还可以使用 Rust 为 Windows 编写应用。

Rust for Windows（目前是预览版）是 Windows 的最新语言投影。 使用 Rust for Windows，可以通过 [windows crate](https://crates.io/crates/windows)（crate 是 Rust 对二进制文件或库的术语，和/或生成到其中的源代码的术语）直接、无缝地使用任何（过去、现在和将来的）Windows API。

无论是无时间限制的函数（如 [CreateEventW](/windows/win32/api/synchapi/nf-synchapi-createeventw) 和 [WaitForSingleObject](/windows/win32/api/synchapi/nf-synchapi-waitforsingleobject)）、功能强大的图形引擎（如 [Direct3D](/windows/win32/direct3d12/directx-12-programming-guide)）、传统窗口函数（如 [CREATEWINDOWEXW](/windows/win32/api/winuser/nf-winuser-createwindowexw) 和 [DispatchMessageW](/windows/win32/api/winuser/nf-winuser-dispatchmessagew)），还是最新的用户界面 (UI) 框架（如 [Composition](/uwp/api/windows.ui.composition) 和 [Xaml](/uwp/api/windows.ui.xaml)），都可以使用 [windows crate](https://crates.io/crates/windows) 来调用。

[win32metadata](https://github.com/microsoft/win32metadata) 项目旨在为 Win32 API 提供元数据。 此元数据描述了 API 表面 &mdash; 强类型 API 签名、参数和类型。 这样，整个 Windows API 能够以一种自动化和完整的方式进行投影，以供 Rust（以及 C# 和 C++ 等语言）使用。 另请参阅[让 Win32 API 更容易被更多的语言访问](https://blogs.windows.com/windowsdeveloper/2021/01/21/making-win32-apis-more-accessible-to-more-languages/)。

作为 Rust 开发人员，你将使用 Cargo（Rust 的包管理工具）&mdash;以及 `https://crates.io`（Rust 社区的 crate 注册表）&mdash;来管理项目中的依赖项。 好消息是，可以从 Rust 应用中引用 [windows crate](https://crates.io/crates/windows)，然后立即开始调用 Windows API。 还可以在 `https://docs.rs` 上找到 Rust 关于 [windows crate 的文档](https://docs.rs/windows/0.3.1/windows/)。

与 [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/) 类似，[Rust for Windows](https://github.com/microsoft/windows-rs) 是在 GitHub 上开发的开放源代码语言投影。 如果有关于 Rust for Windows 的问题，或想要报告与之相关的问题，请使用 [Rust for Windows](https://github.com/microsoft/windows-rs) 存储库。

Rust for Windows 存储库还有[一些可供遵循的简单示例](https://github.com/microsoft/windows-rs/tree/master/examples)。 Robert Mikhayelyan 的[扫雷游戏](https://github.com/robmikh/minesweeper-rs)就是一个很好的示例应用。

## <a name="contribute-to-rust-for-windows"></a>参与 Rust for Windows

[Rust For Windows](https://github.com/microsoft/windows-rs) 欢迎你的参与！

* 识别和修复[源代码](https://github.com/microsoft/windows-rs/tree/master/src)中的 bug

## <a name="rust-documentation-for-the-windows-api"></a>Windows API 的 Rust 文档

Rust for Windows 受益于 Rust 开发人员所畅享的完善的工具链。 但是，如果让整个 Windows API 能够被轻松调用是一项艰巨任务，则还可以参阅 [Rust 关于 Windows API 的文档](https://microsoft.github.io/windows-docs-rs/doc/bindings/Windows/)。

此资源基本上介绍了如何将 Windows API 和类型投影到惯用 Rust 中。 使用它，可以浏览或搜索你需要了解的 API，以及你需要知道如何调用它的 API。

## <a name="writing-an-app-with-rust-for-windows"></a>使用 Rust for Windows 编写应用

下一主题是 [RSS 阅读器教程](rss-reader-rust-for-windows.md)，我们将演练如何使用 Rust for Windows 编写一个简单的应用。

## <a name="related"></a>相关内容

* [在 Windows 上通过 Rust 进行开发的概述](overview.md)
* [RSS 阅读器教程](rss-reader-rust-for-windows.md)
* [windows crate](https://crates.io/crates/windows)
* [关于 windows crate 的文档](https://docs.rs/windows/0.3.1/windows/)
* [Win32 元数据](https://github.com/microsoft/win32metadata)
* [让 Win32 API 更容易被更多的语言访问](https://blogs.windows.com/windowsdeveloper/2021/01/21/making-win32-apis-more-accessible-to-more-languages/)
* [Windows API 的 Rust 文档](https://microsoft.github.io/windows-docs-rs/doc/bindings/windows/)
* [适用于 Windows 的 Rust](https://github.com/microsoft/windows-rs)
* [扫雷示例应用](https://github.com/robmikh/minesweeper-rs)
