---
Description: 你可以使用 RichEditBox 控件来输入和编辑包含格式文本、超链接和图像的 RTF 文档。 你可以通过将 IsReadOnly 属性设置为 true，将 RichEditBox 设置为只读。
title: RichEditBox
ms.assetid: 4AFC0DFA-3B89-434D-9F86-4309CCFF7839
label: Rich edit box
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: cf2eebdc97a1052dd3f4a3e00dd3a911cb80bace
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970662"
---
# <a name="rich-edit-box"></a>富编辑框

你可以使用 RichEditBox 控件来输入和编辑包含格式文本、超链接和图像的 RTF 文档。 你可以通过将 IsReadOnly 属性设置为 **true**，从而将 RichEditBox 设置为只读。

**获取 Windows UI 库**

|  |  |
| - | - |
| ![WinUI 徽标](images/winui-logo-64x64.png) | Windows UI 库 2.2 或更高版本包含此控件的使用圆角的新模板。 有关详细信息，请参阅[圆角半径](/windows/uwp/design/style/rounded-corner)。 WinUI 是一种 NuGet 包，其中包含用于 Windows 应用的新控件和 UI 功能。 有关详细信息（包括安装说明），请参阅 [Windows UI 库](https://docs.microsoft.com/uwp/toolkits/winui/)。 |

> **平台 API**：[RichEditBox 类](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox)、[Document 属性](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox.document)、[IsReadOnly 属性](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox.isreadonly)、[IsSpellCheckEnabled 属性](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox.isspellcheckenabled)

## <a name="is-this-the-right-control"></a>这是正确的控件吗？

使用 **RichEditBox** 显示和编辑文本文件。 不要像使用其他标准文本输入框那样使用 RichEditBox 在应用中获取用户输入。 而应使用它来处理独立于应用的文本文件。 通常需要将输入到 RichEditBox 的文本保存为 .rtf 文件。
-   如果多行文本框的主要用途是创建只读文档（如博客条目或电子邮件内容），且这些文档需要富文本，则请改用[富文本块](/windows/uwp/design/controls-and-patterns/rich-text-block)。
-   在捕获将仅供使用但不向用户重新显示的文本时，请使用纯文本输入控件。
-   对于所有其他情况，请使用纯文本输入控件。

有关选择正确的文本控件的详细信息，请参阅[文本控件](text-controls.md)文章。

## <a name="examples"></a>示例

<table>
<th align="left">XAML 控件库<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>如果已安装 <strong style="font-weight: semi-bold">XAML 控件库</strong>应用，请单击此处<a href="xamlcontrolsgallery:/item/RichEditBox">打开此应用，了解 RichEditBox 的实际应用</a>。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">获取 XAML 控件库应用 (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">获取源代码 (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

此富编辑框会在自身中打开富文本文档。 格式和文件按钮不属于富编辑框，但你至少应提供最小小限度的设置按钮样式集，并实现它们的操作。

![打开了文档的 RTF 框](images/rich-edit-box.png)

## <a name="create-a-rich-edit-box"></a>创建富编辑框

默认情况下，RichEditBox 支持拼写检查。 若要禁用拼写检查器，请将 [IsSpellCheckEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox.isspellcheckenabled) 属性设置为 **false**。 有关详细信息，请参阅[拼写检查器指南](text-controls.md)文章。

请使用 RichEditBox 的 [Document](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox.document) 属性来获取其内容。 RichEditBox 的内容是一个 [Windows.UI.Text.ITextDocument](https://docs.microsoft.com/windows/desktop/api/tom/nn-tom-itextdocument) 对象，与 RichTextBlock 控件不同，它使用 [Windows.UI.Xaml.Documents.Block](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Block) 对象作为其内容。 ITextDocument 接口提供了一种方法，可用来执行将文档加载并保存到流中、检索文本范围、获取主动选择、撤消和恢复更改、设置默认格式设置属性等操作。

以下示例显示了如何在 RichEditBox 中编辑、加载并保存富文本格式 (.rtf) 文件。

```xaml
<RelativePanel Margin="20" HorizontalAlignment="Stretch">
    <RelativePanel.Resources>
        <Style TargetType="AppBarButton">
            <Setter Property="IsCompact" Value="True"/>
        </Style>
    </RelativePanel.Resources>
    <AppBarButton x:Name="openFileButton" Icon="OpenFile"
                  Click="OpenButton_Click" ToolTipService.ToolTip="Open file"/>
    <AppBarButton Icon="Save" Click="SaveButton_Click"
                  ToolTipService.ToolTip="Save file"
                  RelativePanel.RightOf="openFileButton" Margin="8,0,0,0"/>

    <AppBarButton Icon="Bold" Click="BoldButton_Click" ToolTipService.ToolTip="Bold"
                  RelativePanel.LeftOf="italicButton" Margin="0,0,8,0"/>
    <AppBarButton x:Name="italicButton" Icon="Italic" Click="ItalicButton_Click"
                  ToolTipService.ToolTip="Italic" RelativePanel.LeftOf="underlineButton" Margin="0,0,8,0"/>
    <AppBarButton x:Name="underlineButton" Icon="Underline" Click="UnderlineButton_Click"
                  ToolTipService.ToolTip="Underline" RelativePanel.AlignRightWithPanel="True"/>

    <RichEditBox x:Name="editor" Height="200" RelativePanel.Below="openFileButton"
                 RelativePanel.AlignLeftWithPanel="True" RelativePanel.AlignRightWithPanel="True"/>
</RelativePanel>
```


```csharp
private async void OpenButton_Click(object sender, RoutedEventArgs e)
{
    // Open a text file.
    Windows.Storage.Pickers.FileOpenPicker open =
        new Windows.Storage.Pickers.FileOpenPicker();
    open.SuggestedStartLocation =
        Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
    open.FileTypeFilter.Add(".rtf");

    Windows.Storage.StorageFile file = await open.PickSingleFileAsync();

    if (file != null)
    {
        try
        {
            Windows.Storage.Streams.IRandomAccessStream randAccStream =
        await file.OpenAsync(Windows.Storage.FileAccessMode.Read);

            // Load the file into the Document property of the RichEditBox.
            editor.Document.LoadFromStream(Windows.UI.Text.TextSetOptions.FormatRtf, randAccStream);
        }
        catch (Exception)
        {
            ContentDialog errorDialog = new ContentDialog()
            {
                Title = "File open error",
                Content = "Sorry, I couldn't open the file.",
                PrimaryButtonText = "Ok"
            };

            await errorDialog.ShowAsync();
        }
    }
}

private async void SaveButton_Click(object sender, RoutedEventArgs e)
{
    Windows.Storage.Pickers.FileSavePicker savePicker = new Windows.Storage.Pickers.FileSavePicker();
    savePicker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;

    // Dropdown of file types the user can save the file as
    savePicker.FileTypeChoices.Add("Rich Text", new List<string>() { ".rtf" });

    // Default file name if the user does not type one in or select a file to replace
    savePicker.SuggestedFileName = "New Document";

    Windows.Storage.StorageFile file = await savePicker.PickSaveFileAsync();
    if (file != null)
    {
        // Prevent updates to the remote version of the file until we
        // finish making changes and call CompleteUpdatesAsync.
        Windows.Storage.CachedFileManager.DeferUpdates(file);
        // write to file
        Windows.Storage.Streams.IRandomAccessStream randAccStream =
            await file.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);

        editor.Document.SaveToStream(Windows.UI.Text.TextGetOptions.FormatRtf, randAccStream);

        // Let Windows know that we're finished changing the file so the
        // other app can update the remote version of the file.
        Windows.Storage.Provider.FileUpdateStatus status = await Windows.Storage.CachedFileManager.CompleteUpdatesAsync(file);
        if (status != Windows.Storage.Provider.FileUpdateStatus.Complete)
        {
            Windows.UI.Popups.MessageDialog errorBox =
                new Windows.UI.Popups.MessageDialog("File " + file.Name + " couldn't be saved.");
            await errorBox.ShowAsync();
        }
    }
}

private void BoldButton_Click(object sender, RoutedEventArgs e)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
        charFormatting.Bold = Windows.UI.Text.FormatEffect.Toggle;
        selectedText.CharacterFormat = charFormatting;
    }
}

private void ItalicButton_Click(object sender, RoutedEventArgs e)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
        charFormatting.Italic = Windows.UI.Text.FormatEffect.Toggle;
        selectedText.CharacterFormat = charFormatting;
    }
}

private void UnderlineButton_Click(object sender, RoutedEventArgs e)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
        if (charFormatting.Underline == Windows.UI.Text.UnderlineType.None)
        {
            charFormatting.Underline = Windows.UI.Text.UnderlineType.Single;
        }
        else {
            charFormatting.Underline = Windows.UI.Text.UnderlineType.None;
        }
        selectedText.CharacterFormat = charFormatting;
    }
}
```

## <a name="choose-the-right-keyboard-for-your-text-control"></a>为文本控件选择正确的键盘

若要帮助用户使用触摸键盘或软输入面板 (SIP) 输入数据，你可以将文本控件的输入范围设置为与期望用户输入的数据类型匹配。 默认键盘布局通常适用于处理富文本文档。

有关如何使用输入范围的详细信息，请参阅[使用输入范围更改触摸键盘](https://docs.microsoft.com/windows/uwp/design/input/use-input-scope-to-change-the-touch-keyboard)。

## <a name="dos-and-donts"></a>应做事项和禁止事项

- 创建 RTF 框时，提供样式设置按钮并实现它们的操作。
- 使用与应用样式一致的字体。
- 使文本控件的高度足够大，以便容纳典型输入。
- 不要让文本输入控件在用户键入时增加高度。
- 当用户仅需要一行时，不要使用多行文本框。
- 如果纯文本控件足够使用，不要使用富文本控件。

## <a name="get-the-sample-code"></a>获取示例代码

- [XAML 控件库示例](https://github.com/Microsoft/Xaml-Controls-Gallery) - 以交互式格式查看所有 XAML 控件。

## <a name="related-articles"></a>相关文章

- [文本控件](text-controls.md)
- [拼写检查指南](text-controls.md)
- [添加搜索](search.md)
- [文本输入指南](text-controls.md)
- [TextBox 类](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)
- [Windows.UI.Xaml.Controls PasswordBox 类](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)
