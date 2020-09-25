---
title: 启动 Microsoft Store 应用
description: 本主题介绍了 ms-windows-store URI 方案。 应用可以使用此 URI 方案将 Microsoft Store 应用启动到存储中的特定页面。
ms.assetid: 9A9C6576-1637-47D1-AC3B-D1A20D49E0FF
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5d9571fa5abc07272d1b48c40274cbb952c0d754
ms.sourcegitcommit: eda7bbe9caa9d61126e11f0f1a98b12183df794d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91216350"
---
# <a name="launch-the-microsoft-store-app"></a>启动 Microsoft Store 应用



本主题介绍了 **ms-windows 应用商店：** URI 方案。 应用可以使用此 URI 方案，通过 [**LaunchUriAsync**](/uwp/api/windows.system.launcher.launchuriasync) 方法，将 Microsoft Store 应用启动到存储中的特定页面。

此示例显示了如何在“游戏”页面中打开 Microsoft Store：

```cs
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-windows-store://navigatetopage/?Id=Games"));
```

## <a name="ms-windows-store-uri-scheme-reference"></a>ms-windows-store: URI 方案引用

<table>
<tr><th>描述</th><th></th><th>URI 方案</th></tr>
<tr><td>启动应用商店的主页。</td><td /><td>ms-windows-store://home</td></tr>
<tr><td>启动应用商店中的顶级垂直类别内容。<p>请注意：并非所有用户都可以访问所有垂直类别内容。</p>
</td><td /><td>
<p>ms-windows-store://navigatetopage/?Id=Apps </p>
<p>ms-windows-store://navigatetopage/?Id=Games</p>
<p>ms-windows-store://navigatetopage/?Id=Music</p>
<p>ms-windows-store://navigatetopage/?Id=Video</p>
<p>ms-windows-store://navigatetopage/?Id=LOB</p>
</td>
</tr>
<tr>
<td rowspan="4">启动产品的产品详细信息页面 (PDP)。 <p>应用商店 ID 已向所有使用 Windows 10 的客户推荐，并且将适用于所有操作系统版本，但执行此操作的早期方法（例如：PFN）仍受支持。</p>
<p>可以在 <a href="https://partner.microsoft.com/dashboard">合作伙伴中心</a> 的每个应用的 "应用管理" 部分中的 " <a href="/windows/uwp/publish/view-app-identity-details">应用标识</a> " 页上找到这些值。</p>
</td>
<td>
应用商店 ID <p>（建议）</p>
</td>
<td>
<p>ms-windows-store://pdp/?ProductId=9WZDNCRFHVJL</p>
</td>
</tr>
<tr>
<td>程序包系列名称 (PFN)</td>
<td>ms-windows-store://pdp/?PFN= Microsoft.Office.OneNote_8wekyb3d8bbwe
</td>
</tr>
<tr>
<td>产品 ID (Windows Phone 7.x/8.x)</td>
<td>ms-windows-store://pdp/?PhoneAppId=ca05b3ab-f157-450c-8c49-a1f127f5e71d</td>
</tr>
<tr>
<td>产品 ID (Windows 8.x)</td>
<td>ms-windows-store://pdp/?AppId=f022389f-f3a6-417e-ad23-704fbdf57117
</td>
</tr>
<tr>
<td rowspan="4">启动产品的写评论体验。</td>
<td>应用商店 ID <p>（建议）</p></td>
<td>ms-windows-store://review/?ProductId=9WZDNCRFHVJL </td>
</tr>
<tr>
<td>程序包系列名称 (PFN)</td>
<td>ms-windows-store://review/?PFN= Microsoft.Office.OneNote_8wekyb3d8bbwe
</td>
</tr>
<tr>
<td>产品 ID (Windows Phone 7.x/8.x)</td>
<td>ms-windows-store://reviewapp/?AppId=ca05b3ab-f157-450c-8c49-a1f127f5e71d </td>
</tr>
<tr>
<td>产品 ID (Windows 8.x)</td>
<td>ms-windows-store://review/?AppId=f022389f-f3a6-417e-ad23-704fbdf57117 </td>
</tr>
<tr>
<td>启动与文件扩展名关联的产品的搜索。 </td>
<td />
<td>ms-windows-store://assoc/?FileExt=pdf
</td>
</tr>
<tr>
<td>启动与协议关联的产品的搜索。</td>
<td />
<td>ms-windows-store://assoc/?Protocol=ms-word </td>
</tr>
<tr>
<td>启动与一个或多个标记关联的产品的搜索。 标记应用逗号分隔。
</td>
<td />
<td>
<p>ms-windows-store://assoc/?Tags=Photos_Rich_Media_Edit </p>
<p>ms-windows-store://assoc/?Tags=Photos_Rich_Media_Edit, Camera_Capture_App</p>
</td>
</tr>
<tr>
<td>
启动特定查询的搜索。 允许查询中存在空格。
</td>
<td />
<td>ms-windows-store://search/?query=OneNote </td>
</tr>
<tr>
<td>启动类别中的产品的搜索。</td>
<td />
<td>
<p>ms-windows-store://browse/?type=Apps&amp;cat=Productivity</p>
<p>ms-windows-store://browse/?type=Apps&amp;cat=Health+%26+fitness </p>
</td>
</tr>
<tr>
<td>启动指定的发布者中产品的搜索。 允许名称中存在空格。
</td>
<td />
<td>ms-windows-store://publisher/?name=Microsoft Corporation
</td>
</tr>
<tr><td>启动下载和 更新页面。</td>
<td />
<td>ms-windows-store://downloadsandupdates </td>
</tr>
<tr>
<td>启动应用商店设置 页面。</td>
<td />
<td>ms-windows-store://settings </td>
</tr>
</table>

 

 