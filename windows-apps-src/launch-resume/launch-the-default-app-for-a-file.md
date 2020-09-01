---
title: 启动文件的默认应用
description: 了解如何使用 Windows.System。用于启动应用程序无法自行处理的文件的默认处理程序的启动器 API。
ms.assetid: BB45FCAF-DF93-4C99-A8B5-59B799C7BD98
ms.date: 07/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 34aee5b2e2f04b7e5d72a7bc31d2cfafc1bcb6fb
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89167841"
---
# <a name="launch-the-default-app-for-a-file"></a>启动文件的默认应用

**重要的 API**

-   [**Windows.System.Launcher.LaunchFileAsync**](/uwp/api/windows.system.launcher.launchfileasync)

了解如何启动文件的默认应用。 很多应用需要使用它们自身无法处理的文件。 例如，电子邮件应用接收大量文件类型并且需要使用一种方式在其默认处理程序中启动这些文件。 这些步骤显示了如何使用 [**Windows.System.Launcher**](/uwp/api/Windows.System.Launcher) API 为应用自身无法处理的文件启动默认处理程序。

## <a name="get-the-file-object"></a>获取文件对象

首先，获取该文件的 [**Windows.Storage.StorageFile**](/uwp/api/Windows.Storage.StorageFile) 对象。

如果该文件包含在应用的程序包中，则可以使用 [**Package.InstalledLocation**](/uwp/api/windows.applicationmodel.package.installedlocation) 属性获取 [**Windows.Storage.StorageFolder**](/uwp/api/Windows.Storage.StorageFolder) 对象并且使用 [**Windows.Storage.StorageFolder.GetFileAsync**](/uwp/api/windows.storage.storagefolder.getfileasync) 方法获取 [**StorageFile**](/uwp/api/Windows.Storage.StorageFile) 对象。

如果该文件在已知的文件夹中，则可以使用 [**Windows.Storage.KnownFolders**](/uwp/api/Windows.Storage.KnownFolders) 类的属性获取 [**StorageFolder**](/uwp/api/Windows.Storage.StorageFolder) 并且使用 [**GetFileAsync**](/uwp/api/windows.storage.storagefolder.getfileasync) 方法获取 [**StorageFile**](/uwp/api/Windows.Storage.StorageFile) 对象。

## <a name="launch-the-file"></a>启动该文件

Windows 提供了用于为文件启动默认处理程序的多个不同选项。 这些选项将在此图表中和以下各节中进行介绍。

| 选项 | 方法 | 描述 |
|--------|--------|-------------|
| 默认启动 | [**LaunchFileAsync(IStorageFile)**](/uwp/api/windows.system.launcher.launchfileasync) | 使用默认处理程序启动指定的文件。 |
| 打开方式启动 | [**LaunchFileAsync(IStorageFile, LauncherOptions)**](/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) | 启动指定的文件，该文件让用户通过“打开方式”对话框选取处理程序。 |
| 使用推荐的应用反馈启动 | [**LaunchFileAsync(IStorageFile, LauncherOptions)**](/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) | 使用默认处理程序启动指定的文件。 如果系统上未安装处理程序，则向用户推荐应用商店中的应用。 |
| 以所需的其余视图启动 | [**LaunchFileAsync(IStorageFile, LauncherOptions)**](/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_)（仅适用于 Windows） | 使用默认处理程序启动指定的文件。 指定首选项以便在启动后停留于屏幕上，然后请求特定窗口大小。 [**LauncherOptions.DesiredRemainingView**](/uwp/api/windows.system.launcheroptions.desiredremainingview) 在移动设备系列上不受支持。 |

### <a name="default-launch"></a>默认启动

