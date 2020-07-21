---
ms.assetid: B071F6BC-49D3-4E74-98EA-0461A1A55EFB
description: 如果你有应用和加载项的目录，你可以使用 Microsoft Store 收集 API 和 Microsoft Store 购买 API 访问你的服务中的这些产品的所有权信息。
title: 管理来自服务的产品授权
ms.date: 08/01/2018
ms.topic: article
keywords: Windows 10, uwp, Microsoft Store 收集 API, Microsoft Store 购买 API, 查看产品, 授予产品
ms.localizationpriority: medium
ms.openlocfilehash: 2d0df7780943717d8e01f0efcf1583efe05608af
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259219"
---
# <a name="manage-product-entitlements-from-a-service"></a>管理来自服务的产品授权

如果你有应用和加载项的目录，你可以使用 *Microsoft Store 收集 API* 和 *Microsoft Store 购买 API* 访问你的服务中的这些产品的权益信息。 *权益*表示客户使用通过 Microsoft Store 发布的应用或加载项的权利。

这些 API 由 REST 方法组合而成，旨在供开发人员用于跨平台服务支持的加载项目录。 这些 API 支持你执行以下操作：

-   Microsoft Store 收集 API：[查询用户拥有的产品](query-for-products.md)和[将可消费产品报告为已完成](report-consumable-products-as-fulfilled.md)。
-   Microsoft Store 购买 API：[向用户授予免费产品](grant-free-products.md)、[获取用户订阅](get-subscriptions-for-a-user.md)和[更改用户订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)。

