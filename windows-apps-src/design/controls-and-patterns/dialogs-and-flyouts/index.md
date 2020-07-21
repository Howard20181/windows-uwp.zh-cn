---
Description: 对话框和浮出控件显示当用户请求这些元素或发生需要通知或批准的操作时出现的瞬态 UI 元素。
title: 对话框和浮出控件
template: detail.hbs
ms.date: 07/06/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c61d1478c38df315a3fe3c20151de8c2bfbca4e2
ms.sourcegitcommit: f727b68e86a86c94eff00f67ed79a1c12666e7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81123565"
---
# <a name="dialogs-and-flyouts"></a>对话框和浮出控件

对话框和浮出控件是当发生需要通知、批准或来自用户的其他信息的操作时出现的瞬态 UI 元素。

> **平台 API：** [ContentDialog 类](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)、[Flyout 类](/uwp/api/Windows.UI.Xaml.Controls.Flyout)

**对话框**

![对话框示例](../images/dialogs/dialog_RS2_delete_file.png)

对话框是用于提供上下文应用信息的模式 UI 覆盖。 除非明确取消对话框，否则它会阻止与应用窗口的交互。 它们通常会请求用户进行某种类型的操作。

**浮出控件**

![浮出控件示例](../images/flyout-example2.png)

浮出控件是轻量级上下文弹出窗口，用于显示与用户正在执行的操作相关的 UI。 它包含放置和大小调整逻辑，可用于显示辅助控件或显示关于某个项目的更多详细信息。

和对话框不同，浮出控件可通过点击或单击浮出控件之外的某处、按 Escape 键或后退按钮、调整应用窗口大小或更改设备的方向来快速取消。

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

对话框和浮出控件确保用户知道重要信息，但它们也会干扰用户体验。 由于对话框是模式对话框（阻止），因此它们会干扰用户，在用户与该对话框交互前阻止它们执行任何其他操作。 浮出控件提供较和谐的体验，但显示过多的浮出控件可能令人分心。

确定要使用对话框还是浮出控件后，你需要选择使用哪一个。

鉴于对话框会阻止交互而浮出控件不会，因此对话框应专门用于你希望用户放下一切事务专注于特定的部分信息或回答问题的情况。 另一方面，当你希望将注意力吸引到某些内容，但如果用户想要忽略它也无妨时，可以使用浮出控件。

   <p><b>将对话框用于...</b> <br/>
<ul>
<li>表明用户在继续操作之前<b>必须</b>阅读并确认的重要信息。 示例包括：
<ul>
  <li>当用户的安全可能受到威胁时</li>
  <li>当用户准备永久改变宝贵资产时</li>
  <li>当用户准备删除宝贵资产时</li>
  <li>确认应用内购买</li>
</ul>

</li>
<li>应用于整个应用上下文的错误消息，如连接错误。</li>
<li>问题，在应用需要询问用户阻止问题时（例如当应用不能代表用户进行选择时）。 阻止问题不能忽略或延迟，并应该向用户提供明确定义的选项。</li>
</ul>
</p>


   <p><b>将浮出控件用于...</b> <br/>
<ul>
<li>收集在可以完成某个操作前所需的其他信息。</li>
<li>显示仅在一些时间内相关的信息。 例如，在照片库应用中，当用户单击某个图像缩略图时，你可以使用浮出控件显示图像的大型版本。</li>
<li>显示详细信息，例如页面上某个项目的详细信息或较长说明。</li>
</ul></p>

## <a name="ways-to-avoid-using-dialogs-and-flyouts"></a>如何避免使用对话框和浮出控件

请考虑要共享的信息的重要性：它的重要性是否足以要干扰用户？ 此外，请考虑需要显示该信息的频率；如果你要每几分钟显示一个对话框或通知，你可能希望改为在主 UI 中为此信息分配空间。 例如，在聊天客户端中，你可能显示当前在线的好友列表，并在好友登录时突出显示他们，而不是每次有好友登录时都显示浮出控件。

对话框经常用于在执行某个操作（如删除某个文件）前确认该操作。 如果你预期用户经常执行某个特定操作，请考虑为用户提供在犯错时撤销操作的方法，而不是强迫用户每次都确认该操作。

## <a name="how-to-create-a-dialog"></a>如何创建对话框

请参阅[对话框文章](dialogs.md)。 

## <a name="how-to-create-a-flyout"></a>如何创建浮出控件

请参阅[浮出控件文章](flyouts.md)。 

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="../images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装了 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处打开该应用，了解 <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> 或 <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> 的实际应用。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

