---
description: 成功上传程序包后，将看到一个表格，指示将以排名顺序向特定 Windows 10 设备系列（如果适用，也包含早期 OS 版本）提供哪些程序包。
title: 设备系列可用性
ms.date: 02/26/2021
ms.topic: article
keywords: windows 10, uwp, 程序包, 上传, 设备系列可用性
ms.localizationpriority: medium
ms.openlocfilehash: 3885a5f85a8380e87d753721c6b9ca8b0facc475
ms.sourcegitcommit: e8ea2a36e4f2b9e0326958d226a36dd30c3efa57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105099788"
---
# <a name="device-family-availability"></a>设备系列可用性

[成功将包上载到 "**包**" 页面](upload-app-packages.md)后，"**设备系列可用性**" 部分会显示一个表，该表指示将向特定 Windows 10 设备家族 (及更早版本的操作系统版本（如果适用) ）按顺序排列。 本部分还允许选择是否向特定 Windows 10 设备系列上的客户提供提交。

> [!NOTE]
> 如果尚未上传程序包，**设备系列可用性** 部分将显示带有复选框的 Windows 10 设备系列，这些复选框允许你指示是否向这些设备系列上的客户提供提交。 表格将在上传一个或多个程序包后显示。

本部分还包含一个复选框，用于指示是否要允许 Microsoft 将应用提供给任何未来的 Windows 10 设备系列。 我们建议将此复选框保持选中状态，以便你的应用可随着新设备系列的引入而提供给更多潜在客户。


## <a name="choosing-which-device-families-to-support"></a>选择要支持哪些设备系列

如果你上传面向一个单独的设备系列的程序包，我们将选中该复选框，以便将这些程序包提供给该类设备上的新客户。 例如，如果程序包面向 Windows.Desktop，则会针对该程序包选中 **Windows 10 桌面版** 复选框（并且你将无法选中其他设备系列的复选框）。

面向 Windows.Universal 设备系列的程序包可以在任何 Windows 10 设备（包括 Xbox One）上运行。 默认情况下，我们会将这些程序包提供给 *除* Xbox 之外所有类型的设备上的新客户。

你可以取消选中任何 Windows 10 设备系列的复选框（如果你不希望向该设备类型上的客户提供提交）。 如果未选中某个设备系列的复选框，该设备类型上的新客户将无法获取应用（尽管已拥有该应用的客户仍然可以使用它，并将获取提交的任何更新）。

