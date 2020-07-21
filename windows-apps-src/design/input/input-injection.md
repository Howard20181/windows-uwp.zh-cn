---
Description: 模拟和自动执行 Windows 应用中的设备（例如键盘、鼠标、触摸、笔和游戏板）的输入。
title: 通过输入式注入模拟用户输入
label: Input injection
template: detail.hbs
keywords: 设备, 数字化器, 输入, 交互, 注入
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 7bcd63d940e53d90d925f83f27e4c77165a11650
ms.sourcegitcommit: 124cea09be7f12444bfff541badb65d539d55c83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84124119"
---
# <a name="simulate-user-input-through-input-injection"></a>通过输入式注入模拟用户输入

通过 Windows 应用程序中的键盘、鼠标、触摸、笔和游戏板等设备模拟并自动执行用户输入。

> **重要 API**：[**Windows.UI.Input.Preview.Injection**](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection)

## <a name="overview"></a>概述

使用输入注入，你的 Windows 应用程序可以模拟来自各种输入设备的输入，并将该输入定向到任何位置，包括应用的工作区之外（即使是使用管理员权限运行的应用，如注册表编辑器）。

对于需要提供可访问性、测试（即席、自动）以及远程访问和支持功能的功能的 Windows 应用程序和工具，输入注入非常有用。

## <a name="setup"></a>设置

若要在 Windows 应用中使用输入注入 Api，需要将以下内容添加到应用程序清单中：

1. 右键单击 **Package.appxmanifest** 文件并选择**查看代码**。
1. 将下列内容插入到 `Package` 代码中：
    - `xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"`
    - `IgnorableNamespaces="rescap"`
1. 将下列内容插入到 `Capabilities` 代码中：
    - `<rescap:Capability Name="inputInjectionBrokered" />`

## <a name="duplicate-user-input"></a>复制用户输入

| ![触控输入注入示例](images/injection/touch-input-injection.gif) | 
|:--:|
| *触控输入注入示例* |

在此示例中，我们演示了如何使用输入式注入 API ([Windows.UI.Input.Preview.Injection](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection)) 侦听应用一个区域的鼠标输入事件，并模拟其他区域中对应的触控输入事件。

**从[输入注入示例下载此示例（鼠标到触摸）](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-input-injection-mouse-to-touch.zip)**

1. 首先，我们设置 UI (MainPage.xaml)。

    我们有两个网格区域（一个用于鼠标输入，另一个用于注入式触控输入），每个区域均有四个按钮。
      > [!NOTE] 
      > 必须为网格背景分配一个值（ `Transparent` 在本例中为），否则，将不会检测到指针事件。

    在输入区域检测到任何鼠标点击时，对应的触控事件将插入到输入式注入区域。 来自注入输入的按钮点击在标题区域报告。

    ```xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel Grid.Row="0"
                    Margin="10">
            <TextBlock Style="{ThemeResource TitleTextBlockStyle}" 
                       Name="titleText"
                       Text="Touch input injection"
                       HorizontalTextAlignment="Center" />
            <TextBlock Style="{ThemeResource BodyTextBlockStyle}"
                       Name="statusText"
                       HorizontalTextAlignment="Center" />
        </StackPanel>
        <Grid HorizontalAlignment="Center"
                        Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Column="0" 
                       Grid.Row="0" 
                       Style="{ThemeResource CaptionTextBlockStyle}"
                       Text="User mouse input area"/>
            <!-- Background must be set to something, otherwise pointer events are not detected. -->
            <Grid Name="ContainerInput" 
                  Grid.Column="0" 
                  Grid.Row="1"
                  HorizontalAlignment="Stretch" 
                  Background="Transparent" 
                  BorderBrush="Green" 
                  BorderThickness="2" 
                  MinHeight="100" MinWidth="300" 
                  Margin="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Button Name="B1" 
                        Grid.Column="0" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50"
                        Content="B1" />
                <Button Name="B2" 
                        Grid.Column="1" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50"
                        Content="B2" />
                <Button Name="B3" 
                        Grid.Column="2" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50"
                        Content="B3" />
                <Button Name="B4" 
                        Grid.Column="3" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50"
                        Content="B4" />
            </Grid>
            <TextBlock Grid.Column="1" 
                       Grid.Row="0"                         
                       Style="{ThemeResource CaptionTextBlockStyle}"
                       Text="Injected touch input area"/>
            <Grid Name="ContainerInject"
                  Grid.Column="1"  
                  Grid.Row="1"
                  HorizontalAlignment="Stretch"
                  BorderBrush="Red" 
                  BorderThickness="2" 
                  MinHeight="100" MinWidth="300" 
                  Margin="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Button Name="B1i" Click="Button_Click_Injected"
                        Content="B1i"
                        Grid.Column="0" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50" />
                <Button Name="B2i" Click="Button_Click_Injected"
                        Content="B2i"
                        Grid.Column="1" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50" />
                <Button Name="B3i" Click="Button_Click_Injected"
                        Content="B3i"
                        Grid.Column="2" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50" />
                <Button Name="B4i" Click="Button_Click_Injected"
                        Content="B4i"
                        Grid.Column="3" 
                        HorizontalAlignment="Center" 
                        Width="50" Height="50" />
            </Grid>
        </Grid>
    </Grid>
    ```

