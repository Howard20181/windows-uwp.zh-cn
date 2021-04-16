---
description: 本文演示如何使用 XAML 岛在 WPF 应用中托管标准 WinRT XAML 控件。
title: 使用 XAML 岛在 WPF 应用中托管标准 WinRT XAML 控件
ms.date: 10/02/2020
ms.topic: article
keywords: windows 10, uwp, Windows 窗体, wpf, xaml 岛, 包装控件, 标准控件, InkCanvas, InkToolbar
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 3358e679b44affdb5207207a30fe6b5282ac6759
ms.sourcegitcommit: f7c7a2ae6367e114a8b9d438963082440cd24043
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315080"
---
# <a name="host-a-standard-winrt-xaml-control-in-a-wpf-app-using-xaml-islands"></a>使用 XAML 岛在 WPF 应用中托管标准 WinRT XAML 控件

本文演示了使用 [XAML 岛](xaml-islands.md)在面向 .NET Core 3.1 的 WPF 应用中托管标准 WinRT XAML 控件（即 Windows SDK 提供的第一方 WinRT XAML 控件）的两种方法：

* 它演示了如何在 Windows 社区工具包中使用[包装控件](xaml-islands.md#wrapped-controls) 来托管 UWP [InkCanvas](/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 和 [InkToolbar](/uwp/api/windows.ui.xaml.controls.inktoolbar) 控件。 这些控件包装一小部分有用 WinRT XAML 控件的界面和功能。 可以在 WPF 或 Windows 窗体项目的设计图面中直接添加这些控件，然后在设计器中像使用任何其他 WPF 或 Windows 窗体控件那样使用它们。

* 它还演示了如何使用 Windows 社区工具包中的 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 控件托管 UWP [CalendarView](/uwp/api/Windows.UI.Xaml.Controls.CalendarView) 控件。 由于只有一小部分 WinRT XAML 控件作为包装控件提供，可以使用 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 来托管任何其他标准 WinRT XAML 控件。

尽管本文介绍了如何在 WPF 应用中托管 WinRT XAML 控件，但该过程与在 Windows 窗体应用中托管控件类似。

> [!NOTE]
> 目前仅支持在面向 .NET Core 3.x 的应用中使用 XAML 岛托管 WPF 和 Windows 窗体应用中的 WinRT XAML 控件。 尚不支持在面向 .NET 5 的应用中和面向任何 .NET Framework 版本的应用中使用 XAML 岛。

## <a name="required-components"></a>必需的组件

若要在 WPF（或 Windows 窗体）应用中托管 WinRT XAML 控件，解决方案中需要有以下组件。 本文提供了创建各个组件的说明。

* **项目和应用源代码**。 目前仅支持在面向 .NET Core 3.x 的应用中使用 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 控件来托管 WinRT XAML 控件。

* **定义派生自 XamlApplication 的根应用程序类的 UWP 应用项目**。 WPF 或 Windows 窗体项目必须有权访问 Windows 社区工具包提供的 [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) 类的实例，以便它能够发现和加载自定义 UWP XAML 控件。 建议使用 WPF 或 Windows 窗体应用解决方案中单独的 UWP 应用项目定义此对象，来实现访问。 

    > [!NOTE]
    > 尽管托管第一方 WinRT XAML 控件不需要 `XamlApplication` 对象，但应用需要此对象来支持各种 XAML 岛方案，其中包括托管自定义 WinRT XAML 控件。 因此，建议始终在使用 XAML 岛的任何解决方案中定义一个 `XamlApplication` 对象。

    > [!NOTE]
    > 解决方案只能包含一个定义 `XamlApplication` 对象的项目。 定义 `XamlApplication` 对象的项目必须包含在 XAML 岛上托管 WinRT XAML 控件所用的全部其他库和项目的引用。

## <a name="create-a-wpf-project"></a>创建 WPF 项目

开始操作前，先按照下面的说明创建 WPF 项目，并对它进行配置，用于托管 XAML 岛。 如果你有现有的 WPF 项目，则可以将这些步骤和代码示例用于你的项目。

1. 在 Visual Studio 2019 中，新建一个“WPF 应用(.NET Core)”项目  。 若尚未创建项目，必须先安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet/current) 的最新版本。

