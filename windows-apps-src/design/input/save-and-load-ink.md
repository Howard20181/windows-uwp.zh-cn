---
Description: 支持 Windows Ink 的 windows 应用可以序列化墨迹笔划并将其反序列化为墨迹序列化格式（ISF）文件。 ISF 文件是一种 GIF 图像，带有适用于所有笔划墨迹属性和行为的其他元数据。 无法启用墨迹的应用可以查看静态 GIF 图像，包括 alpha 通道背景透明度。
title: 存储和检索 Windows Ink 笔划数据
ms.assetid: C96C9D2F-DB69-4883-9809-4A0DF7CEC506
label: Store and retrieve Windows Ink stroke data
template: detail.hbs
keywords: Windows Ink, Windows Inking, DirectInk, InkPresenter, InkCanvas, ISF, 墨迹序列化格式, 用户交互, 输入
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 819358fb775444d62cbad414668a779fc5c305ca
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970242"
---
# <a name="store-and-retrieve-windows-ink-stroke-data"></a>存储和检索 Windows Ink 笔划数据


支持 Windows Ink 的 windows 应用可以序列化墨迹笔划并将其反序列化为墨迹序列化格式（ISF）文件。 ISF 文件是一种 GIF 图像，带有适用于所有笔划墨迹属性和行为的其他元数据。 无法启用墨迹的应用可以查看静态 GIF 图像，包括 alpha 通道背景透明度。

> [!NOTE]
> ISF 为墨迹的最紧凑持久表现形式。 该格式可以嵌入到二进制文档格式（例如 GIF 文件），也可以直接放置在剪贴板上。

> **重要 API**：[**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)、[**Windows.UI.Input.Inking**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking)

## <a name="save-ink-strokes-to-a-file"></a>将笔划墨迹保存到文件

在此，我们展示如何保存在 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 控件上绘制的笔划墨迹。

**下载此示例从[墨迹序列化格式（ISF）文件保存和加载墨笔划](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)**

1.  首先，我们设置 UI。

    UI 包括“保存”、“加载”、“清除”按钮和 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)。
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="Header" 
                       Text="Basic ink store sample" 
                       Style="{ThemeResource HeaderTextBlockStyle}" 
                       Margin="10,0,0,0" />
            <Button x:Name="btnSave" 
                    Content="Save" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnLoad" 
                    Content="Load" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnClear" 
                    Content="Clear" 
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <InkCanvas x:Name="inkCanvas" />
        </Grid>
    </Grid>
```

2.  然后，我们设置一些基本墨迹输入行为。

    [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 配置为将来自笔和鼠标的输入数据解释为笔划墨迹 ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes))，并且将声明用于按钮上的单击事件的侦听器。
```csharp
public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for button click to initiate save.
        btnSave.Click += btnSave_Click;
        // Listen for button click to initiate load.
        btnLoad.Click += btnLoad_Click;
        // Listen for button click to clear ink canvas.
        btnClear.Click += btnClear_Click;
    }
```

3.  最后，我们将墨迹保存在 "**保存**" 按钮的 click 事件处理程序中。

    [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker) 允许用户选择保存墨迹数据的文件和位置。

    选定文件后，我们打开设置为 [**ReadWrite**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IRandomAccessStream)的 [**IRandomAccessStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileAccessMode) 流。

    然后，调用 [**SaveAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.iinkstrokecontainer.saveasync) 将由 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 管理的笔划墨迹序列化到流。

```csharp
// Save ink data to a file.
    private async void btnSave_Click(object sender, RoutedEventArgs e)
    {
        // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

        // Strokes present on ink canvas.
        if (currentStrokes.Count > 0)
        {
            // Let users choose their ink file using a file picker.
            // Initialize the picker.
            Windows.Storage.Pickers.FileSavePicker savePicker = 
                new Windows.Storage.Pickers.FileSavePicker();
            savePicker.SuggestedStartLocation = 
                Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
            savePicker.FileTypeChoices.Add(
                "GIF with embedded ISF", 
                new List<string>() { ".gif" });
            savePicker.DefaultFileExtension = ".gif";
            savePicker.SuggestedFileName = "InkSample";

            // Show the file picker.
            Windows.Storage.StorageFile file = 
                await savePicker.PickSaveFileAsync();
            // When chosen, picker returns a reference to the selected file.
            if (file != null)
            {
                // Prevent updates to the file until updates are 
                // finalized with call to CompleteUpdatesAsync.
                Windows.Storage.CachedFileManager.DeferUpdates(file);
                // Open a file stream for writing.
                IRandomAccessStream stream = await file.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);
                // Write the ink strokes to the output stream.
                using (IOutputStream outputStream = stream.GetOutputStreamAt(0))
                {
                    await inkCanvas.InkPresenter.StrokeContainer.SaveAsync(outputStream);
                    await outputStream.FlushAsync();
                }
                stream.Dispose();

                // Finalize write so other apps can update file.
                Windows.Storage.Provider.FileUpdateStatus status =
                    await Windows.Storage.CachedFileManager.CompleteUpdatesAsync(file);

                if (status == Windows.Storage.Provider.FileUpdateStatus.Complete)
                {
                    // File saved.
                }
                else
                {
                    // File couldn't be saved.
                }
            }
            // User selects Cancel and picker returns null.
            else
            {
                // Operation cancelled.
            }
        }
    }
