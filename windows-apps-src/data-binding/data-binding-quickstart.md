---
ms.assetid: A9D54DEC-CD1B-4043-ADE4-32CD4977D1BF
title: 数据绑定概述
description: 本主题介绍了如何在通用 Windows 平台 (UWP) 应用中将控件（或其他 UI 元素）绑定到单个项目，或者将项目控件绑定到项目集合。
ms.date: 10/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cppcx
ms.openlocfilehash: c21ea3baaa5992877d1fc0f695e36dda53f69ce2
ms.sourcegitcommit: 27787a579e497d097382338654ed371b661cc3b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107321708"
---
# <a name="data-binding-overview"></a>数据绑定概述

本主题介绍了如何在通用 Windows 平台 (UWP) 应用中将控件（或其他 UI 元素）绑定到单个项目，或者将项目控件绑定到项目集合。 此外，我们还介绍了如何控制项的呈现、基于所选内容实现详细信息视图，以及转换数据以供显示。 有关更多详细信息，请参阅[深入了解数据绑定](data-binding-in-depth.md)。

## <a name="prerequisites"></a>必备条件

此主题假设你知道如何创建基本的 UWP 应用。 有关创建你的第一个 UWP 应用的说明，请参阅 [Windows 应用入门](../get-started/index.md)。

## <a name="create-the-project"></a>创建项目

创建一个新的“空白应用程序(Windows 通用)”  项目。 将它命名为“Quickstart”。

## <a name="binding-to-a-single-item"></a>绑定到单个项目

每个绑定均由一个绑定目标和一个绑定源构成。 通常，绑定目标是控件或其他 UI 元素的属性，而绑定源是类实例（数据模型或视图模型）的属性。 本示例演示了如何将控件绑定到单个项目。 绑定目标是 **TextBlock** 的 **Text** 属性。 绑定源是一个名为 **Recording** 的简单类的实例，该类表示音频录制。 我们先来看一下类。

如果使用的是 C# 或 C++/CX，请将一个新类添加到项目中，并将该类命名 Recording  。

如果使用的是 [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md)，请将新的 Midl 文件 (.idl) 项添加到项目中，如下面 C++/WinRT 代码示例清单中所示对其进行命名  。 将这些新文件的内容替换为列表中显示的 [MIDL 3.0](/uwp/midl-3/intro) 代码，生成项目以生成 `Recording.h``.cpp``RecordingViewModel.h` 和 `.cpp`，然后将代码添加到生成的文件以匹配列表。 有关这些生成的文件以及如何将它们复制到项目中的详细信息，请参阅 [XAML 控件；绑定到 C++/WinRT 属性](../cpp-and-winrt-apis/binding-property.md)。

```csharp
namespace Quickstart
{
    public class Recording
    {
        public string ArtistName { get; set; }
        public string CompositionName { get; set; }
        public DateTime ReleaseDateTime { get; set; }
        public Recording()
        {
            this.ArtistName = "Wolfgang Amadeus Mozart";
            this.CompositionName = "Andante in C for Piano";
            this.ReleaseDateTime = new DateTime(1761, 1, 1);
        }
        public string OneLineSummary
        {
            get
            {
                return $"{this.CompositionName} by {this.ArtistName}, released: "
                    + this.ReleaseDateTime.ToString("d");
            }
        }
    }
    public class RecordingViewModel
    {
        private Recording defaultRecording = new Recording();
        public Recording DefaultRecording { get { return this.defaultRecording; } }
    }
}
```