调用 [**Windows.System.Launcher.LaunchFileAsync(IStorageFile)**](/uwp/api/windows.system.launcher.launchfileasync) 方法以启动默认的应用。 此示例使用 [**Windows.Storage.StorageFolder.GetFileAsync**](/uwp/api/windows.storage.storagefolder.getfileasync) 方法启动应用包中包含的图像文件 test.png。

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
   string imageFile = @"images\test.png";
   
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);
   
   if (file != null)
   {
      // Launch the retrieved file
      var success = await Windows.System.Launcher.LaunchFileAsync(file);

      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```vb
async Sub DefaultLaunch()
   ' Path to the file in the app package to launch
   Dim imageFile = "images\test.png"
   Dim file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile)
   
   If file IsNot Nothing Then
      ' Launch the retrieved file
      Dim success = await Windows.System.Launcher.LaunchFileAsync(file)

      If success Then
         ' File launched
      Else
         ' File launch failed
      End If
   Else
      ' Could not find file
   End If
End Sub
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Launch the retrieved file
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^getFileOperation(installFolder->GetFileAsync("images\\test.png"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Launch the retrieved file
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

### <a name="open-with-launch"></a>打开方式启动

在 [**LauncherOptions.DisplayApplicationPicker**](/uwp/api/windows.system.launcheroptions.displayapplicationpicker) 设置为 **true** 的情况下调用 [**Windows.System.Launcher.LaunchFileAsync(IStorageFile, LauncherOptions)**](/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) 方法以启动用户从 **“打开方式”** 对话框中选择的应用。

当用户希望针对某个特定文件选择默认应用以外的应用时，我们建议你使用 **“打开方式”** 对话框。 例如，如果你的应用允许用户启动某个图像文件，则默认的处理程序将可能是查看器应用。 在某些情况下，用户可能需要编辑图像而不只是查看图像。 使用 **“打开方式”** 选项及 **“应用程序栏”** 或上下文菜单中的备用命令，让用户在此类情况下打开 **“打开方式”** 对话框并选择编辑器应用。

![用于 .png 文件启动的“打开方式”对话框。 该对话框包含一个复选框，用于指定用户的选择是应该用于所有 .png 文件还是只用于这一个 .png 文件。 该对话框包含四个应用选项，用于启动文件和“更多选项”链接。](images/checkboxopenwithdialog.png)

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
      string imageFile = @"images\test.png";
      
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);

   if (file != null)
   {
      // Set the option to show the picker
      var options = new Windows.System.LauncherOptions();
      options.DisplayApplicationPicker = true;

      // Launch the retrieved file
      bool success = await Windows.System.Launcher.LaunchFileAsync(file, options);
      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```vb
async Sub DefaultLaunch()

   ' Path to the file in the app package to launch
   Dim imageFile = "images\test.png"

   Dim file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile)

   If file IsNot Nothing Then
      ' Set the option to show the picker
      Dim options = Windows.System.LauncherOptions()
      options.DisplayApplicationPicker = True

      ' Launch the retrieved file
      Dim success = await Windows.System.Launcher.LaunchFileAsync(file)

      If success Then
         ' File launched
      Else
         ' File launch failed
      End If
   Else
      ' Could not find file
   End If
End Sub
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Set the option to show the picker
        Windows::System::LauncherOptions launchOptions;
        launchOptions.DisplayApplicationPicker(true);

        // Launch the retrieved file
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file, launchOptions);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^> getFileOperation(installFolder->GetFileAsync("images\\test.png"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Set the option to show the picker
         auto launchOptions = ref new Windows::System::LauncherOptions();
         launchOptions->DisplayApplicationPicker = true;

         // Launch the retrieved file
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file, launchOptions));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

**使用推荐的应用反馈启动**

