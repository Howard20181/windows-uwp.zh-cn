---
title: 面向 Windows 即服务的应用开发
description: 了解创新、开发和交付的 Windows 即服务 (WaaS) 方法，以及以社区为中心的 Windows 预览体验成员测试计划。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: f384ca56-f2b2-4793-b251-f7f5735376bb
ms.localizationpriority: medium
ms.openlocfilehash: d301a2dfcc478f6d90b8aa562bfbd34cf79493c1
ms.sourcegitcommit: 39fb8c0dff1b98ededca2f12e8ea7977c2eddbce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91750133"
---
# <a name="application-development-for-windows-as-a-service"></a>面向 Windows 即服务的应用程序开发

**适用于**
-   Windows 10
-   Windows 10 移动版
-   Windows 10 IoT 核心版 

在当今的环境中，用户期望经常受到以设备为中心的体验影响，需要以月而非年为单位来衡量完整的产品周期。 此外，新版本必须持续提供，并且必须在对用户影响最小的情况下部署。 Microsoft 通过实现名为 [Windows 即服务 (WaaS)](/windows/deployment/update/waas-overview) 的创新、开发和交付的新方法设计 Windows 10 以满足这些要求。 在保持高质量级别的同时极大地缩短产品周期的关键是以社区为中心的创新测试方法，Microsoft 已针对 Windows 10 实现此方法。 名为“Windows 预览体验成员”的社区由世界各地数百万的用户组成。 当 Windows 预览体验成员选择加入社区时，他们将在产品周期的过程中测试许多版本，并通过名为“外部测试版”的迭代方法向 Microsoft 提供反馈。

作为外部测试版分发的版本向 Windows 工程团队提供大量关于版本在实际使用中执行情况的数据。 与 Windows 预览体验成员一起执行外部测试版还使 Microsoft 能够在比以往更多的各种硬件、应用程序和网络环境中测试版本，并且更快地发现问题。 因此，Microsoft 认为侧重于社区的外部测试将实现比以往更快速的创新交付和更好的公开发布质量。

## <a name="windows-10-release-types-and-cadences"></a>Windows 10 版本类型和节奏

尽管 Microsoft 向 Windows 预览体验成员发布外部测试版，但 Microsoft 将持续向公众广泛发布两种类型的 Windows 10 版本：

**功能更新**在已运行 Windows 10 的设备上安装最新特性、体验和功能。 因为功能更新包含整个 Windows 副本，因此客户也将它们用于在运行 Windows 7 或 Windows 8.1 的现有设备和未安装操作系统的新设备上安装 Windows 10。 Microsoft 预期每半年发布一次更新。 

质量更新提供安全问题解决方案和其他重要的 Bug 修复  。 以每月一次或多次的频率提供质量更新，以对当前受支持的每项功能进行改进。 Microsoft 将继续在“更新星期二”（有时称为“修补程序星期二”）发布质量更新。 此外，如果需要满足客户需求，Microsoft 可能会在“更新星期二”之外的过程发布 Windows 10 的其他质量更新。

在 Windows 10 开发期间，Microsoft 简化了 Windows 产品工程设计和发布周期，以便我们能够比以往更快地交付客户需要的特性、体验以及功能。 同时我们创造新的方法来交付并安装功能更新和质量更新，从而简化部署和持续的管理、扩大保持使用最新 Windows 功能和体验的员工基础，并降低企业的总成本。 因此，我们已实现新的服务选项（称为“半年频道”和“长期服务频道 (LTSC)”），用于提供实用解决方案，以使企业环境中的更多设备比以往更进一步地保持最新更新。

如下表所示，描述了各种服务频道及其主要属性。

