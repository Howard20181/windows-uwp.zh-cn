---
Description: 基于边缘的动画显示或隐藏源自屏幕边缘的 UI。
title: 基于边缘的 UI 动画
ms.assetid: 5A8F73B1-F4F6-424b-9EDF-A9766C5DEAE8
label: Motion--edge-based UI
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b36f609308d559f5f0cbb56c90420f2381fb8e53
ms.sourcegitcommit: eda7bbe9caa9d61126e11f0f1a98b12183df794d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2020
ms.locfileid: "91220170"
---
# <a name="edge-based-ui-animations"></a>基于边缘的 UI 动画





基于边缘的动画显示或隐藏源自屏幕边缘的 UI。 显示和隐藏动画可以由用户或应用启动。 此 UI 可以覆盖应用，也可以作为主屏表面的一部分。 如果此 UI 是应用图面的一部分，那么应用的其余部分可能需要相应调整大小。

> **重要 API**：[**EdgeUIThemeTransition 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.EdgeUIThemeTransition)


## <a name="dos-and-donts"></a>应做事项和禁止事项


-   使用边缘 UI 动画可显示或隐藏没有扩展到屏幕的自定义消息或错误栏。
-   使用面板动画可显示将滑动相当长距离进入屏幕的 UI，如任务窗格或自定义软键盘。
-   将 UI 从其要附着的相同边缘滑入。
-   将 UI 从其出发的相同边缘滑出。
-   如果应用的内容需要调整大小以响应 UI 的滑入或滑出，请使用淡化动画来调整大小。
    -   如果在滑入 UI，则在边缘 UI 或页面动画之后使用淡化动画。
    -   如果在滑出 UI，则在边缘 UI 或页面动画的同时使用淡化动画。
-   不要将这些动画用于通知。 通知不应放在基于边缘的 UI 中。
-   不要将边缘 UI 或面板动画用于不在屏幕边缘的任何 UI 容器或控件。 这些动画仅用于显示屏幕边缘的 UI、调整其大小以及对其进行取消。 若要移动其他类型的 UI，请使用重新定位动画。

    ![显示何时使用边缘 UI 或面板动画，以及何时使用重新定位。](images/edgevsreposition.png)

## <a name="related-articles"></a>相关文章


**面向开发人员**
* [动画概述](./xaml-animation.md)
* [创建基于边缘的 UI 动画](/previous-versions/windows/apps/jj649428(v=win.10))
* [快速入门：使用库动画创建 UI 动画](/previous-versions/windows/apps/hh452703(v=win.10))
* [**EdgeUIThemeTransition 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.EdgeUIThemeTransition)
* [**PaneThemeTransition 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.PaneThemeTransition)
* [创建淡化动画](/previous-versions/windows/apps/jj649429(v=win.10))
* [创建重新定位动画](/previous-versions/windows/apps/jj649434(v=win.10))

 

 
