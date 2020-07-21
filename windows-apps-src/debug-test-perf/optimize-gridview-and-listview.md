---
ms.assetid: 26DF15E8-2C05-4174-A714-7DF2E8273D32
title: ListView 和 GridView UI 优化
description: 通过 UI 虚拟化、元素减少和项目的进度更新来改进 ListView 和 GridView 性能和启动时间。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 73da4a2a590c5f1d860bb480c6d81b01e5e93819
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79210073"
---
# <a name="listview-and-gridview-ui-optimization"></a>ListView 和 GridView UI 优化


**注意**   有关详细信息，请参阅 //build/ 会话：[当用户与 GridView 和 ListView 中的大量数据交互时可以显著提高性能](https://channel9.msdn.com/events/build/2013/3-158)。

通过 UI 虚拟化、元素减少和项目的进度更新来改进 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 和 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) 性能和启动时间。 有关数据虚拟化技术，请参阅 [ListView 和 GridView 数据虚拟化](listview-and-gridview-data-optimization.md)。

## <a name="two-key-factors-in-collection-performance"></a>集锦性能的两个关键因素

操作集锦是一个常见方案。 照片查看器具有照片集锦，阅读器具有文章/书籍/故事的集锦，而购物应用具有产品集锦。 本主题介绍可以采取哪些措施来提高应用操作集锦的效率。

对于集锦有两个关键性能因素：一个是 UI 线程创建项目的时间；另一个是原始数据集和用于呈现该数据的 UI 元素这两者所使用的内存。

对于平滑平移/滚动，UI 线程是否高效、智能地完成实例化、数据绑定和设置项目布局至关重要。

## <a name="ui-virtualization"></a>UI 虚拟化