> [!NOTE]
> Microsoft Store 收集 API 和购买 API 使用 Azure Active Directory (Azure AD) 身份验证访问客户所有权信息。 要使用这些 API，你（或你的组织）必须具有 Azure AD 目录，并且你必须具有该目录的[全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限。 如果你已使用 Office 365 或 Microsoft 的其他业务服务，表示你已经具有 Azure AD 目录。

## <a name="overview"></a>概述

以下步骤介绍了使用 Microsoft Store 收集 API 和购买 API 的端到端过程：

1.  [在 Azure AD 中配置应用程序](#step-1)。
2.  [在合作伙伴中心将 Azure AD 应用程序 ID 与应用程序相关联](#step-2)。
3.  在你的服务中，[创建 Azure AD 访问令牌](#step-3)，这些令牌表示你的发布者标识。
4.  在客户端 Windows 应用程序中，[创建一个 MICROSOFT STORE ID 密钥](#step-4)来表示当前用户的身份，并将此密钥传递回你的服务。
5.  在你具有所需的 Azure AD 访问令牌和 Microsoft Store ID 密钥后，[从你的服务调用 Microsoft Store 收集 API 或购买 API](#step-5)。

此端到端过程涉及两个执行不同任务的软件组件：

* **你的服务**。 这是一个在你的业务环境中安全运行的应用程序，它可以使用你选择的任何开发平台来实现。 服务负责创建方案所需的 Azure AD 访问令牌，并为 Microsoft Store 收集 API 和购买 API 调用 REST Uri。
* **你的客户端 Windows 应用程序**。 这是你想要为其访问和管理客户权限信息（包括应用的加载项）的应用。 此应用负责创建 Microsoft Store ID 密钥，你需要从服务中调用 Microsoft Store 收集 API 和购买 API。

<span id="step-1"/>

## <a name="step-1-configure-an-application-in-azure-ad"></a>步骤1：在 Azure AD 中配置应用程序

你必须创建 Azure AD Web 应用程序、检索应用程序的租户 ID 和应用程序 ID，并生成一个密钥，然后才能使用 Microsoft Store 收集 API 或购买 API。 Azure AD Web 应用程序表示你要从中调用 Microsoft Store 收集 API 或购买 API 的服务。 需要租户 ID、应用程序 ID 和密钥，才能生成需要调用 API Azure AD 访问令牌。

> [!NOTE]
> 你只需执行一次本部分中任务。 更新 Azure AD 应用程序清单并拥有租户 ID、应用程序 ID 和客户端密码后，Azure AD 随时都可以重复使用这些值。

1.  如果尚未这样做，请按照将[应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)中的说明向 Azure AD 注册**WEB 应用/API**应用程序。
    > [!NOTE]
    > 注册应用程序时，必须选择**Web 应用/API**作为应用程序类型，以便可以检索应用程序的密钥（也称为*客户端密码*）。 若要调用 Microsoft Store 收集 API 或购买 API，必须在稍后步骤从 Azure AD 中请求访问令牌时提供客户端密码。

2.  在[Azure 管理门户](https://portal.azure.com/)中，导航到 " **Azure Active Directory**"。 选择你的目录，在左侧导航窗格中单击 "**应用注册**"，然后选择你的应用程序。
3.  你将转到应用程序的主注册页。 在此页上，复制 "**应用程序 ID** " 值以供稍后使用。
4.  创建稍后需要的密钥（这全部称为*客户端机密*）。 在左窗格中，单击 "**设置**"，然后单击 "**密钥**"。 在此页上，完成[创建密钥](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis)的步骤。 复制此密钥供以后使用。
5.  向[应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)添加几个必需的受众 uri。 在左窗格中，单击 "**清单**"。 单击 "**编辑**"，将 `"identifierUris"` 部分替换为以下文本，然后单击 "**保存**"。

    ```json
    "identifierUris" : [                                
            "https://onestore.microsoft.com",
            "https://onestore.microsoft.com/b2b/keys/create/collections",
            "https://onestore.microsoft.com/b2b/keys/create/purchase"
        ],
    ```

    这些字符串表示你的应用程序支持的受众。 在稍后的步骤中，你将创建与其中每个受众值关联的 Azure AD 访问令牌。

<span id="step-2"/>

## <a name="step-2-associate-your-azure-ad-application-id-with-your-client-app-in-partner-center"></a>步骤2：将 Azure AD 应用程序 ID 与合作伙伴中心的客户端应用相关联

你必须在合作伙伴中心将 Azure AD 应用程序 ID 与应用程序（或包含该外接程序的应用程序）相关联，然后才能使用 Microsoft Store 收集 API 或购买 API 来配置应用或外接程序的所有权和购买。

> [!NOTE]
> 你只需执行一次此任务。

1.  登录到 "[合作伙伴中心](https://partner.microsoft.com/dashboard)" 并选择应用。
2.  请访问 "**服务**" &gt;**产品集合和购买**"页，并将 Azure AD 应用程序 id 输入其中一个可用的**客户端 id**字段。

<span id="step-3"/>

## <a name="step-3-create-azure-ad-access-tokens"></a>第 3 步：创建 Azure AD 访问令牌

你的服务必须先创建几个不同的表示你的发布者标识的 Azure AD 访问令牌，然后你才能检索 Microsoft Store ID 密钥或调用 Microsoft Store 收集 API 或购买 API。 每个令牌将与不同的 API 一起使用。 每个令牌的生命周期为 60 分钟，你可以在它们到期后进行刷新。

> [!IMPORTANT]
> 仅在服务的上下文而非应用中创建 Azure AD 访问令牌。 客户端密码在发送到你的应用时可能会遭泄露。

<span id="access-tokens" />

### <a name="understanding-the-different-tokens-and-audience-uris"></a>了解不同的令牌和受众 URI

根据你希望在 Microsoft Store 收集 API 或购买 API 中调用的方法，你必须创建两个或三个不同的令牌。 每个访问令牌都与不同的受众 URI（即你之前添加到 Azure AD 应用程序清单的 `"identifierUris"` 部分的 URI）关联。

  * 在所有情况下，你都必须使用 `https://onestore.microsoft.com` 受众 URI 创建令牌。 在稍后的步骤中，你要将此令牌传递到 Microsoft Store 收集 API 或购买 API 中的方法的**授权**标题。
      > [!IMPORTANT]
      > 将 `https://onestore.microsoft.com` 受众仅与安全存储在服务中的访问令牌一起使用。 在服务之外公开访问令牌和此受众会让你的服务易受到重播攻击。

  * 如果你想要在 Microsoft Store 收集 API 中调用某个方法以[查询用户拥有的产品](query-for-products.md)或[将可消费产品报告为已完成](report-consumable-products-as-fulfilled.md)，则还必须使用 `https://onestore.microsoft.com/b2b/keys/create/collections` 受众 URI 创建令牌。 在稍后的步骤中，你要将此令牌传递到 Windows SDK 中的客户端方法，以请求可与 Microsoft Store 收集 API 一起使用的 Microsoft Store ID 密钥。

  * 如果你想要调用 Microsoft Store 购买 API 中的方法来[向用户授予免费产品](grant-free-products.md)、[获取用户订阅](get-subscriptions-for-a-user.md)或[更改用户订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)，则必须使用 `https://onestore.microsoft.com/b2b/keys/create/purchase` 受众 URI 创建一个令牌。 在稍后的步骤中，你要将此令牌传递到 Windows SDK 中的客户端方法，以请求可与 Microsoft Store 购买 API 一起使用的 Microsoft Store ID 密钥。

<span />

### <a name="create-the-tokens"></a>创建令牌

若要创建访问令牌，请按照[使用客户端凭据的服务到服务调用](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/)中的说明在服务中使用 OAuth 2.0 API，以便将 HTTP POST 发送到 ```https://login.microsoftonline.com/<tenant_id>/oauth2/token``` 终结点。 示例请求如下所示。

``` syntax
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://onestore.microsoft.com
```

对于每个令牌，请指定以下参数数据：

* 对于*客户端\_id*和*客户端\_机密*参数，请为你从[Azure 管理门户](https://portal.azure.com/)检索的应用程序指定应用程序 id 和客户端密码。 若要创建带有 Microsoft Store 收集 API 或购买 API 所需的身份验证级别的访问令牌，这两个参数都是必需的。

* 对于*资源*参数，请指定[上一节](#access-tokens)中列出的受众 URI 之一，具体取决于要创建的访问令牌的类型。

在你的访问令牌到期后，你可按照[此处](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens)的说明刷新令牌。 有关访问令牌的结构的更多详细信息，请参阅[支持的令牌和声明类型](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)。

<span id="step-4"/>

## <a name="step-4-create-a-microsoft-store-id-key"></a>第 4 步：创建 Microsoft Store ID 密钥

你的应用必须先创建 Microsoft Store ID 密钥并将其发送给服务，然后你才能调用 Microsoft Store 收集 API 或购买 API 中的任何方法。 此密钥是 JSON Web 令牌 (JWT)，表示你想要访问其产品所有权信息的用户的标识。 有关此密钥中的声明的详细信息，请参阅 [Microsoft Store ID 密钥中的声明](#claims-in-a-microsoft-store-id-key)。

当前，创建 Microsoft Store ID 密钥的唯一方法是通过你的应用中的客户端代码调用通用 Windows 平台 (UWP) API。 生成的密钥表示当前在设备上登录到 Microsoft Store 的用户的身份。

> [!NOTE]
> 每个 Microsoft Store ID 密钥的有效期为 90 天。 密钥到期后，可以[续订该密钥](renew-a-windows-store-id-key.md)。 我们建议你续订 Microsoft Store ID 密钥，而非创建新密钥。

<span />

### <a name="to-create-a-microsoft-store-id-key-for-the-microsoft-store-collection-api"></a>为 Microsoft Store 收集 API 创建 Microsoft Store ID 密钥

按照以下步骤创建可与 Microsoft Store 收集 API 一起使用的 Microsoft Store ID 密钥，以[查询用户拥有的产品](query-for-products.md)或[将可消费产品报告为已完成](report-consumable-products-as-fulfilled.md)。

1.  将具有受众 URI 值 `https://onestore.microsoft.com/b2b/keys/create/collections` 的 Azure AD 访问令牌从服务传递到客户端应用。 这是你在[步骤 3 的早先阶段](#step-3)创建的令牌之一。

2.  在你的应用代码中，调用以下方法之一以检索 Microsoft Store ID 密钥：

  * 如果你的应用使用 [Windows.Services.Store](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext) 命名空间中的 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store) 类来管理应用内购买，请使用 [StoreContext.GetCustomerCollectionsIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getcustomercollectionsidasync) 方法。

  * 如果你的应用使用 [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) 命名空间中的 [CurrentApp](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store) 类来管理应用内购买，请使用 [CurrentApp.GetCustomerCollectionsIdAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getcustomercollectionsidasync) 方法。

    将 Azure AD 访问令牌传递给该方法的 *serviceTicket* 参数。 如果你在作为当前应用程序的发布者管理的服务上下文中维护匿名用户 Id，还可以将用户 ID 传递到*publisherUserId*参数，以将当前用户与新的 Microsoft Store ID 密钥关联（用户 id 将嵌入密钥中）。 否则，如果不需要将用户 ID 与 Microsoft Store ID 密钥相关联，则可以将任何字符串值传递到*publisherUserId*参数。

3.  在应用成功创建 Microsoft Store ID 密钥后，请将该密钥传递回服务。

<span />

### <a name="to-create-a-microsoft-store-id-key-for-the-microsoft-store-purchase-api"></a>为 Microsoft Store 购买 API 创建 Microsoft Store ID 密钥

按照以下步骤创建可与 Microsoft Store 购买 API 一起使用的 Microsoft Store ID 密钥，以[向用户授予免费产品](grant-free-products.md)、[获取用户订阅](get-subscriptions-for-a-user.md)或[更改用户订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)。

1.  将具有受众 URI 值 `https://onestore.microsoft.com/b2b/keys/create/purchase` 的 Azure AD 访问令牌从服务传递到客户端应用。 这是你在[步骤 3 的早先阶段](#step-3)创建的令牌之一。

2.  在你的应用代码中，调用以下方法之一以检索 Microsoft Store ID 密钥：

  * 如果你的应用使用 [Windows.Services.Store](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext) 命名空间中的 [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store) 类来管理应用内购买，请使用 [StoreContext.GetCustomerPurchaseIdAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getcustomerpurchaseidasync) 方法。

  * 如果你的应用使用 [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp) 命名空间中的 [CurrentApp](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store) 类来管理应用内购买，请使用 [CurrentApp.GetCustomerPurchaseIdAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getcustomerpurchaseidasync) 方法。

    将 Azure AD 访问令牌传递给该方法的 *serviceTicket* 参数。 如果你在作为当前应用程序的发布者管理的服务上下文中维护匿名用户 Id，还可以将用户 ID 传递到*publisherUserId*参数，以将当前用户与新的 Microsoft Store ID 密钥关联（用户 id 将嵌入密钥中）。 否则，如果不需要将用户 ID 与 Microsoft Store ID 密钥相关联，则可以将任何字符串值传递到*publisherUserId*参数。

3.  在应用成功创建 Microsoft Store ID 密钥后，请将该密钥传递回服务。

### <a name="diagram"></a>图示

下图说明了创建 Microsoft Store ID 密钥的过程。

  ![创建 Windows 应用商店 ID 密钥](images/b2b-1.png)

<span id="step-5"/>

## <a name="step-5-call-the-microsoft-store-collection-api-or-purchase-api-from-your-service"></a>步骤 5：从你的服务调用 Microsoft Store 收集 API 或购买 API

在你的服务具有允许其访问特定用户的产品所有权信息的 Microsoft Store ID 密钥后，你的服务可通过遵循以下说明调用 Microsoft Store 收集 API 或购买 API：

* [查询产品](query-for-products.md)
* [按满足情况报告可耗用产品](report-consumable-products-as-fulfilled.md)
* [授予免费产品](grant-free-products.md)
* [获取用户的订阅](get-subscriptions-for-a-user.md)
* [更改用户的订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)

对于每个方案，请将以下信息传递到 API：

-   在请求标头中，传递具有受众 URI 值 `https://onestore.microsoft.com` 的Azure AD 访问令牌。 这是你在[步骤 3 的早先阶段](#step-3)创建的令牌之一。 此令牌代表你的发布者标识。
-   在请求正文中，从你的应用中的客户端代码传递你在[步骤 4 的早期阶段](#step-4)检索的 Microsoft Store ID 密钥。 此密钥表示你想要访问其产品所有权信息的用户的标识。

### <a name="diagram"></a>图示

下图描述了在 Microsoft Store 收集 API 中调用方法或从服务购买 API 的过程。

  ![调用集合或购买 API](images/b2b-2.png)

## <a name="claims-in-a-microsoft-store-id-key"></a>Microsoft Store ID 密钥中的声明

Microsoft Store ID 密钥是 JSON Web 令牌 (JWT)，该令牌表示你想要访问其产品所有权信息的用户的标识。 当使用 Base64 解码时，Microsoft Store ID 密钥包含以下声明。

* `iat`：&nbsp;&nbsp;&nbsp;标识颁发密钥的时间。 此声明可用于确定令牌的有效期。 此值表示为纪元时间。
* `iss`：&nbsp;&nbsp;&nbsp;标识颁发者。 这与 `aud` 声明具有相同的值。
* `aud`：&nbsp;&nbsp;&nbsp;标识受众。 必须是下列值之一：`https://collections.mp.microsoft.com/v6.0/keys` 或 `https://purchase.mp.microsoft.com/v6.0/keys`。
* `exp`：&nbsp;&nbsp;&nbsp;标识过期时间或过期时间，超过该时间后，将不再接受该密钥来处理除续订密钥以外的任何内容。 此声明的值表示为纪元时间。
* `nbf`：&nbsp;&nbsp;&nbsp;标识将接受令牌进行处理的时间。 此声明的值表示为纪元时间。
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/clientId`：&nbsp;&nbsp;&nbsp;标识开发人员的客户端 ID。
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/payload`：&nbsp;&nbsp;&nbsp;一种不透明的有效负载（加密和 Base64 编码），其中包含仅供 Microsoft Store 服务使用的信息。
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/userId`：&nbsp;&nbsp;&nbsp;在服务上下文中标识当前用户的用户 ID。 此值与你传递到*用于创建密钥的方法*的可选 [publisherUserId](#step-4) 参数中的值相同。
* `http://schemas.microsoft.com/marketplace/2015/08/claims/key/refreshUri`：&nbsp;&nbsp;&nbsp;可用于续订密钥的 URI。

以下是一个解码的 Microsoft Store ID 密钥标头的示例。

```json
{
    "typ":"JWT",
    "alg":"RS256",
    "x5t":"agA_pgJ7Twx_Ex2_rEeQ2o5fZ5g"
}
```

以下是一个解码的 Microsoft Store ID 密钥声明集的示例。

```json
{
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/clientId": "1d5773695a3b44928227393bfef1e13d",
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/payload": "ZdcOq0/N2rjytCRzCHSqnfczv3f0343wfSydx7hghfu0snWzMqyoAGy5DSJ5rMSsKoQFAccs1iNlwlGrX+/eIwh/VlUhLrncyP8c18mNAzAGK+lTAd2oiMQWRRAZxPwGrJrwiq2fTq5NOVDnQS9Za6/GdRjeiQrv6c0x+WNKxSQ7LV/uH1x+IEhYVtDu53GiXIwekltwaV6EkQGphYy7tbNsW2GqxgcoLLMUVOsQjI+FYBA3MdQpalV/aFN4UrJDkMWJBnmz3vrxBNGEApLWTS4Bd3cMswXsV9m+VhOEfnv+6PrL2jq8OZFoF3FUUpY8Fet2DfFr6xjZs3CBS1095J2yyNFWKBZxAXXNjn+zkvqqiVRjjkjNajhuaNKJk4MGHfk2rZiMy/aosyaEpCyncdisHVSx/S4JwIuxTnfnlY24vS0OXy7mFiZjjB8qL03cLsBXM4utCyXSIggb90GAx0+EFlVoJD7+ZKlm1M90xO/QSMDlrzFyuqcXXDBOnt7rPynPTrOZLVF+ODI5HhWEqArkVnc5MYnrZD06YEwClmTDkHQcxCvU+XUEvTbEk69qR2sfnuXV4cJRRWseUTfYoGyuxkQ2eWAAI1BXGxYECIaAnWF0W6ThweL5ZZDdadW9Ug5U3fZd4WxiDlB/EZ3aTy8kYXTW4Uo0adTkCmdLibw=",
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/userId": "infusQMLaYCrgtC0d/SZWoPB4FqLEwHXgZFuMJ6TuTY=",
    "http://schemas.microsoft.com/marketplace/2015/08/claims/key/refreshUri": "https://collections.mp.microsoft.com/v6.0/b2b/keys/renew",
    "iat": 1442395542,
    "iss": "https://collections.mp.microsoft.com/v6.0/keys",
    "aud": "https://collections.mp.microsoft.com/v6.0/keys",
    "exp": 1450171541,
    "nbf": 1442391941
}
```

## <a name="related-topics"></a>相关主题

* [查询产品](query-for-products.md)
* [按满足情况报告可耗用产品](report-consumable-products-as-fulfilled.md)
* [授予免费产品](grant-free-products.md)
* [获取用户的订阅](get-subscriptions-for-a-user.md)
* [更改用户的订阅的计费状态](change-the-billing-state-of-a-subscription-for-a-user.md)
* [续订 Microsoft Store ID 密钥](renew-a-windows-store-id-key.md)
* [将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)
* [了解 Azure Active Directory 应用程序清单]( https://go.microsoft.com/fwlink/?LinkId=722500)
* [支持的令牌和声明类型](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
