---
title: 处理文件激活
description: 应用可注册为特定文件类型的默认处理程序。
ms.assetid: A0F914C5-62BC-4FF7-9236-E34C5277C363
ms.date: 07/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: 4377db57b3cd713bae8f9c80a0116d016722be19
ms.sourcegitcommit: 90fe7a9a5bfa7299ad1b78bbef289850dfbf857d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756527"
---
# <a name="handle-file-activation"></a>处理文件激活

**重要的 API**

-   [**Windows.ApplicationModel.Activation.FileActivatedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.FileActivatedEventArgs)
-   [**Windows.UI.Xaml.Application.OnFileActivated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onfileactivated)

您的应用程序可以注册为特定文件类型的默认处理程序。 Windows 桌面应用程序和通用 Windows 平台 (UWP) 应用可以注册为默认文件处理程序。 如果用户将你的应用选作特定文件类型的默认处理程序，则每次启动该类型文件时都将激活你的应用。

如果希望处理某种类型文件的所有文件启动，建议你仅针对该文件类型进行注册。 如果你的应用仅需要在内部使用该文件类型，则无需注册为默认的处理程序。 如果选择了针对某种文件类型进行注册，则为该文件类型激活应用时必须向最终用户提供预期的功能。 例如，某个图片查看器应用可以注册为显示 .jpg 文件。 有关文件关联的详细信息，请参阅[文件类型和 URI 指南](https://docs.microsoft.com/windows/uwp/files/index)。

这些步骤显示了如何注册自定义文件类型 .alsdk，以及在用户启动 .alsdk 文件时如何激活你的应用。

> **注意**   在 UWP 应用中，某些 Uri 和文件扩展名保留供内置应用程序和操作系统使用。 使用保留的 URI 或文件扩展名注册应用的尝试将被忽略。 有关详细信息，请参阅[保留的文件和 URI 方案名](reserved-uri-scheme-names.md)。

## <a name="step-1-specify-the-extension-point-in-the-package-manifest"></a>步骤 1：指定程序包清单中的扩展点

应用仅接收程序包清单中列出的文件扩展名的激活事件。 下面是指示应用处理扩展名为 `.alsdk` 的文件的方法。

1.  在**解决方案资源管理器**中，双击 "appxmanifest.xml" 以打开清单设计器。 选择**声明**选项卡，并在**可用声明**下拉列表中，选择**文件类型关联**，然后单击**添加**。 有关文件关联使用的标识符的更多详细信息，请参阅[编程标识符](https://docs.microsoft.com/windows/desktop/shell/fa-progids)。

    以下是清单设计器中每个可以填写的字段的简短描述：

| 字段 | 说明 |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **显示名称** | 为一组文件类型指定显示名称。 显示名称用于标识**控制面板**上 "[设置默认程序](https://docs.microsoft.com/windows/desktop/shell/default-programs)" 中的文件类型。 |
| **徽标** | 指定用于标识桌面上和 "**控制面板**" 上 "[设置默认程序](https://docs.microsoft.com/windows/desktop/shell/default-programs)" 中的文件类型的徽标。 如果不指定徽标，则使用应用程序的小徽标。 |
| **信息提示** | 为一组文件类型指定[信息提示](https://docs.microsoft.com/windows/desktop/shell/fa-progids)。 当用户将光标悬停在此类型的文件的图标上时，将显示此工具提示文本。 |
| **名称** | 为共享相同显示名称、徽标、信息提示和编辑标志的一组文件类型选择一个名称。 选择一个可在整个应用更新期间保持不变的组名称。 **注意**  “名称”必须全部为小写字母。 |
| **内容类型** | 对于特定文件类型，请指定 MIME 内容类型，如 **image/jpeg**。 **有关允许的内容类型的重要说明：** 以下是由于被保留或禁止而无法输入到程序包清单中的 MIME 内容类型的字母顺序列表：**application/force-download**、**application/octet-stream**、**application/unknown**、**application/x-msdownload**。 |
| **文件类型** | 指定要注册的文件类型，前面带有句点，如“.jpeg”。 **保留和禁止的文件类型：** 请参阅[保留 URI 方案名称和文件类型](reserved-uri-scheme-names.md)以获取因为被保留或禁止而无法为 UWP 应用注册的内置应用的文件类型的字母顺序列表。 |

2.  输入 `alsdk` 作为**名称**。
3.  输入 `.alsdk` 作为 **“文件类型”**。
4.  输入 "图像 \\Icon.png" 作为徽标。
5.  按 Ctrl+S 保存对 package.appxmanifest 的更改。

上述步骤会向程序包清单中添加一个类似于此的 [**Extension**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-1-extension) 元素。 **windows.fileTypeAssociation** 类别指示应用处理扩展名为 `.alsdk` 的文件。

```xml
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap:FileTypeAssociation Name="alsdk">
            <uap:Logo>images\icon.png</uap:Logo>
            <uap:SupportedFileTypes>
              <uap:FileType>.alsdk</uap:FileType>
            </uap:SupportedFileTypes>
          </uap:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
```

## <a name="step-2-add-the-proper-icons"></a>步骤 2：添加适当的图标

成为文件类型默认应用的应用在整个系统中的各个位置显示其图标。 例如，这些图标显示在以下位置中：

-   Windows 资源管理器项视图、上下文菜单以及功能区
-   默认程序控制面板
-   文件选取器
-   在“开始”屏幕上搜索结果

在项目中包含一个 44x44 的图标，以便你的徽标可以显示在这些位置。 匹配应用磁贴徽标的外观，并使用应用的背景色而不是使图标透明。 将徽标扩展到边缘而无需填充。 在白色背景上测试你的图标。 有关图标的更多详细信息，请参阅[磁贴和图标资源指南](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/app-assets)。

## <a name="step-3-handle-the-activated-event"></a>步骤 3：处理激活的事件

[**OnFileActivated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onfileactivated) 事件处理程序接收所有文件激活事件。

```csharp
protected override void OnFileActivated(FileActivatedEventArgs args)
{
       // TODO: Handle file activation
       // The number of files received is args.Files.Size
       // The name of the first file is args.Files[0].Name
}
```

```vb
Protected Overrides Sub OnFileActivated(ByVal args As Windows.ApplicationModel.Activation.FileActivatedEventArgs)
      ' TODO: Handle file activation
      ' The number of files received is args.Files.Size
      ' The name of the first file is args.Files(0).Name
End Sub
```

```cppwinrt
void App::OnFileActivated(Windows::ApplicationModel::Activation::FileActivatedEventArgs const& args)
{
    // TODO: Handle file activation.
    auto numberOfFilesReceived{ args.Files().Size() };
    auto nameOfTheFirstFile{ args.Files().GetAt(0).Name() };
}
```

```cpp
void App::OnFileActivated(Windows::ApplicationModel::Activation::FileActivatedEventArgs^ args)
{
    // TODO: Handle file activation
    // The number of files received is args->Files->Size
    // The name of the first file is args->Files->GetAt(0)->Name
}
```

> [!NOTE]
> 通过文件协定启动时，请确保 "后退" 按钮将用户返回到启动应用程序的屏幕，而不是应用程序以前的内容。

建议为打开新页的每个激活事件创建新的 XAML**帧**。 这样，新 XAML 框架的导航 backstack 不包含应用在挂起时当前窗口上可能具有的任何以前的内容。 如果你决定使用单个 XAML**帧**来启动和文件协定，则在导航到新页面之前，应清除**框架**导航日志中的页面。

当应用通过文件激活启动时，应考虑包括允许用户返回到应用的顶级页面的 UI。

## <a name="remarks"></a>备注

收到的文件可能来自不受信任的来源。 我们建议在对该文件采取操作之前，先对文件的内容进行验证。

## <a name="related-topics"></a>相关主题

### <a name="complete-example"></a>完整示例

* [关联启动示例](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/AssociationLaunching)

### <a name="concepts"></a>概念

* [默认程序](https://docs.microsoft.com/windows/desktop/shell/default-programs)
* [文件类型和协议关联模式](https://docs.microsoft.com/windows/desktop/w8cookbook/file-type-and-protocol-associations-model)

### <a name="tasks"></a>任务

* [启动文件的默认应用](launch-the-default-app-for-a-file.md)
* [处理 URI 激活](handle-uri-activation.md)

### <a name="guidelines"></a>准则

* [文件类型和 URI 的指南](https://docs.microsoft.com/windows/uwp/files/index)

### <a name="reference"></a>参考
* [Windows.ApplicationModel.Activation.FileActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.FileActivatedEventArgs)
* [Windows.UI.Xaml.Application.OnFileActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onfileactivated)