UI 虚拟化是你可以实现的最重要改进。 这意味着按需创建表示项目的 UI 元素。 对于绑定到 1000 个项目的集合的项目控件，同时为所有项目创建 UI 会造成资源浪费，因为它们不可能全部同时显示。 [ListView  ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 和 [GridView  ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)（及其他标准 [ItemsControl  ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl) 派生的控件）可为你执行 UI 虚拟化。 当项目即将滚动到视图中时（只距离几页），框架将为这些项目生成 UI 并缓存它们。 当这些项目不太可能再次显示时，框架将回收内存。

如果提供自定义项目面板模板（请参阅 [**ItemsPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel)），务必使用虚拟化面板，例如 [**ItemsWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid) 或 [**ItemsStackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsStackPanel)。 如果使用 [**VariableSizedWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VariableSizedWrapGrid)、[**WrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid) 或 [**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel)，则不会实现虚拟化。 此外，只有当使用 [**ItemsWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid) 或 [**ItemsStackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsStackPanel) 时才会引发以下 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 事件：[**ChoosingGroupHeaderContainer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.choosinggroupheadercontainer)、[**ChoosingItemContainer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) 和 [**ContainerContentChanging**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging)。

视口的概念对于 UI 虚拟化至关重要，因为该框架必须创建可能显示的元素。 通常，[**ItemsControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl) 的视口是逻辑控件的范围。 例如 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 的视口是 **ListView** 元素的宽度和高度。 某些面板允许子元素使用无限的空间（例如 [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) 和 [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)），并自动调整行或列的大小。 将虚拟化的 **ItemsControl** 放置在类似的面板中后，它有足够的空间用于显示它的所有项目，此时虚拟化便失去意义。 还原虚拟化的方法是对 **ItemsControl** 设置宽度和高度。

## <a name="element-reduction-per-item"></a>按项目减少元素

将用于呈现项目的 UI 元素数量保持在合理的最小值范围内。

当一个项目控件首次显示时，将创建呈现完整项目的视口所需的所有元素。 此外，当项目接近视口时，框架将使用绑定数据对象更新缓存项目模板中的 UI 元素。 最大程度地降低模板内标记的复杂度可节省 UI 线程所占用的内存以及所花费的时间，从而提高响应速度（尤其在平移/滚动时）。 所讨论的模板为项目模板（请参阅 [**ItemTemplate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate)）和 [**ListViewItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewitem) 或 [**GridViewItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridviewitem) 的控件模板（项目控件模板，或 [**ItemContainerStyle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle)）。 即使仅少量减少元素数量，由此带来的优势也因显示的项目数量而成倍增加。

有关减少元素的示例，请参阅[优化 XAML 标记](optimize-xaml-loading.md)。

[  **ListViewItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewitem) 和 [**GridViewItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridviewitem) 的默认控件模板包含一个 [**ListViewItemPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.ListViewItemPresenter) 元素。 此表示器是已优化的单个元素，用于显示焦点、选择和其他视觉状态的复杂视觉效果。 如果你已拥有自定义项目控件模板 ([**ItemContainerStyle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle))，或者如果你将来要编辑项目控件模板的副本，建议你使用 **ListViewItemPresenter**，因为在大多数情况下，该元素可以最好地平衡性能与自定义。 你可以自定义该表示器，方法是为其设置属性。 例如，以下提供的标记可删除在选定项目时默认出现的复选标记，并将选定项目的背景色更改为橙色。

```xml
...
<ListView>
 ...
 <ListView.ItemContainerStyle>
 <Style TargetType="ListViewItem">
 <Setter Property="Template">
 <Setter.Value>
 <ControlTemplate TargetType="ListViewItem">
 <ListViewItemPresenter SelectionCheckMarkVisualEnabled="False" SelectedBackground="Orange"/>
 </ControlTemplate>
 </Setter.Value>
 </Setter>
 </Style>
 </ListView.ItemContainerStyle>
</ListView>
<!-- ... -->
```

存在大约 25 个属性，自描述名称类似于 [**SelectionCheckMarkVisualEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectioncheckmarkvisualenabled) 和 [**SelectedBackground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedbackground)。 如果表示器类型已证实不足以为你的用例进行自定义，你可以改为编辑 `ListViewItemExpanded` 或 `GridViewItemExpanded` 控件模板的副本。 它们位于 `\Program Files (x86)\Windows Kits\10\DesignTime\CommonConfiguration\Neutral\UAP\<version>\Generic\generic.xaml` 中。 请注意，使用这些模板意味着将要付出一些性能的代价来增加自定义项。

<span id="update-items-incrementally"/>

## <a name="update-listview-and-gridview-items-progressively"></a>渐进更新 ListView 和 GridView 项目

如果要使用数据虚拟化，则可以保持 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 和 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) 的较高响应速度，方法是配置控件以针对仍在加载的项目呈现临时 UI 元素。 在数据加载时，临时元素随后会逐步替换为实际 UI。