2. 确保已启用[包引用](/nuget/consume-packages/package-references-in-project-files)：

    1. 在 Visual Studio 中，单击“工具”->“NuGet 程序包管理器”->“程序包管理器设置”。
    2. 确保为“默认程序包管理格式”选择了“PackageReference”   。

3. 在“解决方案资源管理器”中，右键单击相应的 WPF 项目并选择“管理 NuGet 包” 。

4. 选择“浏览”选项卡，搜索 [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) 包，并安装最新稳定版。 此包提供了使用适用于 WPF 的 WinRT XAML 包装控件所需的全部内容，包括 [InkCanvas](/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)、[InkToolbar](/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) 和 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 控件。
    > [!NOTE]
    > Windows 窗体应用必须使用 [Microsoft.Toolkit.Forms.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) 包。

5. 将解决方案配置为面向特定平台，例如 x86 或 x64。 大多数 XAML 岛方案在面向任何 CPU 的项目中不受支持。

    1. 在“解决方案资源管理器”中，右键单击相应的解决方案节点，选择“属性” -> “配置属性” -> “配置管理器”   。 
    2. 在“活动解决方案平台”  下，选择“新建”  。 
    3. 在“新建解决方案平台”对话框中，选择“x64”或“x86”，并按“确认”     。 
    4. 关闭打开的对话框。

## <a name="define-a-xamlapplication-class-in-a-uwp-app-project"></a>在 UWP 应用项目中定义 XamlApplication 类

接下来，将 UWP 应用项目添加到解决方案，并将此项目中的默认 `App` 类修改为派生自 Windows 社区工具包提供的 [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) 类。 此类支持 [IXamlMetadaraProvider](/uwp/api/Windows.UI.Xaml.Markup.IXamlMetadataProvider) 接口，该接口使应用能够在运行时发现和加载应用程序的当前目录内程序集中的自定义 UWP XAML 控件的元数据。 此类还为当前线程初始化 UWP XAML 框架。

> [!NOTE]
> 尽管托管第一方 WinRT XAML 控件不需要此步骤，但应用需要 `XamlApplication` 对象来支持各种 XAML 岛方案，其中包括托管自定义 WinRT XAML 控件。 因此，建议始终在使用 XAML 岛的任何解决方案中定义一个 `XamlApplication` 对象。

1. 在“解决方案资源管理器”中，右键单击解决方案节点，然后选择“添加” -> “新建项目”  。
2. 向你的解决方案中添加一个空白应用（通用 Windows）项目。 确保目标版本和最低版本均设置为 Windows 10 1903（内部版本 18362）或更高版本。 此外，请确保这个新的 UWP 项目未在 WPF 项目的子文件夹中。 否则，WPF 应用稍后将尝试生成 UWP XAML 标记，就像它是 WPF XAML 一样。
3. 在 UWP 应用项目中，安装 [Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) NuGet 包（最新稳定版）。
4. 打开 App.xaml 文件，将此文件的内容替换为以下 XAML。 将 `MyUWPApp` 替换为 UWP 应用项目的命名空间。

    ```xml
    <xaml:XamlApplication
        x:Class="MyUWPApp.App"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xaml="using:Microsoft.Toolkit.Win32.UI.XamlHost"
        xmlns:local="using:MyUWPApp">
    </xaml:XamlApplication>
    ```

5. 打开 App.xaml.cs 文件，将此文件的内容替换为以下代码。 将 `MyUWPApp` 替换为 UWP 应用项目的命名空间。

    ```csharp
    namespace MyUWPApp
    {
        public sealed partial class App : Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication
        {
            public App()
            {
                this.Initialize();
            }
        }
    }
    ```

6. 从 UWP 应用项目删除 MainPage.xaml 文件。
7. 生成 UWP 应用项目。