```

> [!NOTE]
> GIF 是保存墨迹数据的唯一受支持的文件格式。 但是，[**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.loadasync) 方法（在下一部分中演示）不支持向后兼容的其他格式。

## <a name="load-ink-strokes-from-a-file"></a>从文件加载笔划墨迹

在此，我们展示如何从文件加载笔划墨迹，并在 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 控件上呈现它们。

**下载此示例从[墨迹序列化格式（ISF）文件保存和加载墨笔划](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)**

1.  首先，我们设置 UI。

    UI 包括“保存”、“加载”、“清除”按钮和 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)。
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="Header" 
                       Text="Basic ink store sample" 
                       Style="{ThemeResource HeaderTextBlockStyle}" 
                       Margin="10,0,0,0" />
            <Button x:Name="btnSave" 
                    Content="Save" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnLoad" 
                    Content="Load" 
                    Margin="50,0,10,0"/>
            <Button x:Name="btnClear" 
                    Content="Clear" 
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <InkCanvas x:Name="inkCanvas" />
        </Grid>
    </Grid>
```

2.  然后，我们设置一些基本墨迹输入行为。

    [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 配置为将来自笔和鼠标的输入数据解释为笔划墨迹 ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes))，并且将声明用于按钮上的单击事件的侦听器。
```csharp
public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for button click to initiate save.
        btnSave.Click += btnSave_Click;
        // Listen for button click to initiate load.
        btnLoad.Click += btnLoad_Click;
        // Listen for button click to clear ink canvas.
        btnClear.Click += btnClear_Click;
    }
```

3.  最后，我们将墨迹加载到 "**加载**" 按钮的 click 事件处理程序中。

    [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) 允许用户选择检索已保存墨迹数据的文件和位置。

    选定文件后，我们打开设置为 [**Read**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IRandomAccessStream) 的 [**IRandomAccessStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileAccessMode) 流。

    然后，调用 [**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.loadasync) 读取、反序列化已保存的笔划墨迹，并将其加载到 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer)。 将笔划加载到 **InkStrokeContainer** 会使 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 立即将它们呈现到 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)。

    > [!NOTE]
    > 在加载新笔划之前，InkStrokeContainer 中的所有现有笔划都将清除。

``` csharp
// Load ink data from a file.
private async void btnLoad_Click(object sender, RoutedEventArgs e)
{
    // Let users choose their ink file using a file picker.
    // Initialize the picker.
    Windows.Storage.Pickers.FileOpenPicker openPicker =
        new Windows.Storage.Pickers.FileOpenPicker();
    openPicker.SuggestedStartLocation =
        Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
    openPicker.FileTypeFilter.Add(".gif");
    // Show the file picker.
    Windows.Storage.StorageFile file = await openPicker.PickSingleFileAsync();
    // User selects a file and picker returns a reference to the selected file.
    if (file != null)
    {
        // Open a file stream for reading.
        IRandomAccessStream stream = await file.OpenAsync(Windows.Storage.FileAccessMode.Read);
        // Read from file.
        using (var inputStream = stream.GetInputStreamAt(0))
        {
            await inkCanvas.InkPresenter.StrokeContainer.LoadAsync(inputStream);
        }
        stream.Dispose();
    }
    // User selects Cancel and picker returns null.
    else
    {
        // Operation cancelled.
    }
}
```

