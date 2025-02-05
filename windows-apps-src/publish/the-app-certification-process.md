---
description: 当你完成应用提交的创建并单击提交到 Microsoft Store 时，提交将进入认证步骤。
title: 应用认证过程
ms.assetid: 0DCB4344-224D-4E5A-899F-FF7A89F23DBC
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10，uwp，发布，预处理，证书，发布，挂起，提交，发布，状态，时间
ms.localizationpriority: medium
ms.openlocfilehash: 414a182074f03256c3c66492eab21e574e1a960c
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93034980"
---
# <a name="the-app-certification-process"></a>应用认证过程

当你完成应用提交的创建并单击 **提交到 Microsoft Store** 时，提交将进入认证步骤。 此过程通常在几小时内完成，但在某些情况下可能需要最多三个工作日。 提交通过认证后，客户可能需要长达24小时的时间才能查看应用程序的新提交列表，或使用包的更改进行更新的提交。 如果更新只更改商店列表详细信息，则发布过程将在不到一小时内完成。  提交发布后，会收到通知，仪表板中的应用状态将 **在应用商店中** 。

## <a name="preprocessing"></a>预处理

成功上载应用包并提交应用进行认证后，应用包将排队接受测试。 如果在预处理过程中检测到任何错误，我们会显示一条消息。 有关可能错误的详细信息，请参阅[解决提交错误](resolve-submission-errors.md)。

## <a name="certification"></a>认证

在此阶段中，将执行多个测试：

-   **安全性测试：** 第一项测试检查应用包中是否存在病毒和恶意软件。 如果应用未能通过这项测试，你将需要运行最新防病毒软件检查开发系统，然后在安全的系统上重新生成你的应用包。
-   **技术合规性测试：** 由 Windows 应用认证工具包测试技术合规性。 （你应该始终确保先[使用 Windows 应用认证工具包测试应用](../debug-test-perf/windows-app-certification-kit.md)，然后再将其提交至应用商店。）
-   **内容合规性：** 测试所需的时间会有所不同，具体取决于应用的复杂程度、包含的视觉内容量以及近期提交的应用数量。 请务必在[认证说明](notes-for-certification.md)页中提供测试者需注意的全部信息。

认证流程完成后，你将会收到一份认证报告，告知你的应用是否已通过认证。 如果应用未通过认证，该报告将指出未能通过哪项测试，或者未能满足哪项[策略](store-policies.md)。 修复问题后，你可以为应用创建新提交以再次开始认证过程。

## <a name="release"></a>发布

当你的应用程序通过认证后，就可以移动到 **发布** 过程。

- 如果已指明应该尽快发布提交 (默认选项) ，发布过程将立即开始。
- 如果这是您第一次发布应用程序，并且您在 " [计划](configure-precise-release-scheduling.md#release)" 部分中指定了 " **发布日期** "，则该应用程序将根据您的 **发布日期** 选项变为可用。
- 如果你已使用 [发布保留选项](manage-submission-options.md#publishing-hold-options) 来指定在特定日期之前不应将其释放，我们将一直等到该日期开始发布过程，除非你选择 " **更改发布日期** "。
- 如果已使用 [发布保留选项](manage-submission-options.md#publishing-hold-options) 来指定要手动发布提交，则在选择 " **立即发布** " 之前将不会启动发布过程 (或选择 " **更改发布日期** "，然后选择特定的日期) 。


## <a name="publishing"></a>发布

你的应用包已经过数字签名，目的是防止它们在发布后遭到篡改。 一旦开始执行此阶段，你将再也无法取消提交或更改其发布日期。

对于包含对应用包的更改的新应用和更新，发布过程将在24小时内完成。 对于仅更改诸如商店列表详细信息等选项的更新，但不更改应用程序包，发布过程将花费不到一小时。

如果你的应用处于发布阶段，你的应用程序的 "状态" 列中的 " **显示详细信息** " 链接可让你了解你的新包和商店列表详细信息可供每个受支持的 OS 版本的客户使用。 尚未完成的步骤将显示 **挂起** 。 在此过程完成之前，你的应用程序将保留在发布阶段，这意味着新包和/或列表详细信息可供你的应用程序的所有潜在客户使用。

## <a name="in-the-store"></a>已在 Microsoft Store 

在成功完成上述步骤后，提交的状态将从 **正在发布** 更改为 **已在 Microsoft Store** 。 你的提交将在 Microsoft Store 中提供给客户以供其下载（除非你选择了另外的[可发现性](choose-visibility-options.md#discoverability)选项）。 

> [!NOTE]
> 我们还会在应用发布后对应用进行抽查，以便可以找出潜在问题并确保你的应用符合所有 [Microsoft Store 策略](store-policies.md)。 如果我们发现任何问题，将通知你该问题及其解决方法（如果适用）或是否已从应用商店中删除。

 

 

 




