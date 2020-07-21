---
ms.assetid: 12ECEA89-59D2-4BCE-B24C-5A4DD525E0C7
title: 访问家庭组内容
description: 访问存储在用户的“家庭组”文件夹中的内容，包括图片、音乐和视频。
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 07d94f5b11acfe14bf55392c5cbf2c1b7bcfbeef
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "66369388"
---
# <a name="accessing-homegroup-content"></a>访问家庭组内容



**重要的 API**

-   [**Windows.Storage.KnownFolders 类**](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders)

访问存储在用户的“家庭组”文件夹中的内容，包括图片、音乐和视频。

## <a name="prerequisites"></a>必备条件

-   **了解通用 Windows 平台 (UWP) 应用的异步编程**

    若要了解如何使用 C# 或 Visual Basic 编写异步应用，请参阅[使用 C# 或 Visual Basic 调用异步 API](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic)。 若要了解如何使用 C++ 编写异步应用，请参阅[使用 C++ 进行异步编程](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps)。

-   **应用功能声明**

    若要访问家庭组内容，用户的计算机必须已设置家庭组，且你的应用必须至少具有以下功能之一：**picturesLibrary**、**musicLibrary** 或 **videosLibrary**。 当你的应用访问“家庭组”文件夹时，它将仅看到与在你的应用清单中声明的功能相对应的库。 若要了解详细信息，请参阅[文件访问权限](file-access-permissions.md)。

    > [!NOTE]
    > 无论在应用清单中声明了什么功能，也无论用户的共享设置如何，你的应用都看不到家庭组的文档库中的内容。     

-   **了解如何使用文件选取器**

    你通常使用文件选取器访问家庭组中的文件和文件夹。 若要了解如何使用文件选取器，请参阅[使用选取器打开文件和文件夹](quickstart-using-file-and-folder-pickers.md)

-   **了解文件和文件夹查询**

    你可以使用查询来枚举家庭组中的文件和文件夹。 若要了解有关文件和文件夹查询的信息，请参阅[枚举和查询文件和文件夹](quickstart-listing-files-and-folders.md)。

## <a name="open-the-file-picker-at-the-homegroup"></a>在家庭组打开文件选取器

按以下步骤打开文件选取器的一个实例，让用户从家庭组中选取文件和文件夹：

