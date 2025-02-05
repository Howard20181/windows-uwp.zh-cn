---
description: 了解如何在显示或隐藏触摸键盘时定制你的应用 UI。
title: 响应触摸键盘的存在
ms.assetid: 70C6130E-23A2-4F9D-88E7-7060062DA988
label: Respond to the presence of the touch keyboard
template: detail.hbs
keywords: 键盘, 辅助功能, 导航, 焦点, 文本, 输入, 用户交互
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: c3431fcafb86428ce5eddb8ea7a6b0187b64a5e5
ms.sourcegitcommit: a3bbd3dd13be5d2f8a2793717adf4276840ee17d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93035080"
---
# <a name="respond-to-the-presence-of-the-touch-keyboard"></a>响应触摸键盘的存在

了解如何在显示或隐藏触摸键盘时定制你的应用 UI。

### <a name="important-apis"></a>重要的 API

- [AutomationPeer](/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationPeer)
- [InputPane](/uwp/api/Windows.UI.ViewManagement.InputPane)

![默认布局模式中的触摸键盘](images/keyboard/default.png)

<sup>默认布局模式下的触摸键盘</sup>

对于支持触摸的设备，触摸键盘支持文本输入。 默认情况下，当用户点击可编辑的输入字段时，Windows 应用文本输入控件将调用触摸键盘。 当用户在表单的控件之间导航时，触摸键盘通常保持可见，不过此行为可能因表单内其他控件类型的不同而有所不同。

若要针对并非派生自标准文本输入控件的自定义文本输入控件支持相应的触摸键盘行为，必须使用 <a href="/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationPeer">AutomationPeer</a> 类向 Microsoft UI 自动化公开控件，并实现正确的 UI 自动化控件模式。 请参阅[键盘辅助功能](../accessibility/keyboard-accessibility.md)和[自定义的自动化对等](../accessibility/custom-automation-peers.md)。

在此支持添加到你的自定义控件后，你便可以对触摸键盘的存在做出适当的响应。

**先决条件：**

本主题基于[键盘交互](keyboard-interactions.md)生成。

你应该对标准键盘交互、处理键盘输入和事件以及 UI 自动化有一个基本了解。

如果你不熟悉如何开发 Windows 应用程序，请参阅这些主题，了解此处讨论的技术。

- [创建第一个应用](../../get-started/your-first-app.md)
- 借助[事件和路由事件概述](../../xaml-platform/events-and-routed-events-overview.md)了解事件

**用户体验指南：**

有关设计适用于键盘输入的有用且具有吸引力的应用程序的有用技巧，请参阅 [键盘交互](./keyboard-interactions.md) 。

## <a name="touch-keyboard-and-a-custom-ui"></a>触摸键盘和自定义 UI

下面是关于自定义的文本输入控件的一些基本建议。

- 在与表单的整个交互中显示触摸键盘。

- 确保你的自定义控件具有正确的 UI 自动化[AutomationControlType](/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType)，以确保在焦点从文本输入字段移出时（但仍然位于文本输入的上下文中）保持键盘始终显示。 例如，如果在文本输入时打开了一个菜单，但你希望该键盘一直显示，则该菜单必须具有 **AutomationControlType** 菜单。

- 不要操作 UI 自动化属性来控制触摸键盘。 其他辅助工具依赖于 UI 自动化属性的精度。

- 确保用户在交互时始终可以看到输入字段。

    由于触摸键盘在屏幕上 occludes，因此在用户浏览窗体上的控件（包括当前不在视图中的控件）时，Windows 将确保带有焦点的输入字段滚动到视图中。

    自定义 UI 时，可通过处理 [InputPane](/uwp/api/windows.ui.viewmanagement.inputpane.showing) 对象所公开的 [Showing](/uwp/api/windows.ui.viewmanagement.inputpane.hiding) 和 [**Hiding**](/uwp/api/Windows.UI.ViewManagement.InputPane) 事件，针对触摸键盘的外观提供相似的行为。

    ![显示和没有显示触摸键盘的表单](images/touch-keyboard-pan1.png)

    在某些情况下，会有一些 UI 元素应始终保留在屏幕上。 对 UI 进行设计，以便表单控件包含在平移区域中并且重要的 UI 元素时静态的。 例如：

    ![包含应始终位于视图中的区域的表单](images/touch-keyboard-pan2.png)

## <a name="handling-the-showing-and-hiding-events"></a>处理 Showing 和 Hiding 事件

下面的示例演示如何为触摸键盘的 [显示](/uwp/api/windows.ui.viewmanagement.inputpane.showing) 和 [隐藏](/uwp/api/windows.ui.viewmanagement.inputpane.hiding) 事件附加事件处理程序。

