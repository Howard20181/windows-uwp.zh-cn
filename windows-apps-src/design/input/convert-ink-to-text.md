---
Description: 使用手写识别和墨迹分析，将 Windows Ink 笔划识别为文本和形状。
title: 将 Windows Ink 笔划识别为文本和形状
ms.assetid: C2F3F3CE-737F-4652-98B7-5278A462F9D3
label: Recognize Windows Ink strokes as text
template: detail.hbs
keywords: Windows Ink, Windows Ink 书写, DirectInk, InkPresenter, InkCanvas, 手写识别，用户交互，输入
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: d496051a066ffcf9e8df5d4798415e6089cad4d1
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970912"
---
# <a name="recognize-windows-ink-strokes-as-text-and-shapes"></a>将 Windows Ink 笔划识别为文本和形状

使用 Windows Ink 内置的识别功能将笔划墨迹转换为文本和形状。

> **重要 API**：[**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)、[**Windows.UI.Input.Inking**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking)

## <a name="free-form-recognition-with-ink-analysis"></a>带有墨迹分析的自由格式识别

我们在此处演示如何使用 Windows Ink 分析引擎 ([Windows.UI.Input.Inking.Analysis](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis) 将 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 上的一组自由格式笔划分类、分析和识别为文本或形状。 （除了文本和形状识别外，墨迹分析还可用于识别文档结构、项目符号列表和通用绘画。）

> [!NOTE]
> 对于基本的单行纯文本情形（如表单输入），请参阅本主题后面的[受限制的手写识别](#constrained-handwriting-recognition)。

在本示例中，用户在单击某个按钮以指示完成绘画时，将会启动识别。

**从[Ink 分析示例下载此示例（基本）](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-analysis-basic.zip)**

1. 首先，我们设置 UI (MainPage.xaml)。 

   UI 包含一个“识别”按钮、一个 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 和一个标准 [**Canvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.canvas)。 当按下“识别”按钮时，将会分析墨迹画布上的所有墨迹笔划，并将对应的形状和文本绘制在标准画布上（如果成功识别）。 原始的笔划墨迹随后将从墨迹画布中删除。

   ```xaml
   <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel" 
                    Orientation="Horizontal" 
                    Grid.Row="0">
            <TextBlock x:Name="Header" 
                        Text="Basic ink analysis sample" 
                        Style="{ThemeResource HeaderTextBlockStyle}" 
                        Margin="10,0,0,0" />
            <Button x:Name="recognize" 
                    Content="Recognize" 
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid x:Name="drawingCanvas" Grid.Row="1">

            <!-- The canvas where we render the replacement text and shapes. -->
            <Canvas x:Name="recognitionCanvas" />
            <!-- The canvas for ink input. -->
            <InkCanvas x:Name="inkCanvas" />

        </Grid>
   </Grid>
   ```

2. 在 UI 代码隐藏文件 (MainPage.xaml.cs) 中，添加墨迹和墨迹分析功能所需的命名空间类型引用：
    - [Windows.UI.Input.Inking](https://docs.microsoft.com/uwp/api/windows.ui.input.inking)
    - ["UI"。](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis)
    - [Windows.UI.Xaml.Shapes](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes)

3. 然后，我们指定全局变量：

   ```csharp
    InkAnalyzer inkAnalyzer = new InkAnalyzer();
    IReadOnlyList<InkStroke> inkStrokes = null;
    InkAnalysisResult inkAnalysisResults = null;
   ```

4. 接着，我们设置一些基本墨迹输入行为：
    - 将 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 配置为将来自笔、鼠标和触摸的输入数据解释为墨迹笔划 ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes))。 
    - 笔划墨迹使用指定的 [**InkDrawingAttributes**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 在 [**InkCanvas**](https://docs.microsoft.com/windows/desktop/tablet/inkdrawingattributes-class) 上呈现。 
    - 还声明一个用于“识别”按钮上的单击事件的侦听器。

    ```csharp
    /// <summary>
    /// Initialize the UI page.
    /// </summary>
    public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen | 
            Windows.UI.Core.CoreInputDeviceTypes.Touch;

        // Set initial ink stroke attributes.
        InkDrawingAttributes drawingAttributes = new InkDrawingAttributes();
        drawingAttributes.Color = Windows.UI.Colors.Black;
        drawingAttributes.IgnorePressure = false;
        drawingAttributes.FitToCurve = true;
        inkCanvas.InkPresenter.UpdateDefaultDrawingAttributes(drawingAttributes);

        // Listen for button click to initiate recognition.
        recognize.Click += RecognizeStrokes_Click;
    }
    ```

5. 在本例中，我们在“识别”按钮的单击事件处理程序中执行墨迹分析。
    - 首先，调用 [**InkCanvas.InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.InkPresenter)的 [**StrokeContainer**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.StrokeContainer) 上的 [**GetStrokes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.GetStrokes)，以获得当前所有墨迹笔划的集合。
    - 如果存在墨迹笔划，则通过调用将其传递至 InkAnalyzer 的 [**AddDataForStrokes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer#Windows_UI_Input_Inking_Analysis_InkAnalyzer_AddDataForStrokes_Windows_Foundation_Collections_IIterable_Windows_UI_Input_Inking_InkStroke__)。
    - 我们正在尝试识别绘画和文本，但你可以使用 [**SetStrokeDataKind**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer.setstrokedatakind) 方法指定是只对文本（包括文档结构和项目符号列表）感兴趣，还是只对绘画（包括形状识别）感兴趣。
    - 调用 [**AnalyzeAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer.AnalyzeAsync) 以启动墨迹分析并获得 [**InkAnalysisResult**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisresult)。
    - 如果 [**Status**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisresult.Status) 返回**已更新**状态，请调用 [**InkAnalysisNodeKind.InkWord**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisnodekind) 和 [**InkAnalysisNodeKind.InkDrawing**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisnodekind) 的 [**FindNodes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisroot.findnodes)。
    - 重复两组节点类型，并在识别画布上（墨迹画布下面）绘制各自的文本或形状。
    - 最后，删除 InkAnalyzer 中已识别的节点和墨迹画布中对应的墨迹笔划。

    ```csharp
    /// <summary>
    /// The "Analyze" button click handler.
    /// Ink recognition is performed here.
    /// </summary>
    /// <param name="sender">Source of the click event</param>
    /// <param name="e">Event args for the button click routed event</param>
    private async void RecognizeStrokes_Click(object sender, RoutedEventArgs e)
    {
        inkStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
        // Ensure an ink stroke is present.
        if (inkStrokes.Count > 0)
        {
            inkAnalyzer.AddDataForStrokes(inkStrokes);

            // In this example, we try to recognizing both 
            // writing and drawing, so the platform default 
            // of "InkAnalysisStrokeKind.Auto" is used.
            // If you're only interested in a specific type of recognition,
            // such as writing or drawing, you can constrain recognition 
            // using the SetStrokDataKind method as follows:
            // foreach (var stroke in strokesText)
            // {
            //     analyzerText.SetStrokeDataKind(
            //      stroke.Id, InkAnalysisStrokeKind.Writing);
            // }
            // This can improve both efficiency and recognition results.
            inkAnalysisResults = await inkAnalyzer.AnalyzeAsync();

            // Have ink strokes on the canvas changed?
            if (inkAnalysisResults.Status == InkAnalysisStatus.Updated)
            {
                // Find all strokes that are recognized as handwriting and 
                // create a corresponding ink analysis InkWord node.
                var inkwordNodes = 
                    inkAnalyzer.AnalysisRoot.FindNodes(
                        InkAnalysisNodeKind.InkWord);

                // Iterate through each InkWord node.
                // Draw primary recognized text on recognitionCanvas 
                // (for this example, we ignore alternatives), and delete 
                // ink analysis data and recognized strokes.
                foreach (InkAnalysisInkWord node in inkwordNodes)
                {
                    // Draw a TextBlock object on the recognitionCanvas.
                    DrawText(node.RecognizedText, node.BoundingRect);

                    foreach (var strokeId in node.GetStrokeIds())
                    {
                        var stroke = 
                            inkCanvas.InkPresenter.StrokeContainer.GetStrokeById(strokeId);
                        stroke.Selected = true;
                    }
                    inkAnalyzer.RemoveDataForStrokes(node.GetStrokeIds());
                }
                inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();

                // Find all strokes that are recognized as a drawing and 
                // create a corresponding ink analysis InkDrawing node.
                var inkdrawingNodes =
                    inkAnalyzer.AnalysisRoot.FindNodes(
                        InkAnalysisNodeKind.InkDrawing);
                // Iterate through each InkDrawing node.
                // Draw recognized shapes on recognitionCanvas and
                // delete ink analysis data and recognized strokes.
                foreach (InkAnalysisInkDrawing node in inkdrawingNodes)
                {
                    if (node.DrawingKind == InkAnalysisDrawingKind.Drawing)
                    {
                        // Catch and process unsupported shapes (lines and so on) here.
                    }
                    // Process generalized shapes here (ellipses and polygons).
                    else
                    {
                        // Draw an Ellipse object on the recognitionCanvas (circle is a specialized ellipse).
                        if (node.DrawingKind == InkAnalysisDrawingKind.Circle || node.DrawingKind == InkAnalysisDrawingKind.Ellipse)
                        {
                            DrawEllipse(node);
                        }
                        // Draw a Polygon object on the recognitionCanvas.
                        else
                        {
                            DrawPolygon(node);
                        }
                        foreach (var strokeId in node.GetStrokeIds())
                        {
                            var stroke = inkCanvas.InkPresenter.StrokeContainer.GetStrokeById(strokeId);
                            stroke.Selected = true;
                        }
                    }
                    inkAnalyzer.RemoveDataForStrokes(node.GetStrokeIds());
                }
                inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();
            }
        }
    }
    ```

6. 以下是在我们的识别画布上绘制 TextBlock 的函数。 我们使用 ink 画布上关联的墨笔划的边框来设置 TextBlock 的位置和字体大小。

   ```csharp
    /// <summary>
    /// Draw ink recognition text string on the recognitionCanvas.
    /// </summary>
    /// <param name="recognizedText">The string returned by text recognition.</param>
    /// <param name="boundingRect">The bounding rect of the original ink writing.</param>
    private void DrawText(string recognizedText, Rect boundingRect)
    {
        TextBlock text = new TextBlock();
        Canvas.SetTop(text, boundingRect.Top);
        Canvas.SetLeft(text, boundingRect.Left);
    
        text.Text = recognizedText;
        text.FontSize = boundingRect.Height;
    
        recognitionCanvas.Children.Add(text);
    }
   ```

7. 以下是在我们的识别画布上绘制椭圆和多边形的函数。 我们使用 ink 画布上关联的墨迹笔划的边框来设置形状的位置和字体大小。

   ```csharp
    // Draw an ellipse on the recognitionCanvas.
    private void DrawEllipse(InkAnalysisInkDrawing shape)
    {
        var points = shape.Points;
        Ellipse ellipse = new Ellipse();

        ellipse.Width = shape.BoundingRect.Width;
        ellipse.Height = shape.BoundingRect.Height;

        Canvas.SetTop(ellipse, shape.BoundingRect.Top);
        Canvas.SetLeft(ellipse, shape.BoundingRect.Left);

        var brush = new SolidColorBrush(Windows.UI.ColorHelper.FromArgb(255, 0, 0, 255));
        ellipse.Stroke = brush;
        ellipse.StrokeThickness = 2;
        recognitionCanvas.Children.Add(ellipse);
    }

    // Draw a polygon on the recognitionCanvas.
    private void DrawPolygon(InkAnalysisInkDrawing shape)
    {
        List<Point> points = new List<Point>(shape.Points);
        Polygon polygon = new Polygon();

        foreach (Point point in points)
        {
            polygon.Points.Add(point);
        }

        var brush = new SolidColorBrush(Windows.UI.ColorHelper.FromArgb(255, 0, 0, 255));
        polygon.Stroke = brush;
        polygon.StrokeThickness = 2;
        recognitionCanvas.Children.Add(polygon);
    }
   ```

以下是正在操作的示例：

| 分析前 | 分析后 |
| --- | --- |
| ![分析前](images/ink/ink-analysis-raw2-small.png) | ![分析后](images/ink/ink-analysis-analyzed2-small.png) |

## <a name="constrained-handwriting-recognition"></a>受限制的手写识别

在上一个部分（[带有墨迹分析的自由格式识别](#free-form-recognition-with-ink-analysis)）中，我们演示了如何使用[墨迹分析 API](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis) 分析和识别 InkCanvas 区域内的任意墨迹笔划。

在本部分中，我们将演示如何使用 Windows Ink 手写识别引擎（不是墨迹分析）将 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 上的一组笔划转换为文本（基于已安装的默认语言包）。

> [!NOTE]
> 本部分所示的基本手写识别最适合于单行文本输入情形，如表单输入。 如果是包含文档结构、列表项、形状和绘画的分析和解释的更丰富识别情形（包括文本识别在内），请参阅上一部分：[带有墨迹分析的自由格式识别](#free-form-recognition-with-ink-analysis)。

在本示例中，用户在单击某个按钮以指示完成书写时，将会启动识别。

**从[墨迹手写识别示例](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-handwriting-reco.zip)下载此示例**

1. 首先，我们设置 UI。

   此 UI 包括一个“识别”按钮、[**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 以及一个用于显示识别结果的区域。    

   ```xaml
   <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="HeaderPanel"
                    Orientation="Horizontal"
                    Grid.Row="0">
                <TextBlock x:Name="Header"
                    Text="Basic ink recognition sample"
                    Style="{ThemeResource HeaderTextBlockStyle}"
                    Margin="10,0,0,0" />
                <Button x:Name="recognize"
                    Content="Recognize"
                    Margin="50,0,10,0"/>
        </StackPanel>
        <Grid Grid.Row="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="Auto"/>
            </Grid.RowDefinitions>
            <InkCanvas x:Name="inkCanvas"
                Grid.Row="0"/>
            <TextBlock x:Name="recognitionResult"
                Grid.Row="1"
                Margin="50,0,10,0"/>
        </Grid>
    </Grid>
    ```

2. 对于本示例，你需要首先添加墨迹功能所需的命名空间类型引用：
    - [Windows UI 输入](https://docs.microsoft.com/uwp/api/windows.ui.input)
    - [Windows.UI.Input.Inking](https://docs.microsoft.com/uwp/api/windows.ui.input.inking)

3. 然后，我们设置一些基本墨迹输入行为。

    将 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 配置为将来自笔和鼠标的输入数据解释为笔划墨迹 ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes))。 笔划墨迹使用指定的 [**InkDrawingAttributes**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 在 [**InkCanvas**](https://docs.microsoft.com/windows/desktop/tablet/inkdrawingattributes-class) 上呈现。 还声明一个用于“识别”按钮上的单击事件的侦听器。

    ```csharp
    public MainPage()
        {
            this.InitializeComponent();

            // Set supported inking device types.
            inkCanvas.InkPresenter.InputDeviceTypes =
                Windows.UI.Core.CoreInputDeviceTypes.Mouse |
                Windows.UI.Core.CoreInputDeviceTypes.Pen;

            // Set initial ink stroke attributes.
            InkDrawingAttributes drawingAttributes = new InkDrawingAttributes();
            drawingAttributes.Color = Windows.UI.Colors.Black;
            drawingAttributes.IgnorePressure = false;
            drawingAttributes.FitToCurve = true;
            inkCanvas.InkPresenter.UpdateDefaultDrawingAttributes(drawingAttributes);

            // Listen for button click to initiate recognition.
            recognize.Click += Recognize_Click;
        }
    ```

4. 最后，执行基本手写识别。 在本例中，我们使用“识别”按钮的单击事件处理程序来执行手写识别。

   - [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 将所有笔划墨迹存储在 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 对象中。 笔划通过 **InkPresenter** 的 [**StrokeContainer**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.strokecontainer) 属性公开，并使用 [**GetStrokes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.getstrokes) 方法检索。 

    ```csharp
    // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
    ```

    - 创建一个 [**InkRecognizerContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognizerContainer) 来管理手写识别过程。

    ```csharp
    // Create a manager for the InkRecognizer object
        // used in handwriting recognition.
        InkRecognizerContainer inkRecognizerContainer =
            new InkRecognizerContainer();
    ```

    - 调用[**RecognizeAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.recognizeasync)来检索一组[**InkRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognitionResult)对象。 为[**InkRecognizer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognizer)检测到的每个单词生成识别结果。

    ```csharp
    // Recognize all ink strokes on the ink canvas.
        IReadOnlyList<InkRecognitionResult> recognitionResults =
            await inkRecognizerContainer.RecognizeAsync(
                inkCanvas.InkPresenter.StrokeContainer,
                InkRecognitionTarget.All);
    ```

    - 每个[**InkRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognitionResult)对象都包含一组文本候选项。 此列表中最顶层的项被识别引擎视为最佳匹配项，后跟其余候选项，以降低置信度。

       我们循环访问每个[**InkRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognitionResult)并编译候选列表。 随后将显示候选项，并清除[**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) （这也会清除[**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)）。

    ```csharp
    string str = "Recognition result\n";
        // Iterate through the recognition results.
        foreach (var result in recognitionResults)
        {
            // Get all recognition candidates from each recognition result.
            IReadOnlyList<string> candidates = result.GetTextCandidates();
            str += "Candidates: " + candidates.Count.ToString() + "\n";
            foreach (string candidate in candidates)
            {
                str += candidate + " ";
            }
        }
        // Display the recognition candidates.
        recognitionResult.Text = str;
        // Clear the ink canvas once recognition is complete.
        inkCanvas.InkPresenter.StrokeContainer.Clear();
    ```

    - 下面是完整的 click 处理程序示例。

    ```csharp
    // Handle button click to initiate recognition.
        private async void Recognize_Click(object sender, RoutedEventArgs e)
        {
            // Get all strokes on the InkCanvas.
            IReadOnlyList<InkStroke> currentStrokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

            // Ensure an ink stroke is present.
            if (currentStrokes.Count > 0)
            {
                // Create a manager for the InkRecognizer object
                // used in handwriting recognition.
                InkRecognizerContainer inkRecognizerContainer =
                    new InkRecognizerContainer();

                // inkRecognizerContainer is null if a recognition engine is not available.
                if (!(inkRecognizerContainer == null))
                {
                    // Recognize all ink strokes on the ink canvas.
                    IReadOnlyList<InkRecognitionResult> recognitionResults =
                        await inkRecognizerContainer.RecognizeAsync(
                            inkCanvas.InkPresenter.StrokeContainer,
                            InkRecognitionTarget.All);
                    // Process and display the recognition results.
                    if (recognitionResults.Count > 0)
                    {
                        string str = "Recognition result\n";
                        // Iterate through the recognition results.
                        foreach (var result in recognitionResults)
                        {
                            // Get all recognition candidates from each recognition result.
                            IReadOnlyList<string> candidates = result.GetTextCandidates();
                            str += "Candidates: " + candidates.Count.ToString() + "\n";
                            foreach (string candidate in candidates)
                            {
                                str += candidate + " ";
                            }
                        }
                        // Display the recognition candidates.
                        recognitionResult.Text = str;
                        // Clear the ink canvas once recognition is complete.
                        inkCanvas.InkPresenter.StrokeContainer.Clear();
                    }
                    else
                    {
                        recognitionResult.Text = "No recognition results.";
                    }
                }
                else
                {
                    Windows.UI.Popups.MessageDialog messageDialog = new Windows.UI.Popups.MessageDialog("You must install handwriting recognition engine.");
                    await messageDialog.ShowAsync();
                }
            }
            else
            {
                recognitionResult.Text = "No ink strokes to recognize.";
            }
        }
    ```

## <a name="international-recognition"></a>国际识别

内置于 Windows 墨迹平台的手写识别包括 Windows 支持的一小组广泛的区域设置和语言。

有关 [**InkRecognizer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognizer) 支持的语言列表，请参阅 [**InkRecognizer.Name**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkrecognizer.name) 属性主题。

你的应用可以查询已安装的手写识别引擎的集合，并使用其中一个引擎或让用户选择其首选语言。

**注意**  用户可以通过转到 "**设置-&gt;时间 & 语言**" 来查看已安装语言的列表。 已安装的语言列在 "**语言**" 下。

若要安装新语言包并为该语言启用手写识别：

1. 转到**设置&gt;时间和语言&gt;区域和语言**。
2. 选择 "**添加语言**"。
3. 从列表中选择某种语言，然后选择区域版本。 该语言现在在**区域和语言**页面上列出。
4. 单击 "语言"，然后选择 "**选项**"。
5. 在 "**语言选项**" 页面上，下载**手写识别引擎**（他们还可以在此处下载完整语言包、语音识别引擎和键盘布局）。

我们在此处演示如何使用手写识别引擎基于所选的识别器来解释 [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 上的一组笔划。

用户通过在完成书写时单击某个按钮来启动识别。

1. 首先，我们设置 UI。

   UI 包含一个“识别”按钮、一个列出已安装手写识别器的组合框、[**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 和一个用于显示识别结果的区域。

    ```xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <StackPanel x:Name="HeaderPanel"
                        Orientation="Horizontal"
                        Grid.Row="0">
                <TextBlock x:Name="Header"
                           Text="Advanced international ink recognition sample"
                           Style="{ThemeResource HeaderTextBlockStyle}"
                           Margin="10,0,0,0" />
                <ComboBox x:Name="comboInstalledRecognizers"
                         Margin="50,0,10,0">
                    <ComboBox.ItemTemplate>
                        <DataTemplate>
                            <StackPanel Orientation="Horizontal">
                                <TextBlock Text="{Binding Name}" />
                            </StackPanel>
                        </DataTemplate>
                    </ComboBox.ItemTemplate>
                </ComboBox>
                <Button x:Name="buttonRecognize"
                        Content="Recognize"
                        IsEnabled="False"
                        Margin="50,0,10,0"/>
            </StackPanel>
            <Grid Grid.Row="1">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <InkCanvas x:Name="inkCanvas"
                           Grid.Row="0"/>
                <TextBlock x:Name="recognitionResult"
                           Grid.Row="1"
                           Margin="50,0,10,0"/>
            </Grid>
        </Grid>
    ```

2. 然后，我们设置一些基本墨迹输入行为。

   将 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 配置为将来自笔和鼠标的输入数据解释为笔划墨迹 ([**InputDeviceTypes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.inputdevicetypes))。 笔划墨迹使用指定的 [**InkDrawingAttributes**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) 在 [**InkCanvas**](https://docs.microsoft.com/windows/desktop/tablet/inkdrawingattributes-class) 上呈现。

   我们调用 `InitializeRecognizerList` 函数以使用已安装手写识别器的列表来填充识别器组合框。

   我们还声明“识别”按钮上的单击事件的侦听器和识别组合框上的选择更改事件。

    ```csharp
     public MainPage()
     {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Set initial ink stroke attributes.
        InkDrawingAttributes drawingAttributes = new InkDrawingAttributes();
        drawingAttributes.Color = Windows.UI.Colors.Black;
        drawingAttributes.IgnorePressure = false;
        drawingAttributes.FitToCurve = true;
        inkCanvas.InkPresenter.UpdateDefaultDrawingAttributes(drawingAttributes);

        // Populate the recognizer combo box with installed recognizers.
        InitializeRecognizerList();

        // Listen for combo box selection.
        comboInstalledRecognizers.SelectionChanged +=
            comboInstalledRecognizers_SelectionChanged;

        // Listen for button click to initiate recognition.
        buttonRecognize.Click += Recognize_Click;
    }
    ```

3. 我们使用已安装手写识别器的列表来填充识别器组合框。

   创建一个 [**InkRecognizerContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognizerContainer) 来管理手写识别过程。 使用此对象调用 [**GetRecognizers**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkrecognizercontainer.getrecognizers) 并检索已安装识别器的列表来填充识别器组合框。

    ```csharp
    // Populate the recognizer combo box with installed recognizers.
    private void InitializeRecognizerList()
    {
        // Create a manager for the handwriting recognition process.
        inkRecognizerContainer = new InkRecognizerContainer();
        // Retrieve the collection of installed handwriting recognizers.
        IReadOnlyList<InkRecognizer> installedRecognizers =
            inkRecognizerContainer.GetRecognizers();
        // inkRecognizerContainer is null if a recognition engine is not available.
        if (!(inkRecognizerContainer == null))
        {
            comboInstalledRecognizers.ItemsSource = installedRecognizers;
            buttonRecognize.IsEnabled = true;
        }
    }
    ```
    
4. 如果识别器组合框选择发生更改，请更新手写识别器。

   基于识别器组合框中所选的识别器使用 [**InkRecognizerContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognizerContainer) 调用 [**SetDefaultRecognizer**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkrecognizercontainer.setdefaultrecognizer)。

    ```csharp
    // Handle recognizer change.
        private void comboInstalledRecognizers_SelectionChanged(
            object sender, SelectionChangedEventArgs e)
        {
            inkRecognizerContainer.SetDefaultRecognizer(
                (InkRecognizer)comboInstalledRecognizers.SelectedItem);
        }
    ```

5. 最后，我们基于所选的手写识别器执行手写识别。 在本例中，我们使用“识别”按钮的单击事件处理程序来执行手写识别。

   - [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 将所有笔划墨迹存储在 [**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) 对象中。 笔划通过 **InkPresenter** 的 [**StrokeContainer**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.strokecontainer) 属性公开，并使用 [**GetStrokes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokecontainer.getstrokes) 方法检索。

    ```csharp
    // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes =
            inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
    ```

    - 调用[**RecognizeAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkmanager.recognizeasync)来检索一组[**InkRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognitionResult)对象。

      为[**InkRecognizer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognizer)检测到的每个单词生成识别结果。

    ```csharp
    // Recognize all ink strokes on the ink canvas.
    IReadOnlyList<InkRecognitionResult> recognitionResults =
        await inkRecognizerContainer.RecognizeAsync(
            inkCanvas.InkPresenter.StrokeContainer,
            InkRecognitionTarget.All);
    ```

    - 每个[**InkRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognitionResult)对象都包含一组文本候选项。 此列表中最顶层的项被识别引擎视为最佳匹配项，后跟其余候选项，以降低置信度。

       我们循环访问每个[**InkRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkRecognitionResult)并编译候选列表。 随后将显示候选项，并清除[**InkStrokeContainer**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkStrokeContainer) （这也会清除[**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas)）。

    ```csharp
    string str = "Recognition result\n";
    // Iterate through the recognition results.
    foreach (InkRecognitionResult result in recognitionResults)
    {
            // Get all recognition candidates from each recognition result.
            IReadOnlyList<string> candidates =
                result.GetTextCandidates();
            str += "Candidates: " + candidates.Count.ToString() + "\n";
            foreach (string candidate in candidates)
            {
                str += candidate + " ";
            }
    }
    // Display the recognition candidates.
    recognitionResult.Text = str;
    // Clear the ink canvas once recognition is complete.
    inkCanvas.InkPresenter.StrokeContainer.Clear();
    ```

    - 下面是完整的 click 处理程序示例。

    ```csharp
    // Handle button click to initiate recognition.
    private async void Recognize_Click(object sender, RoutedEventArgs e)
    {
        // Get all strokes on the InkCanvas.
        IReadOnlyList<InkStroke> currentStrokes =
            inkCanvas.InkPresenter.StrokeContainer.GetStrokes();

        // Ensure an ink stroke is present.
        if (currentStrokes.Count > 0)
        {
            // inkRecognizerContainer is null if a recognition engine is not available.
            if (!(inkRecognizerContainer == null))
            {
                // Recognize all ink strokes on the ink canvas.
                IReadOnlyList<InkRecognitionResult> recognitionResults =
                    await inkRecognizerContainer.RecognizeAsync(
                        inkCanvas.InkPresenter.StrokeContainer,
                        InkRecognitionTarget.All);
                // Process and display the recognition results.
                if (recognitionResults.Count > 0)
                {
                    string str = "Recognition result\n";
                    // Iterate through the recognition results.
                    foreach (InkRecognitionResult result in recognitionResults)
                    {
                        // Get all recognition candidates from each recognition result.
                        IReadOnlyList<string> candidates =
                            result.GetTextCandidates();
                        str += "Candidates: " + candidates.Count.ToString() + "\n";
                        foreach (string candidate in candidates)
                        {
                            str += candidate + " ";
                        }
                    }
                    // Display the recognition candidates.
                    recognitionResult.Text = str;
                    // Clear the ink canvas once recognition is complete.
                    inkCanvas.InkPresenter.StrokeContainer.Clear();
                }
                else
                {
                    recognitionResult.Text = "No recognition results.";
                }
            }
            else
            {
                Windows.UI.Popups.MessageDialog messageDialog =
                    new Windows.UI.Popups.MessageDialog(
                        "You must install handwriting recognition engine.");
                await messageDialog.ShowAsync();
            }
        }
        else
        {
            recognitionResult.Text = "No ink strokes to recognize.";
        }
    }
    ```

## <a name="dynamic-recognition"></a>动态识别

虽然前面的两个示例要求用户按下按钮来开始识别，但是你也可以使用与基本计时函数配对的笔划输入来执行动态识别。

在此示例中，我们将使用与前面的国际识别示例相同的 UI 和笔划设置。

1. 这些全球对象（[InkAnalyzer](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer)、[InkStroke](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstroke)、[InkAnalysisResult](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisresult)、[DispatcherTimer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dispatchertimer)）用于整个应用。

    ```csharp
    // Stroke recognition globals.
    InkAnalyzer inkAnalyzer;
    DispatcherTimer recoTimer;
    ```

2. 我们将添加两个 [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas.inkpresenter) 笔划事件（[**StrokesCollected**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.strokescollected) 和 [**StrokeStarted**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokeinput.strokestarted)）的侦听器（而不是用于启动识别的按钮），并设置一个带有一秒 [**Tick**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DispatcherTimer) 间隔的基本计时器 ([**DispatcherTimer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dispatchertimer.tick))。

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();

        // Set supported inking device types.
        inkCanvas.InkPresenter.InputDeviceTypes =
            Windows.UI.Core.CoreInputDeviceTypes.Mouse |
            Windows.UI.Core.CoreInputDeviceTypes.Pen;

        // Listen for stroke events on the InkPresenter to 
        // enable dynamic recognition.
        // StrokesCollected is fired when the user stops inking by 
        // lifting their pen or finger, or releasing the mouse button.
        inkCanvas.InkPresenter.StrokesCollected += inkCanvas_StrokesCollected;
        // StrokeStarted is fired when ink input is first detected.
        inkCanvas.InkPresenter.StrokeInput.StrokeStarted +=
            inkCanvas_StrokeStarted;

        inkAnalyzer = new InkAnalyzer();

        // Timer to manage dynamic recognition.
        recoTimer = new DispatcherTimer();
        recoTimer.Interval = TimeSpan.FromSeconds(1);
        recoTimer.Tick += recoTimer_TickAsync;
    }
    ```

3. 然后，我们为第一步中声明的 InkPresenter 事件定义处理程序（我们还替代 [**OnNavigatingFrom**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatingfrom) 页面事件以管理我们的计时器）。

    - [**StrokesCollected**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter.strokescollected)  
    将墨迹笔划 ([**AddDataForStrokes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer.adddataforstrokes)) 添加到 InkAnalyzer，当用户（通过抬起笔或手指或释放鼠标按钮）停止墨迹书写时，启动识别计时器。 在无墨迹输入的一秒后，启动识别。  

        使用 [**SetStrokeDataKind**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer.setstrokedatakind) 方法指定是只对文本（包括文档结构和项目符号列表）感兴趣，还是只对绘画（包括形状识别）感兴趣。

    - [**StrokeStarted**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstrokeinput.strokestarted)  
    如果新的笔划在下一个计时器滴答事件前开始，则停止计时器，因为新的笔划可能是单次手写输入的延续。

    ```csharp
    // Handler for the InkPresenter StrokeStarted event.
    // Don't perform analysis while a stroke is in progress.
    // If a new stroke starts before the next timer tick event,
    // stop the timer as the new stroke is likely the continuation
    // of a single handwriting entry.
    private void inkCanvas_StrokeStarted(InkStrokeInput sender, PointerEventArgs args)
    {
        recoTimer.Stop();
    }
    // Handler for the InkPresenter StrokesCollected event.
    // Stop the timer and add the collected strokes to the InkAnalyzer.
    // Start the recognition timer when the user stops inking (by 
    // lifting their pen or finger, or releasing the mouse button).
    // If ink input is not detected after one second, initiate recognition.
    private void inkCanvas_StrokesCollected(InkPresenter sender, InkStrokesCollectedEventArgs args)
    {
        recoTimer.Stop();
        // If you're only interested in a specific type of recognition,
        // such as writing or drawing, you can constrain recognition 
        // using the SetStrokDataKind method, which can improve both 
        // efficiency and recognition results.
        // In this example, "InkAnalysisStrokeKind.Writing" is used.
        foreach (var stroke in args.Strokes)
        {
            inkAnalyzer.AddDataForStroke(stroke);
            inkAnalyzer.SetStrokeDataKind(stroke.Id, InkAnalysisStrokeKind.Writing);
        }
        recoTimer.Start();
    }
    // Override the Page OnNavigatingFrom event handler to 
    // stop our timer if user leaves page.
    protected override void OnNavigatingFrom(NavigatingCancelEventArgs e)
    {
        recoTimer.Stop();
    }
    ```

4. 最后，执行手写识别。 在此示例中，我们使用 [**DispatcherTimer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dispatchertimer.tick) 的 [**Tick**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DispatcherTimer) 事件处理程序来启动手写识别。
    - 调用 [**AnalyzeAsync**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalyzer.AnalyzeAsync) 以启动墨迹分析并获得 [**InkAnalysisResult**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisresult)。
    - 如果 [**Status**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisresult.Status) 返回**已更新**状态，请为 [**InkAnalysisNodeKind.InkWord**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisnodekind) 节点类型调用 [**FindNodes**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.analysis.inkanalysisroot.findnodes)。
    - 重复节点并显示识别出的文本。
    - 最后，删除 InkAnalyzer 中已识别的节点和墨迹画布中对应的墨迹笔划。

    ```csharp
    private async void recoTimer_TickAsync(object sender, object e)
    {
        recoTimer.Stop();
        if (!inkAnalyzer.IsAnalyzing)
        {
            InkAnalysisResult result = await inkAnalyzer.AnalyzeAsync();

            // Have ink strokes on the canvas changed?
            if (result.Status == InkAnalysisStatus.Updated)
            {
                // Find all strokes that are recognized as handwriting and 
                // create a corresponding ink analysis InkWord node.
                var inkwordNodes =
                    inkAnalyzer.AnalysisRoot.FindNodes(
                        InkAnalysisNodeKind.InkWord);

                // Iterate through each InkWord node.
                // Display the primary recognized text (for this example, 
                // we ignore alternatives), and then delete the 
                // ink analysis data and recognized strokes.
                foreach (InkAnalysisInkWord node in inkwordNodes)
                {
                    string recognizedText = node.RecognizedText;
                    // Display the recognition candidates.
                    recognitionResult.Text = recognizedText;

                    foreach (var strokeId in node.GetStrokeIds())
                    {
                        var stroke =
                            inkCanvas.InkPresenter.StrokeContainer.GetStrokeById(strokeId);
                        stroke.Selected = true;
                    }
                    inkAnalyzer.RemoveDataForStrokes(node.GetStrokeIds());
                }
                inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();
            }
        }
        else
        {
            // Ink analyzer is busy. Wait a while and try again.
            recoTimer.Start();
        }
    }
    ```

## <a name="related-articles"></a>相关文章

- [笔和触笔交互](pen-and-stylus-interactions.md)

### <a name="topic-samples"></a>主题示例

- [墨迹分析示例（基本）(C#)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-analysis-basic.zip)
- [墨迹手写识别示例 (C#)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-handwriting-reco.zip)

### <a name="other-samples"></a>其他示例

- [简单墨迹示例 (C#/C++)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SimpleInk)
- [复杂墨迹示例 (C++)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk)
- [墨迹示例 (JavaScript)](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BJavaScript%5D-Windows%208%20app%20samples/JavaScript/Windows%208%20app%20samples/Input%20Ink%20sample%20(Windows%208))
- [入门教程： Windows 应用中的支持墨迹](https://github.com/Microsoft/Windows-tutorials-inputs-and-devices/tree/master/GettingStarted-Ink)
- [Coloring Book 示例](https://github.com/Microsoft/Windows-appsample-coloringbook)
- [系列说明示例](https://github.com/Microsoft/Windows-appsample-familynotes)
