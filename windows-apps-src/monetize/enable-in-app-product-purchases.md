---
Description: 无论你的应用是否免费，你都可以直接从应用中销售内容、其他应用或新的应用功能（例如解锁游戏的下一关）。 下面我们显示了如何在应用中启用这些产品。
title: 启用应用内产品购买
ms.assetid: D158E9EB-1907-4173-9889-66507957BD6B
keywords: uwp, 加载项, 应用内购买, IAP, Windows.ApplicationModel.Store
ms.date: 08/25/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: ab2b5937746f006c0f5efd296e9b4a4f3cb82696
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89171571"
---
# <a name="enable-in-app-product-purchases"></a>启用应用内产品购买

无论你的应用是否免费，你都可以直接从应用中销售内容、其他应用或新的应用功能（例如解锁游戏的下一关）。 下面我们显示了如何在应用中启用这些产品。

> [!IMPORTANT]
> 本文介绍如何使用 [Windows.ApplicationModel.Store](/uwp/api/windows.applicationmodel.store) 命名空间的成员来支持应用内产品购买。 此命名空间不再更新新功能，我们建议你使用 [Windows.Services.Store](/uwp/api/windows.services.store) 命名空间。 **Windows 服务**命名空间支持最新的加载项类型，如存储管理的可使用的加载项和订阅，旨在与合作伙伴中心和应用商店支持的未来类型的产品和功能兼容。 **Windows.Services.Store** 命名空间在 Windows 10 版本 1607 中引入，它仅可用于面向 **Windows 10 周年纪念版（10.0；版本 14393）或 Visual Studio** 更高版本的项目中。 有关使用 **Windows.Services.Store** 命名空间启用应用内产品购买的更多信息，请参阅[此文章](enable-in-app-purchases-of-apps-and-add-ons.md)。

> [!NOTE]
> 试用版应用不能提供应用内产品。 仅当使用试用版应用的客户购买了完整版应用后，他们才可以购买应用内产品。

## <a name="prerequisites"></a>必备条件

