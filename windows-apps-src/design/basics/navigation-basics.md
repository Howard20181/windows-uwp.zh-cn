---
description: Windows 应用中的导航基于导航结构、导航元素和系统级功能的灵活模型。
title: Windows 应用的导航基础知识
ms.assetid: B65D33BA-AAFE-434D-B6D5-1A0C49F59664
label: Navigation design basics
template: detail.hbs
op-migration-status: ready
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 63ea04040d550bced414e9591dbf3ffd44de8da2
ms.sourcegitcommit: 77af97719a439f5e73a6109b42fd3110bcb2843b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107219084"
---
# <a name="navigation-design-basics-for-windows-apps"></a>Windows 应用的导航设计基础知识

![导航基础知识标题](images/nav/navigation-basics-header.jpg)

如果将应用看作是一个页面集合，那么导航描述的是在页面之间和页面内的移动行为  。 它是用户体验的起点，也是用户查找感兴趣的内容和功能的方式。 它非常重要，但可能很难做到理想化。

关于导航，我们面临着大量的选择。 我们可以：

:::row:::
    :::column:::
        ![导航示例 1](images/nav/nav-1.svg)

要求用户按顺序浏览一系列页面。
    :::column-end:::
    :::column:::
        ![导航示例 2](images/nav/nav-2.svg)

提供一个菜单，使用户能够直接跳转到任何页面。
    :::column-end:::
    :::column:::
        ![导航示例 3](images/nav/nav-3.svg)

将所有内容放在单个页面上，并提供查看内容的筛选机制。
    :::column-end:::
:::row-end:::

尽管没有哪一个导航设计能够适合所有应用，但有一些原则和指南可帮助你为应用确定合适的设计。

## <a name="principles-of-good-navigation"></a>良好导航原则

让我们开始了解良好导航设计的基本原则：

- **一致性：** 满足用户的期望。
- **简易性：** 按需行事，不要过度。
- **明确性：** 提供清楚的路径和选项。

### <a name="consistency"></a>一致性

