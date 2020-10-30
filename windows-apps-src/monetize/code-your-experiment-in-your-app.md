---
description: 若要在具有 A/B 测试的通用 Windows 平台 (UWP) 应用中运行实验，必须在你的应用中为实验编码。
title: 针对实验为你的应用编码
ms.assetid: 6A5063E1-28CD-4087-A4FA-FBB511E9CED5
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, Microsoft Store Services SDK, A/B 测试, 实验
ms.localizationpriority: medium
ms.openlocfilehash: a5229be4d0ea2ce98ec10530458fe29af10fa7f0
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93033600"
---
# <a name="code-your-app-for-experimentation"></a>针对实验为你的应用编码

在 [合作伙伴中心创建项目和定义远程变量](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md)后，便可以将通用 WINDOWS 平台 (UWP) 应用中的代码更新为：
* 从合作伙伴中心接收远程变量值。
* 使用远程变量为你的用户配置应用体验。
* 将事件记录到合作伙伴中心，用于指示用户查看实验并执行了所需操作 (也称为 *转换* ) 。

若要向你的应用添加此行为，请使用 Microsoft Store Services SDK 所提供的 API。

以下部分介绍了获取试验并将事件记录到合作伙伴中心的一般过程。 在将应用程序编码为试验后，可以 [在合作伙伴中心定义试验](define-your-experiment-in-the-dev-center-dashboard.md)。 有关演示如何创建并运行实验的端到端过程的演练，请参阅[通过 A/B 测试来创建并运行你的第一个实验](create-and-run-your-first-experiment-with-a-b-testing.md)。

> [!NOTE]
> Microsoft Store Services SDK 中的某些试验 Api 使用 [异步模式](../threading-async/asynchronous-programming-universal-windows-platform-apps.md) 从合作伙伴中心检索数据。 这意味着，这些方法的部分执行可能会在调用这些方法后发生，以便你的应用的 UI 可以一边保持响应，一边完成操作。 异步模式要求你的应用在调用该 API 时使用 **async** 关键字和 **await** 运算符，如本文中的代码示例所示。 按照惯例，异步方法以 **Async** 结尾。

## <a name="configure-your-project"></a>配置项目

若要开始，请在开发计算机上安装 Microsoft Store Services SDK，并将必要的参考添加到项目。

1. [安装 Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk)。
2. 在 Visual Studio 中打开项目。
3. 在“解决方案资源管理器”中，展开项目节点、右键单击 **“引用”** ，然后选择 **“添加引用”** 。
3. 在 **引用管理器** 中，展开 " **通用 Windows** "，然后单击 " **扩展** "。
4. 在 Sdk 列表中，选中 " **Microsoft Engagement 框架** " 旁边的复选框，然后单击 **"确定"** 。

> [!NOTE]
> 本文中的代码示例假设代码文件中已有 **System.Threading.Tasks** 和 **Microsoft.Services.Store.Engagement** 命名空间的 **using** 语句。

## <a name="get-variation-data-and-log-the-view-event-for-your-experiment"></a>获取变体数据并记录实验的视图事件

在你的项目中，找到你想要在实验中修改的功能代码。 添加用于检索变体数据的代码，使用此数据修改正在测试的功能的行为，然后将实验的查看事件记录到合作伙伴中心的 A/B 测试服务。

你需要的具体代码将依应用而定，但以下示例演示了基本过程。 有关完整的代码示例，请参阅[利用 A/B 测试创建和运行你的第一个试验](create-and-run-your-first-experiment-with-a-b-testing.md)。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="ExperimentCodeSample":::

以下步骤详细描述了此过程的重要部分。

1. 声明一个 [StoreServicesExperimentVariation](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) 对象，该对象表示当前变体分配和一个 [StoreServicesCustomEventLogger](/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger) 对象，你将使用该对象将视图和转换事件记录到合作伙伴中心。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet1":::