-   可添加供客户购买的功能的 Windows 应用。
-   首次编码和测试新的应用内产品时，必须使用 [CurrentAppSimulator](/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) 对象而不是 [CurrentApp](/uwp/api/Windows.ApplicationModel.Store.CurrentApp) 对象。 这样，你可以使用对许可证服务器的模拟调用验证许可证逻辑，而不是调用实时服务器。 要执行此操作，需要在% userprofile% \\ AppData \\ 本地 \\ 包 \\ &lt; 包名称 &gt; \\ LocalState \\ Microsoft \\ Windows Store \\ ApiData 中自定义名为 WindowsStoreProxy.xml 的文件。 Microsoft Visual Studio 仿真器会在你首次运行应用时创建此文件，你也可以在运行时加载一个自定义文件。 有关详细信息，请参阅[将 WindowsStoreProxy.xml 文件与 CurrentAppSimulator 一起使用](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md#proxy)。
-   本主题还参考了[应用商店示例](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store)中提供的代码示例。 若要获得为通用 Windows 平台 (UWP) 应用提供的不同货币化选项的实际体验，此示例是一个不错的选择。

## <a name="step-1-initialize-the-license-info-for-your-app"></a>步骤 1：为你的应用初始化许可证信息

当应用在执行初始化时，请通过初始化 [CurrentApp](/uwp/api/Windows.ApplicationModel.Store.CurrentApp) 或 [CurrentAppSimulator](/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) 获取应用的 [LicenseInformation](/uwp/api/Windows.ApplicationModel.Store.LicenseInformation) 对象，从而启用应用内产品的购买。

> [!div class="tabbedCodeSnippets"]
[!code-csharp[EnableInAppPurchases](./code/InAppPurchasesAndLicenses/cs/EnableInAppPurchases.cs#InitializeLicenseTest)]

## <a name="step-2-add-the-in-app-offers-to-your-app"></a>步骤 2：向应用添加应用内付费内容

针对你希望通过应用内产品提供的每项功能，创建一个付费内容并将它添加到你的应用中。

> [!IMPORTANT]
> 向应用商店提交你的应用之前，必须将你希望呈现给客户的所有应用内产品都添加到应用中。 如果你稍后需要添加新的应用内产品，则必须更新应用并重新提交新版本。

1.  **创建应用内付费内容标记**

    通过标记来标识应用中的每个应用内产品。 此标记是一个字符串，在你的应用和应用商店中定义和使用它来标识特定的应用内产品。 为它提供一个独特（对于你的应用）且有意义的名称，以便你可以在编码时快速标识它所表示的正确功能。 下面是一些名称示例：

    * “SpaceMissionLevel4”
    * “ContosoCloudSave”
    * “RainbowThemePack”

  > [!NOTE]
  > 您在代码中使用的应用程序内产品标记必须与您[在合作伙伴中心定义应用程序的对应外](../publish/add-on-submissions.md)接程序时指定的[产品 ID](../publish/set-your-add-on-product-id.md#product-id)值匹配。

2.  **在条件块中编码功能**

    必须将与应用内产品关联的每个功能的代码都放在条件块中，从而进行测试以查看客户是否有使用该功能的许可证。

    下面的示例介绍了如何在特定于许可证的条件块中编码名为 **featureName** 的产品功能。 字符串 **featureName** 是在应用中唯一标识此产品的标记，它也用于在应用商店中标识此产品。

    > [!div class="tabbedCodeSnippets"]
    [!code-csharp[EnableInAppPurchases](./code/InAppPurchasesAndLicenses/cs/EnableInAppPurchases.cs#CodeFeature)]

3.  **为此功能添加购买 UI**

    你的应用还必需为客户提供一种方式来购买应用内产品提供的产品或功能。 客户无法以购买完整应用的方式通过应用商店来购买上述内容。

    下面介绍如何测试，以查看客户是否已拥有应用内产品；如果尚未拥有，则显示购买对话框，让用户可以购买该内容。 将注释“显示购买对话框”替换为你的购买对话框自定义代码（如一个包含友好的“购买此应用！”按钮的页面） 。

    > [!div class="tabbedCodeSnippets"]
    [!code-csharp[EnableInAppPurchases](./code/InAppPurchasesAndLicenses/cs/EnableInAppPurchases.cs#BuyFeature)]

## <a name="step-3-change-the-test-code-to-the-final-calls"></a>步骤 3：更改测试代码以达到最后要求

这是一个简单的步骤：在应用的代码中将对 [CurrentAppSimulator](/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) 的每个引用更改为 [CurrentApp](/uwp/api/Windows.ApplicationModel.Store.CurrentApp)。 你不再需要提供 WindowsStoreProxy.xml 文件，因此请将它从你的应用路径中删除（但是你可能希望保存它，以便在下一步中配置应用内付费内容时进行引用）。

## <a name="step-4-configure-the-in-app-product-offer-in-the-store"></a>步骤 4：在应用商店中配置应用内产品付费内容

在 "合作伙伴中心" 中，导航到你的应用并创建与应用内产品产品/服务匹配的 [外接](../publish/add-on-submissions.md) 程序。 为你的加载项定义产品 ID、类型、价格以及其他属性。 请确保完全按照测试时在 WindowsStoreProxy.xml 中设置的配置来配置它。

  > [!NOTE]
  > 你在代码中使用的应用内产品/服务令牌必须与你为合作伙伴中心中的相应外接程序指定的 [产品 ID](../publish/set-your-add-on-product-id.md#product-id) 值匹配。

## <a name="remarks"></a>备注

如果你对向客户提供可消费应用内产品选项（可购买、用完，然后根据需要再次购买的项目）感兴趣，请参阅[启用可消费应用内产品购买](enable-consumable-in-app-product-purchases.md)主题。

如果你需要使用收据来验证用户是否进行了应用内购买，请务必查看[使用收据验证产品购买](use-receipts-to-verify-product-purchases.md)。

## <a name="related-topics"></a>相关主题


* [启用可消费应用内产品购买](enable-consumable-in-app-product-purchases.md)
* [管理应用内产品的大目录](manage-a-large-catalog-of-in-app-products.md)
* [使用收据验证产品购买](use-receipts-to-verify-product-purchases.md)
* [应用商店示例（演示试用版和应用内购买）](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store)