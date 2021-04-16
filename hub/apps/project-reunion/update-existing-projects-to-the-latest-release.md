---
description: 本文提供了有关将使用早期版本的 Project 留尼汪岛或 WinUI 3 版本创建的项目更新到最新版本的说明。
title: 将现有项目更新到最新版本的项目留尼汪岛
ms.topic: article
ms.date: 04/07/2021
keywords: windows win32, 桌面开发, project reunion
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: b4836da83b6bbb2214cc6c5ad446144aac0e168d
ms.sourcegitcommit: df14e7768acdb243190e3418db5afa5d65c5ff88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107574731"
---
# <a name="update-existing-projects-to-the-latest-release-of-project-reunion"></a>将现有项目更新到最新版本的项目留尼汪岛

如果创建的项目使用的是早期版本的项目留尼汪岛或 WinUI 3，则可以更新项目以使用最新稳定版本 (版本 0.5.5) 。

> [!NOTE]
> 由于每个应用的单独场景的唯一性，这些说明可能会有问题。 请谨慎地遵循这些说明进行操作，如果发现问题，请[在 GitHub 存储库上报告错误](https://github.com/microsoft/microsoft-ui-xaml/issues/new/choose)。

## <a name="update-from-project-reunion-050"></a>从项目留尼汪岛0.5.0 更新

如果使用 "项目创建版本 0.5.0" 创建了项目，则可以按照以下说明更新项目，以将项目更新为 0.5.5 (最新稳定版本的) 。 此版本包含几个重要的 bug 修复。

> [!NOTE]
> 如果你使用项目留尼汪岛 0.5 VSIX 创建了项目，则可以通过 Visual Studio 扩展管理器自动更新项目，而无需执行以下手动步骤。 在 Visual Studio 2019 中，单击 "**扩展**  ->  " "**管理扩展**"，然后从左侧菜单栏中选择 "**更新**"。 从列表中选择 "项目留出"，并单击 " **更新**"。 

在开始之前，请确保已安装所有项目留尼汪岛0.5 必备组件，包括最新的项目留尼汪岛 VSIX 和 NuGet 包。 有关更多详细信息，请参阅 [安装说明](get-started-with-project-reunion.md#set-up-your-development-environment)。

首先，请执行以下操作：
- 在 wapproj 文件中，如果 **TargetPlatformMinVersion** 早于10.0.17763.0，请将其更改为10.0.17763.0。

接下来，对项目进行以下更改：
1. 若要获取最新稳定版本的所有更改，需要将 .NET SDK 显式设置为最新版本。 为此，请将以下项组添加到 .csproj 文件中，并保存项目：

    ```xml
    <ItemGroup>            
        <FrameworkReference Update="Microsoft.Windows.SDK.NET.Ref" RuntimeFrameworkVersion="10.0.18362.16" />
        <FrameworkReference Update="Microsoft.Windows.SDK.NET.Ref" TargetingPackVersion="10.0.18362.16" />
    </ItemGroup>
    ```

    请注意，可以在可能的情况下删除 .NET 5.0.6。 

3. 在 Visual Studio 中，转到“工具” -> “NuGet 包管理器” -> “包管理器控制台”  。

4. 输入以下命令：

    ```Console
    uninstall-package Microsoft.ProjectReunion -ProjectName {yourProject}
    uninstall-package Microsoft.ProjectReunion.Foundation -ProjectName {yourProject}
    uninstall-package Microsoft.ProjectReunion.WinUI -ProjectName {yourProject}
    install-package Microsoft.ProjectReunion -Version 0.5.5 -ProjectName {yourProjectName}
    ```

5. 在应用程序 (package).wapproj 中进行以下更改：
  
    1. 添加以下部分：

        ```xml
        <ItemGroup>
            <PackageReference Include="Microsoft.ProjectReunion" Version="[0.5.5]">
                <IncludeAssets>build</IncludeAssets>
            </PackageReference>
        </ItemGroup>
        ```

    2. 删除以下各行：

        ```xml
        <AppxTargetsLocation Condition="'$(AppxTargetsLocation)'==''">$(MSBuildThisFileDirectory)build\</AppxTargetsLocation>
        ```

        以及：

        ```xml
        <Import Project="$(Microsoft_ProjectReunion_AppXReference_props)" />
        <Import Project="$(Microsoft_WinUI_AppX_targets)" />
        ```

        此项组：

        ```xml
        <ItemGroup>
            <PackageReference Include="Microsoft.ProjectReunion" Version="[0.5.0]" GeneratePathProperty="true">
              <ExcludeAssets>all</ExcludeAssets>
            </PackageReference>
            <PackageReference Include="Microsoft.ProjectReunion.WinUI" Version="[0.5.0]" GeneratePathProperty="true">
              <ExcludeAssets>all</ExcludeAssets>
            </PackageReference>
        </ItemGroup>
        ```

## <a name="update-from-project-reunion-05-preview"></a>从项目留尼汪岛0.5 预览版更新

如果已使用 "Project 留尼汪岛0.5 预览版" 创建了项目，则可以按照以下说明更新项目，将项目更新 (最新稳定版本的) 。

在开始之前，请确保已安装所有项目留尼汪岛0.5 必备组件，包括最新的项目留尼汪岛 VSIX 和 NuGet 包。 有关更多详细信息，请参阅 [安装说明](get-started-with-project-reunion.md#set-up-your-development-environment)。

首先，请执行以下操作：

- 在 wapproj 文件中，如果 **TargetPlatformMinVersion** 早于10.0.17763.0，请将其更改为10.0.17763.0。
- 如果应用使用 `Application.Suspending` 事件，请确保删除或更改该行，因为 `Application.Suspending` 不再针对桌面应用进行调用。 有关详细信息，请参阅 [API 参考文档](https://docs.microsoft.com/windows/winui/api/microsoft.ui.xaml.application.suspending)。
- C + + 和 c # 应用的默认项目模板包含以下行。 如果代码中仍存在这些行，请确保删除这些行：

    ```csharp
    this.Suspending += OnSuspending;
    ```

    ```cpp
    Suspending({ this, &App::OnSuspending });
    ```

接下来，对项目进行以下更改：
1. 若要获取最新稳定版本的所有更改，需要将 .NET SDK 显式设置为最新版本。 为此，请将以下项组添加到 .csproj 文件中，并保存项目：

    ```xml
    <ItemGroup>            
        <FrameworkReference Update="Microsoft.Windows.SDK.NET.Ref" RuntimeFrameworkVersion="10.0.18362.16" />
        <FrameworkReference Update="Microsoft.Windows.SDK.NET.Ref" TargetingPackVersion="10.0.18362.16" />
    </ItemGroup>
    ```

    请注意，可以在可能的情况下删除 .NET 5.0.6。

2. 在 Visual Studio 中，转到“工具” -> “NuGet 包管理器” -> “包管理器控制台”  。
3. 输入以下命令：

    ```Console
    uninstall-package Microsoft.ProjectReunion -ProjectName {yourProject}
    uninstall-package Microsoft.ProjectReunion.Foundation -ProjectName {yourProject}
    uninstall-package Microsoft.ProjectReunion.WinUI -ProjectName {yourProject}
    install-package Microsoft.ProjectReunion -Version 0.5.5 -ProjectName {yourProjectName}
    ```

4. 在应用程序 (package).wapproj 中进行以下更改：
  
    1. 添加以下部分：

        ```xml
        <ItemGroup>
            <PackageReference Include="Microsoft.ProjectReunion" Version="[0.5.5]">
                <IncludeAssets>build</IncludeAssets>
            </PackageReference>
        </ItemGroup>
        ```

    2. 删除以下各行：

        ```xml
        <AppxTargetsLocation Condition="'$(AppxTargetsLocation)'==''">$(MSBuildThisFileDirectory)build\</AppxTargetsLocation>
        ```

        且

        ```xml
        <Import Project="$(Microsoft_ProjectReunion_AppXReference_props)" />
        <Import Project="$(Microsoft_WinUI_AppX_targets)" />
        ```

        此项组：

        ```xml
        <ItemGroup>
            <PackageReference Include="Microsoft.ProjectReunion" Version="[0.5.0-prerelease]" GeneratePathProperty="true">
              <ExcludeAssets>all</ExcludeAssets>
            </PackageReference>
            <PackageReference Include="Microsoft.ProjectReunion.WinUI" Version="[0.5.0-prerelease]" GeneratePathProperty="true">
              <ExcludeAssets>all</ExcludeAssets>
            </PackageReference>
        </ItemGroup>
        ```

## <a name="update-from-winui-3-preview-4"></a>从 WinUI 3 预览版4更新

如果使用 WinUI 3 预览版4创建了项目，则可以按照以下说明更新项目，以将项目更新为项目留尼汪岛版本 0.5.5 (最新稳定版本) 。

在开始之前，请确保已安装所有项目留尼汪岛0.5 必备组件，包括最新的项目留尼汪岛 VSIX 和 NuGet 包。 有关更多详细信息，请参阅 [安装说明](get-started-with-project-reunion.md#set-up-your-development-environment)。

首先，请执行以下操作：

- 在 wapproj 文件中，如果 TargetPlatformMinVersion 早于10.0.17763.0，请将其更改为10.0.17763.0。
- 如果应用使用 `Application.Suspending` 事件，请确保删除或更改该行，因为 `Application.Suspending` 不再针对桌面应用进行调用。 有关详细信息，请参阅 [API 参考文档](https://docs.microsoft.com/windows/winui/api/microsoft.ui.xaml.application.suspending?view=winui-3.0-preview&preserve-view=true)。
- C + + 和 c # 应用的默认项目模板包含以下行。 如果代码中仍存在这些行，请确保删除这些行：

    ```csharp
    this.Suspending += OnSuspending;
    ```

    ```cpp
    Suspending({ this, &App::OnSuspending });
    ```

接下来，对项目进行以下更改：
1. 若要获取最新稳定版本的所有更改，需要将 .NET SDK 显式设置为最新版本。 为此，请将以下项组添加到 .csproj 文件中，并保存项目：

    ```xml
    <ItemGroup>            
        <FrameworkReference Update="Microsoft.Windows.SDK.NET.Ref" RuntimeFrameworkVersion="10.0.18362.16" />
        <FrameworkReference Update="Microsoft.Windows.SDK.NET.Ref" TargetingPackVersion="10.0.18362.16" />
    </ItemGroup>
    ```

    请注意，可以在可能的情况下删除 .NET 5.0.6。
2. 在 Visual Studio 中，转到“工具” -> “NuGet 包管理器” -> “包管理器控制台”  。
3. 输入以下命令：

    ```Console
    uninstall-package Microsoft.WinUI -ProjectName {yourProject}
    install-package Microsoft.ProjectReunion -Version 0.5.2 -ProjectName {yourProjectName}
    ```

4. 在应用程序 (package).wapproj 中进行以下更改：

    1. 添加以下部分：

        ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ProjectReunion" Version="[0.5.2]">
            <IncludeAssets>build</IncludeAssets>
          </PackageReference>
        </ItemGroup>
        ```

    2. 删除以下各行：

        ```xml
        <AppxTargetsLocation Condition="'$(AppxTargetsLocation)'==''">$(MSBuildThisFileDirectory)build\</AppxTargetsLocation>
        ```

        ```xml
        <Import Project="$(AppxTargetsLocation)Microsoft.WinUI.AppX.targets" />
        ```

5. 在项目的 {YourProject}(package)/build/ 文件夹下，删除现有的 `Microsoft.WinUI.AppX.targets` 文件。