> [!NOTE]
> GIF 是保存墨迹数据的唯一受支持的文件格式。 但是，[**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.loadasync) 方法不支持向后兼容的以下格式。

| 格式                    | 说明 |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InkSerializedFormat       | 指定使用 ISF 持久保存的墨迹。 这是墨迹的最紧凑持久表现形式。 该格式可以嵌入到二进制文档格式，也可以直接放置在剪贴板上。                                                                                                                                                                                                         |
| Base64InkSerializedFormat | 指定通过将 ISF 编码为 base64 流来持久保存墨迹。 提供该格式是为了在 XML 或 HTML 文件中直接对墨迹进行编码。                                                                                                                                                                                                                                                |
| Gif                       | 指定使用作为嵌入到文件中的元数据包含 ISF 的 GIF 文件持久保存的墨迹。 此格式能够在不支持墨迹的应用程序中查看墨迹，并且当返回支持墨迹的应用程序时保持全部墨迹保真度。 此格式适合传输 HTML 文件中的墨迹内容并使其可由墨迹和非墨迹应用程序使用。 |
| Base64Gif                 | 指定使用 base64 编码的加强 GIF 持久保存墨迹。 当在 XML 或 HTML 文件中对墨迹直接进行编码，之后转换为图像时，提供此格式。 该格式的可能用法是采用生成的 XML 格式包含所有墨迹信息，并用于通过可扩展样式表语言转换 (XSLT) 生成 HTML。 

## <a name="copy-and-paste-ink-strokes-with-the-clipboard"></a>使用剪贴板复制并粘贴墨迹笔划

在此，我们演示如何使用剪贴板在应用之间传输笔划墨迹。

若要支持剪贴板功能，内置 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 剪切和复制命令需要一条或多条笔划墨迹已选定。

在此示例中，我们会在使用笔桶按钮（或鼠标右键按钮）修改输入时启用笔划选择。 有关如何实现笔划选择的完整示例，请参阅[笔和触笔交互](pen-and-stylus-interactions.md)中的传递输入以进行高级处理。

**下载此示例从[剪贴板保存和加载墨笔划](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)**

1.  首先，我们设置 UI。

    UI 包括“剪切”、“复制”、“粘贴”和“清除”按钮以及 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 和选择画布。
```    XAML
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
            <TextBlock x:Name="tbHeader" 
                       Text="Basic ink store sample" 
                       Style="{ThemeResource HeaderTextBlockStyle}" 
                       Margin="10,0,0,0" />
            <Button x:Name="btnCut" 
                    Content="Cut" 
                    Margin="20,0,10,0"/>
            <Button x:Name="btnCopy" 
                    Content="Copy" 
                    Margin="20,0,10,0"/>
            <Button x:Name="btnPaste" 
                    Content="Paste" 
                    Margin="20,0,10,0"/>
            <Button x:Name="btnClear" 
                    Content="Clear" 
                    Margin="20,0,10,0"/>
        </StackPanel>
        <Grid x:Name="gridCanvas" Grid.Row="1">
            <!-- Canvas for displaying selection UI. -->
            <Canvas x:Name="selectionCanvas"/>
            <!-- Inking area -->
            <InkCanvas x:Name="inkCanvas"/>
        </Grid>
    </Grid>
```

2.  然后，我们设置一些基本墨迹输入行为。

    将 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 配置为将来自笔和鼠标的输入数据解释为笔划墨迹 ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes))。 适用于按钮上的单击事件的侦听器以及适用于选择功能的指针和笔划事件也在此处声明。

    有关如何实现笔划选择的完整示例，请参阅[笔和触笔交互](pen-and-stylus-interactions.md)中的传递输入以进行高级处理。
