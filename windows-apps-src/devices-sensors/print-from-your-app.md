---
ms.assetid: 9A0F1852-A76B-4F43-ACFC-2CC56AAD1C03
title: 从应用打印
description: 了解如何从通用 Windows 应用中打印文档。 本主题还介绍了如何打印特定页面。
ms.date: 01/29/2018
ms.topic: article
keywords: windows 10，uwp，打印
ms.localizationpriority: medium
ms.openlocfilehash: d14a037a84fe64fd9fd3ccca171e3ecfdaaae472
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258637"
---
# <a name="print-from-your-app"></a>从应用打印



**重要的 API**

-   [ **"打印"** ](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing)
-   [**Windows。** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing)
-   [**PrintDocument**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing.PrintDocument)

了解如何从通用 Windows 应用中打印文档。 本主题还介绍了如何打印特定页面。 有关对打印预览 UI 的更多高级更改，请参阅[自定义打印预览 UI](customize-the-print-preview-ui.md)。

> [!TIP]
> 本主题中的大部分示例都基于[通用 Windows 平台（uwp）打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)，这是 GitHub 上[通用 Windows 平台（uwp）应用示例](https://github.com/Microsoft/Windows-universal-samples)存储库的一部分。

## <a name="register-for-printing"></a>注册打印

在你的应用中添加打印的第一步是注册打印合约。 你的应用必须在你希望你的用户能够从其打印的每个屏幕上执行此操作。 仅显示给用户的屏幕可以注册打印。 如果你的应用的一个屏幕已注册打印，则该屏幕必须取消注册打印（当该屏幕存在时）。 如果该屏幕替换为另一个屏幕，则当下一个屏幕打开时，它必须注册新的打印合约。

> [!TIP]
> 如果需要支持在应用中的多个页面上打印，则可以将此打印代码置于常见帮助器类中，并让应用页面重复使用它。 有关具体做法的示例，请参阅 `PrintHelper`UWP 打印示例[中的 ](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing) 类。

首先，声明 [**PrintManager**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintManager) 和 [**PrintDocument**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing.PrintDocument)。 **PrintManager** 类型以及用于支持其他 Windows 打印功能的类型都位于 [**Windows.Graphics.Printing**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing) 命名空间中。 **PrintDocument** 类型以及支持准备 XAML 内容以供打印的其他类型都位于 [**Windows.UI.Xaml.Printing**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing) 命名空间中。 你可以通过在页面中添加以下 **using** 或 **Imports** 语句，使得编写打印代码更加容易。

```csharp
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
```

[  **PrintDocument**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing.PrintDocument) 类用于处理应用和 [**PrintManager**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintManager) 之间的大部分交互，但是它会公开其自身的多个回调。 在注册期间，创建 **PrintManager** 和 **PrintDocument** 的实例并为其打印事件注册处理程序。

在 [UWP 打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)中，注册通过 `RegisterForPrinting` 方法来执行。

```csharp
public virtual void RegisterForPrinting()
{
   printDocument = new PrintDocument();
   printDocumentSource = printDocument.DocumentSource;
   printDocument.Paginate += CreatePrintPreviewPages;
   printDocument.GetPreviewPage += GetPrintPreviewPage;
   printDocument.AddPages += AddPrintPages;

   PrintManager printMan = PrintManager.GetForCurrentView();
   printMan.PrintTaskRequested += PrintTaskRequested;
}
```

当用户转到支持打印的页面时，它会在 `OnNavigatedTo` 方法中启动注册。

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
   // Initialize common helper class and register for printing
   printHelper = new PrintHelper(this);
   printHelper.RegisterForPrinting();

   // Initialize print content for this scenario
   printHelper.PreparePrintContent(new PageToPrint());

   // Tell the user how to print
   MainPage.Current.NotifyUser("Print contract registered with customization, use the Print button to print.", NotifyType.StatusMessage);
}
```

在示例中，事件处理程序在 `UnregisterForPrinting` 方法中未注册。

```csharp
public virtual void UnregisterForPrinting()
{
    if (printDocument == null)
    {
        return;
    }

    printDocument.Paginate -= CreatePrintPreviewPages;
    printDocument.GetPreviewPage -= GetPrintPreviewPage;
    printDocument.AddPages -= AddPrintPages;

    PrintManager printMan = PrintManager.GetForCurrentView();
    printMan.PrintTaskRequested -= PrintTaskRequested;
}
```

当用户离开支持打印的页面时，将在 `OnNavigatedFrom` 方法中取消注册事件处理程序。 

> [!NOTE]
> 如果有多页应用但不断开打印，则当用户离开页面并返回到该页面时，将引发异常。

```csharp
protected override void OnNavigatedFrom(NavigationEventArgs e)
{
   if (printHelper != null)
   {
         printHelper.UnregisterForPrinting();
   }
}
```

## <a name="create-a-print-button"></a>创建打印按钮

将打印按钮添加到你希望其在其中显示的应用屏幕。 确保它不会干扰你要打印的内容。

```xml
<Button x:Name="InvokePrintingButton" Content="Print" Click="OnPrintButtonClick"/>
```

下一步，将时间处理程序添加到应用的代码以处理 click 事件。 使用 [**ShowPrintUIAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.printmanager.showprintuiasync) 方法开始从你的应用打印。 **ShowPrintUIAsync** 是显示相应打印窗口的异步方法。 我们建议先调用 [**IsSupported**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.printmanager.issupported) 方法，以检查应用是否在支持打印的设备上运行（并处理不支持打印的情况）。 如果此时出于任何其他原因，无法执行打印，**ShowPrintUIAsync** 会引发异常。 我们建议捕获这些异常，并让用户知道何时无法继续打印。

在此示例中，点击按钮将在事件处理程序中显示打印窗口。 如果该方法引发异常（因为不能在该时间执行打印)，则 [**ContentDialog**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog) 控件将通知用户该情况。

```csharp
async private void OnPrintButtonClick(object sender, RoutedEventArgs e)
{
    if (Windows.Graphics.Printing.PrintManager.IsSupported())
    {
        try
        {
            // Show print UI
            await Windows.Graphics.Printing.PrintManager.ShowPrintUIAsync();

        }
        catch
        {
            // Printing cannot proceed at this time
            ContentDialog noPrintingDialog = new ContentDialog()
            {
                Title = "Printing error",
                Content = "\nSorry, printing can' t proceed at this time.", PrimaryButtonText = "OK"
            };
            await noPrintingDialog.ShowAsync();
        }
    }
    else
    {
        // Printing is not supported on this device
        ContentDialog noPrintingDialog = new ContentDialog()
        {
            Title = "Printing not supported",
            Content = "\nSorry, printing is not supported on this device.",PrimaryButtonText = "OK"
        };
        await noPrintingDialog.ShowAsync();
    }
}
```

## <a name="format-your-apps-content"></a>格式化应用的内容

调用 **ShowPrintUIAsync** 时会引发 [**PrintTaskRequested**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_#Windows_Foundation_IAsyncOperationWithProgress_2_Progress) 事件。 此步骤中显示的 **PrintTaskRequested** 事件处理程序将通过调用 [**PrintTaskRequest.CreatePrintTask**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintTask) 方法创建 [**PrintTask**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.printtaskrequest.createprinttask)，并传递打印页面的标题和 [**PrintTaskSourceRequestedHandler**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.printtask.source) 委托的名称。 请注意，在此示例中，**PrintTaskSourceRequestedHandler** 是以内联方式定义的。 **PrintTaskSourceRequestedHandler** 提供用于打印的格式化内容，并在以后对其进行介绍。

在此示例中，还会定义一个完成处理程序以捕获错误。 最好对完成事件进行处理，因为，那样你的应用可以让用户知道是否发生了错误并提供可能的解决方案。 同样，你的应用可以使用完成事件来指示用户在打印作业成功之后要采取的后续步骤。

```csharp
protected virtual void PrintTaskRequested(PrintManager sender, PrintTaskRequestedEventArgs e)
{
   PrintTask printTask = null;
   printTask = e.Request.CreatePrintTask("C# Printing SDK Sample", sourceRequested =>
   {
         // Print Task event handler is invoked when the print job is completed.
         printTask.Completed += async (s, args) =>
         {
            // Notify the user when the print operation fails.
            if (args.Completion == PrintTaskCompletion.Failed)
            {
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     MainPage.Current.NotifyUser("Failed to print.", NotifyType.ErrorMessage);
               });
            }
         };

         sourceRequested.SetSource(printDocumentSource);
   });
}
```

创建打印任务之后，[**PrintManager**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintManager) 将通过引发 [**Paginate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.paginate) 事件来请求要在打印预览 UI 中显示的打印页面集合。 这与 **IPrintPreviewPageCollection** 接口的 **Paginate** 方法对应。 此时将调用在注册期间创建的事件处理程序。

> [!IMPORTANT]
> 如果用户更改打印设置，则将再次调用分页事件处理程序以重新排列内容。 为了获得最佳的用户体验，我们建议在重新排列内容之前检查设置，以避免在不必要时重新初始化分页内容。

在 [**Paginate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.paginate) 事件处理程序中（`CreatePrintPreviewPages`UWP 打印示例[中的 ](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing) 方法），创建要在打印预览 UI 中显示并发送到打印机的页面。 用于准备你的应用的内容以供打印的代码特定于你的应用以及你打印的内容。 请参考 [UWP 打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)源代码，了解它如何格式化其内容以进行打印。

```csharp
protected virtual void CreatePrintPreviewPages(object sender, PaginateEventArgs e)
{
   // Clear the cache of preview pages
   printPreviewPages.Clear();

   // Clear the print canvas of preview pages
   PrintCanvas.Children.Clear();

   // This variable keeps track of the last RichTextBlockOverflow element that was added to a page which will be printed
   RichTextBlockOverflow lastRTBOOnPage;

   // Get the PrintTaskOptions
   PrintTaskOptions printingOptions = ((PrintTaskOptions)e.PrintTaskOptions);

   // Get the page description to deterimine how big the page is
   PrintPageDescription pageDescription = printingOptions.GetPageDescription(0);

   // We know there is at least one page to be printed. passing null as the first parameter to
   // AddOnePrintPreviewPage tells the function to add the first page.
   lastRTBOOnPage = AddOnePrintPreviewPage(null, pageDescription);

   // We know there are more pages to be added as long as the last RichTextBoxOverflow added to a print preview
   // page has extra content
   while (lastRTBOOnPage.HasOverflowContent && lastRTBOOnPage.Visibility == Windows.UI.Xaml.Visibility.Visible)
   {
         lastRTBOOnPage = AddOnePrintPreviewPage(lastRTBOOnPage, pageDescription);
   }

   if (PreviewPagesCreated != null)
   {
         PreviewPagesCreated.Invoke(printPreviewPages, null);
   }

   PrintDocument printDoc = (PrintDocument)sender;

   // Report the number of preview pages created
   printDoc.SetPreviewPageCount(printPreviewPages.Count, PreviewPageCountType.Intermediate);
}
```

要在打印预览窗口中显示特定页面时，[**PrintManager**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintManager) 将引发 [**GetPreviewPage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.getpreviewpage) 事件。 这与 **IPrintPreviewPageCollection** 接口的 **MakePage** 方法对应。 此时将调用在注册期间创建的事件处理程序。

在 [**GetPreviewPage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.getpreviewpage) 事件处理程序（`GetPrintPreviewPage`UWP 打印示例[中的 ](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing) 方法）中，在打印文档上设置相应的页面。

```csharp
protected virtual void GetPrintPreviewPage(object sender, GetPreviewPageEventArgs e)
{
   PrintDocument printDoc = (PrintDocument)sender;
   printDoc.SetPreviewPage(e.PageNumber, printPreviewPages[e.PageNumber - 1]);
}
```

最后，在用户单击打印按钮后，[**PrintManager**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.PrintManager) 会通过调用 **IDocumentPageSource** 接口的 **MakeDocument** 方法，请求要发送到打印机的页面的最终集合。 在 XAML 中，这会引发 [**AddPages**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.addpages) 事件。 此时将调用在注册期间创建的事件处理程序。

在 [**AddPages**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.printing.printdocument.addpages) 事件处理程序（`AddPrintPages`UWP 打印示例[中的 ](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing) 方法）中，将页面集合中的页面添加到要发送到打印机的 [**PrintDocument**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Printing.PrintDocument) 对象。 如果用户指定要打印的特殊页面或页面范围，则使用此处的信息以仅添加将实际发送到打印机的页面。

```csharp
protected virtual void AddPrintPages(object sender, AddPagesEventArgs e)
{
   // Loop over all of the preview pages and add each one to  add each page to be printied
   for (int i = 0; i < printPreviewPages.Count; i++)
   {
         // We should have all pages ready at this point...
         printDocument.AddPage(printPreviewPages[i]);
   }

   PrintDocument printDoc = (PrintDocument)sender;

   // Indicate that all of the print pages have been provided
   printDoc.AddPagesComplete();
}
```

## <a name="prepare-print-options"></a>准备打印选项

下一步，准备打印选项。 例如，本部分将介绍如何设置页面范围选项来允许打印特定页面。 有关更多高级选项，请参阅[自定义打印预览 UI](customize-the-print-preview-ui.md)。

此步骤将创建一个新打印选项，定义该选项支持的一个值列表，然后将该选项添加到打印预览 UI 中。 页面范围选项具有以下设置：

| 选项名称          | 操作 |
|----------------------|--------|
| **全部打印**        | 打印文档中的所有页面。|
| **打印选定内容**  | 仅打印用户选择的内容。|
| **打印范围**      | 显示编辑控件，用户可以将要打印的页面输入到该控件中。|

首先，修改 [**PrintTaskRequested**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_#Windows_Foundation_IAsyncOperationWithProgress_2_Progress) 事件处理程序以添加代码，从而获取一个 [**PrintTaskOptionDetails**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Printing.OptionDetails.PrintTaskOptionDetails) 对象。

```csharp
PrintTaskOptionDetails printDetailedOptions = PrintTaskOptionDetails.GetFromPrintTaskOptions(printTask.Options);
```

清除在打印预览 UI 中显示的选项列表，并添加要在用户想要从应用打印时显示的选项。

> [!NOTE]
> 选项将按附加的顺序显示在打印预览 UI 中，第一个选项显示在窗口顶部。

```csharp
IList<string> displayedOptions = printDetailedOptions.DisplayedOptions;