| 服务选项 | 新功能升级的安装可用性 | 服务生存期 | 主要优势 | 支持版本 |
| --- | --- | --- | --- | --- |
| 半年频道(定向) | 紧接 Microsoft 第一次发布之后 | 18 个月 | 尽快向用户提供新功能 | 家庭版、专业版、教育版、企业版、移动版、IoT 核心版、Windows 10 IoT 核心专业版（IoT 核心专业版） |
| 半年频道 | 大约在 Microsoft 第一次发布之后的 4 个月 | 第一次发布之后的 18 个月 | 在部署之前提供更多时间测试新功能升级 | 专业版、教育版、企业版、移动企业版、IoT 核心专业版 |
| 长期服务频道 (LTSC) | 紧接 Microsoft 发布之后 | 10 年 | 支持采用较少更改的配置长期部署选定的 Windows 10 版本 | 企业 LTSB |

有关详细信息，请参阅[更新和升级的 Windows 10 服务选项](/windows/deployment/update/waas-overview#servicing-channels)。

## <a name="supporting-apps-in-windows-as-a-service"></a>Windows 即服务中的支持应用

支持应用的传统方法已用于发布新的应用版本来响应 Windows 发布。 这假定底层操作系统存在重大更改，这些更改可能导致应用程序出现回归问题。 此模型涉及专用开发和验证周期，要求我们的 ISV 合作伙伴配合 Windows 发布节奏。

在 Windows 即服务模型中，Microsoft 承诺维护底层操作系统的兼容性。 这意味着 Microsoft 将通力合作，以确保不会出现对应用生态系统造成负面影响的重大更改。 在此方案中，当发布一个 Windows 版本时，大多数应用（不具有任何内核依赖关系的应用）仍可继续运行。

鉴于此更改，Microsoft 建议 ISV 合作伙伴将其应用发布和支持从特定的 Windows 版本中分离出来。 我们的共同客户可以通过应用程序生命周期方法得到更好的服务。 这意味着，当某个应用程序版本发布时，它在一段时期内会受支持，无论在此期间发布了多少个 Windows 版本。 ISV 承诺：只要该特定版本的应用在生命周期内受支持，就会对它提供支持。 Microsoft 针对可在[此处](https://support.microsoft.com/hub/4095338/microsoft-lifecycle-policy?C2=14019)参考的 Windows 采用类似的生命周期方法。

此方法将减轻配合 Windows 发布的应用计划的维护负担。 ISV 合作伙伴应该能够按照自己的节奏不受约束地发布功能或更新。 我们认为，我们的合作伙伴可以使用最新应用更新（不依赖 Windows 版本）来持续更新其客户基础。 此外，我们的客户每当在发布 Windows 版本就不必寻找明确的支持声明。 下面是支持声明的一个示例，该声明介绍某个应用可以如何在不同版本的操作系统上受支持：

> **应用程序生命周期支持声明的示例**
>
> Contoso 是一家软件开发公司，也是受欢迎的 Mojave 应用的所有者，该应用在提供企业空间方面占有主要份额。 Contoso 发布其下一个主要版本 Mojave 14.0，并声明自发布之日起提供主流支持三年。 在主流支持期间，对于授权产品提供的所有更新和支持都是免费的。 Contoso 还声明提供额外两年的延长支持，其中客户可以购买更新和支持来获取宽限期。 超出延长支持结束日期后，此产品版本将不再受支持。 在提供主流支持期间，Contoso 会对所有发布版本的 Windows 上的 Mojave 14.0 提供支持。 必要时，Contoso 还会发布 Mojave 更新，该更新独立于 Windows 产品发布。

在以下部分中，你会找到有关 Microsoft 维护底层操作系统的兼容性所采取的步骤的其他信息。 你还会找到有关帮助维护组合操作系统和应用生态系统的兼容性可以采取的步骤的指南。 有一个部分介绍了如何利用 Windows 外部测试版在发布 Windows 版本之前对应用回归进行检测。 最后，我们会介绍如何使用检测和遥测驱动的方法来提高 Windows 版本的质量。 我们建议 ISV 对其应用项目组合采取类似方法。

## <a name="key-changes-since-windows7-to-ensure-app-compatibility"></a>自 Windows 7 以来，确保应用兼容性的主要更改

我们了解兼容性对开发人员的重要性。 ISV 和开发人员希望确保他们的应用在所有受支持版本的 Windows 操作系统上会按预期运行。 消费者和企业在这方面进行了重点投资，他们希望确保所购买的应用仍可继续运行。 我们知道兼容性对购买决定起着主要作用。 发布新的 Windows 版本时，基于最佳做法编写良好的应用所需进行的代码改动较少且可减少碎片 - 这些应用在维护方面可减少工程投入成本，并可更快推向市场。

在 Windows 7 时间范围中，兼容性的实现方法非常被动。 在 Windows 8 中，我们一开始看待兼容性就迥然不同，在开发 Windows 时确保兼容性按设计实现，而不是事后补救。 到目前为止，Windows 10 是兼容性按设计实现的最佳版本的操作系统。 下面提供了我们实现兼容性的几个关键方法：
-   **应用遥测**：这有助于我们在 Windows 生态系统中了解应用的热门程度，以通知兼容性测试。
-   **ISV 合作关系**：直接与外部合作伙伴协作，以向他们提供数据并帮助修复我们的用户遇到的问题。
-   **设计审查，上游检测**：合作伙伴与功能团队合作，以减少对 Windows 进行重大更改的次数。 兼容性审查是我们的功能团队必须通过的门槛。
-   **通信**：严格控制 API 更改，同时改进通信。
-   **外部测试版和反馈循环**：Windows 预览体验成员会收到外部测试版，这有助于我们在向客户发布最终版本之前发现兼容性问题。 此反馈过程不仅可以显露 Bug，还可确保我们交付用户所需的功能。

## <a name="best-practices-for-app-compatibility"></a>应用兼容性的最佳做法

Microsoft 使用诊断和使用情况数据，以便识别和解决问题、改进我们的产品和服务，并为用户提供个性化体验。 我们所收集的使用情况数据还扩展到 Windows 生态系统中的电脑上运行的应用。 基于客户所使用的内容，我们会针对新版本的 Windows 操作系统生成用于测试这些应用、设备和驱动程序的列表。 到目前为止，Windows 10 是兼容性最佳的 Windows 版本，对于成千上万的热门应用而言，其兼容性超过 90%。 如果发现问题，Windows 兼容性团队通常会与 ISV 合作伙伴联系以提供反馈，以便我们双方可以一起合作来制定解决方案。 理想情况下，我们希望广大客户可以无缝地更新 Windows，同时不会丢失用于实现他们的生产效率或享受娱乐所依赖的操作系统或应用中的功能。

以下部分包含 Microsoft 建议的一些最佳做法，以便你可以确保你的应用与 Windows 10 兼容。

### <a name="windows-version-check"></a>Windows 版本检查

OS 版本已根据 Windows 10 递增。 这意味着内部版本号已更改为 10.0。 同过去一样，在 OS 版本更改后我们将尽力维护应用程序和设备兼容性。 对于大多数应用类别（不具有任何内核依存关系），所做的更改不会对应用功能造成负面影响，并且现有应用仍可继续在 Windows 10 上正常工作。

此更改的体现特定于应用。 这意味着专门检查 OS 版本的任何应用将获得更高的版本号，这可能会导致出现以下一个或多个情形：
-   应用安装程序可能无法安装应用，并且应用可能无法启动。
-   应用可能会变得不稳定或崩溃。
-   应用可能会生成错误消息，但仍继续正常运行。

某些应用执行版本检查，只向用户传递一条警告。 但有些应用与版本检查紧密相连（在驱动程序中，或在内核模式下，以避免检测）。 在这些情况下，如果找到的版本不正确，应用将失败。 不使用版本检查，我们建议采用以下方法之一：
-   如果应用依赖于特定的 API 功能，请确保找准正确的 API 版本。
-   确保通过 APISet 或其他公共 API 检测更改，不要将版本用作某些功能或修复的代理。 如果存在重大更改，而正常检查未检测到，则表明存在 Bug。
-   确保应用不会使用奇怪的方式检查版本，如通过注册表、文件版本、偏移、内核模式、驱动程序或其他方式。 如果应用确实需要检查版本，则使用 GetVersion API，它应该会返回主要版本号、次要版本号和内部版本号。
-   如果你使用的是 [GetVersion](/windows/win32/api/sysinfoapi/nf-sysinfoapi-getversion) API，请记住，此 API 的行为在 Windows 8.1 以后已发生变化。

如果你拥有反恶意软件或防火墙应用之类的应用，应通过常规反馈渠道和通过 Windows 预览体验计划进行工作。

### <a name="undocumented-apis"></a>未记录的 API

你的应用不应该调用未记录的 Windows API，也不应该依赖特定 Windows 文件导出或注册表项。 这会导致功能损坏、数据丢失和潜在的安全问题。 如果你的应用所需的功能不可用，可以借此机会通过常规反馈渠道和通过 Windows 预览体验计划提供反馈。

### <a name="develop-universal-windows-platform-uwp-and-centennial-apps"></a>开发通用 Windows 平台 (UWP) 和 Centennial 应用

我们鼓励所有 Win32 应用 ISV 开发[通用 Windows 平台 (UWP)](https://blogs.windows.com/windowsdeveloper/2016/02/25/an-update-on-the-developer-opportunity-and-windows-10/)，特别是 [Centennial](https://channel9.msdn.com/Events/Build/2015/2-692) 应用要与时俱进。 开发这些应用包有许多好处，而使用传统 Win32 安装程序则没有。 UWP 应用在 [Microsoft Store](https://blogs.windows.com/windowsdeveloper/2016/02/04/windows-store-trends-february-2016/) 中也受支持，因此它便于你将你的用户自动更新到一致版本，从而降低支持成本。

如果你的 Win32 应用类型不适用于 Centennial 模型，我们强烈建议你使用正确的安装程序，并确保经过全面测试。 安装程序是用户或客户对你的应用的首次体验，因此确保该安装程序运行良好。 安装程序时常运行不良，或者并未针对所有情形进行全面测试。 在你的用户开始操作之前，[Windows 应用认证工具包](https://developer.microsoft.com/windows/develop/app-certification-kit)可以帮助你测试 Win32 应用的安装和卸载、帮助标识未记录的 API 的使用情况以及其他与性能相关的基本最佳做法问题。

**最佳做法：**
-   使用同时适用 32 位和 64 位版本的 Windows 的安装程序。
-   设计你的安装程序，以便可在多种情形（用户或计算机级别）下运行。
-   将所有 Windows 可再发行组件保留在原始打包中 - 如果你重新打包这些可再发行组件，则可能会中断安装程序。
-   为你的安装程序安排开发时间 - 这些安装程序在软件开发生命周期过程中往往被作为可交付结果而遭忽略。

## <a name="optimized-test-strategies-and-flighting"></a>经优化的测试策略和外部测试版

Windows 操作系统外部测试版是指在向一般大众发布最终版本之前，提供给 Windows 预览体验成员的临时版本。 对这些临时版本进行外部测试的预览体验成员越多，我们收到有关生成质量、兼容性等的反馈就越多，这有助于改进最终版本的质量。 你可以参与此外部测试版计划，以确保你的应用能够在操作系统的迭代版本上按预期运行。 此外，我们还鼓励你提供有关这些外部测试版为你工作的方式、遇到的问题等反馈。

如果你的应用位于 Store 内，则可以通过 Store 对你的应用进行外部测试，这意味着会将你的应用提供给 Windows 预览体验成员进行安装。 用户可以安装你的应用，而你则可以在将你的应用发布给一般大众之前收到有关该应用的初步反馈。 以下部分概述了针对 Windows 外部测试版测试你的应用的步骤。

### <a name="step-1-become-a-windows-insider-and-participate-in-flighting"></a>步骤 1：成为 Windows 预览体验成员并参与外部测试版
作为 [Windows 预览体验成员](https://insider.windows.com/)，你可以帮助塑造 Windows 的未来 - 你的反馈将帮助我们改进该平台中的特性和功能。 这是一个充满活力的社区，在这里你可以联系其他爱好者、加入论坛、交换意见，以及了解即将推出的仅面向预览体验成员的活动。

由于你有权访问预览版的 Windows 10、Windows 10 移动版和最新的 Windows SDK 以及仿真器，因此你可以随心所欲地使用所有工具来开发出色的应用，并了解通用 Windows 平台和 Microsoft Store 中的新增功能。

这还是一个生成出色硬件的极好机会，借助预览版的硬件开发工具包，你可以开发适用于 Windows 的通用驱动程序。 在受支持的 IoT 开发板上还提供 IoT 核心版 Insider Preview，以便你可以使用通用 Windows 平台生成令人惊叹的连接解决方案。

在你成为 Windows 预览体验成员之前，请注意，参与对象仅面向如下用户：
-   希望试用尚在开发中的软件。
-   希望分享有关软件和平台的反馈。
-   不介意有大量的更新或一段时间后 UI 设计可能发生重大更改。
-   真正了解你的电脑，满意地解决问题、备份数据、格式化硬盘驱动器、重新安装操作系统或还原旧操作系统（如有必要）。
-   了解什么是 ISO 文件以及如何使用该文件。
-   不在日常使用的计算机或设备中安装该软件。

### <a name="step-2-test-your-scenarios"></a>步骤 2：测试方案

在你更新到外部测试版后，以下是一些帮助你开始测试和收集反馈的示例测试用例。 对于其中大多数测试，请确保包括 x86 和 AMD64 系统。
**全新安装测试：** 全新安装 Windows 10 时，确保你的应用功能完善。 如果你的应用未通过此测试和升级测试，则该问题很可能是由底层操作系统更改或应用中的 Bug 造成的。 如果在研究之后，发现前一种情形是问题的原因，请务必使用 Windows 预览体验计划提供反馈并协助制定解决方案。

**升级测试：** 在从低级别版本的 Windows（即 Windows 7 或 Windows 8.1）升级到 Windows 10 后，检查你的应用的运行状况。 你的应用在升级过程中不应该导致回退，并且在升级之后应继续按预期运行 - 这对于实现无缝升级体验至关重要。

**重新安装测试：** 确保在将电脑从低级别操作系统升级到 Windows 10 后，通过重新安装应用可以恢复应用功能。 如果应用未通过升级测试，并且尚无法排查到这些问题的原因，则重新安装可能会恢复丢失的功能。 通过了重新安装测试表明，该应用的某些部分可能尚未迁移到 Windows 10。

**操作系统\\设备功能测试：** 如果应用依赖操作系统中的特定功能，请确保应用按预期运行。 用于测试的常见区域如下所示（为确保覆盖范围，通常选择常用电脑机型）：
-   音频
-   USB 设备功能（键盘、鼠标、内存条、外部硬盘等）
-   Bluetooth
-   图形\\显示器（多监视器、投影、屏幕旋转等）
-   触摸屏（方向、屏幕键盘、触笔、手势等）
-   触摸板（左\\右按钮、点击、滚动等）
-   触笔（单击\\双击、按下、按住、橡皮擦等）
-   打印\\扫描
-   传感器（加速计、融合等）
-   照相机

### <a name="step-3-provide-feedback"></a>步骤 3:提供反馈

让我们知道你的应用在外部测试版中的执行情况。 当你在测试过程中发现应用中存在问题时，请通过合作伙伴门户（如果你有权访问），或通过你的 Microsoft 代表记录这些 Bug。 我们鼓励提供此信息，以便可以与我们一起为我们的用户生成优质体验。


## <a name="related-topics"></a>相关主题
[用于更新和升级的 Windows 10 服务选项](/windows/manage/introduction-to-windows-10-servicing)