在某些情况下，用户可能未安装用以处理所启动文件的应用。 默认情况下，为处理此类情况，Windows 会向用户提供一个链接，帮助其在应用商店中搜索相应的应用。 如果你希望为用户提供具体的建议，告知他们在此情况下应获取何种应用，则可以随所启用的文件传递该建议。 为此，调用 [**Windows.System.Launcher.launchFileAsync(IStorageFile, LauncherOptions)**](/uwp/api/windows.system.launcher.launchfileasync#Windows_System_Launcher_LaunchFileAsync_Windows_Storage_IStorageFile_Windows_System_LauncherOptions_) 方法，将 [**LauncherOptions.PreferredApplicationPackageFamilyName**](/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname) 设置为应用商店中要推荐的应用的程序包系列名称。 然后，将 [**LauncherOptions.PreferredApplicationDisplayName**](/uwp/api/windows.system.launcheroptions.preferredapplicationdisplayname) 设置为该应用的名称。 Windows 会使用此信息将在应用商店中搜索应用这一常规选项替换为从应用商店中获取推荐的应用这一具体选项。

> [!NOTE]
> 必须将这两个选项都设置为 "推荐应用"。 设置一个而不设置另一个将导致故障。

![用于 .contoso 文件启动的“打开方式”对话框。 因为 .contoso 在计算机上未安装处理程序，所以该对话框中包含一个带有“应用商店”图标和文本的选项，将用户指引到应用商店中的正确处理程序。 该对话框还包含“更多选项”链接。](images/howdoyouwanttoopen.png)

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
   string imageFile = @"images\test.contoso";

   // Get the image file from the package's image directory
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);

   if (file != null)
   {
      // Set the recommended app
      var options = new Windows.System.LauncherOptions();
      options.PreferredApplicationPackageFamilyName = "Contoso.FileApp_8wknc82po1e";
      options.PreferredApplicationDisplayName = "Contoso File App";

      // Launch the retrieved file pass in the recommended app
      // in case the user has no apps installed to handle the file
      bool success = await Windows.System.Launcher.LaunchFileAsync(file, options);
      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```vb
async Sub DefaultLaunch()

   ' Path to the file in the app package to launch
   Dim imageFile = "images\test.contoso"

   ' Get the image file from the package's image directory
   Dim file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile)

   If file IsNot Nothing Then
      ' Set the recommended app
      Dim options = Windows.System.LauncherOptions()
      options.PreferredApplicationPackageFamilyName = "Contoso.FileApp_8wknc82po1e";
      options.PreferredApplicationDisplayName = "Contoso File App";

      ' Launch the retrieved file pass in the recommended app
      ' in case the user has no apps installed to handle the file
      Dim success = await Windows.System.Launcher.LaunchFileAsync(file)

      If success Then
         ' File launched
      Else
         ' File launch failed
      End If
   Else
      ' Could not find file
   End If
End Sub
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Set the recommended app
        Windows::System::LauncherOptions launchOptions;
        launchOptions.PreferredApplicationPackageFamilyName(L"Contoso.FileApp_8wknc82po1e");
        launchOptions.PreferredApplicationDisplayName(L"Contoso File App");

        // Launch the retrieved file, and pass in the recommended app
        // in case the user has no apps installed to handle the file.
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file, launchOptions);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^> getFileOperation(installFolder->GetFileAsync("images\\test.contoso"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Set the recommended app
         auto launchOptions = ref new Windows::System::LauncherOptions();
         launchOptions->PreferredApplicationPackageFamilyName = "Contoso.FileApp_8wknc82po1e";
         launchOptions->PreferredApplicationDisplayName = "Contoso File App";
         
         // Launch the retrieved file pass, and in the recommended app
         // in case the user has no apps installed to handle the file.
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file, launchOptions));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

### <a name="launch-with-a-desired-remaining-view-windows-only"></a>使用所需的其余视图启动（仅适用于 Windows）

调用 [**LaunchFileAsync**](/uwp/api/windows.system.launcher.launchfileasync) 的源应用可请求在文件启动后停留于屏幕上。 默认情况下，Windows 会尝试在处理该文件的源应用和目标应用之间平等地共享所有可用空间。 源应用可使用 [**DesiredRemainingView**](/uwp/api/windows.system.launcheroptions.desiredremainingview) 属性向操作系统指示希望其应用占用较多或较少的可用空间。 此外，还可使用 **DesiredRemainingView** 以指示源应用在文件启动后无需停留于屏幕上，并可由目标应用完全替代。 此属性仅指定调用应用的首选窗口大小。 不指定可能会同时显示在屏幕上的其他应用的行为。

> [!NOTE]
> 当 Windows 确定源应用的最终窗口大小时，Windows 会考虑多个不同的因素，例如，源应用的首选项、屏幕上的应用数和屏幕方向等。 设置 [**DesiredRemainingView**](/uwp/api/windows.system.launcheroptions.desiredremainingview) 并不能保证为源应用设定具体的窗口化行为。

**移动设备系列：  **移动设备家族不支持[**LauncherOptions。**](/uwp/api/windows.system.launcheroptions.desiredremainingview)

```csharp
async void DefaultLaunch()
{
   // Path to the file in the app package to launch
   string imageFile = @"images\test.png";
   
   var file = await Windows.ApplicationModel.Package.Current.InstalledLocation.GetFileAsync(imageFile);

   if (file != null)
   {
      // Set the desired remaining view
      var options = new Windows.System.LauncherOptions();
      options.DesiredRemainingView = Windows.UI.ViewManagement.ViewSizePreference.UseLess;

      // Launch the retrieved file
      bool success = await Windows.System.Launcher.LaunchFileAsync(file, options);
      if (success)
      {
         // File launched
      }
      else
      {
         // File launch failed
      }
   }
   else
   {
      // Could not find file
   }
}
```

```cppwinrt
Windows::Foundation::IAsyncAction MainPage::DefaultLaunch()
{
    auto installFolder{ Windows::ApplicationModel::Package::Current().InstalledLocation() };

    Windows::Storage::StorageFile file{ co_await installFolder.GetFileAsync(L"images\\test.png") };

    if (file)
    {
        // Set the desired remaining view.
        Windows::System::LauncherOptions launchOptions;
        launchOptions.DesiredRemainingView(Windows::UI::ViewManagement::ViewSizePreference::UseLess);

        // Launch the retrieved file.
        bool success = co_await Windows::System::Launcher::LaunchFileAsync(file, launchOptions);
        if (success)
        {
            // File launched
        }
        else
        {
            // File launch failed
        }
    }
    else
    {
        // Could not find file
    }
}
```

```cpp
void MainPage::DefaultLaunch()
{
   auto installFolder = Windows::ApplicationModel::Package::Current->InstalledLocation;

   concurrency::task<Windows::Storage::StorageFile^> getFileOperation(installFolder->GetFileAsync("images\\test.png"));
   getFileOperation.then([](Windows::Storage::StorageFile^ file)
   {
      if (file != nullptr)
      {
         // Set the desired remaining view.
         auto launchOptions = ref new Windows::System::LauncherOptions();
         launchOptions->DesiredRemainingView = Windows::UI::ViewManagement::ViewSizePreference::UseLess;

         // Launch the retrieved file.
         concurrency::task<bool> launchFileOperation(Windows::System::Launcher::LaunchFileAsync(file, launchOptions));
         launchFileOperation.then([](bool success)
         {
            if (success)
            {
               // File launched
            }
            else
            {
               // File launch failed
            }
         });
      }
      else
      {
         // Could not find file
      }
   });
}
```

## <a name="remarks"></a>备注

你的应用不能选择要启动的应用。 用户确定启动哪个应用。 用户可以选择通用 Windows 平台 (UWP) 应用或 Windows 桌面应用。

启动文件时，你的应用必须是前台应用，即必须对用户可见。 此要求有助于确保用户保持控制。 为满足此要求，需确保将文件的所有启动都直接绑定到应用的 UI 中。 大多数情况下，用户总是必须采取某个操作来发起文件启动。

如果包含代码或脚本的文件类型（例如 .exe、.msi 和 .js 文件）由操作系统自动执行，则你无法启动这些文件类型。 此限制可防止用户遭受可能修改操作系统的潜在恶意文件的损害。 如果可以包含脚本的文件类型由可隔离脚本的应用来执行（例如 .docx 文件），则你可以使用此方法来启动这些文件类型。 Microsoft Word 之类的应用可防止 .docx 文件中的脚本修改操作系统。

如果你尝试启动受限制的文件类型，则启动将失败，且会调用错误回调。 如果你的应用处理许多不同类型的文件，并且你预计会遇到该错误，则应该为你的用户提供回退体验。 例如，可以为用户提供将文件保存到桌面的选项，然后用户可以从桌面打开该文件。

## <a name="related-topics"></a>相关主题

### <a name="tasks"></a>任务

* [启动 URI 的默认应用](launch-default-app.md)
* [处理文件激活](handle-file-activation.md)

### <a name="guidelines"></a>指南

* [文件类型和 URI 的指南](../files/index.md)

### <a name="reference"></a>参考

* [**Windows.Storage.StorageFile**](/uwp/api/Windows.Storage.StorageFile)
* [**Windows.System.Launcher.LaunchFileAsync**](/uwp/api/windows.system.launcher.launchfileasync)