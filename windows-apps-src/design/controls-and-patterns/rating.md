---
description: 使用户能够轻松查看和设置反映内容和服务满意度的评分。
title: 评分控件
template: detail.hbs
ms.date: 10/25/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: abarlow
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: d242107789f7c7575b5796c30d371dbe8bb0a412
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970792"
---
# <a name="rating-control"></a>评分控件

评分控件使用户能够查看和设置反映内容和服务满意度的评分。 用户可以使用触摸、笔、鼠标、游戏板或键盘操作评分控件。 下列指南介绍了如何使用分级控件的功能提供灵活性和自定义功能。

![评分控件示例](images/rating_rs2_doc_ratings_intro.png)

**获取 Windows UI 库**

|  |  |
| - | - |
| ![WinUI 徽标](images/winui-logo-64x64.png) | RatingControl 控件作为 Windows UI 库的一部分提供，该库是一个 NuGet 包，其中包含用于 Windows 应用的新控件和 UI 功能  。 有关详细信息（包括安装说明），请参阅 [Windows UI 库](https://docs.microsoft.com/uwp/toolkits/winui/)。 |

> **Windows UI 库 API：** [RatingControl 类](/uwp/api/microsoft.ui.xaml.controls.ratingcontrol)
>
> **平台 API：** [RatingControl 类](/uwp/api/windows.ui.xaml.controls.ratingcontrol)

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处<a href="xamlcontrolsgallery:/item/RatingControl">打开此应用，了解 RatingControl 的实际应用</a>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

### <a name="editable-rating-with-placeholder-value"></a>带占位符值的可编辑评分

很可能使用评分控件的最常见方法是显示平均评分，同时仍然允许用户输入他们自己的评分值。 在这种方案中，评分控件最初设置为反映所有用户对某一具体服务或内容类型（如音乐、视频、书籍等）的平均满意度。 它将保持这种状态，直到有用户出于单独为某一项评分的目的与此控件交互。 此交互更改评分控件的状态以反映该用户的个人满意度评分。

#### <a name="initial-average-rating-state"></a>初始平均评分状态
![初始平均评分状态](images/rating_rs2_doc_movie_aggregate.png)

#### <a name="representation-of-user-rating-once-set"></a>设置后的用户评分表示

![设置后的用户评分表示](images/rating_rs2_doc_movie_user.png)

```XAML
<RatingControl x:Name="MyRating" ValueChanged="RatingChanged"/>
```

```csharp
private void RatingChanged(RatingControl sender, object args)
{
    if (sender.Value == null)
    {
        MyRating.Caption = "(" + SomeWebService.HowManyPreviousRatings() + ")";
    }

    else
    {
        MyRating.Caption = "Your rating";
    }
}
```

### <a name="read-only-rating-mode"></a>只读评分模式

有时，你需要显示辅助内容评分，如在推荐内容中显示或在显示评论及其相应评分的列表时显示的内容。 在这种情况下，用户应该不能编辑评分，所以你可以将此控件设置为只读。
出于 UI 设计和性能原因，在非常大的虚拟化内容列表中使用评分控件时，也建议使用只读模式。

![只读长列表](images/rating_rs2_doc_reviews.png)

为此，需要执行以下操作：

```XAML
<RatingControl IsReadOnly="True"/>
```

## <a name="additional-functionality"></a>附加功能

评分控件有很多可用的附加功能。 有关如何使用这些功能的详细信息，请参阅参考文档。
下面是附加功能的不完整列表：
-   出色的长列表性能
-   压缩大小以适合紧凑的 UI 方案
-   连续值填充和评分
-   间距自定义
-   禁用增长动画
-   星数自定义

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。
