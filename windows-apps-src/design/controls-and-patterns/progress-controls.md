---
description: 进度控件将为用户提供关于正在处理运行时间较长的操作的反馈。
title: 进度控件指南
ms.assetid: FD53B716-C43D-408D-8B07-522BC1F3DF9D
label: Progress controls
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: jeffarn
dev-contact: mitra
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 95adaae83596ab33bccbf5e0268d21dbfd53bad2
ms.sourcegitcommit: 8f7d30ad2040a3a4b0cb520b86f8fb448086aae6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107562510"
---
# <a name="progress-controls"></a>进度控件

进度控件将为用户提供关于正在处理运行时间较长的操作的反馈。 这意味着，在进度指示器可见，并且还可以根据所使用的指示器指示等待时长时，用户无法与该应用交互。

**获取 Windows UI 库**

:::row:::
   :::column:::
      ![WinUI 徽标](images/winui-logo-64x64.png)
   :::column-end:::
   :::column span="3":::
      ProgressBar 控件作为 Windows UI 库的一部分提供，该库是一个 NuGet 包，其中包含用于 Windows 应用的新控件和 UI 功能  。 有关详细信息（包括安装说明），请参阅 [Windows UI 库](/uwp/toolkits/winui/)。
   :::column-end:::
   :::column:::

   :::column-end:::
:::row-end:::

> **Windows UI 库 API：** [ProgressBar 类](/uwp/api/Microsoft.UI.Xaml.Controls.ProgressBar)、[IsIndeterminate 属性](/uwp/api/Microsoft.ui.xaml.controls.progressbar.isindeterminate)、[ProgressRing 类](/uwp/api/Microsoft.UI.Xaml.Controls.ProgressRing)、[IsActive 属性](/uwp/api/Microsoft.ui.xaml.controls.progressring.isactive)
>
> **平台 API：** [ProgressBar 类](/uwp/api/Windows.UI.Xaml.Controls.ProgressBar)、[IsIndeterminate 属性](/uwp/api/windows.ui.xaml.controls.progressbar.isindeterminate)、[ProgressRing 类](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing)、[IsActive 属性](/uwp/api/windows.ui.xaml.controls.progressring.isactive)

> [!NOTE]
> ProgressBar 和 ProgressRing 控件有两个版本：一个在平台中，以 Windows.UI.Xaml 命名空间为代表；另一个在 Windows UI 库中，以 Microsoft.UI.Xaml 命名空间为代表。 尽管 ProgressRing 和 ProgressBar 的 API 是相同的，但这两个版本之间的控件外观有所不同。 本文档将显示较新版 Windows UI 库的映像。
在本文档中，我们将使用 XAML 中的 **muxc** 别名表示我们已包含在项目中的 Windows UI 库 API。 我们已将此添加到我们的[页](/uwp/api/windows.ui.xaml.controls.page)元素：

```xaml
xmlns:muxc="using:Microsoft.UI.Xaml.Controls"
```

在后面的代码中，我们还将使用 C# 中的 **muxc** 别名表示我们已包含在项目中的 Windows UI 库 API。 我们在文件顶部添加了此 **using** 语句：

```csharp
using muxc = Microsoft.UI.Xaml.Controls;
```

```vb
Imports muxc = Microsoft.UI.Xaml.Controls
```

## <a name="types-of-progress"></a>进度类型

向用户显示操作正在进行的控件有两个：ProgressBar 或 ProgressRing。 ProgressBar 和 ProgressRing 都有两种状态，用于传达用户是否可以与应用程序进行交互。 

-   ProgressBar 和 ProgressRing 的确定状态显示任务的完成百分比。 此控件应该在持续时间已知的操作中使用，但进度不应阻止用户与应用交互。
-   ProgressBar 的不确定状态显示操作正在进行、没有阻止用户与应用交互，并且完成时间未知。
-   ProgressRing 的不确定状态显示操作正在进行、阻止了用户与应用交互，并且完成时间未知。


另外，进度控件为只读，不具有交互性。 这意味着用户无法直接调用或使用这些控件。

|控制|显示|
|---|---|
| 不确定的进度栏 | ![不确定的进度栏](images/progressbar-indeterminate.gif) |
| 确定的进度栏 | ![确定的进度栏](images/progressbar-determinate.png)|
| 不确定的进度环 | ![不确定的 ProgressRing 状态](images/progressring-indeterminate.gif)|
| 确定的 ProgressRing | ![确定的 ProgressRing 状态](images/progress_ring.jpg)|


## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处打开该应用，了解 <a href="xamlcontrolsgallery:/item/ProgressBar">ProgressBar</a> 或 <a href="xamlcontrolsgallery:/item/ProgressRing">ProgressRing</a> 的实际应用。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="when-to-use-each-control"></a>使用每个控件的时间

在尝试显示正在执行的操作时，通常不清楚要使用的控件和所处的状态（确定和不确定）。 有时不需要进度控件也明显可知任务执行进度，而有时即使使用了进度控件，仍需要使用一行文本向用户解释正在进行的操作。

### <a name="progressbar"></a>进度栏
-   **该控件是否具有已定义的持续时间，或者结束时间是否已预测？**

    使用确定 ProgressBar，并且相应地更新百分比或值。

-   **用户是否可以在不监视操作进度的情况下继续操作？**

    当 ProgressBar 处于使用中时，交互是非模态的，这通常意味着用户不会因该操作的未完成而受到阻止，而是可以在该操作完成之前继续以其他方式使用应用。