## <a name="add-a-reference-to-the-uwp-project-in-your-wpf-project"></a>在 WPF 项目中添加对 UWP 项目的引用

1. 在 WPF 项目文件中指定兼容的框架版本。 

    1. 在“解决方案资源管理器”中，双击 WPF 项目节点，在编辑器中打开项目文件。
    2. 在第一个 PropertyGroup 元素中添加以下子元素。 根据需要更改值的 `19041` 部分，以匹配 UWP 项目的目标版本和最低 OS 版本。

        ```xml
        <AssetTargetFallback>uap10.0.19041</AssetTargetFallback>
        ```

        完成后，PropertyGroup 元素应类似于以下内容。

        ```xml
        <PropertyGroup>
            <OutputType>WinExe</OutputType>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UseWPF>true</UseWPF>
            <Platforms>AnyCPU;x64</Platforms>
            <AssetTargetFallback>uap10.0.19041</AssetTargetFallback>
        </PropertyGroup>
        ```

2. 在“解决方案资源管理器”中，右键单击 WPF 项目下的“依赖项”节点，添加对 UWP 应用项目的引用 。

## <a name="instantiate-the-xamlapplication-object-in-the-entry-point-of-your-wpf-app"></a>在 WPF 应用入口点中实例化 XamlApplication 对象

接下来，将代码添加到 WPF 应用入口点，用于创建刚刚在 UWP 项目中定义的 `App` 类的实例（现在此类派生自 `XamlApplication`）。

1. 在 WPF 项目中，右键单击项目节点，选择“添加” -> “新建项目”，然后选择“类”  。 将类命名为“Program”，然后单击“添加” 。

2. 将生成的 `Program` 类替换为以下代码，然后保存文件。 将 `MyUWPApp` 替换为 UWP 应用项目的命名空间，并将 `MyWPFApp` 替换为 WPF 应用项目的命名空间。

    ```csharp
    public class Program
    {
        [System.STAThreadAttribute()]
        public static void Main()
        {
            using (new MyUWPApp.App())
            {
                MyWPFApp.App app = new MyWPFApp.App();
                app.InitializeComponent();
                app.Run();
            }
        }
    }
    ```

3. 右键单击项目节点，然后选择“属性”。

4. 在属性的“应用程序”选项卡中，单击“启动对象”下拉列表，选择在上一步中添加的 `Program` 类的完全限定的名称 。 
    > [!NOTE]
    > 默认情况下，WPF 项目在不可修改的生成代码文件中定义 `Main` 入口点函数。 此步骤将项目的入口点更改为新 `Program` 类的 `Main` 方法，这样添加的代码即可在应用启动过程中尽可能早地运行。 

5. 保存对项目属性的更改。

## <a name="host-an-inkcanvas-and-inktoolbar-by-using-wrapped-controls"></a>使用包装的控件托管 InkCanvas 和 InkToolbar

将项目配置为使用 UWP XAML 岛后，便可以将 [InkCanvas](/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) 和 [InkToolbar](/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) 包装的 WinRT XAML 控件添加到应用。

1. 在 WPF 项目中，打开 MainWindow.xaml 文件。