1.  **创建并自定义文件选取器**

    使用 [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) 创建文件选取器，然后将选取器的 [**SuggestedStartLocation**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.suggestedstartlocation) 设置为 [**PickerLocationId.HomeGroup**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.PickerLocationId)。 或者，设置与你的用户和你的应用相关的其他属性。 有关帮助你确定如何自定义文件选取器的指南，请参阅[文件选取器指南和清单](https://docs.microsoft.com/windows/uwp/files/quickstart-using-file-and-folder-pickers)。

    此示例创建了一个在家庭组打开的文件选取器，包含任何类型的文件，并将文件显示为缩略图图像：
    ```cs
    Windows.Storage.Pickers.FileOpenPicker picker = new Windows.Storage.Pickers.FileOpenPicker();
    picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
    picker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.HomeGroup;
    picker.FileTypeFilter.Clear();
    picker.FileTypeFilter.Add("*");
    ```

2.  **显示文件选取器并处理已选取的文件。**

    在你创建并自定义文件选取器之后，让用户通过调用 [**FileOpenPicker.PickSingleFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.picksinglefileasync) 来选取一个文件，或通过调用 [**FileOpenPicker.PickMultipleFilesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.pickmultiplefilesasync) 来选取多个文件。

    此示例显示文件选取器以让用户选取一个文件：
    ```cs
    Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();

    if (file != null)
    {
        // Do something with the file.
    }
    else
    {
        // No file returned. Handle the error.
    }   
    ```

## <a name="search-the-homegroup-for-files"></a>在家庭组中搜索文件

此部分显示了如何找到与用户提供的查询词匹配的家庭组项目。

1.  **从用户获取查询词。**

    下面我们将获取一个用户已输入到某个称为 `searchQueryTextBox` 的 [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) 控件中的查询词：
    ```cs
    string queryTerm = this.searchQueryTextBox.Text;    
    ```

2.  **设置查询选项和搜索筛选器。**

    查询选项确定搜索结果是如何进行排序的，而搜索筛选器确定哪些项目包含在搜索结果中。

    此示例设置先按相关性然后按修改日期对搜索结果进行排序的查询选项。 搜索筛选器是用户在上一步中输入的查询词：
    ```cs
    Windows.Storage.Search.QueryOptions queryOptions =
            new Windows.Storage.Search.QueryOptions
                (Windows.Storage.Search.CommonFileQuery.OrderBySearchRank, null);
    queryOptions.UserSearchFilter = queryTerm.Text;
    Windows.Storage.Search.StorageFileQueryResult queryResults =
            Windows.Storage.KnownFolders.HomeGroup.CreateFileQueryWithOptions(queryOptions);    
    ```

3.  **运行查询并处理结果。**

    以下示例在家庭组中运行搜索查询并将任何匹配的文件的名称另存为一个字符串列表。
    ```cs
    System.Collections.Generic.IReadOnlyList<Windows.Storage.StorageFile> files =
        await queryResults.GetFilesAsync();

    if (files.Count > 0)
    {
        outputString += (files.Count == 1) ? "One file found\n" : files.Count.ToString() + " files found\n";
        foreach (Windows.Storage.StorageFile file in files)
        {
            outputString += file.Name + "\n";
        }
    }    
    ```


## <a name="search-the-homegroup-for-a-particular-users-shared-files"></a>在家庭组中搜索某个特定用户的共享文件

此部分介绍如何查找由特定用户共享的家庭组文件。

1.  **获取家庭组用户的集合。**

    家庭组中每个第一级文件夹表示单个家庭组用户。 因此，若要获取家庭组用户的集合，请调用 [**GetFoldersAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfoldersasync) 检索顶级家庭组文件夹。
    ```cs
    System.Collections.Generic.IReadOnlyList<Windows.Storage.StorageFolder> hgFolders =
        await Windows.Storage.KnownFolders.HomeGroup.GetFoldersAsync();    
    ```

2.  **查找目标用户的文件夹，然后创建一个限定于该用户的文件夹的文件查询。**

    以下示例循环访问检索的文件夹以查找目标用户的文件夹。 然后，它设置查询选项以查找文件夹中的所有文件，这些文件先按相关性然后按数据修改日期进行排序。 此示例构建一个字符串，此字符串报告找到的文件数以及文件的名称。
    ```cs
    bool userFound = false;
    foreach (Windows.Storage.StorageFolder folder in hgFolders)
    {
        if (folder.DisplayName == targetUserName)
        {
            // Found the target user's folder, now find all files in the folder.
            userFound = true;
            Windows.Storage.Search.QueryOptions queryOptions =
                new Windows.Storage.Search.QueryOptions
                    (Windows.Storage.Search.CommonFileQuery.OrderBySearchRank, null);
            queryOptions.UserSearchFilter = "*";
            Windows.Storage.Search.StorageFileQueryResult queryResults =
                folder.CreateFileQueryWithOptions(queryOptions);
            System.Collections.Generic.IReadOnlyList<Windows.Storage.StorageFile> files =
                await queryResults.GetFilesAsync();

            if (files.Count > 0)
            {
                string outputString = "Searched for files belonging to " + targetUserName + "'\n";
                outputString += (files.Count == 1) ? "One file found\n" : files.Count.ToString() + " files found\n";
                foreach (Windows.Storage.StorageFile file in files)
                {
                    outputString += file.Name + "\n";
                }
            }
        }
    }    
    ```

## <a name="stream-video-from-the-homegroup"></a>从家庭组流式传输视频

请按以下步骤从家庭组流式传输视频内容：

1.  **在应用中包含一个 MediaElement。**

    [  **MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 允许你在你的应用中播放音频和视频内容。 有关音频和视频播放的详细信息，请参阅[创建自定义传输控件](https://docs.microsoft.com/windows/uwp/controls-and-patterns/custom-transport-controls)和[音频、视频和相机](https://docs.microsoft.com/windows/uwp/audio-video-camera/index)。
    ```HTML
    <Grid x:Name="Output" HorizontalAlignment="Left" VerticalAlignment="Top" Grid.Row="1">
        <MediaElement x:Name="VideoBox" HorizontalAlignment="Left" VerticalAlignment="Top" Margin="0" Width="400" Height="300"/>
    </Grid>    
    ```

2.  **在家庭组打开某个文件选取器，并应用某个筛选器，该筛选器包含采用你的应用支持的格式的视频文件。**

    此示例在文件打开选取器中包含 .mp4 和 .wmv 文件。
    ```cs
    Windows.Storage.Pickers.FileOpenPicker picker = new Windows.Storage.Pickers.FileOpenPicker();
    picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
    picker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.HomeGroup;
    picker.FileTypeFilter.Clear();
    picker.FileTypeFilter.Add(".mp4");
    picker.FileTypeFilter.Add(".wmv");
    Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();   
    ```

3.  **打开用户选择的文件以进行读取访问，并将文件流设置为** [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) 的源，然后播放该文件。
    ```cs
    if (file != null)
    {
        var stream = await file.OpenAsync(Windows.Storage.FileAccessMode.Read);
        VideoBox.SetSource(stream, file.ContentType);
        VideoBox.Stop();
        VideoBox.Play();
    }
    else
    {
        // No file selected. Handle the error here.
    }    
    ```