如果你的应用支持它们，建议你将所有复选框保持选中状态，除非你有特殊原因需要限制可以获取你的应用的 Windows 10 设备类型。 例如，如果你知道你的应用不会在 [Surface Hub](https://developer.microsoft.com/windows/surfacehub) 和/或 [Microsoft HoloLens](https://developer.microsoft.com/mixed-reality) 上提供良好的体验，则可以取消选中 **Windows 10 协同版** 和/或 **Windows 10 全息版** 复选框。 这将阻止任何新客户在这些设备上获取应用。 如果你之后决定已准备好将该应用提供给那些客户，可通过选中这些复选框来新建提交。

<span id="xbox" />

**Windows 10 Xbox** 是唯一一个默认情况下未针对 Windows.Universal 程序包选中的 Windows 10 设备系列。 如果你的应用并非游戏（或者它是游戏并且你启用了 [Xbox Live 创意者计划](/gaming/xbox-live/get-started-with-creators/get-started-with-xbox-live-creators)或者已通过 [概念审批](../gaming/concept-approval.md)过程），而你的提交包含使用 Windows 10 SDK 版本 14393 或更高版本编译的非特定程序包和/或 x64 UWP 程序包，则可以选中 **Windows 10 Xbox** 复选框，来将该应用提供给 Xbox One 上的客户。

> [!IMPORTANT]
> 为了使应用能够在 Xbox 设备上启动，必须包含使用 Windows SDK 版本 14393 或更高版本编译的非特定程序包或 x64 程序包。 但是，如果选中了 **Windows 10 Xbox**，则适用于 Xbox 的最高版本程序包（即，面向 Xbox 或通用设备系列的非特定程序包或 x64 程序包）将始终提供给 Xbox 上的客户，即使它使用早期 SDK 版本进行编译也是如此。 因此，确保使用 Windows SDK 版本 14393 或更高版本对适用于 Xbox 的版本最高的程序包进行编译至关重要。 如果不是这样，将显示一条错误消息，指示系统 Xbox 客户将无法启动应用。 
> 
> 若要解决此错误，可以执行以下操作之一：
> - 将适用的程序包替换为使用 Windows SDK 版本 14393 或更高版本编译的新程序包。
> - 如果已有的程序包支持 Xbox 并使用 Windows SDK 版本 14393 或更高版本进行了编译，则增加它的版本号，以便它提交中版本最高的程序包。
> - 取消选中“Windows 10 Xbox”复选框。
>   
> 如果仍然无法解决该问题，请联系支持人员。

如果要为 Windows 10 IoT 核心版提交 UWP 应用，你不应该在上载软件包后对默认选择进行更改；Windows 10 IoT 没有单独的复选框。 有关发布 IoT 核心版 UWP 应用的详细信息，请参阅 [IoT 核心版 UWP 应用的 Microsoft Store 支持](/windows/iot-core/commercialize-your-device/installingandservicing)。

如果你提交以前发布的应用程序，其中包含可在 **Windows 8/8.1** 上运行的包和 **Windows Phone 2.x 及更早** 版本，这些包将可供这些操作系统版本上的客户使用。 若要停止向这些客户提供应用，请从提交中删除相应的程序包。

> [!IMPORTANT]
> 若要完全阻止特定的 Windows 10 设备系列进行提交，请更新清单中的 [**y**](/uwp/schemas/appxpackage/uapmanifestschema/element-targetdevicefamily) 元素，使其仅面向你要支持 (（即，windows Mobile 或 windows）) 的设备系列，而不是将其保留为通用设备) 系列的 Windows 通用值 (，Microsoft Visual Studio 默认情况下它包含在清单中。

请务必了解：你在 **设备系列可用性** 部分中所做的选择仅应用于全新购买。 已拥有你的应用的任何用户都可以继续使用它，并且将获得你提交的任何更新，即使你在此处删除了设备系列也是如此。 这甚至适用于在升级到 Windows 10 之前获取你的应用的客户。 例如，如果你有一个已发布的应用程序，其中包含 Windows Phone 8.1 包，并且你添加了面向 Windows 通用设备系列的 Windows 10 (UWP) 包，则拥有你的 Windows Phone 8.1 包的 Windows 10 移动客户将获得此 Windows 10 (UWP) 包的更新，即使你已取消选中 **Windows 10 移动** 版的复选框也是如此。

有关设备系列的详细信息，请参阅 [通过扩展 sdk 进行编程](/uwp/extension-sdks/device-families-overview)。


## <a name="understanding-ranking"></a>了解分级

除了允许您指示哪些 Windows 10 设备家族可下载您的提交，" **设备家族可用性** " 部分显示了将提供给不同设备系列的特定包。 如果你有多个程序包可以在某个设备系列上运行，该表格将基于程序包的版本号指示程序包的提供顺序。 有关 Store 如何基于版本号对程序包分级的详细信息，请参阅[程序包版本编号](package-version-numbering.md)。 

例如，假设你有两个程序包：Package_A.appxupload 和 Package_B.appxupload。 对于给定的设备系列，如果 Package_A.appxupload 排名第 1 而 Package_B.appxupload 排名第 2，这意味着当该设备类型上的客户获取应用时，Store 将先尝试提供 Package_A.appxupload。 如果客户设备无法运行 Package_A.appxupload，Store 将提供 Package_B.appxupload。 如果客户的设备无法为该设备系列运行任何包 (例如，如果应用支持 **的最高** 版本高于客户设备上的版本) 则客户将无法下载该设备上的应用。

> [!NOTE]
> 在确定提供给定客户的包时，不会考虑 .xap 包中 (以前发布的应用的版本编号) 。 因此，如果你有多个相同等级的 .xap 程序包，你将看到一个星号而非数字，客户可能会收到任一程序包。 若要将客户从一个 .xap 程序包更新到较新的程序包，请确保在新提交中删除旧的 .xap 程序包。