```cppwinrt
// Recording.idl
namespace Quickstart
{
    runtimeclass Recording
    {
        Recording(String artistName, String compositionName, Windows.Globalization.Calendar releaseDateTime);
        String ArtistName{ get; };
        String CompositionName{ get; };
        Windows.Globalization.Calendar ReleaseDateTime{ get; };
        String OneLineSummary{ get; };
    }
}

// RecordingViewModel.idl
import "Recording.idl";

namespace Quickstart
{
    runtimeclass RecordingViewModel
    {
        RecordingViewModel();
        Quickstart.Recording DefaultRecording{ get; };
    }
}

// Recording.h
// Add these fields:
...
#include <sstream>
...
private:
    std::wstring m_artistName;
    std::wstring m_compositionName;
    Windows::Globalization::Calendar m_releaseDateTime;
...

// Recording.cpp
// Implement like this:
...
Recording::Recording(hstring const& artistName, hstring const& compositionName, Windows::Globalization::Calendar const& releaseDateTime) :
    m_artistName{ artistName.c_str() },
    m_compositionName{ compositionName.c_str() },
    m_releaseDateTime{ releaseDateTime } {}

hstring Recording::ArtistName(){ return hstring{ m_artistName }; }
hstring Recording::CompositionName(){ return hstring{ m_compositionName }; }
Windows::Globalization::Calendar Recording::ReleaseDateTime(){ return m_releaseDateTime; }

hstring Recording::OneLineSummary()
{
    std::wstringstream wstringstream;
    wstringstream << m_compositionName.c_str();
    wstringstream << L" by " << m_artistName.c_str();
    wstringstream << L", released: " << m_releaseDateTime.MonthAsNumericString().c_str();
    wstringstream << L"/" << m_releaseDateTime.DayAsString().c_str();
    wstringstream << L"/" << m_releaseDateTime.YearAsString().c_str();
    return hstring{ wstringstream.str().c_str() };
}
...

// RecordingViewModel.h
// Add this field:
...
#include "Recording.h"
...
private:
    Quickstart::Recording m_defaultRecording{ nullptr };
...

// RecordingViewModel.cpp
// Implement like this:
...
Quickstart::Recording RecordingViewModel::DefaultRecording()
{
    Windows::Globalization::Calendar releaseDateTime;
    releaseDateTime.Year(1761);
    releaseDateTime.Month(1);
    releaseDateTime.Day(1);
    m_defaultRecording = winrt::make<Recording>(L"Wolfgang Amadeus Mozart", L"Andante in C for Piano", releaseDateTime);
    return m_defaultRecording;
}
...
```

```cppcx
// Recording.h
#include <sstream>
namespace Quickstart
{
    public ref class Recording sealed
    {
    private:
        Platform::String^ artistName;
        Platform::String^ compositionName;
        Windows::Globalization::Calendar^ releaseDateTime;
    public:
        Recording(Platform::String^ artistName, Platform::String^ compositionName,
            Windows::Globalization::Calendar^ releaseDateTime) :
            artistName{ artistName },
            compositionName{ compositionName },
            releaseDateTime{ releaseDateTime } {}
        property Platform::String^ ArtistName
        {
            Platform::String^ get() { return this->artistName; }
        }
        property Platform::String^ CompositionName
        {
            Platform::String^ get() { return this->compositionName; }
        }
        property Windows::Globalization::Calendar^ ReleaseDateTime
        {
            Windows::Globalization::Calendar^ get() { return this->releaseDateTime; }
        }
        property Platform::String^ OneLineSummary
        {
            Platform::String^ get()
            {
                std::wstringstream wstringstream;
                wstringstream << this->CompositionName->Data();
                wstringstream << L" by " << this->ArtistName->Data();
                wstringstream << L", released: " << this->ReleaseDateTime->MonthAsNumericString()->Data();
                wstringstream << L"/" << this->ReleaseDateTime->DayAsString()->Data();
                wstringstream << L"/" << this->ReleaseDateTime->YearAsString()->Data();
                return ref new Platform::String(wstringstream.str().c_str());
            }
        }
    };
    public ref class RecordingViewModel sealed
    {
    private:
        Recording ^ defaultRecording;
    public:
        RecordingViewModel()
        {
            Windows::Globalization::Calendar^ releaseDateTime = ref new Windows::Globalization::Calendar();
            releaseDateTime->Year = 1761;
            releaseDateTime->Month = 1;
            releaseDateTime->Day = 1;
            this->defaultRecording = ref new Recording{ L"Wolfgang Amadeus Mozart", L"Andante in C for Piano", releaseDateTime };
        }
        property Recording^ DefaultRecording
        {
            Recording^ get() { return this->defaultRecording; };
        }
    };
}

// Recording.cpp
#include "pch.h"
#include "Recording.h"
```