2. 在 XAML 文件顶部附近的 Window 元素中，添加以下属性。 这会引用 [InkCanvas](/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) 和 [InkToolbar](/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) 包装的 WinRT XAML 控件的 XAML 命名空间。

    ```xml
    xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    添加此属性后，Window 元素现在应如下所示。

    ```xml
    <Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:WpfApp"
            xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            x:Class="WpfApp.MainWindow"
            mc:Ignorable="d"
            Title="MainWindow" Height="800" Width="800">
    ```

3. 在 MainWindow.xaml 文件中，将现有的 `<Grid>` 元素替换为以下 XAML。 此 XAML 将 [InkCanvas](/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) 和 [InkToolbar](/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) 控件（使用前面作为命名空间定义的 Controls 关键字作为前缀）添加到 `<Grid>`。

    ```xml
    <Grid Margin="10,50,10,10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <Controls:InkToolbar x:Name="myInkToolbar" TargetInkCanvas="{x:Reference myInkCanvas}" Grid.Row="0" Width="300"
            Height="50" Margin="10,10,10,10" HorizontalAlignment="Left" VerticalAlignment="Top" />
        <Controls:InkCanvas x:Name="myInkCanvas" Grid.Row="1" HorizontalAlignment="Left" Width="600" Height="400"
            Margin="10,10,10,10" VerticalAlignment="Top" />
    </Grid>
    ```

    > [!NOTE]
    > 还可以将这些控件和其他包装控件从“工具箱”的“Windows 社区工具包”部分拖到设计器中，通过这种方式将它们添加到该窗口中。

4. 保存 MainWindow.xaml 文件。

    如果设备支持数字笔（如 Surface），并且你是在物理计算机上运行此实验室，则现在可以使用笔生成并运行应用，并在屏幕上绘制数字墨迹。 但是，如果没有支持笔的设备，并且尝试使用鼠标进行签名，则不会有任何活动。 发生这种情况的原因是，默认情况下，仅为数字笔启用 InkCanvas 控件。 不过，你可以更改此行为。

5. 打开 MainWindow.xaml.cs 文件。

6. 将以下命名空间声明添加到文件的顶部：

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

7. 找到 `MainWindow()` 构造函数。 在 `InitializeComponent()` 方法后添加以下代码行，并保存代码文件。

    ```csharp
    this.myInkCanvas.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    可以使用 InkPresenter  对象自定义默认墨迹书写体验。 此代码使用 InputDeviceTypes  属性启用鼠标和笔输入。

8. 再次按 F5，在调试程序中生成并运行应用。 如果你使用的是带鼠标的计算机，请确认可以用鼠标在墨迹画布空间中绘制一些内容。

## <a name="host-a-calendarview-by-using-the-host-control"></a>使用主机控件托管 CalendarView

将 [InkCanvas](/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) 和 [InkToolbar](/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) 包装的 WinRT XAML 控件添加到应用后，便可以使用 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 控件将 [CalendarView](/uwp/api/Windows.UI.Xaml.Controls.CalendarView) 添加到应用。