2. 声明将分配给要检索的实验的[项目 ID](run-app-experiments-with-a-b-testing.md#terms) 的字符串变量。
    > [!NOTE]
    > 在 [合作伙伴中心创建项目](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md)时，你可以获取项目 ID。 以下所示的项目 ID 仅用作示例。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet2":::

3. 通过调用静态 [GetCachedVariationAsync](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getcachedvariationasync) 方法为你的实验获取当前缓存的变体分配，并将你的实验的项目 ID 传递给该方法。 此方法将返回可通过 [ExperimentVariation](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariationresult.experimentvariation) 属性访问变体分配的 [StoreServicesExperimentVariationResult](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariationresult) 对象。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet3":::

4. 检查 [IsStale](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.isstale) 属性，确定是否需要通过服务器的远程变体分配刷新缓存的变体分配。 如果它确实需要刷新，请调用静态 [GetRefreshedVariationAsync](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getrefreshedvariationasync) 方法检查服务器的更新的变体分配，并刷新本地缓存的变体。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet4":::

5. 使用 [StoreServicesExperimentVariation](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) 对象的 [GetBoolean](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getboolean)、[GetDouble](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getdouble)、[GetInt32](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getint32) 或 [GetString](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getstring) 方法获取变体分配的值。 在每个方法中，第一个参数是要检索的变体的名称 (此名称与你在 "合作伙伴中心) 中输入的变体名称相同。 第二个参数是此方法应返回的默认值（如果它无法从合作伙伴中心检索指定的值） (例如，如果没有网络连接) ，并且该变体的缓存版本不可用。

    以下示例使用 [GetString](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation.getstring) 获取名为 *buttonText* 的变量，并指定 **Grey Button** 的默认变量值。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet5":::

6. 在代码中，使用变量值修改正在测试的功能的行为。 例如，以下代码将 *buttonText* 值分配给你的应用中某个按钮的内容。 此示例假设已在项目的别处定义了该按钮。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet6":::

7. 最后，将实验的 [查看事件](run-app-experiments-with-a-b-testing.md#terms) 记录到合作伙伴中心的 A/B 测试服务。 将 ```logger``` 字段初始化为 [StoreServicesCustomEventLogger](/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger) 对象，然后调用 [LogForVariation](/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation) 方法。 传递 [StoreServicesExperimentVariation](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) 对象，该对象表示 (的当前变体分配，该对象提供有关事件到合作伙伴中心) 的上下文以及试验的视图事件的名称。 此名称必须与你在合作伙伴中心为试验输入的视图事件名称匹配。 你的代码应在用户开始查看实验的一部分变体时记录视图事件。

    以下示例演示了如何记录名为 **userViewedButton** 的视图事件。 在此示例中，实验的目标是获取单击应用中按钮的用户，以便在应用完成变体数据（在这种情况下，为按钮文本）检索并将它分配给该按钮的内容后，会记录视图事件。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet7":::

## <a name="log-conversion-events-to-partner-center"></a>向合作伙伴中心记录转换事件

接下来，添加在合作伙伴中心将 [转换事件](run-app-experiments-with-a-b-testing.md#terms) 记录到 A/B 测试服务的代码。 你的代码应在用户达到实验目标时记录转换事件。 你需要的具体代码将依应用而定，但在此处是一般步骤。 有关完整的代码示例，请参阅[利用 A/B 测试创建和运行你的第一个试验](create-and-run-your-first-experiment-with-a-b-testing.md)。

1. 在用户达到实验其中一个目标时运行的代码中，重新调用 [LogForVariation](/uwp/api/microsoft.services.store.engagement.storeservicescustomeventlogger.logforvariation) 方法，并传递 [StoreServicesExperimentVariation](/uwp/api/microsoft.services.store.engagement.storeservicesexperimentvariation) 对象和实验的转换事件名称。 此名称必须与你在合作伙伴中心为试验输入的转换事件名称之一匹配。

    以下示例从按钮的 **Click** 事件处理程序中记录名为 **userClickedButton** 的转换事件。 在此示例中，实验的目标是获取单击按钮的用户。

    :::code language="csharp" source="~/../snippets-windows/windows-uwp/monetize/StoreSDKSamples/cs/ExperimentExamples.cs" id="Snippet8":::

## <a name="next-steps"></a>后续步骤

在你的应用中为实验编码后，你可以随时执行以下步骤：
1. [在合作伙伴中心定义试验](define-your-experiment-in-the-dev-center-dashboard.md)。 创建可为 A/B 测试定义视图事件、转换事件和唯一变体的实验。
2. [在合作伙伴中心内运行和管理试验](manage-your-experiment.md)。


## <a name="related-topics"></a>相关主题

* [在合作伙伴中心创建项目和定义远程变量](create-a-project-and-define-remote-variables-in-the-dev-center-dashboard.md)
* [在合作伙伴中心中定义实验](define-your-experiment-in-the-dev-center-dashboard.md)
* [在合作伙伴中心中管理实验](manage-your-experiment.md)
* [通过 A/B 测试创建和运行你的第一个实验](create-and-run-your-first-experiment-with-a-b-testing.md)
* [通过 A/B 测试运行应用实验](run-app-experiments-with-a-b-testing.md)
