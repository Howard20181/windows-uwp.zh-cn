---
description: 描述在 Microsoft Store 中声明 Windows 应用程序可访问的要求。
ms.assetid: 59FA3B87-75A6-4B30-BA7C-A0E769D68050
title: 应用商店中的辅助功能
label: Accessibility in the Store
template: detail.hbs
ms.date: 09/24/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4a46f757629489250ed417a9a67488cba7ec7240
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93032700"
---
# <a name="accessibility-in-the-store"></a>应用商店中的辅助功能  



描述在 Microsoft Store 中声明 Windows 应用程序可访问的要求。

将你的应用提交至 Microsoft Store 进行认证时，可以将你的应用声明为辅助应用。 将你的应用声明为辅助应用，这样可使关注辅助应用的用户（如视力受损的用户）更轻松地发现该应用。 用户在搜索 Microsoft Store 时使用 **辅助** 筛选器发现辅助应用。 将你的应用声明为辅助应用还可以将 **辅助** 标记添加到应用的说明中。

通过将你的应用声明为辅助应用，即表明该应用具有用户对使用下列一个或多个项目的主要方案所需的[基本辅助功能信息](basic-accessibility-information.md)：

* 键盘。
* 高对比度主题。
* 可变的每英寸点数 (dpi) 设置。
* Windows 辅助功能等常见辅助技术，其中包括讲述人、放大镜、屏幕键盘。

如果你构建的应用可实现辅助功能并进行了辅助功能测试，你应将自己的应用声明为辅助应用。 这表示，你已完成下列工作：

* 为 UI 元素设置了所有相关辅助功能信息，这些元素包括名称、角色、值，等等。
* 实现了完整的键盘辅助功能，使用户能够：
    * 仅用键盘即可完成主要应用方案。
    * 用 Tab 按逻辑顺序在 UI 元素间移动。
    * 使用箭头键在某控件的 UI 元素间导航。
    * 使用键盘快捷方式实现主要应用功能。
    * 针对选项卡使用讲述人触控笔势，针对不带键盘的设备使用等效箭头键。
* 确保你的应用 UI 具有视觉辅助功能：文本对比率最低为 4.5:1、不单单依赖颜色传递信息等等。
* 使用 [**Inspect**](/windows/desktop/WinAuto/inspect-objects) 和 [**UIAVerify**](/windows/desktop/WinAuto/ui-automation-verify) 等辅助功能测试工具验证过你的辅助功能实现，已解决由类似工具报告的、优先级为 1 的所有错误。
* 已使用讲述人、放大镜、屏幕键盘、高对比度主题和已调节的 dpi 设置从头到尾验证了你的应用的主要方案。

请参阅[辅助功能清单](accessibility-checklist.md)，以查看这些过程以及有助于实现辅助功能的资源的链接。

<span id="related_topics"/>

## <a name="related-topics"></a>相关主题    
* [辅助功能](accessibility.md)