> [!NOTE]
> [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 控件由 [Microsoft.Toolkit.Wpf.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost) 包提供。 此包包含在你之前安装的 [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) 包中。

1. 在“解决方案资源管理器”中，打开“MainWindow.xaml”文件 。

2. 在 XAML 文件顶部附近的 Window 元素中，添加以下属性。 这会引用 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 控件的 XAML 命名空间。

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    添加此属性后，Window 元素现在应如下所示。

    ```xml
    <Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:WpfApp"
            xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            x:Class="WpfApp.MainWindow"
            mc:Ignorable="d"
            Title="MainWindow" Height="800" Width="800">
    ```

4. 在 MainWindow.xaml 文件中，将现有的 `<Grid>` 元素替换为以下 XAML。 此 XAML 将一行添加到网格，并将 [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) 对象添加到最后一行。 为了托管 UWP [CalendarView](/uwp/api/Windows.UI.Xaml.Controls.CalendarView) 控件，此 XAML 将 `InitialTypeName` 属性设置为控件的完全限定的名称。 此 XAML 还为 `ChildChanged` 事件定义了一个事件处理程序，该事件是在呈现托管控件时引发的。

    ```xml
    <Grid Margin="10,50,10,10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <Controls:InkToolbar x:Name="myInkToolbar" TargetInkCanvas="{x:Reference myInkCanvas}" Grid.Row="0" Width="300"
            Height="50" Margin="10,10,10,10" HorizontalAlignment="Left" VerticalAlignment="Top" />
        <Controls:InkCanvas x:Name="myInkCanvas" Grid.Row="1" HorizontalAlignment="Left" Width="600" Height="400" 
            Margin="10,10,10,10" VerticalAlignment="Top" />
        <xamlhost:WindowsXamlHost x:Name="myCalendar" InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Row="2" 
              Margin="10,10,10,10" Width="600" Height="300" ChildChanged="MyCalendar_ChildChanged"  />
    </Grid>
    ```

5. 保存 MainWindow.xaml 文件，然后打开 MainWindow.xaml.cs 文件。

7. 将以下命名空间声明添加到文件的顶部：

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

10. 将以下 `ChildChanged` 事件处理程序方法添加到 `MainWindow` 类，并保存代码文件。 如果已呈现托管控件，此事件处理程序将运行并为日历控件的 `SelectedDatesChanged` 事件创建一个简单的事件处理程序。

    ```csharp
    private void MyCalendar_ChildChanged(object sender, EventArgs e)
    {
        WindowsXamlHost windowsXamlHost = (WindowsXamlHost)sender;

        Windows.UI.Xaml.Controls.CalendarView calendarView =
            (Windows.UI.Xaml.Controls.CalendarView)windowsXamlHost.Child;

        if (calendarView != null)
        {
            calendarView.SelectedDatesChanged += (obj, args) =>
            {
                if (args.AddedDates.Count > 0)
                {
                    MessageBox.Show("The user selected a new date: " + 
                        args.AddedDates[0].DateTime.ToString());
                }
            };
        }
    }
    ```

11. 再次按 F5，在调试程序中生成并运行应用。 确认日历控件现在显示在窗口的底部。

## <a name="package-the-app"></a>打包应用

可以选择在 [MSIX 包](/windows/msix)中打包 WPF 应用以供部署。 MSIX 是适用于 Windows 的新式应用打包技术，并且基于 MSI、.appx、App-V 和 ClickOnce 安装技术的组合。

下面的说明介绍了如何在 Visual Studio 2019 中使用 [Windows 应用程序打包项目](/windows/msix/desktop/desktop-to-uwp-packaging-dot-net)将解决方案中的所有组件打包到 MSIX 包。 只有在需要将 WPF 应用打包到 MSIX 包时，才需要使用这些步骤。

> [!NOTE]
> 如果选择不在 [MSIX 包](/windows/msix)中打包应用程序以供部署，则运行应用的计算机必须安装有 [Visual C++ 运行时](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)。

1. 向解决方案中添加一个新的 [Windows 应用程序打包项目](/windows/msix/desktop/desktop-to-uwp-packaging-dot-net)。 创建项目时，请选择与 UWP 项目相同的目标版本和最低版本 。

2. 在打包项目中，右键单击“应用程序”节点，然后选择“添加引用”   。 在项目列表中，选择解决方案中的 WPF 项目，然后单击“确认”。

    > [!NOTE]
    > 如果你想要在 Microsoft Store 中发布应用，必须在打包项目中添加对 UWP 项目的引用。

3. 将解决方案配置为面向特定平台，例如 x86 或 x64。 在使用 Windows 应用程序打包项目将 WPF 应用生成到 MSIX 包中时，必须执行此操作。

    1. 在“解决方案资源管理器”中，右键单击相应的解决方案节点，选择“属性” -> “配置属性” -> “配置管理器”   。
    2. 在“活动解决方案平台”下，选择“x64”或“x86” 。
    3. 在 WPF 项目的行中，在“平台”列中选择“新建”。
    4. 在“新建解决方案平台”对话框中，选择“x64”或“x86”（你为“活动解决方案平台”选择的相同平台），然后单击“确定”。
    5. 关闭打开的对话框。

5. 生成并运行打包项目。 确认 WPF 按预期运行且 UWP 控件按预期显示。

## <a name="related-topics"></a>相关主题

* [在桌面应用中托管 UWP XAML 控件（XAML 岛）](xaml-islands.md)
* [XAML 岛代码示例](https://github.com/microsoft/Xaml-Islands-Samples)
* [InkCanvas](/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)
* [InkToolbar](/windows/communitytoolkit/controls/wpf-winforms/inktoolbar)
* [WindowsXamlHost](/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
