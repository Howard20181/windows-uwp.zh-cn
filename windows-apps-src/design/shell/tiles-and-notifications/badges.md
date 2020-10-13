---
description: 了解如何使用磁贴、锁屏提醒、Toast 以及通知提供应用入口点并使用户了解最新信息。
title: 适用于 Windows 应用的 Badge 通知
ms.assetid: 48ee4328-7999-40c2-9354-7ea7d488c538
label: Tiles, badges, and notifications
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a49d771b7efdbb7e787db0cbadea45c255a1120e
ms.sourcegitcommit: 140bbbab0f863a7a1febee85f736b0412bff1ae7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91984593"
---
# <a name="badge-notifications-for-windows-apps"></a>适用于 Windows 应用的 Badge 通知

 

<div style="float:left; font-size:80%; text-align:left; margin: 0px 15px 15px 0px;">
<img src="images/badge-example.png" alt="A tile with a numeric badge displaying the number 63 to indicate 63 unread mails." style="padding-bottom:0.0em; margin-bottom: 2px" /><br/>显示数字锁屏提醒的磁贴<br/> 数字 63，表示有 63 封未读邮件。</div>

通知锁屏提醒可传达特定于应用的摘要或状态信息。 这些信息可以是数字 (1-99) 或系统提供的一组字形中的一个。 通过锁屏提醒实现最佳传达的信息示例包括：联机游戏中的网络连接状态、消息应用中的用户状态、邮件应用中未读邮件的数量，以及社交媒体应用中新消息的数量。 

无论应用是否正在运行，通知锁屏提醒都显示在应用的任务栏图标上和它的开始磁贴的右下角。 锁屏提醒可在所有大小的磁贴上显示。  

> [!NOTE]
> 你不能提供自己的锁屏提醒图像；仅可以使用系统提供的锁屏提醒图像。


## <a name="numeric-badges"></a>数字锁屏提醒

“值” | 徽章 | XML
--|--|--
从 1 到 99 的一个数字 如果值为 0，则等同于字形值“无”，将清除锁屏提醒。 | <img src="images/badges/badge-numeric.png" alt="A numeric badge less than 100." /> | `<badge value="1"/>`
大于 99 的任何数字。 | <img src="images/badges/badge-numeric-greater.png" alt="A numeric badge greater than 99." /></td> | `<badge value="100"/>`

## <a name="glyph-badges"></a>字形锁屏提醒
锁屏提醒可以显示一组不可扩展的状态字形之一，但不可显示数字。 

状态 | 标志符号 | XML
--|--|--
无 | （未显示锁屏提醒。） | `<badge value="none"/>`
activity | <img src="images/badges/badge-activity.png" alt="Glyph" /> | `<badge value="activity"/>`
闹钟 | <img src="images/badges/badge-alarm.png" alt="Glyph" /> | `<badge value="alarm"/>`
警报 | <img src="images/badges/badge-alert.png" alt="Glyph" /> | `<badge value="alert"/>`
注意 | <img src="images/badges/badge-attention.png" alt="Glyph" /> | `<badge value="attention"/>`
可用 | <img src="images/badges/badge-available.png" alt="Glyph" /> | `<badge value="available"/>`
离开 | <img src="images/badges/badge-away.png" alt="Glyph" /> | `<badge value="away"/>`
忙碌 | <img src="images/badges/badge-busy.png" alt="Glyph" /> | `<badge value="busy"/>`
error | <img src="images/badges/badge-error.png" alt="Glyph" /> | `<badge value="error"/>`
newMessage | <img src="images/badges/badge-newMessage.png" alt="Glyph" /> | `<badge value="newMessage"/>`
已暂停 | <img src="images/badges/badge-paused.png" alt="Glyph" /> | `<badge value="paused"/>`
正在播放 | <img src="images/badges/badge-playing.png" alt="Glyph" /> | `<badge value="playing"/>`
没空 | <img src="images/badges/badge-unavailable.png" alt="Glyph" /> | `<badge value="unavailable"/>`</td>

## <a name="create-a-badge"></a>创建锁屏提醒

这些示例演示如何创建徽章更新。

### <a name="create-a-numeric-badge"></a>创建数字锁屏提醒

````csharp
private void setBadgeNumber(int num)
{

    // Get the blank badge XML payload for a badge number
    XmlDocument badgeXml = 
        BadgeUpdateManager.GetTemplateContent(BadgeTemplateType.BadgeNumber);

    // Set the value of the badge in the XML to our number
    XmlElement badgeElement = badgeXml.SelectSingleNode("/badge") as XmlElement;
    badgeElement.SetAttribute("value", num.ToString());

    // Create the badge notification
    BadgeNotification badge = new BadgeNotification(badgeXml);

    // Create the badge updater for the application
    BadgeUpdater badgeUpdater = 
        BadgeUpdateManager.CreateBadgeUpdaterForApplication();

    // And update the badge
    badgeUpdater.Update(badge);

}
````

### <a name="create-a-glyph-badge"></a>创建字形锁屏提醒
````csharp
private void updateBadgeGlyph()
{
    string badgeGlyphValue = "alert";

    // Get the blank badge XML payload for a badge glyph
    XmlDocument badgeXml = 
        BadgeUpdateManager.GetTemplateContent(BadgeTemplateType.BadgeGlyph);

    // Set the value of the badge in the XML to our glyph value
    Windows.Data.Xml.Dom.XmlElement badgeElement = 
        badgeXml.SelectSingleNode("/badge") as Windows.Data.Xml.Dom.XmlElement;
    badgeElement.SetAttribute("value", badgeGlyphValue);

    // Create the badge notification
    BadgeNotification badge = new BadgeNotification(badgeXml);

    // Create the badge updater for the application
    BadgeUpdater badgeUpdater = 
        BadgeUpdateManager.CreateBadgeUpdaterForApplication();

    // And update the badge
    badgeUpdater.Update(badge);

}
````

### <a name="clear-a-badge"></a>清除锁屏提醒

````csharp
private void clearBadge()
{
    BadgeUpdateManager.CreateBadgeUpdaterForApplication().Clear();
}
````

## <a name="get-the-sample-code"></a>获取示例代码

* [通知示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Notifications)<br/> 显示如何创建动态磁贴、发送锁屏提醒更新和显示 Toast 通知。 

## <a name="related-articles"></a>相关文章

* [自适应和交互式 Toast 通知](adaptive-interactive-toasts.md)
* [创建磁贴](creating-tiles.md)
* [创建自适应磁贴](create-adaptive-tiles.md)
