---
title: 通过游戏盈利
description: 在 Windows 10 上实现通用 Windows 平台 (UWP) 游戏的横幅广告、间隙视频广告和应用内购买。
ms.assetid: 79f4e177-d8e7-45d3-8a78-31d4c2fe298a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 游戏, 盈利
ms.localizationpriority: medium
ms.openlocfilehash: 009d4740fed47c7cde392d41bf52384071715106
ms.sourcegitcommit: afc4ff2c89f148d32073ab1cc42063ccdc573a8c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104618"
---
#  <a name="monetization-for-games"></a>通过游戏盈利

作为游戏开发人员，你需要知道盈利方式，以便你可以维持你的业务并一直做感兴趣的事：创建出色游戏。 本文概述了通用 Windows 平台 (UWP) 游戏的盈利方法以及如何实现它们。

过去，你只是为游戏设置一个价格，然后等待用户在应用商店中购买该游戏。 但是，今天你有多种选项。 你可以选择将游戏分配给“实体”应用商店、在线销售游戏（通过物理复制或软复制），也可以让所有人免费玩游戏，但融入某种形式的广告或可供购买的应用内商品。 游戏也不再仅是单独的产品。 除了主游戏，它们通常还附带可供购买的额外内容。

你可以通过以下一种或多种方式，推广 UWP 游戏并通过其盈利。
* 将您的游戏置于 Microsoft Store 中，这是一个安全的在线商店，提供 [全球分布](#worldwide-distribution-channel)。 世界各地的玩家均可以[你设置的价格](#set-a-price-for-your-game)在线购买你的游戏。
* 使用 Windows SDK 中的 API 创建[游戏内购买](#in-game-purchases)。 玩家可以从你的游戏内购买商品，或购买装备、皮肤、地图或游戏关卡等额外内容。
* 使用 [Microsoft 广告 SDK](https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDK) 中的 API 从广告网络显示广告。 你可以[在你的游戏中显示广告](#display-ads-in-your-game)并向玩家提供观看视频广告换取游戏内奖励的选项。
* [通过广告市场活动最大程度地发展游戏的潜在客户](#maximize-your-games-potential-through-ad-campaigns)。 使用付费、社区（免费）或自家（免费）广告推广你的游戏，以扩大其用户群。

## <a name="worldwide-distribution-channel"></a>全球分配渠道

Microsoft Store 可以使你的游戏可在全球200多个国家和地区下载，并支持通过各种形式的付款（包括签证、MasterCard 和 PayPal）进行计费。 有关国家和地区的完整列表，请参阅 [定义市场选择](../publish/define-market-selection.md)。

## <a name="set-a-price-for-your-game"></a>为你的游戏设置价格

发布到应用商店的 UWP 游戏可以是 _付费_ 游戏，也可以是 _免费_ 游戏。 付费游戏允许你预先向玩家收取你设置的游戏价格，而免费游戏允许用户在未支付游戏的情况下下载和玩游戏。

下面是与在应用商店中设置你的游戏价格相关的一些重要概念。

### <a name="base-price"></a>基价

游戏基价可确定你的游戏属于 _付费_ 还是 _免费_ 类别。 你可以使用 [合作伙伴中心](https://partner.microsoft.com/dashboard) 来配置基于国家和地区的基本价格。
确定价格的过程可能包括[销往其他国家/地区时的税收义务](/partner-center/tax-details-marketplace)和[特定市场的成本注意事项](../publish/define-market-selection.md)。 还可以[为特定市场设置自定义价格](../publish/set-and-schedule-app-pricing.md#override-base-price-for-specific-markets)。

### <a name="sale-price"></a>售价

推广你的游戏的一种方法是限时低价销售。 也可以将售价设置为 __免费__，以使你的游戏可供免费下载。
可通过设置销售的开始日期和结束日期，提前计划销售市场活动。 有关详细信息，请参阅[打折出售应用和加载项](../publish/put-apps-and-add-ons-on-sale.md)。

## <a name="in-game-purchases"></a>游戏内购买

游戏内购买是指在游戏内购买的产品。 它们还通常称为 _应用内购买_。 在 Microsoft Store 中，这些产品称为 " _外接程序_"。 [外接程序](../publish/add-on-submissions.md) 通过合作伙伴中心发布。 你还需要在你的游戏代码中启用加载项。

### <a name="types-of-add-ons"></a>加载项类型

你可以在应用商店中创建两种类型的加载项：_耐用型_ 或 _易耗型_。 耐用型加载项可以保留一段指定时间，并且在过期前只能购买一次。 易耗型加载项可以反复购买和使用。

创建易耗型加载项时，确定你想要跟踪它们的方式，&mdash;即它们由 _开发人员托管_ 还是由 _应用商店托管_（此功能将在 Windows 10 版本 1607 中开始提供）。 使用由开发人员管理的可执行程序，你负责跟踪工作人员的项目余额;使用存储管理的可执行程序时，Microsoft Store 会持续跟踪项的余额。 有关详细信息，请参阅[易耗型加载项概述](../monetize/enable-consumable-add-on-purchases.md)。

### <a name="create-in-game-purchases"></a>创建游戏内购买

最新应用内购买和许可证信息 API 是 Windows SDK 中 [Windows.Services.Store](/uwp/api/windows.services.store) 命名空间的一部分（从 Windows 10 版本 1607 开始）。 如果你要面向 1607 或更高版本开发新游戏，我们建议你使用 __Windows.Services.Store__ 命名空间，因为它支持最新的加载项类型并且性能更佳。
它还旨在与合作伙伴中心和商店支持的产品和功能的未来类型兼容。 如果要面向以前版本的 Windows 10 开发，请改为使用 [Windows.ApplicationModel.Store](/uwp/api/windows.applicationmodel.store) 命名空间。

有关详细信息，请转到[应用内购买和试用](../monetize/in-app-purchases-and-trials.md)。

#### <a name="simplified-purchase-example"></a>简化的购买示例

本部分使用简化的购买示例说明如何使用不同的方法调用实现购买流程。

|游戏内操作/活动                                                | 游戏后台任务                  |
|--------------------------------------------------------------------------|----------------------------------------|
|玩家进入商店。 购买菜单将弹出，显示可用的加载项和购买价格 |  游戏[检索加载项的产品信息](../monetize/get-product-info-for-apps-and-add-ons.md)、[确定加载项是否具有正确的许可证](../monetize/get-license-info-for-apps-and-add-ons.md)，并显示可供玩家在购买菜单上购买的加载项。                           |
|玩家单击 __购买__ 即可购买物品             |" __购买__ " 操作将发送一个请求来购买该项，并开始付款过程以获取它。 实现根据商品类型而异。 如果它是[耐用型或一次性购买商品](../monetize/enable-in-app-purchases-of-apps-and-add-ons.md)，客户只能拥有一个商品，直到该商品过期为止。 如果该商品是[易耗型](../monetize/enable-consumable-add-on-purchases.md)商品，客户可以拥有一个或多个商品。 |

### <a name="test-in-game-purchases-during-game-development"></a>在游戏开发期间测试游戏内购买

因为加载项必须与游戏一起创建，因此你的游戏必须在应用商店中发布并提供。 本部分中的步骤显示了如何在游戏仍处于开发阶段创建加载项。
（如果你的完成游戏已在应用商店中上架，可先跳过这三个步骤，然后直接转到[在应用商店中创建加载项](#create-an-add-on-in-the-store)。）

在游戏仍处于开发阶段创建加载项：
1. [创建包](#create-a-package)
2. [将游戏发布为已隐藏](#publish-the-game-as-hidden)
3. [将 Visual Studio 中的游戏解决方案与应用商店相关联](#associate-your-game-solution-with-the-store)
4. [在应用商店中创建加载项](#create-an-add-on-in-the-store)

#### <a name="create-a-package"></a>创建包

对于任何要发布的游戏，必须满足最低 Windows 应用认证要求。 你可以使用 Windows 10 SDK 中包含的 [Windows 应用认证工具包](../debug-test-perf/windows-app-certification-kit.md)在游戏上运行测试，以帮助确保该应用可供发布到应用商店中。 如果尚未下载包含 Windows 应用认证工具包的 Windows 10 SDK，请转到 [Windows 10 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。

若要创建可上传到应用商店的程序包：

1. 在 Visual Studio 中打开你的游戏解决方案。
2. 在 Visual Studio 中，参阅 "__项目__  >  __商店__" "  >  __创建应用包"。__
3. 对于 " __是否要生成要上载到 Microsoft Store 的包？__ " 选项，请选择 __"是"__。
4. 登录到 [合作伙伴中心](https://partner.microsoft.com/dashboard) 开发人员帐户。 或者[注册](https://developer.microsoft.com/store/register)开发者帐户（如果没有）。
5. 选择要为其创建上传包的应用。 如果尚未创建应用提交，请提供新的应用名称创建新提交。 有关详细信息，请参阅[通过保留名称创建应用](../publish/create-your-app-by-reserving-a-name.md)。
6. 成功创建包后，单击 " __启动 Windows 应用程序认证包__ " 以启动测试过程。
7. 修复所有错误即可创建游戏程序包。

#### <a name="publish-the-game-as-hidden"></a>将游戏发布为已隐藏

1. 请参阅 [合作伙伴中心](https://partner.microsoft.com/dashboard) 并登录。
2. 在 " __仪表板概述__ " 或 " __所有应用__ " 页上，单击要使用的应用。 如果尚未创建应用提交，请单击 " __创建新应用__ 并保留名称"。
3. 在 " __应用概述__ " 页上，单击 " __开始提交__"。
4. 配置此新提交。 在提交页面上：
    * 单击 " __定价和可用性__"。 在 " __可见性__ " 部分中，选择 "__隐藏此应用并防止获取 ...__"，以确保只有开发团队可以访问该游戏。 有关更多详细信息，请转到[分发和可见性](../publish/set-app-pricing-and-availability.md)。
    * 单击 __“属性”__。 在 " __类别和子类别__ " 部分中，选择 " __游戏__ "，然后选择适合你的游戏的子类别。
    * 单击 " __年龄分级__"。 准确填写调查表。
    * 单击 __程序包__。 上传在上一步中创建的游戏程序包。
5. 按照仪表板中的任何其他提交提示，你可以成功发布此对公众保持隐藏的游戏。
6. 单击 __"提交到应用商店"__。

有关详细信息，请转到[应用提交](../publish/app-submissions.md)。

在你的游戏提交到应用商店后，它将进入[应用认证过程](../publish/the-app-certification-process.md)。 此过程最多需要 16 个小时，才能使游戏列出。

#### <a name="associate-your-game-solution-with-the-store"></a>将你的游戏解决方案与应用商店相关联

通过在 Visual Studio 中打开你的游戏解决方案：

1. 中转到 __项目__  >  __存储__  >  __将应用与应用商店关联 ...__
2. 登录到合作伙伴中心开发人员帐户，并选择要与此解决方案相关联的应用名称。
3. 双击 __Package.appxmanifest.xml 文件__ ，然后单击 " __打包__ " 选项卡以检查游戏是否正确关联。

如果已将解决方案关联到处于活动状态并在应用商店中列出的已发布游戏，你的解决方案将具有活动许可证，这进一步完成了为你的游戏创建加载项过程。 有关详细信息，请参阅[打包应用](../packaging/index.md)。

#### <a name="create-an-add-on-in-the-store"></a>在应用商店中创建加载项

创建加载项时，确保将它们与正确的游戏提交相关联。 有关如何配置与加载项相关联的所有各种信息的详细信息，请参阅[加载项提交](../publish/add-on-submissions.md)。

1. 请参阅 [合作伙伴中心](https://partner.microsoft.com/dashboard) 并登录。
2. 在 " __仪表板概述__ " 或 " __所有应用__ " 页上，单击要为其创建外接程序的应用程序。
3. 在 " __应用概述__ " 页上的 " __外接__ 程序" 部分中，选择 " __创建新的外接程序__"。
4. 选择该加载项的产品类型：__开发人员托管的易耗型__、__应用商店托管的易耗型__ 或 __耐用型__。
5. 输入唯一的产品 ID，该 ID 在将此加载项集成到你的游戏代码时用作字符串变量。 客户不会看到此 ID。 有关详细信息，请参阅[设置你的应用产品类型和产品 ID](../publish/set-your-add-on-product-id.md)。

加载项的其他配置包括：
* [属性](../publish/enter-add-on-properties.md)
* [定价和可用性](../publish/set-add-on-pricing-and-availability.md)
* [商店列表](../publish/create-add-on-store-listings.md)

如果游戏包含许多外接程序，可以使用 __Microsoft Store 提交 API__ 以编程方式创建它们。 有关详细信息，请参阅 [使用 Microsoft Store 服务创建和管理提交](../monetize/create-and-manage-submissions-using-windows-store-services.md)。

## <a name="display-ads-in-your-game"></a>在游戏中显示广告

Microsoft 广告 SDK 中的库和工具有助于你在游戏中设置服务，以从广告网络接收广告。 将向你的玩家显示实时广告，当他们查看或与显示的广告交互时，你将从广告商处获得收益。
有关详细信息，请参阅[在应用中显示广告](../monetize/display-ads-in-your-app.md)。

### <a name="ad-formats"></a>广告格式

可使用 Microsoft 广告 SDK 显示一些类型的广告：

* 横幅广告 &mdash; 占用部分游戏屏幕并且通常放置在游戏内的广告。
* 间隙视频广告 &mdash; 全屏显示广告，在两个关卡之间使用时非常有效。 正确实现后，它们不会像横幅广告那样显眼。
* 本机广告 &mdash; 基于组件的广告，其中每一个广告创意元素（如标题、图像、说明和行动号召文字）都作为可集成到应用中的单独元素提供给你的应用。

### <a name="which-ads-are-displayed"></a>显示哪些广告？

默认情况下，你的应用将显示来自 Microsoft 付费广告网络的广告。 若要最大化你的广告收益，你可以为你的广告单元启用广告中介，以显示来自其他付费广告网络的广告。 有关当前产品/服务的详细信息，请参阅[广告中介](../publish/in-app-ads.md#mediation)指南。

### <a name="which-markets-allow-ads-to-be-displayed"></a>哪些市场允许显示广告？

有关支持广告的国家和地区的完整列表，请参阅[广告网络支持的市场](../publish/in-app-ads.md#network-markets)。

### <a name="apis-for-displaying-ads"></a>用于显示广告的 API

Microsoft 广告 SDK 中的 [AdControl](/uwp/api/microsoft.advertising.winrt.ui.adcontrol)、[InterstitialAd](/uwp/api/microsoft.advertising.winrt.ui.interstitialad) 和 [NativeAd](/uwp/api/microsoft.advertising.winrt.ui.nativead) 类用于帮助在游戏中显示广告。

若要开始操作，请使用 Visual Studio 2015 或更高版本下载并安装 [Microsoft 广告 SDK](https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDK)。 有关详细信息，请参阅[安装 Microsoft 广告 SDK](../monetize/install-the-microsoft-advertising-libraries.md)。

#### <a name="implementation-guides"></a>实现指南

这些演练介绍了如何使用 __AdControl__、__InterstitialAd__ 和 __NativeAd__ 实现广告：

* [在 XAML 和 .NET 中创建横幅广告](../monetize/adcontrol-in-xaml-and--net.md)
* [在 HTML5 和 JavaScript 中创建横幅广告](../monetize/adcontrol-in-html-5-and-javascript.md)
* [创建间隙广告](../monetize/interstitial-ads.md)
* [创建本机广告](../monetize/native-ads.md)

在开发期间，你可以使用[测试广告单元值](../monetize/set-up-ad-units-in-your-app.md)查看广告的呈现方式。 这些测试广告单元值也在上述演练中使用。

下面是一些在设计和实现过程中向你提供帮助的最佳做法。

* [横幅广告的最佳做法](../monetize/ui-and-user-experience-guidelines.md)
* [间隙广告的最佳做法](../monetize/ui-and-user-experience-guidelines.md)

有关常见开发问题（如不显示广告、黑盒闪烁并消失或广告不刷新）的解决方案，请参阅[疑难解答指南](../monetize/known-issues-for-the-advertising-libraries.md)。

### <a name="prepare-for-release-by-replacing-ad-unit-test-values"></a>通过替换广告单元测试值准备发布

准备好移动到实时测试或在发布的游戏中接收广告时，必须将测试广告单元值更新为针对你的游戏提供的实际值。 若要为你的游戏创建广告单元，请参阅[在应用中设置广告单元](../monetize/set-up-ad-units-in-your-app.md)。

### <a name="other-ad-networks"></a>其他广告网络

这些网络是其他广告网络，它们提供的 SDK 用于向 UWP 应用和游戏提供广告服务。

#### <a name="vungle"></a>Vungle

适用于 Windows 的 Vungle SDK 在应用和游戏中提供视频广告。 若要下载 SDK，请转到 [Vungle SDK](https://publisher.vungle.com/sdk/)。

#### <a name="smaato"></a>Smaato

Smaato 可以将横幅广告合并到 UWP 应用和游戏中。 下载 [SDK](https://www.smaato.com/resources/sdks/)，有关详细信息，请参阅[文档](https://wiki.smaato.com/display/SPX/Windows+Phone)。

#### <a name="adduplex"></a>AdDuplex

AdDuplex 可用于在你的游戏中实现横幅或间隙广告。

若要了解有关将 AdDuplex 直接集成到 Windows 10 XAML 项目中的详细信息，请转到 AdDuplex 网站：
* 横幅广告：[适用于 XAML 的 Windows 10 SDK](https://adduplex.zendesk.com/hc/articles/204849031-Windows-10-SDK-for-XAML-apps-installation-and-usage)
* 间隙广告：[Windows 10 XAML AdDuplex 间隙广告安装和使用](https://adduplex.zendesk.com/hc/articles/204849091-Windows-10-XAML-AdDuplex-Interstitial-Ad-Installation-and-Usage)

有关将 AdDuplex SDK 集成到使用 Unity 创建的 Windows 10 UWP 游戏的信息，请参阅[适用于 Unity 应用的 Windows 10 SDK 安装和使用](https://adduplex.zendesk.com/hc/articles/207279435-Windows-10-SDK-for-Unity-apps-installation-and-usage)。

## <a name="maximize-your-games-potential-through-ad-campaigns"></a>通过广告市场活动最大程度地发展游戏的潜在客户

执行下一步，使用广告推广你的应用。 当为你的游戏[创建广告市场活动](../monetize/index.md)时，其他应用和游戏将显示可推广你的游戏的广告。

从多个市场活动类型中进行选择，这些活动可帮助增加你的玩家群。

|市场活动类型             | 你的游戏的广告显示在...                                                                                                                                                                   |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|已付                      |与你的游戏的设备或类别匹配的应用。                                                                                                                                                   |
|免费社区            |由已选择加入社区广告市场活动的其他开发人员发布的应用。 有关详细信息，请参阅[关于社区广告](../monetize/index.md)。|
|免费自家                |仅你发布的应用。 有关详细信息，请参阅[关于自家广告](../monetize/index.md)。                                                            |

## <a name="related-links"></a>相关链接

* [获取付款](/partner-center/marketplace-get-paid)
* [帐户类型、位置和费用](../publish/account-types-locations-and-fees.md)
* [分析](../publish/analytics.md)
* [全球化和本地化](../design/globalizing/globalizing-portal.md)
* [实现应用的试用版](../monetize/implement-a-trial-version-of-your-app.md)
* [通过 A/B 测试运行应用实验](../monetize/run-app-experiments-with-a-b-testing.md)