---
Description: 按照以下指南准备要提交到 Microsoft Store 的应用包。
title: 应用包要求
ms.assetid: 651B82BA-9D0C-45AC-8997-88CD93DC903C
ms.date: 10/31/2018
ms.topic: article
keywords: Windows 10, uwp, 程序包要求, 程序包, 程序包格式, 受支持的版本, 提交, windows 10, uwp, package requirements, packages, package format, supported version, submit
ms.localizationpriority: medium
ms.openlocfilehash: 9c478b038ff729f753696d10fc18b21391a3a7de
ms.sourcegitcommit: 96b7be654a0922eeb421b5fa51ebfc586abe74fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945985"
---
# <a name="app-package-requirements"></a>应用包要求

按照以下指南准备要提交到 Microsoft Store 的应用包。

## <a name="before-you-build-your-apps-package-for-the-microsoft-store"></a>为 Microsoft Store 生成应用包之前

请确保[使用 Windows 应用认证工具包测试应用](../debug-test-perf/windows-app-certification-kit.md)。 我们还建议在不同类型的硬件上测试你的应用。 请注意，在我们对你的应用进行认证并在 Microsoft Store 中发布之前，该应用只能在具有开发者许可证的计算机上安装和运行。

## <a name="building-the-app-package-using-microsoft-visual-studio"></a>使用 Microsoft Visual Studio 生成应用包

如果你使用 Microsoft Visual Studio 作为开发环境，则你已经拥有可使创建应用包的过程变得快速而轻松的内置工具。 有关详细信息，请参阅[打包应用](../packaging/index.md)。

> [!NOTE]
> 请确保所有文件名都使用 ANSI。 

在 Visual Studio 中创建程序包时，请确保使用与你的开发者帐户关联的相同帐户登录。 程序包清单的某些部分具有与你的帐户相关的特定详细信息。 将自动检测和添加此信息。 如果没有将其他信息添加到清单，你可能会遇到软件包上传失败。 

生成应用的 UWP 包时，Visual Studio 可以创建 .msix 或 appx 文件，或 msixupload 或 .appxupload 文件。 对于 UWP 应用，建议你始终在 "[包](upload-app-packages.md)" 页中上传 msixupload 或 .appxupload 文件。 有关如何打包 UWP 应用以上架 Microsoft Store 的详细信息，请参阅[使用 Visual Studio 打包 UWP 应用](/windows/msix/package/packaging-uwp-apps)。

不必使用来自受信任的证书颁发机构的根证书对你的应用包进行签名。


### <a name="app-bundles"></a>应用程序包

对于 UWP 应用，Visual Studio 可生成应用捆绑包（.msixbundle 或 .appxbundle）来减小用户下载的应用的大小。 仅当已定义特定于语言的资源、大量图像缩放资源或适用于特定版本的 Microsoft DirectX 的资源时，该操作才有用。

> [!NOTE]
> 一个应用程序包可以包含所有体系结构的程序包。

使用应用程序包，用户将仅下载相关文件，而不是所有可能的资源。 有关应用程序包的详细信息，请参阅[打包应用](../packaging/index.md)和[使用 Visual Studio 打包 UWP 应用](/windows/msix/package/packaging-uwp-apps)。


## <a name="building-the-app-package-manually"></a>手动生成应用包

如果未使用 Visual Studio 创建程序包，则必须[手动创建程序包清单](https://docs.microsoft.com/uwp/schemas/appxpackage/how-to-create-a-package-manifest-manually)。

确保查看[应用包清单](https://docs.microsoft.com/uwp/schemas/appxpackage/appx-package-manifest)文档，以获取完整清单的详细信息和要求。 你的清单必须遵循程序包清单架构，才能通过认证。

你的清单必须包含有关你的帐户和应用的某些特定信息。 通过查看仪表板中应用概述页面的 **“应用管理”** 部分中的[查看应用标识详细信息](view-app-identity-details.md)，可找到此信息。

> [!NOTE]
> 清单中的值区分大小写。 空格和其他标点符号也必须匹配。 请小心输入值并进行检查，以确保这些值准确无误。


应用捆绑包（. .msixbundle 或 .appxbundle）使用不同的清单。 查看[捆绑包清单](https://docs.microsoft.com/uwp/schemas/bundlemanifestschema/bundle-manifest)文档，以获取应用程序包清单的详细信息和要求。 请注意，在 .msixbundle 或 .appxbundle 中，每个包含包的清单必须使用相同的元素和属性（ [Identity](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity)元素的**ProcessorArchitecture**特性除外）。

> [!TIP]
> 提交包之前，请务必运行[Windows 应用认证工具包](../debug-test-perf/windows-app-certification-kit.md)。 此操作可帮助你确定你的清单是否存在任何可能导致认证或提交失败的问题。


## <a name="package-format-requirements"></a>程序包格式要求

你的应用包必须符合这些要求。

| 应用包属性 | 要求                                                          |
|----------------------|----------------------------------------------------------------------|
| 包大小         | .msixbundle 或 .appxbundle：每个包的最大 25 GB <br>面向每个包的 Windows 10:25 GB 最大值的 .msix 或 .appx 包<br>面向 Windows 8.1 的 .appx 程序包：每个程序包最大为 8 GB <br> 面向 Windows 8 的 .appx 程序包：每个程序包最大为 2 GB <br> 面向 Windows Phone 8.1 的 .appx 程序包：每个程序包最大为 4 GB <br> .xap 程序包：每个程序包最大为 1 GB                                                                           |
| 块映射哈希     | SHA2-256 算法                                                   |

> [!IMPORTANT]
> 你不能再上传使用 Windows Phone 3.x SDK 生成的新 XAP 包。 已存储在 XAP 包中的应用将在 Windows 10 移动版设备上继续运行。 有关详细信息，请参阅此[博客文章](https://blogs.windows.com/windowsdeveloper/2018/08/20/important-dates-regarding-apps-with-windows-phone-8-x-and-earlier-and-windows-8-8-1-packages-submitted-to-microsoft-store)。

## <a name="supported-versions"></a>支持的版本

对于 UWP 应用，所有程序包都必须以 Microsoft Store 支持的 Windows 10 版本为目标。 你的程序包支持的版本必须在应用清单的 [TargetDeviceFamily](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-targetdevicefamily) 元素的 **MinVersion** 和 **MaxVersionTested** 属性中指明。

当前受支持的版本范围是： 
- 最低版本：10.0.10240.0
- 最大值：10.0.17763。1


## <a name="storemanifest-xml-file"></a>StoreManifest XML 文件

StoreManifest.xml 是一种可选的配置文件，可包含在应用包中。 该文件旨在支持程序包清单未涵盖的功能，例如将应用声明为 Microsoft Store 设备应用或声明程序包适用于某个设备所依据的要求。 如果使用此项，则会使用应用包提交 StoreManifest.xml，且必须位于应用主项目的根文件夹中。 有关详细信息，请参阅 [StoreManifest 架构](https://docs.microsoft.com/uwp/schemas/storemanifest/store-manifest-schema-portal)。

 

 