displayedOptions.Clear();
displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Copies);
displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.Orientation);
displayedOptions.Add(Windows.Graphics.Printing.StandardPrintTaskOptions.ColorMode);
```

创建新打印选项并初始化选项值列表。

```csharp
// Create a new list option
PrintCustomItemListOptionDetails pageFormat = printDetailedOptions.CreateItemListOption("PageRange", "Page Range");
pageFormat.AddItem("PrintAll", "Print all");
pageFormat.AddItem("PrintSelection", "Print Selection");
pageFormat.AddItem("PrintRange", "Print Range");
```

添加你的自定义打印选项并分配事件处理程序。 将自定义选项附加到最后，这样它会显示在选项列表的底部。 但是，你可以将其放在列表中的任意位置，自定义打印选项无需添加到最后位置。

```csharp
// Add the custom option to the option list
displayedOptions.Add("PageRange");

// Create new edit option
PrintCustomTextOptionDetails pageRangeEdit = printDetailedOptions.CreateTextOption("PageRangeEdit", "Range");

// Register the handler for the option change event
printDetailedOptions.OptionChanged += printDetailedOptions_OptionChanged;
```

[  **CreateTextOption**](https://docs.microsoft.com/uwp/api/windows.graphics.printing.optiondetails.printtaskoptiondetails.createtextoption) 方法会创建“范围”文本框。 用户将在此文本框中输入在他们选择“打印范围”选项时希望打印的特定页面。

## <a name="handle-print-option-changes"></a>处理打印选项更改

**OptionChanged** 事件处理程序执行两项工作。 第一，它根据用户选择的页面范围选项显示和隐藏页面范围的文本编辑字段。 第二，它将测试输入到页面范围文本框中的文本，以确保它代表文档的有效页面范围。

此示例演示如何在[UWP 打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)中处理打印选项更改事件。

```csharp
async void printDetailedOptions_OptionChanged(PrintTaskOptionDetails sender, PrintTaskOptionChangedEventArgs args)
{
   if (args.OptionId == null)
   {
         return;
   }

   string optionId = args.OptionId.ToString();

   // Handle change in Page Range Option
   if (optionId == "PageRange")
   {
         IPrintOptionDetails pageRange = sender.Options[optionId];
         string pageRangeValue = pageRange.Value.ToString();

         selectionMode = false;

         switch (pageRangeValue)
         {
            case "PrintRange":
               // Add PageRangeEdit custom option to the option list
               sender.DisplayedOptions.Add("PageRangeEdit");
               pageRangeEditVisible = true;
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     ShowContent(null);
               });
               break;
            case "PrintSelection":
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     Scenario4PageRange page = (Scenario4PageRange)scenarioPage;
                     PageToPrint pageContent = (PageToPrint)page.PrintFrame.Content;
                     ShowContent(pageContent.TextContentBlock.SelectedText);
               });
               RemovePageRangeEdit(sender);
               break;
            default:
               await scenarioPage.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                     ShowContent(null);
               });
               RemovePageRangeEdit(sender);
               break;
         }

         Refresh();
   }
   else if (optionId == "PageRangeEdit")
   {
         IPrintOptionDetails pageRange = sender.Options[optionId];
         // Expected range format (p1,p2...)*, (p3-p9)* ...
         if (!Regex.IsMatch(pageRange.Value.ToString(), @"^\s*\d+\s*(\-\s*\d+\s*)?(\,\s*\d+\s*(\-\s*\d+\s*)?)*$"))
         {
            pageRange.ErrorText = "Invalid Page Range (eg: 1-3, 5)";
         }
         else
         {
            pageRange.ErrorText = string.Empty;
            try
            {
               GetPagesInRange(pageRange.Value.ToString());
               Refresh();
            }
            catch (InvalidPageException ipex)
            {
               pageRange.ErrorText = ipex.Message;
            }
         }
   }
}
```

> [!TIP]
> 参见[UWP 打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)中的 `GetPagesInRange` 方法，详细了解如何分析用户在 "范围" 文本框中输入的页范围。

## <a name="preview-selected-pages"></a>预览所选页面

针对打印而设置你的应用内容的格式的方式取决于你的应用及其内容的性质。 [UWP 打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)中使用的打印帮助器类，用于设置要打印的内容的格式。

打印部分页面时，可以通过多种方式在打印预览中显示内容。 无论你采用哪种方法在打印预览中显示页面范围，打印输出都必须仅包含选中的页面。

-   无论是否已指定页面范围，都在打印预览中显示所有页面，这可以让用户了解实际将打印哪些页面。
-   仅在打印预览中显示用户的页面范围所选中的页面，这会在用户更改页面范围时更新显示器。
-   在打印预览中显示所有页面，但灰显未在用户所选页面范围内的页面。

## <a name="related-topics"></a>相关主题

* [打印设计准则](https://docs.microsoft.com/windows/uwp/devices-sensors/printing-and-scanning)
* [生成2015视频：开发在 Windows 10 中打印的应用](https://channel9.msdn.com/Events/Build/2015/2-94)
* [UWP 打印示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Printing)
