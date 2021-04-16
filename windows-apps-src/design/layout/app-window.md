---
description: 使用 AppWindow 类在单独的窗口中查看应用的不同组成部分。
title: 使用 AppWindow 类显示应用的辅助窗口
ms.date: 07/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 903fe20cef1e3ccaa5db4e1cffc6b6583a878376
ms.sourcegitcommit: f7c7a2ae6367e114a8b9d438963082440cd24043
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315090"
---
# <a name="show-multiple-views-with-appwindow"></a>使用 AppWindow 显示多个视图

[AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 及其相关 API 简化了多窗口应用的创建，因为它可以让你在辅助窗口中显示应用内容，同时仍在每个窗口中处理同一 UI 线程。

> [!NOTE]
> AppWindow 目前以预览版提供。 这意味着，可将使用 AppWindow 的应用提交到 Store，但某些平台和框架组件已知不能与 AppWindow 配合工作（请参阅[限制](/uwp/api/windows.ui.windowmanagement.appwindow#limitations)）。

本文将使用一个名为 `HelloAppWindow` 的示例应用来演示一些多窗口方案。 该示例应用演示以下功能：

- 从主页取消停靠某个控件，并在新窗口中打开它。
- 在新窗口中打开某个页面的新实例。
- 在应用中以编程方式调整新窗口的大小和位置。
- 将 ContentDialog 关联到应用中的相应窗口。

![具有单个窗口的示例应用](images/hello-app-window-single.png)
  
> 具有单个窗口的示例应用

![具有取消停靠颜色选取器和辅助窗口的示例](images/hello-app-window-multi.png)

> 具有取消停靠颜色选取器和辅助窗口的示例

> **重要 API**：[Windows.UI.WindowManagement 命名空间](/uwp/api/windows.ui.windowmanagement)、[AppWindow 类](/uwp/api/windows.ui.windowmanagement.appwindow)

## <a name="api-overview"></a>API 概述

从 Windows 10 版本 1903 (SDK 18362) 开始提供 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 类，以及 [WindowManagement](/uwp/api/windows.ui.windowmanagement) 命名空间中的其他 API。 如果应用面向早期版本的 Windows 10，则必须[使用 ApplicationView 创建辅助窗口](application-view.md)。 WindowManagement API 仍在开发中，存在 API 参考文档中所述的[限制](/uwp/api/windows.ui.windowmanagement.appwindow#limitations)。

下面是用于在 AppWindow 中显示内容的一些重要 API。

### <a name="appwindow"></a>AppWindow

[AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 类可用于在辅助窗口中显示 Windows 运行时应用的一部分。 它在概念上类似于 [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview)，但行为和生存期不同。 AppWindow 的一项主要功能是，每个实例共享它们在创建时所在的同一 UI 处理线程（包括事件调度程序），从而简化了多窗口应用。

只能将 XAML 内容连接到 AppWindow，不支持本机 DirectX 或全息内容。 但是，可以显示承载 DirectX 内容的 XAML [SwapChainPanel](/uwp/api/windows.ui.xaml.controls.swapchainpanel)。

### <a name="windowingenvironment"></a>WindowingEnvironment

使用 [WindowingEnvironment](/uwp/api/windows.ui.windowmanagement.windowingenvironment) API 可以了解正在呈现应用的环境，因此可以根据需要改编应用。 此 API 描述环境支持的窗口类型。例如，如果应用在电脑上运行，则窗口类型为 `Overlapped`；如果应用在 Xbox 上运行，则窗口类型为 `Tiled`。 它还提供一组 DisplayRegion 对象，用于描述应用可在逻辑显示器上的哪些区域中显示。

### <a name="displayregion"></a>DisplayRegion

[DisplayRegion](/uwp/api/windows.ui.windowmanagement.displayregion) API 描述视图可在逻辑显示器上的哪个区域中向用户显示。例如，在台式机上，此显示区域是整个显示屏幕减去任务栏区域后的范围。 此显示区域不一定与后备监视器的物理显示区域之间存在 1:1 的映射。 同一监视器中可以有多个显示区域，或者，可将 DisplayRegion 配置为跨多个监视器，前提是这些监视器在所有方面都是相同的。

### <a name="appwindowpresenter"></a>AppWindowPresenter

使用 [AppWindowPresenter](/uwp/api/windows.ui.windowmanagement.appwindowpresenter) API 可以轻松将窗口切换为预定义的配置，例如 `FullScreen` 或 `CompactOverlay`。 这些配置在任何支持此类配置的设备上为用户提供一致的体验。

### <a name="uicontext"></a>UIContext

[UIContext](/uwp/api/windows.ui.uicontext) 是应用窗口或视图的唯一标识符。 它是自动创建的。可以使用 [UIElement.UIContext](/uwp/api/windows.ui.xaml.uielement.uicontext) 属性来检索 UIContext。 XAML 树中的每个 UIElement 具有相同的 UIContext。

 UIContext 非常重要，因为 [Window.Current](/uwp/api/Windows.UI.Xaml.Window.Current) 等 API 以及 `GetForCurrentView` 模式都依赖于在每个要处理的线程中使用单个 ApplicationView/CoreWindow 和单个 XAML 树。 但是，在使用 AppWindow 时情况并非如此，因此可以使用 UIContext 来识别特定的窗口。

### <a name="xamlroot"></a>XamlRoot

[XamlRoot](/uwp/api/windows.ui.xaml.xamlroot) 类包含 XAML 元素树，将该树连接到窗口宿主对象（例如 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 或 [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview)），并提供大小和可见性等信息。 不要直接创建 XamlRoot 对象。 将 XAML 元素附加到 AppWindow 时，系统会创建一个 XamlRoot 对象。 然后，可以使用 [UIElement.XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) 属性来检索 XamlRoot。

有关 UIContext 和 XamlRoot 的详细信息，请参阅[使代码可跨窗口宿主移植](show-multiple-views.md#make-code-portable-across-windowing-hosts)。

## <a name="show-a-new-window"></a>显示新窗口

让我们了解在新的 AppWindow 中显示内容的步骤。

**显示新窗口**

1. 调用静态 [AppWindow.TryCreateAsync](/uwp/api/windows.ui.windowmanagement.appwindow.trycreateasync) 方法以创建新的 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow)。

    ```csharp
    AppWindow appWindow = await AppWindow.TryCreateAsync();
    ```

1. 创建窗口内容。

    通常，你会创建一个 XAML [框架](/uwp/api/Windows.UI.Xaml.Controls.Frame)，然后将该框架导航到在其中定义了应用内容的 XAML [页面](/uwp/api/Windows.UI.Xaml.Controls.Page)。 有关框架和页面的详细信息，请参阅[两个页面之间的对等导航](../basics/navigate-between-two-pages.md)。

    ```csharp
    Frame appWindowContentFrame = new Frame();
    appWindowContentFrame.Navigate(typeof(AppWindowMainPage));
    ```

    但是，在 AppWindow 中可以显示任何 XAML 内容，而不仅仅是显示框架和页面。 例如，可以仅显示单个控件（如 [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker)），或者显示承载 DirectX 内容的 [SwapChainPanel](/uwp/api/windows.ui.xaml.controls.swapchainpanel)。

1. 调用 [ElementCompositionPreview.SetAppWindowContent](/api/windows.ui.xaml.hosting.elementcompositionpreview.setappwindowcontent) 方法以将 XAML 内容附加到 AppWindow。

    ```csharp
    ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);
    ```

    调用此方法会创建一个 [XamlRoot](/uwp/api/windows.ui.xaml.xamlroot) 对象，并将其设置为指定的 UIElement 的 [XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) 属性。

    对于每个 AppWindow 实例，只能调用此方法一次。 设置内容后，对此 AppWindow 实例进一步调用 SetAppWindowContent 将会失败。 此外，如果尝试通过传入 null UIElement 对象来断开 AppWindow 内容的连接，该调用将会失败。

1. 调用 [AppWindow.TryShowAsync](/uwp/api/windows.ui.windowmanagement.appwindow.tryshowasync) 方法以显示新窗口。

    ```csharp
    await appWindow.TryShowAsync();
    ```

## <a name="release-resources-when-a-window-is-closed"></a>关闭窗口时释放资源

始终应该处理 [AppWindow.Closed](/uwp/api/windows.ui.windowmanagement.appwindow.closed) 事件，以释放 XAML 资源（AppWindow 内容）以及对 AppWindow 的引用。

```csharp
appWindow.Closed += delegate
{
    appWindowContentFrame.Content = null;
    appWindow = null;
};
```

> [!TIP]
> 应将 `Closed` 事件处理程序中的代码量尽量保持在最小值，以避免出现意外问题。

## <a name="track-instances-of-appwindow"></a>跟踪 AppWindow 的实例

根据在应用中使用多个窗口的方式，可能需要或者不需要跟踪创建的 AppWindow 实例。 `HelloAppWindow` 示例演示了 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 的多种不同的典型用法。 在此处，我们将了解为何要跟踪这些窗口，以及如何跟踪。

### <a name="simple-tracking"></a>简单跟踪

颜色选取器窗口承载单个 XAML 控件，用来与颜色选取器交互的代码全部驻留在 `MainPage.xaml.cs` 文件中。 颜色选取器窗口仅允许单个实例，它实质上是 `MainWindow` 的扩展。 为了确保只创建一个实例，可以使用页面级变量来跟踪颜色选取器窗口。 在创建新的颜色选取器窗口之前，请先检查是否存在一个实例，如果存在，则跳过创建新窗口的步骤，并只对现有窗口调用 [TryShowAsync](/uwp/api/windows.ui.windowmanagement.appwindow.tryshowasync)。

```csharp
AppWindow colorPickerAppWindow;

// ...

private async void DetachColorPickerButton_Click(object sender, RoutedEventArgs e)
{
    // Create the color picker window.
    if (colorPickerAppWindow == null)
    {
        // ...
        // Create a new window
        colorPickerAppWindow = await AppWindow.TryCreateAsync();
        // ...
    }
    // Show the window.
    await colorPickerAppWindow.TryShowAsync();
}
```

### <a name="track-an-appwindow-instance-in-its-hosted-content"></a>跟踪窗口承载内容中的 AppWindow 实例

`AppWindowPage` 窗口承载完整的 XAML 页面，用来与该页面交互的代码驻留在 `AppWindowPage.xaml.cs` 中。 该窗口允许多个实例，其中每个实例独立运行。

使用页面的功能可以操控窗口、将其设置为 `FullScreen` 或 `CompactOverlay`，此外还可以侦听 [AppWindow.Changed](/uwp/api/windows.ui.windowmanagement.appwindow.changed) 事件以显示有关窗口的信息。 若要调用这些 API，`AppWindowPage` 需要引用承载它的 AppWindow 实例。

如果这就是所需的全部内容，可以在 `AppWindowPage` 中创建一个属性，并在创建时将 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 实例分配给该属性。

**AppWindowPage.xaml.cs**

在 `AppWindowPage` 中，创建一个用于保存 AppWindow 引用的属性。

```csharp
public sealed partial class AppWindowPage : Page
{
    public AppWindow MyAppWindow { get; set; }

    // ...
}
```

**MainPage.xaml.cs**

在 `MainPage` 中，获取对页面实例的引用，并将新建的 AppWindow 分配给 `AppWindowPage` 中的属性。

```csharp
private async void ShowNewWindowButton_Click(object sender, RoutedEventArgs e)
{
    // Create a new window.
    AppWindow appWindow = await AppWindow.TryCreateAsync();

    // Create a Frame and navigate to the Page you want to show in the new window.
    Frame appWindowContentFrame = new Frame();
    appWindowContentFrame.Navigate(typeof(AppWindowPage));

    // Get a reference to the page instance and assign the
    // newly created AppWindow to the MyAppWindow property.
    AppWindowPage page = (AppWindowPage)appWindowContentFrame.Content;
    page.MyAppWindow = appWindow;

    // ...
}
```

### <a name="tracking-app-windows-using-uicontext"></a>使用 UIContext 跟踪应用窗口

你可能还希望能够访问应用的其他组成部分中的 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 实例。 例如，`MainPage` 可以包含一个“全部关闭”按钮，用于关闭所有受跟踪的 AppWindow 实例。

在这种情况下，应使用 [UIContext](/uwp/api/windows.ui.uicontext) 唯一标识符来跟踪 [Dictionary](/dotnet/api/system.collections.generic.dictionary-2?view=dotnet-uwp-10.0) 中的窗口实例。

**MainPage.xaml.cs**

在 `MainPage` 中，创建 Dictionary 作为静态属性。 然后，在创建 Dictionary 时将页面添加到其中，并在页面关闭时删除 Dictionary。 调用 [ElementCompositionPreview.SetAppWindowContent](/api/windows.ui.xaml.hosting.elementcompositionpreview.setappwindowcontent) 后，可以从内容[框架](/uwp/api/Windows.UI.Xaml.Controls.Frame) (`appWindowContentFrame.UIContext`) 获取 UIContext。

```csharp
public sealed partial class MainPage : Page
{
    // Track open app windows in a Dictionary.
    public static Dictionary<UIContext, AppWindow> AppWindows { get; set; }
        = new Dictionary<UIContext, AppWindow>();

    // ...

    private async void ShowNewWindowButton_Click(object sender, RoutedEventArgs e)
    {
        // Create a new window.
        AppWindow appWindow = await AppWindow.TryCreateAsync();

        // Create a Frame and navigate to the Page you want to show in the new window.
        Frame appWindowContentFrame = new Frame();
        appWindowContentFrame.Navigate(typeof(AppWindowPage));

        // Attach the XAML content to the window.
        ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);

        // Add the new page to the Dictionary using the UIContext as the Key.
        AppWindows.Add(appWindowContentFrame.UIContext, appWindow);
        appWindow.Title = "App Window " + AppWindows.Count.ToString();

        // When the window is closed, be sure to release
        // XAML resources and the reference to the window.
        appWindow.Closed += delegate
        {
            MainPage.AppWindows.Remove(appWindowContentFrame.UIContext);
            appWindowContentFrame.Content = null;
            appWindow = null;
        };

        // Show the window.
        await appWindow.TryShowAsync();
    }

    private async void CloseAllButton_Click(object sender, RoutedEventArgs e)
    {
        while (AppWindows.Count > 0)
        {
            await AppWindows.Values.First().CloseAsync();
        }
    }
    // ...
}
```

**AppWindowPage.xaml.cs**

若要在 `AppWindowPage` 代码中使用 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 实例，请使用页面的 [UIContext](/uwp/api/windows.ui.uicontext) 从 `MainPage` 中的静态 Dictionary 检索该实例。 应在页面的 [Loaded](/uwp/api/windows.ui.xaml.frameworkelement.loaded) 事件处理程序而不是构造函数中执行此操作，使 UIContext 不为 null。 可以从页面获取 UIContext：`this.UIContext`。

```csharp
public sealed partial class AppWindowPage : Page
{
    AppWindow window;

    // ...
    public AppWindowPage()
    {
        this.InitializeComponent();

        Loaded += AppWindowPage_Loaded;
    }

    private void AppWindowPage_Loaded(object sender, RoutedEventArgs e)
    {
        // Get the reference to this AppWindow that was stored when it was created.
        window = MainPage.AppWindows[this.UIContext];

        // Set up event handlers for the window.
        window.Changed += Window_Changed;
    }
    // ...
}
```

> [!NOTE]
> `HelloAppWindow` 示例演示了在 `AppWindowPage` 中跟踪窗口的上述两种方式，但你通常只使用其中的一种方式，而不是同时使用。

## <a name="request-window-size-and-placement"></a>请求窗口大小和位置

[AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 类提供多个可用于控制窗口大小和位置的方法。 从方法名称的含义来看，根据环境因素，系统不一定会遵循所请求的更改。

调用 [RequestSize](/uwp/api/windows.ui.windowmanagement.appwindow.requestsize) 来指定所需窗口大小，如下所示。

```csharp
colorPickerAppWindow.RequestSize(new Size(300, 428));
```

用于管理窗口放置的方法被称为 RequestMove*：[RequestMoveAdjacentToCurrentView](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoveadjacenttocurrentview)、[RequestMoveAdjacentToWindow](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoveadjacenttowindow)、[RequestMoveRelativeToDisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoverelativetodisplayregion)、[RequestMoveToDisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindow.requestmovetodisplayregion)。

在此示例中，此代码将窗口移到从中生成了该窗口的主视图旁边。

```csharp
colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
```

若要获取有关窗口当前大小和位置的信息，请调用 [GetPlacement](/uwp/api/windows.ui.windowmanagement.appwindow.getplacement)。 这会返回一个 [AppWindowPlacement](/uwp/api/windows.ui.windowmanagement.appwindowplacement) 对象，该对象提供窗口的当前 [DisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindowplacement.displayregion)、[Offset](/uwp/api/windows.ui.windowmanagement.appwindowplacement.offset) 和 [Size](/uwp/api/windows.ui.windowmanagement.appwindowplacement.size)。

例如，可以调用此代码将窗口移到显示器的右上角。 必须在窗口已显示后调用此代码；否则，调用 GetPlacement 后返回的窗口大小将是 0,0，且偏移量不正确。

```csharp
DisplayRegion displayRegion = window.GetPlacement().DisplayRegion;
double displayRegionWidth = displayRegion.WorkAreaSize.Width;
double windowWidth = window.GetPlacement().Size.Width;
int horizontalOffset = (int)(displayRegionWidth - windowWidth);
window.RequestMoveRelativeToDisplayRegion(displayRegion, new Point(horizontalOffset, 0));
```

## <a name="request-a-presentation-configuration"></a>请求呈现配置

[AppWindowPresenter](/uwp/api/windows.ui.windowmanagement.appwindowpresenter) 类可让你使用能够显示某个 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 的设备的适当预定义配置来显示该 AppWindow。 可以使用 [AppWindowPresentationConfiguration](/uwp/api/windows.ui.windowmanagement.appwindowpresentationconfiguration) 值将窗口置于 `FullScreen` 或 `CompactOverlay` 模式。

此示例演示如何执行以下操作：

- 在可用的窗口呈现状态发生更改时，使用 [AppWindow.Changed](/uwp/api/windows.ui.windowmanagement.appwindow.changed) 事件发出通知。
- 使用 [AppWindow.Presenter](/uwp/api/windows.ui.windowmanagement.appwindow.presenter) 属性获取当前的 [AppWindowPresenter](/uwp/api/windows.ui.windowmanagement.appwindowpresenter)。
- 调用 [IsPresentationSupported](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.ispresentationsupported) 来确定是否支持特定的 [AppWindowPresentationKind](/uwp/api/windows.ui.windowmanagement.appwindowpresentationkind)。
- 调用 [GetConfiguration](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.getconfiguration) 来检查当前使用的配置类型。
- 调用 [RequestPresentation](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.requestpresentation) 来更改当前配置。

```csharp
private void Window_Changed(AppWindow sender, AppWindowChangedEventArgs args)
{
    if (args.DidAvailableWindowPresentationsChange)
    {
        EnablePresentationButtons(sender);
    }

    if (args.DidWindowPresentationChange)
    {
        ConfigText.Text = window.Presenter.GetConfiguration().Kind.ToString();
    }

    if (args.DidSizeChange)
    {
        SizeText.Text = window.GetPlacement().Size.ToString();
    }
}

private void EnablePresentationButtons(AppWindow window)
{
    // Check whether the current AppWindowPresenter supports CompactOverlay.
    if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.CompactOverlay))
    {
        // Show the CompactOverlay button...
        compactOverlayButton.Visibility = Visibility.Visible;
    }
    else
    {
        // Hide the CompactOverlay button...
        compactOverlayButton.Visibility = Visibility.Collapsed;
    }

    // Check whether the current AppWindowPresenter supports FullScreen?
    if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.FullScreen))
    {
        // Show the FullScreen button...
        fullScreenButton.Visibility = Visibility.Visible;
    }
    else
    {
        // Hide the FullScreen button...
        fullScreenButton.Visibility = Visibility.Collapsed;
    }
}

private void CompactOverlayButton_Click(object sender, RoutedEventArgs e)
{
    if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.CompactOverlay)
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.CompactOverlay);
        fullScreenButton.IsChecked = false;
    }
    else
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
    }
}

private void FullScreenButton_Click(object sender, RoutedEventArgs e)
{
    if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.FullScreen)
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.FullScreen);
        compactOverlayButton.IsChecked = false;
    }
    else
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
    }
}
```

## <a name="reuse-xaml-elements"></a>重用 XAML 元素

[AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 允许对同一 UI 线程使用多个 XAML 树。 但是，一个 XAML 元素只能添加到 XAML 树中一次。 若要将 UI 的某个组成部分从一个窗口移到另一个窗口，必须在 XAML 树中管理该组成部分的位置。

此示例演示如何重用 [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker) 控件，同时在主窗口与辅助窗口之间移动它。

颜色选取器在 `MainPage` 的 XAML 中声明，这会将它放到 `MainPage` XAML 树中。

```xaml
<StackPanel x:Name="colorPickerContainer" Grid.Column="1" Background="WhiteSmoke">
    <Button Click="DetachColorPickerButton_Click" HorizontalAlignment="Right">
        <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE2B4;" />
    </Button>
    <ColorPicker x:Name="colorPicker" Margin="12" Width="288"
                 IsColorChannelTextInputVisible="False"
                 ColorChanged="ColorPicker_ColorChanged"/>
</StackPanel>
```

分离颜色选取器以将其放入新的 AppWindow 后，首先必须从 `MainPage` XAML 树中将其删除，方法是从其父容器中将其删除。 此示例也隐藏了父容器，不过不一定要这样。

```csharp
colorPickerContainer.Children.Remove(colorPicker);
colorPickerContainer.Visibility = Visibility.Collapsed;
```

然后，可将颜色选取器添加到新的 XAML 树中。 在此处，先创建充当 ColorPicker 的父容器的 [Grid](/uwp/api/windows.ui.xaml.controls.grid)，并将 ColorPicker 添加为 Grid 的子级。 （这样，以后就可轻松从此 XAML 树中删除 ColorPicker。）然后，在新窗口中将 Grid 设置为 XAML 树的根。

```csharp
Grid appWindowRootGrid = new Grid();
appWindowRootGrid.Children.Add(colorPicker);

// Create a new window
colorPickerAppWindow = await AppWindow.TryCreateAsync();

// Attach the XAML content to our window
ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);
```

当 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 关闭时，将过程反转。 首先，从 [Grid](/uwp/api/windows.ui.xaml.controls.grid) 中删除 [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker)，然后在 `MainPage` 中将 ColorPicker 添加为 [StackPanel](/uwp/api/windows.ui.xaml.controls.stackpanel) 的子级。

```csharp
// When the window is closed, be sure to release XAML resources
// and the reference to the window.
colorPickerAppWindow.Closed += delegate
{
    appWindowRootGrid.Children.Remove(colorPicker);
    appWindowRootGrid = null;
    colorPickerAppWindow = null;

    colorPickerContainer.Children.Add(colorPicker);
    colorPickerContainer.Visibility = Visibility.Visible;
};
```

```csharp
private async void DetachColorPickerButton_Click(object sender, RoutedEventArgs e)
{
    ColorPickerContainer.Visibility = Visibility.Collapsed;

    // Create the color picker window.
    if (colorPickerAppWindow == null)
    {
        ColorPickerContainer.Children.Remove(colorPicker);

        Grid appWindowRootGrid = new Grid();
        appWindowRootGrid.Children.Add(colorPicker);

        // Create a new window
        colorPickerAppWindow = await AppWindow.TryCreateAsync();
        colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
        colorPickerAppWindow.RequestSize(new Size(300, 428));
        colorPickerAppWindow.Title = "Color picker";

        // Attach the XAML content to our window
        ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);

        // When the window is closed, be sure to release XAML resources
        // and the reference to the window.
        colorPickerAppWindow.Closed += delegate
        {
            appWindowRootGrid.Children.Remove(colorPicker);
            appWindowRootGrid = null;
            colorPickerAppWindow = null;

            ColorPickerContainer.Children.Add(colorPicker);
            ColorPickerContainer.Visibility = Visibility.Visible;
        };
    }
    // Show the window.
    await colorPickerAppWindow.TryShowAsync();
}
```

## <a name="show-a-dialog-box"></a>显示对话框

默认情况下，内容对话框相对于根 [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview) 按模式显示。 在 [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) 中使用 [ContentDialog](/uwp/api/windows.ui.xaml.controls.contentdialog) 时，需要手动将对话框中的 XamlRoot 设置为 XAML 宿主的根。

为此，请将 ContentDialog 的 [XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) 属性设置为与 AppWindow 中已包含的某个元素相同的 [XamlRoot](/uwp/api/windows.ui.xaml.xamlroot)。 此处，此代码位于按钮的 [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) 事件处理程序中，因此你可以使用 sender（单击的按钮）来获取 XamlRoot。

```csharp
if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
{
    simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
}
```

如果除了打开主窗口 (ApplicationView) 以外还打开了一个或多个 AppWindow，每个窗口都可以尝试打开一个对话框，因为模式对话框只会阻止它所根植到的窗口。 但是，在每个线程中每次只能打开一个 [ContentDialog](/uwp/api/windows.ui.xaml.controls.contentdialog)。 尝试打开两个 ContentDialog 会引发异常，即使尝试在独立的 AppWindow 中打开。

若要处理此问题，至少应打开 `try/catch` 块中的对话框，以便在另一个对话框已打开的情况下捕获异常。

```csharp
try
{
    ContentDialogResult result = await simpleDialog.ShowAsync();
}
catch (Exception)
{
    // The dialog didn't open, probably because another dialog is already open.
}
```

管理对话框的另一种方式是跟踪当前打开的对话框，并在尝试打开新对话框之前关闭当前打开的对话框。 为此，可在 `MainPage` 中创建一个名为 `CurrentDialog` 的静态属性。

```csharp
public sealed partial class MainPage : Page
{
    // Track the last opened dialog so you can close it if another dialog tries to open.
    public static ContentDialog CurrentDialog { get; set; } = null;

   // ...
}
```

然后，检查是否存在当前已打开的对话框，如果存在，则调用 [Hide](/uwp/api/windows.ui.xaml.controls.contentdialog.hide) 方法将其关闭。 最后，将新对话框分配到 `CurrentDialog`，并尝试显示它。

```csharp
private async void DialogButton_Click(object sender, RoutedEventArgs e)
{
    ContentDialog simpleDialog = new ContentDialog
    {
        Title = "Content dialog",
        Content = "Dialog box for " + window.Title,
        CloseButtonText = "Ok"
    };

    if (MainPage.CurrentDialog != null)
    {
        MainPage.CurrentDialog.Hide();
    }
    MainPage.CurrentDialog = simpleDialog;

    // Use this code to associate the dialog to the appropriate AppWindow by setting
    // the dialog's XamlRoot to the same XamlRoot as an element that is already
    // present in the AppWindow.
    if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
    {
        simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
    }

    try
    {
        ContentDialogResult result = await simpleDialog.ShowAsync();
    }
    catch (Exception)
    {
        // The dialog didn't open, probably because another dialog is already open.
    }
}
```

如果以编程方式关闭某个对话框的做法不可取，请不要将此对话框分配为 `CurrentDialog`。 此处，`MainPage` 显示了一个重要对话框，只有在用户单击 `Ok` 时才应将其关闭。 由于此对话框未分配为 `CurrentDialog`，因此，不会尝试以编程方式关闭它。

```csharp
public sealed partial class MainPage : Page
{
    // Track the last opened dialog so you can close it if another dialog tries to open.
    public static ContentDialog CurrentDialog { get; set; } = null;

    // ...
    private async void DialogButton_Click(object sender, RoutedEventArgs e)
    {
        ContentDialog importantDialog = new ContentDialog
        {
            Title = "Important dialog",
            Content = "This dialog can only be dismissed by clicking Ok.",
            CloseButtonText = "Ok"
        };

        if (MainPage.CurrentDialog != null)
        {
            MainPage.CurrentDialog.Hide();
        }
        // Do not track this dialog as the MainPage.CurrentDialog.
        // It should only be closed by clicking the Ok button.
        MainPage.CurrentDialog = null;

        try
        {
            ContentDialogResult result = await importantDialog.ShowAsync();
        }
        catch (Exception)
        {
            // The dialog didn't open, probably because another dialog is already open.
        }
    }
    // ...
}
```

## <a name="complete-code"></a>完整代码

### <a name="mainpagexaml"></a>MainPage.xaml

```xaml
<Page
    x:Class="HelloAppWindow.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:HelloAppWindow"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition Width="Auto"/>
        </Grid.ColumnDefinitions>
        <StackPanel VerticalAlignment="Center" HorizontalAlignment="Center">
            <Button x:Name="NewWindowButton" Content="Open new window" 
                    Click="ShowNewWindowButton_Click" Margin="0,12"/>
            <Button Content="Open dialog" Click="DialogButton_Click" 
                    HorizontalAlignment="Stretch"/>
            <Button Content="Close all" Click="CloseAllButton_Click" 
                    Margin="0,12" HorizontalAlignment="Stretch"/>
        </StackPanel>

<StackPanel x:Name="colorPickerContainer" Grid.Column="1" Background="WhiteSmoke">
    <Button Click="DetachColorPickerButton_Click" HorizontalAlignment="Right">
        <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE2B4;" />
    </Button>
            <ColorPicker x:Name="colorPicker" Margin="12" Width="288"
                 IsColorChannelTextInputVisible="False"
                 ColorChanged="ColorPicker_ColorChanged"/>
        </StackPanel>
    </Grid>
</Page>

```

### <a name="mainpagexamlcs"></a>MainPage.xaml.cs

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Windows.Foundation;
using Windows.UI;
using Windows.UI.WindowManagement;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Hosting;
using Windows.UI.Xaml.Media;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=402352&clcid=0x409

namespace HelloAppWindow
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        AppWindow colorPickerAppWindow;

        // Track open app windows in a Dictionary.
        public static Dictionary<UIContext, AppWindow> AppWindows { get; set; }
            = new Dictionary<UIContext, AppWindow>();

        // Track the last opened dialog so you can close it if another dialog tries to open.
        public static ContentDialog CurrentDialog { get; set; } = null;

        public MainPage()
        {
            this.InitializeComponent();
        }

        private async void ShowNewWindowButton_Click(object sender, RoutedEventArgs e)
        {
            // Create a new window.
            AppWindow appWindow = await AppWindow.TryCreateAsync();

            // Create a Frame and navigate to the Page you want to show in the new window.
            Frame appWindowContentFrame = new Frame();
            appWindowContentFrame.Navigate(typeof(AppWindowPage));

            // Get a reference to the page instance and assign the
            // newly created AppWindow to the MyAppWindow property.
            AppWindowPage page = (AppWindowPage)appWindowContentFrame.Content;
            page.MyAppWindow = appWindow;
            page.TextColorBrush = new SolidColorBrush(colorPicker.Color);

            // Attach the XAML content to the window.
            ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);

            // Add the new page to the Dictionary using the UIContext as the Key.
            AppWindows.Add(appWindowContentFrame.UIContext, appWindow);
            appWindow.Title = "App Window " + AppWindows.Count.ToString();

            // When the window is closed, be sure to release XAML resources
            // and the reference to the window.
            appWindow.Closed += delegate
            {
                MainPage.AppWindows.Remove(appWindowContentFrame.UIContext);
                appWindowContentFrame.Content = null;
                appWindow = null;
            };

            // Show the window.
            await appWindow.TryShowAsync();
        }

        private async void DialogButton_Click(object sender, RoutedEventArgs e)
        {
            ContentDialog importantDialog = new ContentDialog
            {
                Title = "Important dialog",
                Content = "This dialog can only be dismissed by clicking Ok.",
                CloseButtonText = "Ok"
            };

            if (MainPage.CurrentDialog != null)
            {
                MainPage.CurrentDialog.Hide();
            }
            // Do not track this dialog as the MainPage.CurrentDialog.
            // It should only be closed by clicking the Ok button.
            MainPage.CurrentDialog = null;

            try
            {
                ContentDialogResult result = await importantDialog.ShowAsync();
            }
            catch (Exception)
            {
                // The dialog didn't open, probably because another dialog is already open.
            }
        }

        private async void DetachColorPickerButton_Click(object sender, RoutedEventArgs e)
        {
            // Create the color picker window.
            if (colorPickerAppWindow == null)
            {
                colorPickerContainer.Children.Remove(colorPicker);
                colorPickerContainer.Visibility = Visibility.Collapsed;

                Grid appWindowRootGrid = new Grid();
                appWindowRootGrid.Children.Add(colorPicker);

                // Create a new window
                colorPickerAppWindow = await AppWindow.TryCreateAsync();
                colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
                colorPickerAppWindow.RequestSize(new Size(300, 428));
                colorPickerAppWindow.Title = "Color picker";

                // Attach the XAML content to our window
                ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);

                // Make sure to release the reference to this window, 
                // and release XAML resources, when it's closed
                colorPickerAppWindow.Closed += delegate
                {
                    appWindowRootGrid.Children.Remove(colorPicker);
                    appWindowRootGrid = null;
                    colorPickerAppWindow = null;

                    colorPickerContainer.Children.Add(colorPicker);
                    colorPickerContainer.Visibility = Visibility.Visible;
                };
            }
            // Show the window.
            await colorPickerAppWindow.TryShowAsync();
        }

        private void ColorPicker_ColorChanged(ColorPicker sender, ColorChangedEventArgs args)
        {
            NewWindowButton.Background = new SolidColorBrush(args.NewColor);
        }

        private async void CloseAllButton_Click(object sender, RoutedEventArgs e)
        {
            while (AppWindows.Count > 0)
            {
                await AppWindows.Values.First().CloseAsync();
            }
        }
    }
}

```

### <a name="appwindowpagexaml"></a>AppWindowPage.xaml

```xaml
<Page
    x:Class="HelloAppWindow.AppWindowPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:HelloAppWindow"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <TextBlock x:Name="TitleTextBlock" Text="Hello AppWindow!" FontSize="24" HorizontalAlignment="Center" Margin="24"/>

        <StackPanel VerticalAlignment="Center" HorizontalAlignment="Center">
            <Button Content="Open dialog" Click="DialogButton_Click"
                    Width="200" Margin="0,4"/>
            <Button Content="Move window" Click="MoveWindowButton_Click"
                    Width="200" Margin="0,4"/>
            <ToggleButton Content="Compact Overlay" x:Name="compactOverlayButton" Click="CompactOverlayButton_Click"
                          Width="200" Margin="0,4"/>
            <ToggleButton Content="Full Screen" x:Name="fullScreenButton" Click="FullScreenButton_Click"
                          Width="200" Margin="0,4"/>
            <Grid>
                <TextBlock Text="Size:"/>
                <TextBlock x:Name="SizeText" HorizontalAlignment="Right"/>
            </Grid>
            <Grid>
                <TextBlock Text="Presentation:"/>
                <TextBlock x:Name="ConfigText" HorizontalAlignment="Right"/>
            </Grid>
        </StackPanel>
    </Grid>
</Page>
```

### <a name="appwindowpagexamlcs"></a>AppWindowPage.xaml.cs

```csharp
using System;
using Windows.Foundation;
using Windows.Foundation.Metadata;
using Windows.UI;
using Windows.UI.WindowManagement;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=234238

namespace HelloAppWindow
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class AppWindowPage : Page
    {
        AppWindow window;

        public AppWindow MyAppWindow { get; set; }

        public SolidColorBrush TextColorBrush { get; set; } = new SolidColorBrush(Colors.Black);

        public AppWindowPage()
        {
            this.InitializeComponent();

            Loaded += AppWindowPage_Loaded;
        }

        private void AppWindowPage_Loaded(object sender, RoutedEventArgs e)
        {
            // Get the reference to this AppWindow that was stored when it was created.
            window = MainPage.AppWindows[this.UIContext];

            // Set up event handlers for the window.
            window.Changed += Window_Changed;

            TitleTextBlock.Foreground = TextColorBrush;
        }

        private async void DialogButton_Click(object sender, RoutedEventArgs e)
        {
            ContentDialog simpleDialog = new ContentDialog
            {
                Title = "Content dialog",
                Content = "Dialog box for " + window.Title,
                CloseButtonText = "Ok"
            };

            if (MainPage.CurrentDialog != null)
            {
                MainPage.CurrentDialog.Hide();
            }
            MainPage.CurrentDialog = simpleDialog;

            // Use this code to associate the dialog to the appropriate AppWindow by setting
            // the dialog's XamlRoot to the same XamlRoot as an element that is already 
            // present in the AppWindow.
            if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
            {
                simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
            }

            try
            {
                ContentDialogResult result = await simpleDialog.ShowAsync();
            }
            catch (Exception)
            {
                // The dialog didn't open, probably because another dialog is already open.
            }
        }

        private void Window_Changed(AppWindow sender, AppWindowChangedEventArgs args)
        {
            if (args.DidAvailableWindowPresentationsChange)
            {
                EnablePresentationButtons(sender);
            }

            if (args.DidWindowPresentationChange)
            {
                ConfigText.Text = window.Presenter.GetConfiguration().Kind.ToString();
            }

            if (args.DidSizeChange)
            {
                SizeText.Text = window.GetPlacement().Size.ToString();
            }
        }

        private void EnablePresentationButtons(AppWindow window)
        {
            // Check whether the current AppWindowPresenter supports CompactOverlay.
            if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.CompactOverlay))
            {
                // Show the CompactOverlay button...
                compactOverlayButton.Visibility = Visibility.Visible;
            }
            else
            {
                // Hide the CompactOverlay button...
                compactOverlayButton.Visibility = Visibility.Collapsed;
            }

            // Check whether the current AppWindowPresenter supports FullScreen?
            if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.FullScreen))
            {
                // Show the FullScreen button...
                fullScreenButton.Visibility = Visibility.Visible;
            }
            else
            {
                // Hide the FullScreen button...
                fullScreenButton.Visibility = Visibility.Collapsed;
            }
        }

        private void CompactOverlayButton_Click(object sender, RoutedEventArgs e)
        {
            if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.CompactOverlay)
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.CompactOverlay);
                fullScreenButton.IsChecked = false;
            }
            else
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
            }
        }

        private void FullScreenButton_Click(object sender, RoutedEventArgs e)
        {
            if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.FullScreen)
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.FullScreen);
                compactOverlayButton.IsChecked = false;
            }
            else
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
            }
        }

        private void MoveWindowButton_Click(object sender, RoutedEventArgs e)
        {
            DisplayRegion displayRegion = window.GetPlacement().DisplayRegion;
            double displayRegionWidth = displayRegion.WorkAreaSize.Width;
            double windowWidth = window.GetPlacement().Size.Width;
            int horizontalOffset = (int)(displayRegionWidth - windowWidth);
            window.RequestMoveRelativeToDisplayRegion(displayRegion, new Point(horizontalOffset, 0));
        }
    }
}
```

## <a name="related-topics"></a>相关主题

- [显示多个视图](show-multiple-views.md)
- [使用 ApplicationView 显示多个视图](application-view.md)
