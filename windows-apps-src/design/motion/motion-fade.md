---
description: 使用淡化动画将项目引入或引出视图。 两个常见淡化动画是淡入和淡出。
title: 淡化动画
ms.assetid: 975E5EE3-EFBE-4159-8D10-3C94143DD07F
label: Motion--fades
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 81e632a25398003daa1e73d82c23343f57fd3cd0
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93034220"
---
# <a name="fade-animations"></a>淡化动画



使用淡化动画将项目引入或引出视图。 两个常见淡化动画是淡入和淡出。

> **重要 API** ： [**FadeInThemeAnimation 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation)、 [**FadeOutThemeAnimation 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation)


## <a name="dos-and-donts"></a>应做事项和禁止事项


-   当你的应用在不相关的元素或有大量文本的元素之间过渡时，先使用淡出，然后使用淡入。 这可让传出对象在传入对象显示之前完全消失。
-   如果元素的大小保持不变并且你希望用户感觉他们在查看相同的项，则在传出元素之上淡入一个或多个传入元素。 淡入完成后，可以删除传出项目。 这只是在传出项被传入项完全覆盖时的一个可行选项。
-   避免使用淡化动画添加或删除列表中的项。 相反，请使用为该目的创建的列表动画。
-   避免使用淡化动画更改整个页面内容。 相反，请使用为该目的创建的页面过渡动画。
-   淡出是用于删除元素的不明显的方法。
## <a name="related-articles"></a>相关文章

* [动画概述](./xaml-animation.md)
* [创建淡化动画](/previous-versions/windows/apps/jj649429(v=win.10))
* [快速入门：使用库动画创建 UI 动画](/previous-versions/windows/apps/hh452703(v=win.10))
* [**FadeInThemeAnimation 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation)
* [**FadeOutThemeAnimation 类**](/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation)

 

 