2. 接下来，我们将初始化应用。
    
    在此代码段中，我们声明全局对象并声明鼠标输入区域内指针事件的侦听器 ([AddHandler](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.addhandler))，其在按钮点击事件中可能被标记为已处理。

    [InputInjector](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.inputinjector) 对象表示用于发送输入数据的虚拟输入设备。

    在 `ContainerInput_PointerPressed` 处理程序中，我们调用触控注入函数。

    在 `ContainerInput_PointerReleased` 处理程序中，我们调用 UninitializeTouchInjection 来关闭 [InputInjector](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.inputinjector) 对象。

    ```csharp
    public sealed partial class MainPage : Page
    {
        /// <summary>
        /// The virtual input device.
        /// </summary>
        InputInjector _inputInjector;

        /// <summary>
        /// Initialize the app, set the window size, 
        /// and add pointer input handlers for the container.
        /// </summary>
        public MainPage()
        {
            this.InitializeComponent();

            ApplicationView.PreferredLaunchViewSize =
                new Size(600, 200);
            ApplicationView.PreferredLaunchWindowingMode =
                ApplicationViewWindowingMode.PreferredLaunchViewSize;

            // Button handles PointerPressed/PointerReleased in 
            // the Tapped routed event, but we need the container Grid 
            // to handle them also. Add a handler for both 
            // PointerPressedEvent and PointerReleasedEvent on the input Grid 
            // and set handledEventsToo to true.
            ContainerInput.AddHandler(PointerPressedEvent,
                new PointerEventHandler(ContainerInput_PointerPressed), true);
            ContainerInput.AddHandler(PointerReleasedEvent,
                new PointerEventHandler(ContainerInput_PointerReleased), true);
        }

        /// <summary>
        /// PointerReleased handler for all pointer conclusion events.
        /// PointerPressed and PointerReleased events do not always occur 
        /// in pairs, so your app should listen for and handle any event that 
        /// might conclude a pointer down (such as PointerExited, PointerCanceled, 
        /// and PointerCaptureLost).  
        /// </summary>
        /// <param name="sender">Source of the click event</param>
        /// <param name="e">Event args for the button click routed event</param>
        private void ContainerInput_PointerReleased(
            object sender, PointerRoutedEventArgs e)
        {
            // Prevent most handlers along the event route from handling event again.
            e.Handled = true;

            // Shut down the virtual input device.
            _inputInjector.UninitializeTouchInjection();
        }

        /// <summary>
        /// PointerPressed handler.
        /// PointerPressed and PointerReleased events do not always occur 
        /// in pairs. Your app should listen for and handle any event that 
        /// might conclude a pointer down (such as PointerExited, 
        /// PointerCanceled, and PointerCaptureLost).  
        /// </summary>
        /// <param name="sender">Source of the click event</param>
        /// <param name="e">Event args for the button click routed event</param>
        private void ContainerInput_PointerPressed(
            object sender, PointerRoutedEventArgs e)
        {
            // Prevent most handlers along the event route from 
            // handling the same event again.
            e.Handled = true;

            InjectTouchForMouse(e.GetCurrentPoint(ContainerInput));

        }
        ...
    }
    ```
