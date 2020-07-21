---
ms.assetid: F45E6F35-BC18-45C8-A8A5-193D528E2A4E
description: 了解如何在 UWP 应用中启用应用内购买和试用。
title: 应用内购买和试用
ms.date: 05/09/2018
ms.topic: article
keywords: windows 10, uwp, 应用内购买, IAP, 加载项, 试用, 消耗品, 耐用型, 订阅
ms.localizationpriority: medium
ms.openlocfilehash: 5396a8a6f02271647eb16d469853241b5717bd6e
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209773"
---
# <a name="in-app-purchases-and-trials"></a>应用内购买和试用

Windows SDK 提供可用于实现以下功能的 API，以从通用 Windows 平台 (UWP) 应用获取更多收益：

* **应用内购买**&nbsp;&nbsp;无论你的应用是否免费，你都可以直接从应用中销售内容或新的应用功能（例如解锁游戏的下一关卡）。

* **试用版功能**&nbsp;&nbsp;如果你[将应用配置为合作伙伴中心的免费试用版](../publish/set-app-pricing-and-availability.md#free-trial)，则可以通过在试用期间排除或限制某些功能来吸引客户购买完整版的应用。 也可以在客户购买你的应用之前，启用仅在试用期才会出现的某些功能，如横幅或水印。

本文提供应用内购买和试用在 UWP 应用内的工作原理概述。

<span id="choose-namespace" />

## <a name="choose-which-namespace-to-use"></a>选择要使用哪个命名空间

有两个不同的命名空间可用于向 UWP 应用添加应用内购买和试用功能，具体取决于应用面向 Windows 10 的哪个版本。 尽管这些命名空间中的 API 用于相同目标，但它们的设计完全不同，并且代码在两个 API 之间并不兼容。

* 在 Windows 10 1607 版中 **[开始&nbsp;&nbsp;](https://docs.microsoft.com/uwp/api/windows.services.store)** ，应用程序可以使用此命名空间中的 API 来实现应用程序内购买和试验。 如果应用项目在 Visual Studio 中面向 **Windows 10 周年纪念版（10.0；版本 14393）** 或更高版本，我们建议你使用此命名空间中的成员。 此命名空间支持最新的外接程序类型（如存储管理的可使用的外接程序），并旨在与合作伙伴中心和应用商店支持的产品和功能的未来类型兼容。 有关此命名空间的详细信息，请参阅本文中的[使用 Windows.Services.Store 命名空间进行应用内购买和试用](#api_intro)部分。

* **[Windows.applicationmodel.resources.core](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store)** &nbsp;&nbsp;windows 10 的所有版本也支持在此命名空间中进行应用内购买和试用的旧版 API。 有关 **Windows.ApplicationModel.Store** 命名空间的信息，请参阅[使用 Windows.ApplicationModel.Store 命名空间的应用内购买和试用](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md)。

> [!IMPORTANT]
> **Windows.ApplicationModel.Store** 命名空间不再更新新功能，如果可能，建议你的应用改用 **Windows.Services.Store** 命名空间。 使用[桌上型计算机](https://developer.microsoft.com/windows/bridges/desktop)的 windows 桌面应用程序或在合作伙伴中心使用开发沙盒的应用程序或游戏（例如，与 Xbox Live 集成的任何游戏的情况）不支持**windows.applicationmodel.resources.core**命名空间。

<span id="concepts" />

## <a name="basic-concepts"></a>基本概念

应用商店中提供的每一个项目通常称为*产品*。 大多数开发人员只处理以下类型的产品：*应用*和*加载项*。

加载项是指你在应用的上下文中向客户提供的产品或功能：例如，要在应用或游戏中使用的货币、游戏的新地图或武器、使用无广告应用的功能，或数字内容，如应用的音乐或视频（前提是应用能够提供此类内容）。 每个应用和加载项都有关联的许可证，用于指示用户是否有权使用该应用或加载项。 如果用户有权将该应用或加载项作为试用来使用，则许可证还提供关于该试用的其他信息。

若要为你的应用中的客户提供外接程序，必须[在合作伙伴中心定义适用于你的应用程序的外接](../publish/add-on-submissions.md)程序，以便存储区知道它。 然后，应用可以使用 **Windows.Services.Store** 或 **Windows.ApplicationModel.Store** 命名空间中的 API 提供加载项，作为应用内购买向用户销售。

UWP 应用可提供以下类型的加载项。

| 加载项类型 |  说明  |
|---------|-------------------|
| 耐用品  |  [在合作伙伴中心指定](../publish/enter-iap-properties.md)的生存期内保持的附加项。 <p/><p/>默认情况下，耐用型加载项永远不会过期，在此情况下只能购买它们一次。 如果你为加载项指定特定的持续时间，则用户可以在它过期后重新购买该加载项。 |
| 开发人员管理的易耗品  |  可以购买、使用并在用完后再次购买的加载项。 你负责跟踪用户拥有的加载项所表示商品的余量。<p/><p/>当用户使用与该加载项关联的任何商品时，你负责维护加载项所表示的商品的用户余量，并在用户消耗完所有商品后向 Microsoft Store 将加载项购买报告为已完成。 在你的应用将之前的加载项购买报告为已完成前，用户无法再次购买该加载项。 <p/><p/>例如，如果你的加载项表示游戏中的 100 个硬币，并且用户消耗了 10 个硬币，则你的用户或服务必须为该用户保留 90 个硬币的新剩余余额。 在用户消耗完全部 100 个硬币后，你的应用必须将加载项报告为已完成，然后用户才可以再次购买 100 个硬币的加载项。    |
| 应用商店管理的易耗品  |  可以随时购买、使用并再次购买的加载项。 Microsoft Store 会跟踪用户拥有的加载项所表示商品的余量。<p/><p/>当用户消耗任何与加载项关联的商品时，你负责向 Microsoft Store 报告这些商品已完成，然后 Microsoft Store 会更新用户的余量。 用户可以根据需要多次购买加载项（他们不需要首先使用这些项目）。 你的应用可以随时查询用户的当前余量。 <p/><p/> 例如，如果你的加载项在游戏中表示 100 个硬币的初始数量，并且用户消耗了 50 个硬币，则你的应用将向 Microsoft Store 报告 50 个单位的加载项已完成，然后 Microsoft Store 会更新剩余余额。 如果用户再次购买你的加载项（增加 100 个硬币），他们现在总共有 150 个硬币。 <p/><p/>**注意**&nbsp;&nbsp;若要使用 Microsoft Store 管理的易耗品，应用必须在 Visual Studio 中面向 **Windows 10 周年纪念版（10.0；版本 14393）** 或更高版本，并且必须使用 **Windows.Services.Store** 命名空间，而不是 **Windows.ApplicationModel.Store** 命名空间。  |
| 订阅 | 一种持久型加载项，客户需要按重复时间间隔付费才能继续使用此加载项。 客户可以随时取消订阅，以免日后继续产生费用。 <p/><p/>**注意**&nbsp;&nbsp;若要使用订阅加载项，应用必须在 Visual Studio 中面向 **Windows 10 周年纪念版（10.0；版本 14393）** 或更高版本，并且必须使用 **Windows.Services.Store** 命名空间，而不是 **Windows.ApplicationModel.Store** 命名空间。  |

<span />

> [!NOTE]
> 其他类型的加载项，如使用软件包的耐用型加载项（也称为可下载内容或 DLC），仅适用于有限的开发人员群体，本文档中未涉及。

<span id="api_intro" />

## <a name="in-app-purchases-and-trials-using-the-windowsservicesstore-namespace"></a>使用 Windows.Services.Store 命名空间的应用内购买和试用

此部分概述了 [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) 命名空间的重要任务和概念。 此命名空间仅适用于面向 Visual Studio 中的 **Windows 10 周年纪念版（10.0；版本 14393）** 或更高版本的应用（这对应于 Windows 10 版本 1607）。 我们建议应用使用 **Windows.Services.Store** 命名空间，而非使用 [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store) 命名空间（如可能）。 有关 **Windows.ApplicationModel.Store** 命名空间的信息，请参阅[本文](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md)。

**本部分内容**

* [视频](#video)
* [StoreContext 类入门](#get-started-storecontext)
* [实现应用内购买](#implement-iap)
* [实现试用功能](#implement-trial)
* [测试应用内购买或试用版实现](#testing)
* [应用内购买的收据](#receipts)
* [将 StoreContext 类用于桌面桥](#desktop)
* [产品、Sku 和可用性](#products-skus)
* [存储 Id](#store-ids)

<span id="video" />

### <a name="video"></a>视频

请观看下面的视频，了解如何在你的应用中使用 [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) 命名空间实现应用内购买。
<br/>
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/One-Dev-Minute/Adding-In-App-Purchases-to-Your-UWP-App/player]

<span id="get-started-storecontext" />

### <a name="get-started-with-the-storecontext-class"></a>StoreContext 类入门

**Windows.Services.Store** 命名空间的主要入口点是 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 类。 此类提供的方法可用于获取当前应用及其可用加载项的信息、获取当前应用或其加载项的许可证信息、为当前用户购买应用或加载项以及执行其他任务。 若要获取 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 对象，请执行以下操作之一：

* 在单用户应用（即，仅在启动该应用的用户上下文中运行的应用），使用静态 [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getdefault) 方法获取一个 **StoreContext** 对象，你可以使用该对象访问与用户的 Microsoft Store 相关数据。 大多数通用 Windows 平台 (UWP) 应用是单用户应用。

  ```csharp
  Windows.Services.Store.StoreContext context = StoreContext.GetDefault();
  ```

* 在[多用户应用](../xbox-apps/multi-user-applications.md)中，使用静态 [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getforuser) 方法获取一个 **StoreContext** 对象，你可以使用该对象访问特定用户（使用该应用时使用 Microsoft 帐户登录的用户）的 Microsoft Store 相关数据。 以下示例为第一个可用用户获取 **StoreContext** 对象。

  ```csharp
  var users = await Windows.System.User.FindAllAsync();
  Windows.Services.Store.StoreContext context = StoreContext.GetForUser(users[0]);
  ```

> [!NOTE]
> 使用[桌面桥](https://developer.microsoft.com/windows/bridges/desktop)的 Windows 桌面应用程序必须执行额外步骤来配置 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 对象，然后才可以使用此对象。 有关详细信息，请参阅[此部分](#desktop)。

拥有 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 对象后，可开始调用此对象的方法来获取当前应用以及加载项的应用商店产品信息、检索当前应用及其加载项的许可证信息、为当前用户购买应用或加载项以及执行其他任务。 有关可使用此对象执行的常见任务的详细信息，请参阅以下文章：

* [获取应用和外接程序的产品信息](get-product-info-for-apps-and-add-ons.md)
* [获取应用和外接程序的许可证信息](get-license-info-for-apps-and-add-ons.md)
* [启用应用程序和外接程序的应用内购买](enable-in-app-purchases-of-apps-and-add-ons.md)
* [启用可耗用的附加产品购买](enable-consumable-add-on-purchases.md)
* [为应用启用订阅外接程序](enable-subscription-add-ons-for-your-app.md)
* [实现应用的试用版](implement-a-trial-version-of-your-app.md)

有关演示如何使用 **StoreContext** 和 **Windows.Services.Store** 命名空间中的其他类型的示例应用，请参阅[应用商店示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)。

<span id="implement-iap" />

### <a name="implement-in-app-purchases"></a>实现应用内购买

若要使用 **Windows.Services.Store** 命名空间在应用中向客户提供应用内购买：

1. 如果你的应用程序提供了客户可以购买的外接程序，请[在合作伙伴中心创建适用于你的应用程序的加载项提交](https://docs.microsoft.com/windows/uwp/publish/add-on-submissions)。

2. 在应用中编写代码以[检索应用或应用提供的加载项的产品信息](get-product-info-for-apps-and-add-ons.md)，然后[确定许可证是否处于活动状态](get-license-info-for-apps-and-add-ons.md)（即，用户是否具有使用该应用或加载项的许可证）。 如果许可证不处于活动状态，请显示提供应用或加载项以作为应用内购买向用户销售的 UI。

3. 如果用户选择购买应用或加载项，请使用相应的方法购买该产品：

    * 如果用户正在购买应用或耐用型加载项，请按照[启用应用或加载项的应用内购买](enable-in-app-purchases-of-apps-and-add-ons.md)中的说明操作。
    * 如果用户正在购买易耗型加载项，请按照[启用易耗型加载项购买](enable-consumable-add-on-purchases.md)中的说明操作。
    * 如果用户正在购买订阅加载项，请按照[为应用启用订阅加载项](enable-subscription-add-ons-for-your-app.md)中的说明操作。

4. 按照本文中的[测试指南](#testing)测试实现。

<span id="implement-trial" />

### <a name="implement-trial-functionality"></a>实现试用功能

若要使用 **Windows.Services.Store** 命名空间排除或限制应用的试用版中的功能：

1. [将应用配置为合作伙伴中心的免费试用版](../publish/set-app-pricing-and-availability.md#free-trial)。

2. 在应用中编写代码以[检索应用或应用提供的加载项的产品信息](get-product-info-for-apps-and-add-ons.md)，然后[确定与应用关联的许可证是否是试用许可证](get-license-info-for-apps-and-add-ons.md)。

3. 如果是试用，则排除或限制应用中的特定功能，然后在用户购买完整许可证时启用这些功能。 有关详细信息和代码示例，请参阅[实现应用的试用版](implement-a-trial-version-of-your-app.md)。

4. 按照本文中的[测试指南](#testing)测试实现。

<span id="testing" />

### <a name="test-your-in-app-purchase-or-trial-implementation"></a>测试你的应用内购买或试用实现

如果你的应用使用 **Windows.Services.Store** 命名空间中的 API 实现应用内购买和试用功能，则你必须将应用发布到 Microsoft Store，然后将此应用下载到开发设备上以使用其许可证进行测试。 请按照下述过程测试你的代码：

1. 如果你的应用尚未在应用商店中发布并可用，请确保你的应用满足[Windows 应用认证包](https://developer.microsoft.com/windows/develop/app-certification-kit)的最低要求，在合作伙伴中心[提交你的应用](https://docs.microsoft.com/windows/uwp/publish/app-submissions)，并确保你的应用程序通过认证过程。 在测试应用期间，你可以[将应用配置为在 Microsoft Store 中隐藏](https://docs.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability)。 请注意[包航班](../publish/package-flights.md)的正确配置。 无法下载错误配置的包航班。

2. 接下来，确保已完成以下操作：

    * 在使用 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 类和 **Windows.Services.Store** 命名空间中的其他相关类型的应用中编写代码以实现[应用内购买](#implement-iap)或[试用功能](#implement-trial)。
    * 如果你的应用程序提供了客户可以购买的外接程序，请[在合作伙伴中心为你的应用程序创建加载项提交](https://docs.microsoft.com/windows/uwp/publish/add-on-submissions)。
    * 如果要在试用版应用中排除或限制某些功能，请[在合作伙伴中心将应用配置为免费试用版](../publish/set-app-pricing-and-availability.md#free-trial)。

3. 在项目在 Visual Studio 中打开的情况下，单击**项目菜单**、指向 **Microsoft Store**，然后单击**将应用与 Microsoft Store 关联**。 完成向导中的说明，将应用程序项目与你要用于测试的伙伴中心帐户中的应用程序相关联。
    > [!NOTE]
    > 如果你未将项目与 Microsoft Store 中的应用关联，则 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 方法会将其返回值的 **ExtendedError** 属性设置为错误代码值 0x803F6107。 此值指示应用商店并不了解关于该应用的任何信息。
4. 如果您尚未关联，请从应用商店安装您在上一步中指定的应用、运行该应用一次，然后关闭此应用。 这可确保将应用的有效许可证安装到你的开发设备。

5. 在 Visual Studio 中，开始运行或调试你的项目。 你的代码应从与你的本地项目关联的应用商店应用中检索应用和加载项数据。 如果系统提示你重新安装该应用，请按照说明操作，然后运行或调试项目。
    > [!NOTE]
    > 完成这些步骤后，你可以继续更新应用代码并在开发计算机上调试更新的项目，而无需将新的应用包提交到 Microsoft Store。 获取将要用于测试的本地许可证后，只需要将应用的应用商店版本下载到开发计算机。 完成测试后，只需要将新的应用包提交到应用商店，并允许客户使用应用内购买或应用中的试用相关功能。

如果你的应用使用的是 **Windows.ApplicationModel.Store** 命名空间，则在将应用提交到 Microsoft Store 之前，你可以在应用中使用 [CurrentAppSimulator](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) 类模拟许可证信息进行测试。 有关详细信息，请参阅[CurrentApp 和 CurrentAppSimulator 类入门](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md#get-started-with-the-currentapp-and-currentappsimulator-classes)。  

> [!NOTE]
> **Windows.Services.Store** 命名空间不提供可用于在测试期间模拟许可证信息的类。 如果使用 **Windows.Services.Store** 命名空间实现应用内购买或试用，你必须将应用发布到 Microsoft Store，然后将此应用下载到开发设备上以使用其许可证进行测试（如上所述）。

<span id="receipts" />

### <a name="receipts-for-in-app-purchases"></a>应用内购买的收据

**Windows.Services.Store** 命名空间不在应用代码中提供可用于获取成功购买的交易收据的 API。 这是与使用 **Windows.ApplicationModel.Store** 命名空间的应用不同的体验，该命名空间可[使用客户端 API 检索交易收据](use-receipts-to-verify-product-purchases.md)。

如果使用 **Windows.Services.Store** 命名空间实现应用内购买并且希望验证给定客户是否已购买某个应用或加载项，可使用 [Microsoft Store collection REST API](query-for-products.md) 中的[查询产品方法](view-and-grant-products-from-a-service.md)。 此方法的返回数据确认指定客户是否具有对给定产品的权利，并为用户获取产品的交易提供数据。 Microsoft Store 收集 API 使用 Azure AD 身份验证检索此信息。

<span id="desktop" />

### <a name="using-the-storecontext-class-with-the-desktop-bridge"></a>通过桌面桥使用 StoreContext 类

使用[桌面桥](https://developer.microsoft.com/windows/bridges/desktop)的桌面应用程序可使用 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 类实现应用内购买和试用。 但是，如果你有 Win32 桌面应用程序或具有与呈现框架（如 WPF 应用程序）相关联的窗口句柄 (HWND) 的桌面应用程序，则该应用程序必须配置 **StoreContext** 对象以为该对象所显示的模式对话框指定哪个应用程序窗口是所有者窗口。

许多 **StoreContext** 成员（以及通过 **StoreContext** 对象访问的其他相关类型的成员）针对应用商店相关的操作（如购买产品）向用户显示模式对话框。 如果桌面应用程序未配置 **StoreContext** 对象以指定模式对话框的所有者窗口，则此对象将返回不准确的数据或错误。

若要在使用桌面桥的桌面应用程序中配置 **StoreContext** 对象，请按照这些步骤操作。

1. 进行以下操作之一，使你的应用可以访问 [IInitializeWithWindow](https://docs.microsoft.com/windows/desktop/api/shobjidl_core/nn-shobjidl_core-iinitializewithwindow) 接口：

    * 如果应用程序使用托管语言（如 C# 或 Visual Basic）编写，则在应用代码中使用 **ComImport** 属性声明 [IInitializeWithWindow](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.comimportattribute) 接口，如以下 C# 示例所示。 此示例假设代码文件具有 **System.Runtime.InteropServices** 命名空间的 **using** 语句。

        ```csharp
        [ComImport]
        [Guid("3E68D4BD-7135-4D10-8018-9FB6D9F33FA1")]
        [InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
        public interface IInitializeWithWindow
        {
            void Initialize(IntPtr hwnd);
        }
        ```

    * 如果应用程序是采用 C++ 编写的，请在代码中添加对 shobjidl.h 头文件的引用。 此头文件包含 **IInitializeWithWindow** 接口的声明。

2. 按照本文前面部分所述，使用 [GetDefault](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) 方法（或 [GetForUser](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getdefault)，如果你的应用是[多用户应用](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getforuser)）获取 [StoreContext](../xbox-apps/multi-user-applications.md) 对象并将此对象转换为 [IInitializeWithWindow](https://docs.microsoft.com/windows/desktop/api/shobjidl_core/nn-shobjidl_core-iinitializewithwindow) 对象。 然后，调用 [IInitializeWithWindow.Initialize](https://docs.microsoft.com/windows/desktop/api/shobjidl_core/nf-shobjidl_core-iinitializewithwindow-initialize) 方法，并传递你希望成为所有者（对于 **StoreContext** 方法显示的任何模式对话框）的窗口的句柄。 以下示例 C# 显示如何将应用主窗口的句柄传递到该方法。
    ```csharp
    StoreContext context = StoreContext.GetDefault();
    IInitializeWithWindow initWindow = (IInitializeWithWindow)(object)context;
    initWindow.Initialize(System.Diagnostics.Process.GetCurrentProcess().MainWindowHandle);
    ```

<span id="products-skus" />

### <a name="products-skus-and-availabilities"></a>产品、SKU 和可用性

应用商店中的每个产品都至少有一个 *SKU*，而每个 SKU 都至少有一个*可用性*。 这些概念与合作伙伴中心内的大多数开发人员都是抽象的，大多数开发人员绝不会为其应用或外接程序定义 Sku 或可用性。 但是，由于 **Windows.Services.Store** 命名空间中的 Microsoft Store 产品的对象模型包括 SKU 和可用性，因此大致了解这些概念对于某些情况可能很有帮助。

| Object |  说明  |
|---------|-------------------|
| 产品  |  *产品*是指在 Microsoft Store 中提供的任何类型的产品，包括应用或加载项。 <p/><p/> Microsoft Store 中的每个产品都有相应的 [StoreProduct](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct) 对象。 此类提供可用于访问数据的属性，如产品的应用商店 ID、应用商店列表的图像和视频以及定价信息。 它还提供可用于购买产品的方法。 |
| SKU |  *SKU* 是带有其自己的说明、价格和其他独特产品详细信息的产品特定版本。 每个应用或加载项都有默认的 SKU。 大多数开发人员拥有针对一个应用的多个 SKU 的唯一情况是，他们要发布应用的完整版和试用版（在应用商店目录中，其中每一个版本都是同一个应用的不同 SKU）。 <p/><p/> 某些发布者能够定义他们自己的 SKU。 例如，大型游戏发布者可能发布具有以下两个 SKU 的游戏：一个 SKU 在不允许红色血液的市场中显示绿色血液，另一个 SKU 在所有其他市场中显示红色血液。 另外，销售数字视频内容的发布者可能针对一个视频发布两个 SKU，一个 SKU 用于高清版本，另一个 SKU 用于标清版本。 <p/><p/> Microsoft Store 中的每个 SKU 都有相应的 [StoreSku](https://docs.microsoft.com/uwp/api/windows.services.store.storesku) 对象。 每个 [StoreProduct](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct) 都有可用于访问产品 SKU 的 [Skus](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.skus) 属性。 |
| 可用性  |  *可用性*是带有自己独特定价信息的 SKU 的特定版本。 每个 SKU 都有默认的可用性。 某些发布者能够定义他们自己的可用性来为给定 SKU 引入不同的价格选项。 <p/><p/> Microsoft Store 中的每个可用性都有相应的 [StoreAvailability](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability) 对象。 每个 [StoreSku](https://docs.microsoft.com/uwp/api/windows.services.store.storesku) 都有可用于访问 SKU 可用性的 [Availabilities](https://docs.microsoft.com/uwp/api/windows.services.store.storesku.availabilities) 属性。 对于大多数开发人员来说，每个 SKU 都有单个默认可用性。  |

<span id="store_ids" />

### <a name="store-ids"></a>应用商店 ID

Microsoft Store 中的每个应用、加载项或其他产品都有关联的 **Store ID**（有时也称为*产品 Store ID*）。 许多 API 都需要 Store ID 才能执行有关应用或加载项的操作。

应用商店中的任何产品的应用商店 ID 都是 12 个字符的字母数字字符串，例如 ```9NBLGGH4R315```。 有多种不同的方法可用于获取 Microsoft Store 中某个产品的 Store ID：

* 对于应用程序，可以在合作伙伴中心的 "[应用标识" 页](../publish/view-app-identity-details.md)上获取存储 ID。
* 对于外接程序，你可以在合作伙伴中心的外接程序的 "概述" 页上获取存储 ID。
* 对于任何产品，还可以使用表示此产品的 [StoreProduct](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.storeid) 对象的 [StoreId](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct) 属性以编程方式获取 Store ID。

对于具有 SKU 和可用性的产品，SKU 和可用性还有自己的具有不同格式的 Store ID。

| Object |  应用商店 ID 格式  |
|---------|-------------------|
| SKU |  对于 SKU，Store ID 的格式为 ```<product Store ID>/xxxx```，其中 ```xxxx``` 是 4 个字符的字母数字字符串，用于标识产品的 SKU。 例如， ```9NBLGGH4R315/000N```。 此 ID 由 [StoreSku](https://docs.microsoft.com/uwp/api/windows.services.store.storesku.storeid) 对象的 [StoreId](https://docs.microsoft.com/uwp/api/windows.services.store.storesku) 属性返回，并且有时称为 *SKU 应用商店 ID*。 |
| 可用性  |  对于可用性，Store ID 的格式为 ```<product Store ID>/xxxx/yyyyyyyyyyyy```，其中 ```xxxx``` 是标识产品 SKU 的 4 字符数字字母字符串，而 ```yyyyyyyyyyyy``` 是标识 SKU 可用性的 12 字符字母数字字符串。 例如， ```9NBLGGH4R315/000N/4KW6QZD2VN6X```。 此 ID 由 [StoreAvailability](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability.storeid) 对象的 [StoreId](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability) 属性返回，并且有时称为*可用性应用商店 ID*。  |

<span id="product-ids" />

## <a name="how-to-use-product-ids-for-add-ons-in-your-code"></a>如何在代码中使用加载项的产品 ID

如果要在应用的上下文中向客户提供外接程序，则在合作伙伴中心[创建外接程序提交](../publish/add-on-submissions.md)时，必须为外接程序[输入唯一的产品 ID](../publish/set-your-add-on-product-id.md#product-id) 。 你可以使用此产品 ID 在代码中引用加载项，但可以使用产品 ID 的特定方案取决于你在应用中实现应用内购买所用的命名空间。

> [!NOTE]
> 在合作伙伴中心为外接程序输入的产品 ID 不同于外接程序的[商店 id](#store-ids)。 存储 ID 由合作伙伴中心生成。

### <a name="apps-that-use-the-windowsservicesstore-namespace"></a>使用 Windows.Services.Store 命名空间的应用

如果应用使用的是 **Windows.Services.Store** 命名空间，你可以借助产品 ID 轻松识别表示加载项的 [StoreProduct](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreProduct) 或表示加载项许可证的 [StoreLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense)。 产品 ID 由 [StoreProduct.InAppOfferToken](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreProduct.InAppOfferToken) 和 [StoreLicense.InAppOfferToken](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense.InAppOfferToken) 属性公开。

> [!NOTE]
> 虽然产品 ID 是在代码中识别加载项的有用方式，但 **Windows.Services.Store** 命名空间中的大多数操作使用的是加载项的[ Store ID](#store-ids)，而不是产品 ID。 例如，要以编程方式检索某应用的一个或多个已知加载项，可以向 [GetStoreProductsAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getstoreproductsasync) 方法传递加载项的 Store ID（而不是产品 ID）。 同样，要报告易耗型加载项已完成，可以向 [ReportConsumableFulfillmentAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.reportconsumablefulfillmentasync) 方法传递加载项的 Store ID（而不是产品 ID）。

### <a name="apps-that-use-the-windowsapplicationmodelstore-namespace"></a>使用 Windows.ApplicationModel.Store 命名空间的应用

如果你的应用程序使用**windows.applicationmodel.resources.core**命名空间，你将需要使用在合作伙伴中心为大多数操作分配的产品 ID。 例如：

* 使用产品 ID 识别表示加载项的 [ProductListing](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.productlisting) 或表示加载项许可证的 [ProductLicense](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.productlicense)。 产品 ID 由 [ProductListing.ProductId](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.productlisting.ProductId) 和 [ProductLicense.ProductId](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.productlicense.ProductId) 属性公开。

* 为用户购买加载项时，请使用 [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync) 方法指定产品 ID。 有关详细信息，请参阅[启用应用内产品购买](enable-in-app-product-purchases.md)。

* 报告易耗型加载项已完成时，请使用 [ReportConsumableFulfillmentAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.reportconsumablefulfillmentasync) 方法指定产品 ID。 有关详细信息，请参阅[启用易耗型应用内产品购买](enable-consumable-in-app-product-purchases.md)。

## <a name="related-topics"></a>相关主题

* [获取应用和外接程序的产品信息](get-product-info-for-apps-and-add-ons.md)
* [获取应用和外接程序的许可证信息](get-license-info-for-apps-and-add-ons.md)
* [启用应用程序和外接程序的应用内购买](enable-in-app-purchases-of-apps-and-add-ons.md)
* [启用可耗用的附加产品购买](enable-consumable-add-on-purchases.md)
* [为应用启用订阅外接程序](enable-subscription-add-ons-for-your-app.md)
* [实现应用的试用版](implement-a-trial-version-of-your-app.md)
* [存储操作的错误代码](error-codes-for-store-operations.md)
* [使用 Windows.applicationmodel.resources.core 命名空间进行应用内购买和试验](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md)