接下来，从表示标记页的类公开绑定源类。 我们通过将类型 **RecordingViewModel** 的属性添加到 **MainPage** 来执行此操作。

如果使用 [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md)，则首先更新 `MainPage.idl`。 生成项目以重新生成 `MainPage.h` 和 `.cpp`，并将这些生成的文件中的更改合并到项目中的文件中。

```csharp
namespace Quickstart
{
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.ViewModel = new RecordingViewModel();
        }
        public RecordingViewModel ViewModel{ get; set; }
    }
}
```

```cppwinrt
// MainPage.idl
// Add this property:
import "RecordingViewModel.idl";
...
RecordingViewModel ViewModel{ get; };
...

// MainPage.h
// Add this property and this field:
...
#include "RecordingViewModel.h"
...
    Quickstart::RecordingViewModel ViewModel();

private:
    Quickstart::RecordingViewModel m_viewModel{ nullptr };
...

// MainPage.cpp
// Implement like this:
...
MainPage::MainPage()
{
    InitializeComponent();
    m_viewModel = winrt::make<RecordingViewModel>();
}
Quickstart::RecordingViewModel MainPage::ViewModel()
{
    return m_viewModel;
}
...
```

```cppcx
// MainPage.h
...
#include "Recording.h"

namespace Quickstart
{
    public ref class MainPage sealed
    {
    private:
        RecordingViewModel ^ viewModel;
    public:
        MainPage();

        property RecordingViewModel^ ViewModel
        {
            RecordingViewModel^ get() { return this->viewModel; };
        }
    };
}

// MainPage.cpp
...
MainPage::MainPage()
{
    InitializeComponent();
    this->viewModel = ref new RecordingViewModel();
}
```

最后一步是将 **TextBlock** 绑定到 **ViewModel.DefaultRecording.OneLiner** 属性。

```xml
<Page x:Class="Quickstart.MainPage" ... >
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <TextBlock Text="{x:Bind ViewModel.DefaultRecording.OneLineSummary}"
    HorizontalAlignment="Center"
    VerticalAlignment="Center"/>
    </Grid>
</Page>
```

如果使用 [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md)，则需要删除 MainPage::ClickHandler 函数以便生成项目  。

下面是结果。

![绑定文本块](images/xaml-databinding0.png)

## <a name="binding-to-a-collection-of-items"></a>绑定到项目集合

一个常见情形是绑定到业务对象的集合。 在 C# 和 Visual Basic 中，通用 [**ObservableCollection&lt;T&gt;**](/dotnet/api/system.collections.objectmodel.observablecollection-1) 类是数据绑定的一个很好的集合选择，因为它实现了 [**INotifyPropertyChanged**](/dotnet/api/system.componentmodel.inotifypropertychanged) 和 [**INotifyCollectionChanged**](/dotnet/api/system.collections.specialized.inotifycollectionchanged) 接口。 当添加或删除项目或者列表本身的属性更改时，这些接口将向绑定提供更改通知。 如果你希望你的绑定控件使用集合中的对象属性更改进行更新，则业务对象也应该实现 **INotifyPropertyChanged**。 有关详细信息，请参阅[深入了解数据绑定](data-binding-in-depth.md)。

如果使用 [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md)，则可以在 [XAML 项目控件；绑定到 C++/WinRT 集合](../cpp-and-winrt-apis/binding-collection.md)中了解有关绑定到可观察集合的更多信息。 如果你先阅读该主题，则会更清楚下面显示的 C++/WinRT 代码清单的意图。