3. 下面介绍触控输入注入函数。

    首先，我们调用 [TryCreate](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.inputinjector.trycreate) 来实例化 [InputInjector](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.inputinjector) 对象。

    然后，我们通过 `Default` 的 [InjectedInputVisualizationMode](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.injectedinputvisualizationmode) 调用 [InitializeTouchInjection](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.inputinjector.initializetouchinjection)。

    计算注入点之后，我们调用 [InjectedInputTouchInfo](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.injectedinputtouchinfo) 来初始化要注入的触控点列表（在本示例中，我们创建一个与鼠标输入指针对应的触控点）。

    最后，我们调用 [InjectTouchInput](https://docs.microsoft.com/uwp/api/windows.ui.input.preview.injection.inputinjector.injecttouchinput) 两次，第一次用于指针向下，第二次用于指针向上。

    ```csharp
    /// <summary>
    /// Inject touch input on injection target corresponding 
    /// to mouse click on input target.
    /// </summary>
    /// <param name="pointerPoint">The mouse click pointer.</param>
    private void InjectTouchForMouse(PointerPoint pointerPoint)
    {
        // Create the touch injection object.
        _inputInjector = InputInjector.TryCreate();

        if (_inputInjector != null)
        {
            _inputInjector.InitializeTouchInjection(
                InjectedInputVisualizationMode.Default);

            // Create a unique pointer ID for the injected touch pointer.
            // Multiple input pointers would require more robust handling.
            uint pointerId = pointerPoint.PointerId + 1;

            // Get the bounding rectangle of the app window.
            Rect appBounds =
                Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().VisibleBounds;

            // Get the top left screen coordinates of the app window rect.
            Point appBoundsTopLeft = new Point(appBounds.Left, appBounds.Top);

            // Get a reference to the input injection area.
            GeneralTransform injectArea =
                ContainerInject.TransformToVisual(Window.Current.Content);

            // Get the top left screen coordinates of the input injection area.
            Point injectAreaTopLeft = injectArea.TransformPoint(new Point(0, 0));

            // Get the screen coordinates (relative to the input area) 
            // of the input pointer.
            int pointerPointX = (int)pointerPoint.Position.X;
            int pointerPointY = (int)pointerPoint.Position.Y;

            // Create the point for input injection and calculate its screen location.
            Point injectionPoint =
                new Point(
                    appBoundsTopLeft.X + injectAreaTopLeft.X + pointerPointX,
                    appBoundsTopLeft.Y + injectAreaTopLeft.Y + pointerPointY);

            // Create a touch data point for pointer down.
            // Each element in the touch data list represents a single touch contact. 
            // For this example, we're mirroring a single mouse pointer.
            List<InjectedInputTouchInfo> touchData =
                new List<InjectedInputTouchInfo>
                {
                    new InjectedInputTouchInfo
                    {
                        Contact = new InjectedInputRectangle
                        {
                            Left = 30, Top = 30, Bottom = 30, Right = 30
                        },
                        PointerInfo = new InjectedInputPointerInfo
                        {
                            PointerId = pointerId,
                            PointerOptions =
                            InjectedInputPointerOptions.PointerDown |
                            InjectedInputPointerOptions.InContact |
                            InjectedInputPointerOptions.New,
                            TimeOffsetInMilliseconds = 0,
                            PixelLocation = new InjectedInputPoint
                            {
                                PositionX = (int)injectionPoint.X ,
                                PositionY = (int)injectionPoint.Y
                            }
                    },
                    Pressure = 1.0,
                    TouchParameters =
                        InjectedInputTouchParameters.Pressure |
                        InjectedInputTouchParameters.Contact
                }
            };

            // Inject the touch input. 
            _inputInjector.InjectTouchInput(touchData);

            // Create a touch data point for pointer up.
            touchData = new List<InjectedInputTouchInfo>
            {
                new InjectedInputTouchInfo
                {
                    PointerInfo = new InjectedInputPointerInfo
                    {
                        PointerId = pointerId,
                        PointerOptions = InjectedInputPointerOptions.PointerUp
                    }
                }
            };

            // Inject the touch input. 
            _inputInjector.InjectTouchInput(touchData);
        }
    }
    ```

4. 最后，我们处理输入式注入区域中的所有按钮[点击](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.buttonbase)路由事件，并使用所点击按钮的名称更新 UI。

## <a name="see-also"></a>另请参阅

### <a name="topic-samples"></a>主题示例

- [输入式注入示例（鼠标到触控）](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-input-injection-mouse-to-touch.zip)