-   **关键字**

    如果你的操作与这些关键字相近，或者你想在进行中的操作旁显示与这些关键字相同的文本，请考虑使用 ProgressBar：

    - 正在加载... 
    - 正在检索 
    - 正在处理... 

### <a name="progressring"></a>ProgressRing

-   **该操作会使用户等候继续操作吗？**

    如果操作需要全部（或大部分）应用交互等到它完成后才能进行，则最好选择不确定的 ProgressRing。

    -   **该控件是否具有已定义的持续时间，或者结束时间是否已预测？**

    如果希望视觉对象是环而不是条，请使用确定的 ProgressRing，并相应地更新百分比或值。 

-   **应用是否需要等待用户完成某个任务？**

    如果是，请使用不确定的 ProgressRing，因为它们为用户指示未知的等待时间。

-   **关键字**

    如果你的操作与这些关键字相近，或者你想在进行中的操作旁显示与这些关键字相同的文本，请考虑使用 ProgressRing：

    - *正在刷新*
    - *正在登录...*
    - *正在连接...*

### <a name="no-progress-indication-necessary"></a>不需要进度指示
-   **用户需要知道发生了什么情况吗？**

    例如，如果应用在后台下载内容，但下载不是由用户启动的，则不一定要将此行为告知用户。

-   **操作是否属于不会阻止用户活动且用户基本不感兴趣（但略有趣）的后台活动？**

    如果应用执行的任务不必始终可见，但仍然需要显示状态时，请使用文本。

-   **用户是否仅仅关注操作完成与否？**

    有时最好仅在操作完成后显示一条通知，或者从视觉上提示已瞬时完成操作，并在后台进行最后的处理。

## <a name="progress-controls-best-practices"></a>进度控件最佳做法

也许通过图像最好理解应该在什么情况下使用各种进度控件：

**ProgressBar - 确定**

![ProgressBar 确定示例](images/progress-bar-determinate-example.png)

第一个示例是确定 ProgressBar。 如果已知操作持续时间，则最好使用确定 ProgressBar 显示何时安装、下载、设置等。

**ProgressBar - 不确定**

![ProgressBar 不确定示例](images/progress-bar-indeterminate-example.png)

如果不知道操作持续时间，请使用不确定 ProgressBar。 在填充虚拟化列表和在不确定和确定 ProgressBar 之间创建流畅的视觉过渡时，也可以使用不确定 ProgressBar。

-   **操作是否在虚拟化集合中？**

    如果是，则不要在显示列表项目时在这些项目上放置进度指示器。 相反，使用 ProgressBar，并将其放置在所加载的项目集合顶部，以显示正在提取项目。

**ProgressRing - 不确定**

![ProgressRing 不确定示例](images/progress-ring-indeterminate-example.png)

当暂时不允许用户与应用进行任何进一步交互，或应用正在等待用户输入以便继续运行时，应使用不确定性 ProgressRing。 上面的“正在登录…”一例是最适合使用 ProgressRing 的场景：在登录完成前，用户无法继续使用该应用。

ProgressRing - 确定

![ProgressRing 确定示例](images/progress-ring-determinate-example.png)

如果操作持续时间已知且需要环形视觉对象，则最好使用确定 ProgressRing 显示何时安装、下载、设置等。

## <a name="customizing-a-progress-control"></a>自定义进度控件

两个进度控件都非常简单，但控件的某些视觉功能看起来不能自定义。

**设置 ProgressRing 的大小**

ProgressRing 的大小可以调整到所需大小，但最小只能为 20x20epx。 为了调整 ProgressRing 大小，必须设置其高度和宽度。 如果仅设置高度或宽度，控件将假定最小大小 (20x20epx)；相反，如果高度和宽度设为两个不同大小，将假定两个大小中较小的一个。
若要确保 ProgressRing 符合需要，请将高度和宽度设为相同值：

```XAML
<ProgressRing Height="100" Width="100"/>
```

为了使 ProgressRing 可见并且可以形成动画，必须将 IsActive 属性设为 true：

```XAML
<ProgressRing IsActive="True" Height="100" Width="100"/>
```

```C#
progressRing.IsActive = true;
```

**为进度控件着色**

默认情况下，进度控件的主色设置为系统的主题色。 若要替代此画笔，只需更改任一控件的前景属性。

```XAML
<ProgressRing IsActive="True" Height="100" Width="100" Foreground="Blue"/>
<muxc:ProgressBar Width="100" Foreground="Green"/>
```

更改 ProgressRing 的前景色将更改进度环的填充颜色。 进度条的前景属性可以更改进度条的填充颜色；若要更改进度条的未填充部分，只需重写背景属性。

**显示等待光标**

当应用或操作需要反应时间，最好只显示简单的等待光标，指示用户不应与应用或显示了等待光标的区域交互，直到等待光标消失。

```C#
Window.Current.CoreWindow.PointerCursor = new Windows.UI.Core.CoreCursor(Windows.UI.Core.CoreCursorType.Wait, 10);
```

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。

## <a name="related-articles"></a>相关文章

- [ProgressBar 类](/uwp/api/Windows.UI.Xaml.Controls.ProgressBar)
- [ProgressRing 类](/uwp/api/Windows.UI.Xaml.Controls.ProgressRing)