导航应该与用户期望一致。 通过使用用户熟悉的[标准控件](#use-the-right-controls)并遵循对图标、位置和样式的标准约定，用户将觉得导航可预测且直观。

![页面组件图像](images/nav/page-components.svg)

> 用户希望在标准位置找到某些 UI 元素。

### <a name="simplicity"></a>简易性

减少导航项将为用户简化决策。 允许轻松访问重要目标位置，隐藏不太重要的项目，将帮助用户更快地获得所需内容。

:::row:::
    :::column:::
        ![绿色栏的第一个屏幕截图，其中包含绿色复选标记和文本“执行”。](images/nav/do.svg)

        ![良好导航视图](images/nav/navview-good.svg)

在常见的导航菜单中提供导航项。
    :::column-end:::
    :::column:::
        ![“不良导航视图”示例](images/nav/dont.svg)

        ![不良导航视图](images/nav/navview-bad.svg)

为用户提供多个导航选项。
    :::column-end:::
:::row-end:::

### <a name="clarity"></a>明确性

清晰的路径使用户能够进行逻辑导航。 让导航选项显而易见并阐明页面之间的关系可防止用户迷失。

![应用程序模型的屏幕截图，其中为用户显示了清晰的导航路径。](images/nav/clarity-image.svg)

> 清楚地标记目标位置，使用户知道身在何处。

## <a name="general-recommendations"></a>常规建议

现在，让我们记下我们的设计原则（一致、简单和清晰）并利用这几个原则来得出一些一般建议。

1. 考虑用户的想法。 找出用户使用你的应用的典型路径，思考用户访问每个页面的目的及其下一个目标位置。

2. 避免过多的导航分层。 如果导航级别超过三个，则用户可能进入深层、难以回退。

3. 避免“弹跳”。 “弹跳”会在以下情况下发生：存在相关内容，但需要用户先转到上一级，才能再次向下导航到该内容。

## <a name="use-the-right-structure"></a>使用正确的结构

熟悉了一般导航原则，应该如何确定应用的结构？ 有两个一般结构：平面和分层。

:::row:::
    :::column:::
        ![以平面结构排列的页面](images/nav/flat-lateral-structure.svg)
    :::column-end:::
    :::column span="2":::
        ### <a name="flatlateral"></a>平面/横向

在平面/横向结构中，页面是并排的。 你可以按任意顺序从一个页面转到另一个页面。

我们建议在以下情况下使用平面结构：

- 可以按任意顺序查看页面。
- 页面之间明显不同，并且不具有明显的父/子关系。
- 组中的页面少于 8 个。 <br>
（当存在更多页面时，用户可能难以区分页面或难以弄清它们当前在组中的位置。 如果你认为这对你的应用不构成问题，请继续将页面作为对等方平行排列。 否则，请考虑使用层次结构将页面分为两个或更多的组。）

    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![分层排列的页面](images/nav/hierarchical-structure.svg)
    :::column-end:::
    :::column span="2":::
        ### <a name="hierarchical"></a>分层

在分层结构中，页面将组织为树状结构。 每个子页面有一个父页面，但一个父页面可以有一个或多个子页面。 若要访问子页面，必须经过父页面。

分层结构适用于组织跨很多页面的复杂内容。 缺点是存在一些导航开销：结构越深入，在页面之间切换所需要的点击次数越多。

我们建议在以下情况下使用分层结构：
        
- 应该以特定的顺序遍历页面。
- 页面之间存在清晰的父-子关系。
- 组中的页面多于 7 个。
        
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![带有混合结构的应用](images/nav/combining-structures.svg)
    :::column-end:::
    :::column span="2":::
        ### <a name="combining-structures"></a>组合结构

不一定要在两种结构中二选一：许多设计合理的应用同时使用两者。 在应用中，对可以任何顺序查看的顶层页面使用平面结构，对具有更复杂关系的页面使用分层结构。

如果你的导航结构具有多个级别，我们建议使对等导航元素仅链接到其当前子树内的对等方。 请考虑以下图示，该图显示了具有两个级别的导航结构：

- 在级别 1，对等导航元素应提供对页面 A、B 和 C 的访问权限。
- 在级别 2 上，A2 页面的对等导航元素应该仅链接到其他 A2 页面。 它们不应链接到 C 子树中的级别 2 页面。
    :::column-end:::
:::row-end:::

## <a name="use-the-right-controls"></a>使用正确的控件

对页面结构作出决定后，你需要决定用户如何在这些页面之间导航。 UWP 提供了各种导航控件来帮助确保在你的应用中实现一致且可靠的导航体验。

:::row:::
    :::column:::
        ![框架图像](images/nav/thumbnail-frame.svg)
    :::column-end:::
    :::column span="2":::
        [**框架**](/uwp/api/Windows.UI.Xaml.Controls.Frame)

除少数例外情况，具有多个页面的应用都会使用框架。 通常，应用有一个包含框架和主导航元素（如导航显示控件）的主页面。 当用户选择页面时，框架将加载并显示此页面。
:::row-end:::

:::row:::
    :::column:::
        ![“选项卡和透视表”图像](images/nav/thumbnail-tabs-pivot.svg)
    :::column-end:::
    :::column span="2":::
        [**顶部导航**](../controls-and-patterns/navigationview.md)

显示指向同级别页面的水平链接列表。 [NavigationView](../controls-and-patterns/navigationview.md) 控件实现顶部导航模式。
        
在以下情况下使用顶部导航：

- 想要在屏幕上显示所有导航选项。
- 想要为应用内容提供更多空间。
- 图标无法清楚地描述导航类别。

:::row-end:::

:::row:::
    :::column:::
        ![“选项卡和透视表”图像](images/nav/thumbnail-tabs-pivot.svg)
    :::column-end:::
    :::column span="2":::
        [**选项卡**](../controls-and-patterns/tab-view.md)

显示一组水平的选项卡及其各自的内容。 [TabView](../controls-and-patterns/tab-view.md) 控件用于显示多个页面（或文档）的内容，同时为用户提供重新排列、打开或关闭选项卡的功能。
    
在以下情况下使用选项卡：

- 你希望用户能够动态地打开、关闭或重新排列选项卡。
- 你预料有可能会同时打开大量的选项卡。
- 你期望用户能够在应用程序中使用选项卡的窗口之间轻松移动选项卡，类似于 Microsoft Edge 这样的 Web 浏览器。

:::row-end:::

:::row:::
    :::column:::
        ![导航视图图像](images/nav/thumbnail-navview.svg)
    :::column-end:::
    :::column span="2":::
        [**左侧导航**](../controls-and-patterns/navigationview.md)

显示指向顶层页面的垂直链接列表。 何时使用：
        
- 这些页面存在于顶层。
- 有很多导航项（多于 5 个）
- 你预期用户不会在页面之间频繁切换。

:::row-end:::
        
:::row:::
    :::column:::
        ![列表细节图片](images/nav/thumbnail-list-detail.svg)
    :::column-end:::
    :::column span="2":::
        [**列表/细节**](../controls-and-patterns/list-details.md)

显示项目列表。 通过选择项目，可在细节部分中显示其相应的页面。 何时使用：
        
- 你预期用户在子项目之间频繁切换。
- 你希望用户能够对单个项目或项目组执行高级别操作（例如删除或排序），并且还希望用户可以查看或更新每个项目的详细信息。

列表/细节非常适合电子邮件收件箱、联系人列表和数据输入。
:::row-end:::

:::row:::
    :::column:::
        ![“超链接和按钮”图像](images/nav/thumbnail-hyperlinks-buttons.svg)
    :::column-end:::
    :::column span="2":::
        [**超链接**](../controls-and-patterns/hyperlinks.md)

嵌入式导航元素可显示在页面的内容中。 其他导航元素在页面间应保持一致，而嵌入式导航元素在各页面是唯一的。
:::row-end:::

## <a name="next-add-navigation-code-to-your-app"></a>下一步：向应用添加导航代码

下一篇文章是[实现基本导航](navigate-between-two-pages.md)，将介绍使用 Frame 控件实现应用中两个页面间的基本导航所需的代码。