```csharp
public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for button click to cut ink strokes.
        btnCut.Click += btnCut_Click;
        // Listen for button click to copy ink strokes.
        btnCopy.Click += btnCopy_Click;
        // Listen for button click to paste ink strokes.
        btnPaste.Click += btnPaste_Click;
        // Listen for button click to clear ink canvas.
        btnClear.Click += btnClear_Click;

        // By default, the InkPresenter processes input modified by 
        // a secondary affordance (pen barrel button, right mouse 
        // button, or similar) as ink.
        // To pass through modified input to the app for custom processing 
        // on the app UI thread instead of the background ink thread, set 
        // InputProcessingConfiguration.RightDragAction to LeaveUnprocessed.
        inkCanvas.InkPresenter.InputProcessingConfiguration.RightDragAction =
            InkInputRightDragAction.LeaveUnprocessed;

        // Listen for unprocessed pointer events from modified input.
        // The input is used to provide selection functionality.
        inkCanvas.InkPresenter.UnprocessedInput.PointerPressed +=
            UnprocessedInput_PointerPressed;
        inkCanvas.InkPresenter.UnprocessedInput.PointerMoved +=
            UnprocessedInput_PointerMoved;
        inkCanvas.InkPresenter.UnprocessedInput.PointerReleased +=
            UnprocessedInput_PointerReleased;

        // Listen for new ink or erase strokes to clean up selection UI.
        inkCanvas.InkPresenter.StrokeInput.StrokeStarted +=
            StrokeInput_StrokeStarted;
        inkCanvas.InkPresenter.StrokesErased +=
            InkPresenter_StrokesErased;
    }
```

3.  最后，在添加了笔划选择支持后，我们将在 "**剪切**"、"**复制**" 和 "**粘贴**" 按钮的 click 事件处理程序中实现剪贴板功能。

    对于剪切，我们首先在 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.copyselectedtoclipboard) 的 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 上调用 [**CopySelectedToClipboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter)。

    然后再调用 [**DeleteSelected**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.deleteselected) 从墨迹画布中删除笔划。

    最后，我们从选择画布中删除所有选择笔划。
    
```csharp
private void btnCut_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
        inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();
        ClearSelection();
    }
```
```csharp
// Clean up selection UI.
    private void ClearSelection()
    {
        var strokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
        foreach (var stroke in strokes)
        {
            stroke.Selected = false;
        }
        ClearDrawnBoundingRect();
    }

    private void ClearDrawnBoundingRect()
    {
        if (selectionCanvas.Children.Any())
        {
            selectionCanvas.Children.Clear();
            boundingRect = Rect.Empty;
        }
    }
```

对于复制，我们只需在 [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) 的 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 上调用 [**CopySelectedToClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.copyselectedtoclipboard) 即可。


```csharp
private void btnCopy_Click(object sender, RoutedEventArgs e)
    {
        inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
    }
```

对于粘贴，我们调用 [**CanPasteFromClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.canpastefromclipboard) 以确保可以将剪贴板上的内容粘贴到墨迹画布上。

如果是这样，我们调用 [**PasteFromClipboard**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.pastefromclipboard) 以将剪贴板笔划墨迹插入到 [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) 的 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 中，之后它会将笔划呈现到墨迹画布上。

```csharp
private void btnPaste_Click(object sender, RoutedEventArgs e)
    {
        if (inkCanvas.InkPresenter.StrokeContainer.CanPasteFromClipboard())
        {
            inkCanvas.InkPresenter.StrokeContainer.PasteFromClipboard(
                new Point(0, 0));
        }
        else
        {
            // Cannot paste from clipboard.
        }
    }
```

## <a name="related-articles"></a>相关文章

* [笔和触笔交互](pen-and-stylus-interactions.md)

**主题示例**
* [保存并从墨迹序列化格式 (ISF) 文件加载笔划墨迹](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store.zip)
* [保存并从剪贴板加载笔划墨迹](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-store-clipboard.zip)

**其他示例**
* [简单墨迹示例 (C#/C++)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SimpleInk)
* [复杂墨迹示例 (C++)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk)
* [墨迹示例 (JavaScript)](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BJavaScript%5D-Windows%208%20app%20samples/JavaScript/Windows%208%20app%20samples/Input%20Ink%20sample%20(Windows%208))
* [入门教程： Windows 应用中的支持墨迹](https://github.com/Microsoft/Windows-tutorials-inputs-and-devices/tree/master/GettingStarted-Ink)
* [Coloring Book 示例](https://github.com/Microsoft/Windows-appsample-coloringbook)
* [系列说明示例](https://github.com/Microsoft/Windows-appsample-familynotes)