下面这个示例演示了将 [**ListView**](/uwp/api/Windows.UI.Xaml.Controls.ListView) 绑定到 `Recording` 对象的集合。 让我们先将该集合添加到视图模型。 只需将这些新成员添加到 **RecordingViewModel** 类。

```csharp
public class RecordingViewModel
{
    ...
    private ObservableCollection<Recording> recordings = new ObservableCollection<Recording>();
    public ObservableCollection<Recording> Recordings{ get{ return this.recordings; } }
    public RecordingViewModel()
    {
        this.recordings.Add(new Recording(){ ArtistName = "Johann Sebastian Bach",
            CompositionName = "Mass in B minor", ReleaseDateTime = new DateTime(1748, 7, 8) });
        this.recordings.Add(new Recording(){ ArtistName = "Ludwig van Beethoven",
            CompositionName = "Third Symphony", ReleaseDateTime = new DateTime(1805, 2, 11) });
        this.recordings.Add(new Recording(){ ArtistName = "George Frideric Handel",
            CompositionName = "Serse", ReleaseDateTime = new DateTime(1737, 12, 3) });
    }
}
```

```cppwinrt
// RecordingViewModel.idl
// Add this property:
...
#include <winrt/Windows.Foundation.Collections.h>
...
Windows.Foundation.Collections.IVector<IInspectable> Recordings{ get; };
...

// RecordingViewModel.h
// Change the constructor declaration, and add this property and this field:
...
    RecordingViewModel();
    Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> Recordings();

private:
    Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> m_recordings;
...

// RecordingViewModel.cpp
// Update/add implementations like this:
...
RecordingViewModel::RecordingViewModel()
{
    std::vector<Windows::Foundation::IInspectable> recordings;

    Windows::Globalization::Calendar releaseDateTime;
    releaseDateTime.Month(7); releaseDateTime.Day(8); releaseDateTime.Year(1748);
    recordings.push_back(winrt::make<Recording>(L"Johann Sebastian Bach", L"Mass in B minor", releaseDateTime));

    releaseDateTime = Windows::Globalization::Calendar{};
    releaseDateTime.Month(11); releaseDateTime.Day(2); releaseDateTime.Year(1805);
    recordings.push_back(winrt::make<Recording>(L"Ludwig van Beethoven", L"Third Symphony", releaseDateTime));

    releaseDateTime = Windows::Globalization::Calendar{};
    releaseDateTime.Month(3); releaseDateTime.Day(12); releaseDateTime.Year(1737);
    recordings.push_back(winrt::make<Recording>(L"George Frideric Handel", L"Serse", releaseDateTime));

    m_recordings = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>(std::move(recordings));
}

Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> RecordingViewModel::Recordings() { return m_recordings; }
...
```

```cppcx
// Recording.h
...
public ref class RecordingViewModel sealed
{
private:
    ...
    Windows::Foundation::Collections::IVector<Recording^>^ recordings;
public:
    RecordingViewModel()
    {
        ...
        releaseDateTime = ref new Windows::Globalization::Calendar();
        releaseDateTime->Year = 1748;
        releaseDateTime->Month = 7;
        releaseDateTime->Day = 8;
        Recording^ recording = ref new Recording{ L"Johann Sebastian Bach", L"Mass in B minor", releaseDateTime };
        this->Recordings->Append(recording);
        releaseDateTime = ref new Windows::Globalization::Calendar();
        releaseDateTime->Year = 1805;
        releaseDateTime->Month = 2;
        releaseDateTime->Day = 11;
        recording = ref new Recording{ L"Ludwig van Beethoven", L"Third Symphony", releaseDateTime };
        this->Recordings->Append(recording);
        releaseDateTime = ref new Windows::Globalization::Calendar();
        releaseDateTime->Year = 1737;
        releaseDateTime->Month = 12;
        releaseDateTime->Day = 3;
        recording = ref new Recording{ L"George Frideric Handel", L"Serse", releaseDateTime };
        this->Recordings->Append(recording);
    }
    ...
    property Windows::Foundation::Collections::IVector<Recording^>^ Recordings
    {
        Windows::Foundation::Collections::IVector<Recording^>^ get()
        {
            if (this->recordings == nullptr)
            {
                this->recordings = ref new Platform::Collections::Vector<Recording^>();
            }
            return this->recordings;
        };
    }
};
```