```csharp
using Windows.UI.ViewManagement;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;
using Windows.Foundation;
using Windows.UI.Xaml.Navigation;

namespace SDKTemplate
{
    /// <summary>
    /// Sample page to subscribe show/hide event of Touch Keyboard.
    /// </summary>
    public sealed partial class Scenario2_ShowHideEvents : Page
    {
        public Scenario2_ShowHideEvents()
        {
            this.InitializeComponent();
        }

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            InputPane currentInputPane = InputPane.GetForCurrentView();
            // Subscribe to Showing/Hiding events
            currentInputPane.Showing += OnShowing;
            currentInputPane.Hiding += OnHiding;
        }

        protected override void OnNavigatedFrom(NavigationEventArgs e)
        {
            InputPane currentInputPane = InputPane.GetForCurrentView();
            // Unsubscribe from Showing/Hiding events
            currentInputPane.Showing -= OnShowing;
            currentInputPane.Hiding -= OnHiding;
        }

        void OnShowing(InputPane sender, InputPaneVisibilityEventArgs e)
        {
            LastInputPaneEventRun.Text = "Showing";
        }
        
        void OnHiding(InputPane sender, InputPaneVisibilityEventArgs e)
        {
            LastInputPaneEventRun.Text = "Hiding";
        }
    }
}
```

```cppwinrt
...
#include <winrt/Windows.UI.ViewManagement.h>
...
private:
    winrt::event_token m_showingEventToken;
    winrt::event_token m_hidingEventToken;
...
Scenario2_ShowHideEvents::Scenario2_ShowHideEvents()
{
    InitializeComponent();
}

void Scenario2_ShowHideEvents::OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e)
{
    auto inputPane{ Windows::UI::ViewManagement::InputPane::GetForCurrentView() };
    // Subscribe to Showing/Hiding events
    m_showingEventToken = inputPane.Showing({ this, &Scenario2_ShowHideEvents::OnShowing });
    m_hidingEventToken = inputPane.Hiding({ this, &Scenario2_ShowHideEvents::OnHiding });
}

void Scenario2_ShowHideEvents::OnNavigatedFrom(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e)
{
    auto inputPane{ Windows::UI::ViewManagement::InputPane::GetForCurrentView() };
    // Unsubscribe from Showing/Hiding events
    inputPane.Showing(m_showingEventToken);
    inputPane.Hiding(m_hidingEventToken);
}

void Scenario2_ShowHideEvents::OnShowing(Windows::UI::ViewManagement::InputPane const& /*sender*/, Windows::UI::ViewManagement::InputPaneVisibilityEventArgs const& /*args*/)
{
    LastInputPaneEventRun().Text(L"Showing");
}

void Scenario2_ShowHideEvents::OnHiding(Windows::UI::ViewManagement::InputPane const& /*sender*/, Windows::UI::ViewManagement::InputPaneVisibilityEventArgs const& /*args*/)
{
    LastInputPaneEventRun().Text(L"Hiding");
}
```

```cpp
#include "pch.h"
#include "Scenario2_ShowHideEvents.xaml.h"

using namespace SDKTemplate;
using namespace Platform;
using namespace Windows::Foundation;
using namespace Windows::UI::ViewManagement;
using namespace Windows::UI::Xaml;
using namespace Windows::UI::Xaml::Controls;
using namespace Windows::UI::Xaml::Navigation;

Scenario2_ShowHideEvents::Scenario2_ShowHideEvents()
{
    InitializeComponent();
}

void Scenario2_ShowHideEvents::OnNavigatedTo(NavigationEventArgs^ e)
{
    auto inputPane = InputPane::GetForCurrentView();
    // Subscribe to Showing/Hiding events
    showingEventToken = inputPane->Showing +=
        ref new TypedEventHandler<InputPane^, InputPaneVisibilityEventArgs^>(this, &Scenario2_ShowHideEvents::OnShowing);
    hidingEventToken = inputPane->Hiding +=
        ref new TypedEventHandler<InputPane^, InputPaneVisibilityEventArgs^>(this, &Scenario2_ShowHideEvents::OnHiding);
}

void Scenario2_ShowHideEvents::OnNavigatedFrom(NavigationEventArgs^ e)
{
    auto inputPane = Windows::UI::ViewManagement::InputPane::GetForCurrentView();
    // Unsubscribe from Showing/Hiding events
    inputPane->Showing -= showingEventToken;
    inputPane->Hiding -= hidingEventToken;
}

void Scenario2_ShowHideEvents::OnShowing(InputPane^ /*sender*/, InputPaneVisibilityEventArgs^ /*args*/)
{
    LastInputPaneEventRun->Text = L"Showing";
}

void Scenario2_ShowHideEvents::OnHiding(InputPane^ /*sender*/, InputPaneVisibilityEventArgs ^ /*args*/)
{
    LastInputPaneEventRun->Text = L"Hiding";
}
```

## <a name="related-articles"></a>相关文章

- [键盘交互](keyboard-interactions.md)
- [键盘辅助功能](../accessibility/keyboard-accessibility.md)
- [自定义的自动化对等](../accessibility/custom-automation-peers.md)

### <a name="samples"></a>示例

- [触摸键盘示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard)

### <a name="archive-samples"></a>存档示例

- [输入：触摸键盘示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard)
- [响应屏幕键盘外观示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Responding%20to%20the%20appearance%20of%20the%20on-screen%20keyboard%20sample)
- [XAML 文本编辑示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BVB%5D-Windows%208%20app%20samples/VB/Windows%208%20app%20samples/XAML%20text%20editing%20sample%20(Windows%208))
- [XAML 辅助功能示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/XAML%20accessibility%20sample)