此外，无论从何处加载数据（本地磁盘、网络或云），用户都可能会快速平移/滚动 [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 或 [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView)，从而无法在保持平滑平移/滚动的同时完全保真地呈现每个项目。 若要保持平滑平移/滚动，除了使用占位符之外，还可以选择在多个阶段呈现项目。

这些技术的示例常见于照片查看应用：即使并不加载和显示所有图像，用户仍可以平移/滚动并与集合交互。 或者，对于“电影”项，你可以在第一阶段显示标题，在第二阶段显示评级，在第三阶段显示海报的图像。 用户将尽早看到关于每个项目的最重要数据，这意味着他们可以立刻采取行动。 然后在时间允许的情况下填入次要信息。 下面是可以用于实现这些技术的平台功能。

### <a name="placeholders"></a>占位符

默认情况下启用临时占位符视觉功能，它受 [**ShowsScrollingPlaceholders**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.showsscrollingplaceholders) 属性控制。 在快速平移/滚动期间，此功能为用户提供还有更多项目需要完全显示的视觉提示，同时还可保持平滑度。 如果你使用以下技术之一，当你不希望使系统呈现占位符时，可以将 **ShowsScrollingPlaceholders** 设置为 false。

**使用 x:Phase 的渐进数据模板更新**

下面介绍了如何将 [x:Phase 属性](https://docs.microsoft.com/windows/uwp/xaml-platform/x-phase-attribute)与 [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) 绑定结合使用来实现渐进数据模板更新。

1.  绑定源如下所示（这是我们将要绑定到其中的数据源）。

    ```csharp
    namespace LotsOfItems
    {
        public class ExampleItem
        {
            public string Title { get; set; }
            public string Subtitle { get; set; }
            public string Description { get; set; }
        }

        public class ExampleItemViewModel
        {
            private ObservableCollection<ExampleItem> exampleItems = new ObservableCollection<ExampleItem>();
            public ObservableCollection<ExampleItem> ExampleItems { get { return this.exampleItems; } }

            public ExampleItemViewModel()
            {
                for (int i = 1; i < 150000; i++)
                {
                    this.exampleItems.Add(new ExampleItem(){
                        Title = "Title: " + i.ToString(),
                        Subtitle = "Sub: " + i.ToString(),
                        Description = "Desc: " + i.ToString()
                    });
                }
            }
        }
    }
    ```
2.  下面是 `DeferMainPage.xaml` 包含的标记。 网格视图包含的项目模板具有绑定到 **MyItem** 类的 **Title**、**Subtitle** 和 **Description** 属性的元素。 请注意，**x:Phase** 默认值为 0。 此时，最初呈现的项目将只有标题可见。 然后，所有项目的副标题元素将进行数据绑定并变为可见，以此类推直到所有阶段都已处理。
    ```xml
    <Page
        x:Class="LotsOfItems.DeferMainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:lotsOfItems="using:LotsOfItems"
        mc:Ignorable="d">

        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <GridView ItemsSource="{x:Bind ViewModel.ExampleItems}">
                <GridView.ItemTemplate>
                    <DataTemplate x:DataType="lotsOfItems:ExampleItem">
                        <StackPanel Height="100" Width="100" Background="OrangeRed">
                            <TextBlock Text="{x:Bind Title}"/>
                            <TextBlock Text="{x:Bind Subtitle}" x:Phase="1"/>
                            <TextBlock Text="{x:Bind Description}" x:Phase="2"/>
                        </StackPanel>
                    </DataTemplate>
                </GridView.ItemTemplate>
            </GridView>
        </Grid>
    </Page>
    ```

3.  如果立即运行应用并在网格视图中快速平移/滚动，你会注意到当每个新项目出现在屏幕上时，首先呈现为深灰色矩形（由于 [**ShowsScrollingPlaceholders**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.showsscrollingplaceholders) 属性默认设置为 **true**），然后依次显示标题、副标题和描述。

**使用 ContainerContentChanging 的渐进数据模板更新**

[  **ContainerContentChanging**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging) 事件的常规策略是使用 **Opacity** 隐藏无需立即可见的元素。 当元素已回收时，它们将保留原来的值，因此我们希望在从新的数据项目更新这些值之前隐藏这些元素。 我们对事件参数使用 **Phase** 属性以确定要更新和显示的元素。 如果需要其他阶段，则注册一个回调。

1.  我们将使用 **x:Phase** 所使用的同一绑定源。

2.  下面是 `MainPage.xaml` 包含的标记。 网格视图向其 [**ContainerContentChanging**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging) 事件声明一个处理程序，并且它包含的项目模板具有用于显示 **MyItem** 类的 **Title**、**Subtitle** 和 **Description** 属性的元素。 为了最大程度地获得因使用 **ContainerContentChanging** 所带来的性能优势，我们不在标记中使用绑定，而是改为以编程方式分配值。 此处的例外是显示标题的元素，我们认为它应处于 0 阶段。
    ```xml
    <Page
        x:Class="LotsOfItems.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:lotsOfItems="using:LotsOfItems"
        mc:Ignorable="d">

        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <GridView ItemsSource="{x:Bind ViewModel.ExampleItems}" ContainerContentChanging="GridView_ContainerContentChanging">
                <GridView.ItemTemplate>
                    <DataTemplate x:DataType="lotsOfItems:ExampleItem">
                        <StackPanel Height="100" Width="100" Background="OrangeRed">
                            <TextBlock Text="{x:Bind Title}"/>
                            <TextBlock Opacity="0"/>
                            <TextBlock Opacity="0"/>
                        </StackPanel>
                    </DataTemplate>
                </GridView.ItemTemplate>
            </GridView>
        </Grid>
    </Page>
    ```
3.  最后，以下是 [**ContainerContentChanging**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging) 事件处理程序的实现。 此代码还演示了如何将类型 **RecordingViewModel** 的属性添加到 **MainPage** 以公开用于表示标记页面的类中的绑定源类。 只要在数据模板中没有任何 [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) 绑定，便将事件参数对象标记为已在处理程序的第一阶段中处理，从而提示项目无需设置数据上下文。
    ```csharp
    namespace LotsOfItems
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            public MainPage()
            {
                this.InitializeComponent();
                this.ViewModel = new ExampleItemViewModel();
            }

            public ExampleItemViewModel ViewModel { get; set; }

            // Display each item incrementally to improve performance.
            private void GridView_ContainerContentChanging(ListViewBase sender, ContainerContentChangingEventArgs args)
            {
                if (args.Phase != 0)
                {
                    throw new System.Exception("We should be in phase 0, but we are not.");
                }

                // It's phase 0, so this item's title will already be bound and displayed.

                args.RegisterUpdateCallback(this.ShowSubtitle);

                args.Handled = true;
            }

            private void ShowSubtitle(ListViewBase sender, ContainerContentChangingEventArgs args)
            {
                if (args.Phase != 1)
                {
                    throw new System.Exception("We should be in phase 1, but we are not.");
                }

                // It's phase 1, so show this item's subtitle.
                var templateRoot = args.ItemContainer.ContentTemplateRoot as StackPanel;
                var textBlock = templateRoot.Children[1] as TextBlock;
                textBlock.Text = (args.Item as ExampleItem).Subtitle;
                textBlock.Opacity = 1;

                args.RegisterUpdateCallback(this.ShowDescription);
            }

            private void ShowDescription(ListViewBase sender, ContainerContentChangingEventArgs args)
            {
                if (args.Phase != 2)
                {
                    throw new System.Exception("We should be in phase 2, but we are not.");
                }

                // It's phase 2, so show this item's description.
                var templateRoot = args.ItemContainer.ContentTemplateRoot as StackPanel;
                var textBlock = templateRoot.Children[2] as TextBlock;
                textBlock.Text = (args.Item as ExampleItem).Description;
                textBlock.Opacity = 1;
            }
        }
    }
    ```

4.  如果立即运行应用并快速在网格视图中平移/滚动，你看到的行为与使用 **x:Phase** 时相同。

## <a name="container-recycling-with-heterogeneous-collections"></a>将容器回收用于异类集合

在某些应用程序中，你需要为集锦中的不同类型的项目使用不同类型的 UI。 这可能造成虚拟化面板无法重复使用/循环使用用于显示项目的可视元素。 在平移期间为某个项目重新创建可视元素会撤销虚拟化所提供的许多性能优势。 但是，进行一些规划便可以允许虚拟化面板重复使用这些元素。 开发人员可以根据其方案选择一组选项：[**ChoosingItemContainer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) 事件或项目模板选择器。 **ChoosingItemContainer** 方法具有更好的性能。

**ChoosingItemContainer 事件**

[ChoosingItemContainer  ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) 是一个事件，每当在启动或循环期间需要新项目时，你都可以向 **ListView**/  [GridView  ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) 提供项目 (ListViewItem/[**GridViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView))。 你可以根据容器将显示的数据项目类型创建容器（如以下示例所示）。 **ChoosingItemContainer** 是针对不同项目使用不同数据模板的性能更高的方法。 容器缓存是可以使用 **ChoosingItemContainer** 实现的某些内容。 例如，如果你有五个不同的模板，其中一个模板发生的频率比其他模板高一个数量级，则 ChoosingItemContainer 允许你不仅可以以所需的比率创建项目，还可以保留相应数量的缓存且可用于循环的元素。 [ChoosingGroupHeaderContainer  ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.choosinggroupheadercontainer) 为组标头提供相同的功能。

```csharp
// Example shows how to use ChoosingItemContainer to return the correct
// DataTemplate when one is available. This example shows how to return different 
// data templates based on the type of FileItem. Available ListViewItems are kept
// in two separate lists based on the type of DataTemplate needed.
private void ListView_ChoosingItemContainer
    (ListViewBase sender, ChoosingItemContainerEventArgs args)
{
    // Determines type of FileItem from the item passed in.
    bool special = args.Item is DifferentFileItem;

    // Uses the Tag property to keep track of whether a particular ListViewItem's 
    // datatemplate should be a simple or a special one.
    string tag = special ? "specialFiles" : "simpleFiles";

    // Based on the type of datatemplate needed return the correct list of 
    // ListViewItems, this could have also been handled with a hash table. These 
    // two lists are being used to keep track of ItemContainers that can be reused.
    List<UIElement> relevantStorage = special ? specialFileItemTrees : simpleFileItemTrees;

    // args.ItemContainer is used to indicate whether the ListView is proposing an 
    // ItemContainer (ListViewItem) to use. If args.Itemcontainer, then there was a 
    // recycled ItemContainer available to be reused.
    if (args.ItemContainer != null)
    {
        // The Tag is being used to determine whether this is a special file or 
        // a simple file.
        if (args.ItemContainer.Tag.Equals(tag))
        {
            // Great: the system suggested a container that is actually going to 
            // work well.
        }
        else
        {
            // the ItemContainer's datatemplate does not match the needed 
            // datatemplate.
            args.ItemContainer = null;
        }
    }

    if (args.ItemContainer == null)
    {
        // see if we can fetch from the correct list.
        if (relevantStorage.Count > 0)
        {
            args.ItemContainer = relevantStorage[0] as SelectorItem;
        }
        else
        {
            // there aren't any (recycled) ItemContainers available. So a new one 
            // needs to be created.
            ListViewItem item = new ListViewItem();
            item.ContentTemplate = this.Resources[tag] as DataTemplate;
            item.Tag = tag;
            args.ItemContainer = item;
        }
    }
}
```

**项目模板选择器**

项目模板选择器 ([**DataTemplateSelector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DataTemplateSelector)) 允许应用根据要显示的数据项目类型在运行时返回不同的项目模板。 这使开发更高效，但也使 UI 虚拟化更困难，因为不是每一个项目模板都可重复用于每个数据项目。

在循环使用项目 (**ListViewItem**/**GridViewItem**) 时，框架必须确定可用于循环队列（循环队列是当前未用于显示数据的项目的缓存）的项目是否具有可与当前数据项目所需的项目模板匹配的项目模板。 如果循环队列中没有带有相应项目模板的项目，则将创建一个新项目，并为其实例化相应的项目模板。 另一方面，如果循环队列包含带有相应项目模板的项目，则该项目将从循环队列中删除并用于当前数据项目。 在仅使用少量项目模板以及在使用不同项目模板的项目集锦分布均匀的情况下，项目模板选择器有效。

当使用不同项目模板的项目分布不均匀时，可能需要在平移期间创建新项目模板，并且这将取消虚拟化所提供的许多好处。 此外，在评估是否可为当前数据项目重复使用特定的容器时，项目模板选择器仅考虑五个可能的候选项。 因此，在应用中使用项目模板选择器前，你应谨慎考虑你的数据是否适用于项目模板选择器。 如果你的集锦大部分是同类，则选择器将在大多数时间（可能是所有时间）返回相同的类型。 请注意你要为上述罕见的同质例外所付出的代价，并考虑是否首选使用 [**ChoosingItemContainer**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.choosingitemcontainer)（或两个项目控件）。

 