然后，将 [**ListView**](/uwp/api/Windows.UI.Xaml.Controls.ListView) 绑定到 **ViewModel.Recordings** 属性。

```xml
<Page x:Class="Quickstart.MainPage" ... >
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <ListView ItemsSource="{x:Bind ViewModel.Recordings}"
        HorizontalAlignment="Center" VerticalAlignment="Center"/>
    </Grid>
</Page>
```

我们尚未提供适用于 **Recording** 类的数据模板，因此 UI 框架的最佳做法是针对 [**ListView**](/uwp/api/Windows.UI.Xaml.Controls.ListView) 中的每个项来调用 [**ToString**](/dotnet/api/system.object.tostring#System_Object_ToString)。 **ToString** 的默认实现是返回类型名称。

![绑定列表视图 1](images/xaml-databinding1.png)

为了解决此问题，我们可以重写 [ToString](/dotnet/api/system.object.tostring#System_Object_ToString) 以返回 OneLineSummary 的值，或者提供一个数据模板   。 数据模板选项是更为常见的解决方案，并且是一个更灵活的解决方案。 使用内容控件的 [**ContentTemplate**](/uwp/api/windows.ui.xaml.controls.contentcontrol.contenttemplate) 属性或项目控件的 [**ItemTemplate**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) 属性来指定数据模板。 下面是可用于设计适用于 **Recording** 的数据模板以及结果图示的两种方式。

```xml
<ListView ItemsSource="{x:Bind ViewModel.Recordings}"
HorizontalAlignment="Center" VerticalAlignment="Center">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:Recording">
            <TextBlock Text="{x:Bind OneLineSummary}"/>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

![绑定列表视图 2](images/xaml-databinding2.png)

```xml
<ListView ItemsSource="{x:Bind ViewModel.Recordings}"
HorizontalAlignment="Center" VerticalAlignment="Center">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:Recording">
            <StackPanel Orientation="Horizontal" Margin="6">
                <SymbolIcon Symbol="Audio" Margin="0,0,12,0"/>
                <StackPanel>
                    <TextBlock Text="{x:Bind ArtistName}" FontWeight="Bold"/>
                    <TextBlock Text="{x:Bind CompositionName}"/>
                </StackPanel>
            </StackPanel>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

![绑定列表视图 3](images/xaml-databinding3.png)

有关 XAML 语法的详细信息，请参阅[使用 XAML 创建 UI](../design/basics/xaml-basics-ui.md)。 有关控件布局的详细信息，请参阅[使用 XAML 定义布局](../design/layout/layouts-with-xaml.md)。

## <a name="adding-a-details-view"></a>添加详细信息视图

你可以选择在 [**ListView**](/uwp/api/Windows.UI.Xaml.Controls.ListView) 项目中显示 **Recording** 对象的所有详细信息。 但这样做会占用大量空间。 不过，你可以在该项目中仅显示足够多的数据来标识它，然后在用户做出选择时，你可以在 UI 的单个部分（即，详细信息视图）中显示选定项的所有详细信息。 这种排列也称为主视图/详细信息视图或列表/详细信息视图。

有两种方法可用来执行此操作。 你可以将详细信息视图绑定到 [**ListView**](/uwp/api/Windows.UI.Xaml.Controls.ListView) 的 [**SelectedItem**](/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem) 属性。 或者，可以使用 [CollectionViewSource](/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource)，在此种情况下将 ListView 和详细信息视图同时绑定到 CollectionViewSource（这将为你处理当前选定的项目）    。 下面介绍这两种技术，它们都提供相同结果（图中所示）。

> [!NOTE]
> 到目前为止，本主题中我们仅使用了 [{x:Bind} 标记扩展](../xaml-platform/x-bind-markup-extension.md)，而将在下面介绍的这两种技术要求更为灵活（但性能较低）的 [{Binding} 标记扩展](../xaml-platform/binding-markup-extension.md)。

如果使用的是 C++/WinRT 或 Visual C++ 组件扩展 (C++/CX)，要使用 [{Binding}](../xaml-platform/binding-markup-extension.md) 标记扩展，则需要将 [BindableAttribute](/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) 属性添加到要绑定到的运行时类  。 若要使用 [{x:Bind}](../xaml-platform/x-bind-markup-extension.md)，则不需要该属性。

> [!IMPORTANT]
> 如果使用 [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md)，则在安装了 Windows SDK 版本 10.0.17763.0（Windows 10 版本 1809）或更高版本的情况下，可使用 [BindableAttribute](/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) 属性  。 如果没有该属性，为了能够使用 [{Binding}](../xaml-platform/binding-markup-extension.md) 标记扩展，需要实现 [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) 和 [ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty) 接口。

首先介绍的是 [**SelectedItem**](/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem) 技术。

```csharp
// No code changes necessary for C#.
```

```cppwinrt
// Recording.idl
// Add this attribute:
...
[Windows.UI.Xaml.Data.Bindable]
runtimeclass Recording
...
```

```cppcx
[Windows::UI::Xaml::Data::Bindable]
public ref class Recording sealed
{
    ...
};
```

只需对标记进行更改。

```xml
<Page x:Class="Quickstart.MainPage" ... >
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
            <ListView x:Name="recordingsListView" ItemsSource="{x:Bind ViewModel.Recordings}">
                <ListView.ItemTemplate>
                    <DataTemplate x:DataType="local:Recording">
                        <StackPanel Orientation="Horizontal" Margin="6">
                            <SymbolIcon Symbol="Audio" Margin="0,0,12,0"/>
                            <StackPanel>
                                <TextBlock Text="{x:Bind CompositionName}"/>
                            </StackPanel>
                        </StackPanel>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
            <StackPanel DataContext="{Binding SelectedItem, ElementName=recordingsListView}"
            Margin="0,24,0,0">
                <TextBlock Text="{Binding ArtistName}"/>
                <TextBlock Text="{Binding CompositionName}"/>
                <TextBlock Text="{Binding ReleaseDateTime}"/>
            </StackPanel>
        </StackPanel>
    </Grid>
</Page>
```

对于 [**CollectionViewSource**](/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) 技术，先添加 **CollectionViewSource** 作为页面资源。

```xml
<Page.Resources>
    <CollectionViewSource x:Name="RecordingsCollection" Source="{x:Bind ViewModel.Recordings}"/>
</Page.Resources>
```

然后，在 [**ListView**](/uwp/api/Windows.UI.Xaml.Controls.ListView)（无需再对其进行命名）和详细信息视图上调节绑定，以使用 [**CollectionViewSource**](/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource)。 请注意，如果将详细信息视图直接绑定到 **CollectionViewSource**，即表示你希望绑定到绑定中的当前项目，其中在集合本身上无法找到路径。 无需将 **CurrentItem** 属性指定为绑定路径，尽管由于不确定性你可以这样做）。

```xml
...
<ListView ItemsSource="{Binding Source={StaticResource RecordingsCollection}}">
...
<StackPanel DataContext="{Binding Source={StaticResource RecordingsCollection}}" ...>
...
```

而在每种情况下结果均相同。

> [!NOTE]
> 如果使用的是 C++，那么 UI 不会如下图所示：ReleaseDateTime 属性的呈现方式不同  。 有关此内容的详细讨论，请参阅以下部分。

![绑定列表视图 4](images/xaml-databinding4.png)

## <a name="formatting-or-converting-data-values-for-display"></a>设置数据值的格式或对其进行转换，以供显示

以上呈现有一个问题。 ReleaseDateTime 属性不只是日期，而是[日期时间](/uwp/api/windows.foundation.datetime)（如果使用的是 C++，则为[日历](/uwp/api/windows.globalization.calendar)）    。 因此在 C# 中，它的显示精度比我们所需要的要大。 并且在 C++ 中，其呈现为类型名称。 解决方案是将字符串属性添加到返回 `this.ReleaseDateTime.ToString("d")` 等效项的 Recording 类  。 将该属性命名为 ReleaseDate 可指示它将返回一个日期，而不是返回日期和时间  。 将其命名为 **ReleaseDateAsString** 可进一步指示它将返回一个字符串。

一个更灵活的解决方案是使用称为值转换器的工具。 下面是如何创作你自己的值转换器的示例。 如果使用的是 C#，则将下面的代码添加到 `Recording.cs` 源代码文件中。 如果使用的是 C++/WinRT，则将新的 Midl 文件 (.idl) 项添加到项目中，如下面 C++/WinRT 代码示例清单中所示对其进行命名，生成项目以生成 `StringFormatter.h` 和 `.cpp`，将这些文件添加到项目，然后将代码清单粘贴到其中  。 此外，将 `#include "StringFormatter.h"` 添加到 `MainPage.h`。

```csharp
public class StringFormatter : Windows.UI.Xaml.Data.IValueConverter
{
    // This converts the value object to the string to display.
    // This will work with most simple types.
    public object Convert(object value, Type targetType,
        object parameter, string language)
    {
        // Retrieve the format string and use it to format the value.
        string formatString = parameter as string;
        if (!string.IsNullOrEmpty(formatString))
        {
            return string.Format(formatString, value);
        }

        // If the format string is null or empty, simply
        // call ToString() on the value.
        return value.ToString();
    }

    // No need to implement converting back on a one-way binding
    public object ConvertBack(object value, Type targetType,
        object parameter, string language)
    {
        throw new NotImplementedException();
    }
}
```

```cppwinrt
// pch.h
...
#include <winrt/Windows.Globalization.h>

// StringFormatter.idl
namespace Quickstart
{
    runtimeclass StringFormatter : [default] Windows.UI.Xaml.Data.IValueConverter
    {
        StringFormatter();
    }
}

// StringFormatter.h
#pragma once

#include "StringFormatter.g.h"
#include <sstream>

namespace winrt::Quickstart::implementation
{
    struct StringFormatter : StringFormatterT<StringFormatter>
    {
        StringFormatter() = default;

        Windows::Foundation::IInspectable Convert(Windows::Foundation::IInspectable const& value, Windows::UI::Xaml::Interop::TypeName const& targetType, Windows::Foundation::IInspectable const& parameter, hstring const& language);
        Windows::Foundation::IInspectable ConvertBack(Windows::Foundation::IInspectable const& value, Windows::UI::Xaml::Interop::TypeName const& targetType, Windows::Foundation::IInspectable const& parameter, hstring const& language);
    };
}

namespace winrt::Quickstart::factory_implementation
{
    struct StringFormatter : StringFormatterT<StringFormatter, implementation::StringFormatter>
    {
    };
}

// StringFormatter.cpp
#include "pch.h"
#include "StringFormatter.h"
#include "StringFormatter.g.cpp"

namespace winrt::Quickstart::implementation
{
    Windows::Foundation::IInspectable StringFormatter::Convert(Windows::Foundation::IInspectable const& value, Windows::UI::Xaml::Interop::TypeName const& /* targetType */, Windows::Foundation::IInspectable const& /* parameter */, hstring const& /* language */)
    {
        // Retrieve the value as a Calendar.
        Windows::Globalization::Calendar valueAsCalendar{ value.as<Windows::Globalization::Calendar>() };

        std::wstringstream wstringstream;
        wstringstream << L"Released: ";
        wstringstream << valueAsCalendar.MonthAsNumericString().c_str();
        wstringstream << L"/" << valueAsCalendar.DayAsString().c_str();
        wstringstream << L"/" << valueAsCalendar.YearAsString().c_str();
        return winrt::box_value(hstring{ wstringstream.str().c_str() });
    }

    Windows::Foundation::IInspectable StringFormatter::ConvertBack(Windows::Foundation::IInspectable const& /* value */, Windows::UI::Xaml::Interop::TypeName const& /* targetType */, Windows::Foundation::IInspectable const& /* parameter */, hstring const& /* language */)
    {
        throw hresult_not_implemented();
    }
}
```

```cppcx
...
public ref class StringFormatter sealed : Windows::UI::Xaml::Data::IValueConverter
{
public:
    virtual Platform::Object^ Convert(Platform::Object^ value, TypeName targetType, Platform::Object^ parameter, Platform::String^ language)
    {
        // Retrieve the value as a Calendar.
        Windows::Globalization::Calendar^ valueAsCalendar = dynamic_cast<Windows::Globalization::Calendar^>(value);

        std::wstringstream wstringstream;
        wstringstream << L"Released: ";
        wstringstream << valueAsCalendar->MonthAsNumericString()->Data();
        wstringstream << L"/" << valueAsCalendar->DayAsString()->Data();
        wstringstream << L"/" << valueAsCalendar->YearAsString()->Data();
        return ref new Platform::String(wstringstream.str().c_str());
    }

    // No need to implement converting back on a one-way binding
    virtual Platform::Object^ ConvertBack(Platform::Object^ value, TypeName targetType, Platform::Object^ parameter, Platform::String^ language)
    {
        throw ref new Platform::NotImplementedException();
    }
};
...
```

> [!NOTE]
> 对于上面的 C++/WinRT 代码清单，在 `StringFormatter.idl` 中，我们会使用[默认属性](/windows/desktop/midl/default)将 IValueConverter 声明为默认接口  。 在列表中，StringFormatter 只有一个构造函数，并且没有方法，因此不会为其生成默认接口  。 如果不会将实例成员添加到 StringFormatter，则 `default` 属性是理想选择，因为不需要 QueryInterface 调用 IValueConverter 方法   。 或者，你可以提示要生成的默认 IStringFormatter 接口，并通过使用 [default_interface 属性](/uwp/midl-3/predefined-attributes#the-default_interface-attribute)批注运行时类本身来实现此操作  。 如果将实例成员添加到 StringFormatter（调用频率比 IValueConverter 的方法更高），则该选项是最佳的，因为这样就不需要 QueryInterface 调用实例成员   。

现在，我们可以将 StringFormatter 的实例添加为页面资源，并可在显示 ReleaseDateTime 属性的 TextBlock 的绑定中使用    。

```xml
<Page.Resources>
    <local:StringFormatter x:Key="StringFormatterValueConverter"/>
</Page.Resources>
...
<TextBlock Text="{Binding ReleaseDateTime,
    Converter={StaticResource StringFormatterValueConverter},
    ConverterParameter=Released: \{0:d\}}"/>
...
```

如上所示，为了格式设置灵活性，我们使用标记通过转换器参数将格式字符串传递到转换器。 在本主题所示的代码示例中，只有 C# 值转换器使用该参数。 但你可以轻松地将 C++ 样式格式字符串作为转换器参数传递，并在值转换器中通过格式设置函数（如 wprintf 或 swprintf）使用它   。

下面是结果。

![显示具有自定义格式的日期](images/xaml-databinding5.png)

> [!NOTE]
> 从 Windows 10 版本 1607 开始，XAML 框架提供内置 Boolean-to-Visibility 转换器。 转换器将 true 映射到 Visibility.Visible 枚举值并将 false 映射到 Visibility.Collapsed，以便你可以将 Visibility 属性绑定到布尔值，无需创建转换器     。 若要使用内置转换器，你的应用的最低目标 SDK 版本必须为 14393 或更高版本。 当你的应用面向较早版本的 Windows 10 时，你无法使用它。 有关目标版本的详细信息，请参阅[版本自适应代码](../debug-test-perf/version-adaptive-code.md)。

## <a name="see-also"></a>另请参阅
* [数据绑定](index.md)