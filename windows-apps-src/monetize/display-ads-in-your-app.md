---
ms.assetid: 63A9EDCF-A418-476C-8677-D8770B45D1D7
description: Microsoft 广告 SDK 提供几种通过广告从应用中获取收益的方法。
title: 使用 Microsoft 广告 SDK 在你的应用中显示广告
ms.date: 02/18/2020
ms.topic: article
keywords: windows 10, uwp, 广告, 横幅, 广告控件, 间隙
ms.localizationpriority: medium
ms.openlocfilehash: d693b8a4e07e66eded7d1580291c552e7ab9ca64
ms.sourcegitcommit: 71f9013c41fc1038a9d6c770cea4c5e481c23fbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77507221"
---
# <a name="display-ads-in-your-app-with-the-microsoft-advertising-sdk"></a>使用 Microsoft 广告 SDK 在你的应用中显示广告

>[!WARNING]
> 从2020年6月1日起，将关闭适用于 Windows UWP 应用的 Microsoft Ad 盈利平台。 [了解详细信息](https://social.msdn.microsoft.com/Forums/windowsapps/en-US/db8d44cb-1381-47f7-94d3-c6ded3fea36f/microsoft-ad-monetization-platform-shutting-down-june-1st?forum=aiamgr)

通过使用 Microsoft 广告 SDK 将广告放入面向 Windows 10 的通用 Windows 平台 (UWP) 应用中，增加收入机会。 我们的 ad 盈利平台提供各种 ad 格式，这些格式可无缝集成到你的应用中，并支持包含许多流行 ad 网络的采集。 我们的平台符合 OpenRTB、VAST 2.x、MRAID 2 和 VPAID 3 标准，并与 MOAT 和 IAS 兼容。 

<br/>

<table style="border: none !important;">
<colgroup>
<col width="10%" />
<col width="23%" />
<col width="10%" />
<col width="23%" />
<col width="10%" />
<col width="23%" />
</colgroup>
<tbody>
<tr>
<td align="left"><img src="images/install-sdk.png" alt="Install SDK icon" /></td>
<td align="left"><b>开始行动</b><br/><br/>
    <a href="https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDK">安装 MICROSOFT ADVERTISING SDK</a>
</td>
<td align="left"><img src="images/write-code.png" alt="Develop icon" /></td>
<td align="left"><b>开发人员指南</b><br/><br/>
    <a href="banner-ads.md">横幅广告</a>
    <br/>
    <a href="interstitial-ads.md">插播式广告</a>
    <br/>
    <a href="native-ads.md">本机广告</a>
    </td>
<td align="left"><img src="images/api-reference.png" alt="API ref icon" /></td>
<td align="left"><b>其他资源</b><br/><br/>
    <a href="set-up-ad-units-in-your-app.md">在应用中设置 ad 单位</a>
    <br/>
    <a href="best-practices-for-ads-in-apps.md">最佳做法</a>
    <br/>
    <a href="https://docs.microsoft.com/uwp/api/overview/advertising">API 参考</a>
    </td>
</tr>
</tbody>
</table>

## <a name="step-1-install-the-microsoft-advertising-sdk"></a>步骤 1：安装 Microsoft 广告 SDK

若要开始使用，请在你用于构建应用的开发计算机上安装 [Microsoft 广告 SDK](https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDK)。 有关安装说明，请参阅[此文章](install-the-microsoft-advertising-libraries.md)。

## <a name="step-2-implement-ads-in-your-app"></a>步骤 2：在应用中实现广告

Microsoft 广告 SDK 提供了你可以在应用中使用的多种不同类型的广告控件。 选择哪些类型的广告最适合你的情形，然后在你的应用中添加代码以显示这些广告。 在此步骤中，你将使用测试广告单元来了解你的应用是如何在测试期间呈现广告的。

### <a name="banner-ads"></a>横幅广告

这类广告为静态显示广告，它使用应用中的页面矩形部分显示促销内容。 这些广告可以定期自动刷新。 如果你是在应用中使用广告的新用户，则这是一个很好的开始。

有关说明和代码示例，请参阅[此文章](adcontrol-in-xaml-and--net.md)。

![addreferences](images/banner-ad.png)

### <a name="interstitial-video-and-interstitial-banner-ads"></a>间隙视频和间隙横幅广告

这些广告是一种全屏广告，通常要求用户观看视频或通过单击它们来继续运行应用或游戏。 我们支持两类间隙广告：视频和横幅。

有关说明和代码示例，请参阅[此文章](interstitial-ads.md)。

![addreferences](images/interstitial-ad.png)

### <a name="native-ads"></a>本机广告

这些广告是基于组件的广告。 这种广告的每一部分创意（如标题、图像、说明和行动文本）都作为一种单独的元素交付到应用中，你可以使用你自己的字体、颜色和其他 UI 组件将这些元素集成到你的应用中。

有关说明和代码示例，请参阅[此文章](native-ads.md)。

![addreferences](images/native-ad.png)

<span id="ad-mediation"/>

## <a name="step-3-create-an-ad-unit-and-configure-mediation"></a>步骤 3：创建广告单元和配置中介

完成应用测试后，可以将其提交到应用商店，请在合作伙伴中心的[应用内广告](../publish/in-app-ads.md)页上创建一个 ad 单元。 然后，更新应用代码以使用此广告单元，从而使你的应用能够接收实时广告。 有关详细信息，请参阅[在应用中设置广告单元](set-up-ad-units-in-your-app.md#live-ad-units)。

默认情况下，你的应用将显示来自 Microsoft 付费广告网络的广告。 若要最大化广告收益，可以为广告单元启用[广告中介](ad-mediation-service.md)，以显示来自其他付费广告网络（如 Taboola 和 Smaato）的广告。 你还可以通过在 Microsoft 应用促销活动中提供广告服务，来提高你的应用推广能力。

要在 UWP 应用中开始使用广告中介，请为广告单元[配置广告中介设置](../publish/in-app-ads.md#mediation-settings)。 默认情况下，我们会使用机器学习算法自动配置中介设置，以帮助最大化你的广告在应用支持的市场中的收益。 但是，你也可以手动选择想要使用的网络。 不论采用哪种方式，均需要在我们的服务器上完全配置中介设置；你无需在应用中更改任何代码。    

## <a name="step-4-submit-your-app-and-review-performance"></a>步骤 4：提交你的应用并查看性能

完成使用广告开发应用后，可以在合作伙伴中心[提交更新的应用](https://docs.microsoft.com/windows/uwp/publish/app-submissions)，使其在商店中可用。 显示广告的应用必须满足 [Microsoft Store 政策的 10.10 部分](https://docs.microsoft.com/legal/windows/agreements/store-policies#1010-advertising-conduct-and-content)和[应用开发人员协议的附录 E](https://docs.microsoft.com/legal/windows/agreements/app-developer-agreement) 中指定的其他要求。

在应用程序发布并在应用商店中可用后，可以在合作伙伴中心查看[广告性能报告](../publish/advertising-performance-report.md)，并继续对你的中介设置进行更改，以优化广告的性能。 广告收入包含在你的[支出汇总](../publish/payout-summary.md)中。

<span id="additional-help" />

## <a name="additional-help"></a>其他帮助

要获取有关使用 Microsoft 广告 SDK 的其他帮助，请使用以下资源。

|  任务    | 资源 |               
|----------|-------|
| 报告错误或获取对广告的辅助支持     | 访问[支持页面](https://developer.microsoft.com/windows/support)，然后选择**应用内广告**。        |
| 获取社区支持     | 访问[论坛](https://social.msdn.microsoft.com/forums/windowsapps/en-US/home?category=windowsapps)。       |
| 下载演示如何向应用添加横幅和间隙广告的示例项目。     | 请参阅 [GitHub 上的广告示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Advertising)。       |
| 了解有关 Windows 应用的最新盈利机会     | 访问[获取应用收益](https://developer.microsoft.com/store/monetize)。        |

## <a name="windows-81-and-windows-phone-8x-apps"></a>Windows 8.1 和 Windows Phone 8.x 应用

对于 Windows 8.1 和 Windows Phone 8.x 应用，我们提供[适用于 Windows 和 Windows Phone 8.x 的 Microsoft 广告 SDK](https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDKforWindowsandWindowsPhone8x)。 有关在 Windows 8.1 和 Windows Phone 8.x 应用中使用此 SDK 显示广告的更多信息，请参阅[此文章](https://docs.microsoft.com/previous-versions/windows/apps/dn792120(v=win.10))。

## <a name="related-topics"></a>相关主题

* [Microsoft 广告 SDK](https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDK)
* [广告效果报告](../publish/advertising-performance-report.md)
* [Windows 高级广告出版商计划](windows-premium-ads-publishers-program.md)
