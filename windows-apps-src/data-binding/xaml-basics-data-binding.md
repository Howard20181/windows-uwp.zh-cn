---
title: 创建数据绑定
description: 通过按照本教程操作，了解如何使用 XAML 和 C# 创建数据绑定，以在 UI 与数据之间建立直接链接。
keywords: XAML, UWP, 入门
ms.date: 08/20/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 3435718794cb22745e1438ef634db29076ed33c1
ms.sourcegitcommit: 6cb20dca1cb60b4f6b894b95dcc2cc3a166165ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636657"
---
# <a name="tutorial-create-data-bindings"></a>教程：创建数据绑定

假设你已设计并实现了一个外形美观的 UI，并在其中填充了占位符图像、“假文”样板文本以及尚未执行任何操作的控件。 接下来，需要将其连接到实际数据，并将其从设计原型转换为动态应用。

本教程将介绍如何用数据绑定替换样板并在 UI 和数据之间创建其他直接链接。 此外，介绍如何格式化或转换要显示的数据，并使 UI 和数据保持同步。完成本教程后，便可以改善 XAML 和 C# 代码的简洁性与结构，使其更易于维护和扩展。

你将从简化版本的 PhotoLab 示例开始。 此简易版本包括完整的数据层以及基本 XAML 页面布局，并省去了许多功能以使代码更易于在上下文中浏览。 本教程并不生成完整的应用，因此，请务必查看最终版本以了解诸如自定义动画和自适应布局等功能。 可以在 [Windows-appsample-photo-lab](https://github.com/Microsoft/Windows-appsample-photo-lab) 存储库的根文件夹中查找最终版本。

PhotoLab 示例应用有两个页面。 主页显示照片库视图，以及关于每个图像文件的一些信息。

![Photo lab 主页的屏幕截图。](../design/basics/images/xaml-basics/mainpage.png)

详细信息页  显示选定的单张照片。 利用浮出编辑菜单，可以修改、重命名和保存照片。

![Photo lab 详细信息页的屏幕截图。](../design/basics/images/xaml-basics/detailpage.png)

## <a name="prerequisites"></a>必备条件

+ Visual Studio 2019：[下载 Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)（社区版本是免费的。）
+ Windows 10 SDK（10.0.17763.0 或更高版本）：[下载最新的 Windows SDK（免费）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
+ Windows 10 版本 1809 或更高版本

## <a name="part-0-get-the-starter-code-from-github"></a>第 0 部分：从 GitHub 获取起始代码

在本教程中，我们将从一个简化版的 PhotoLab 示例开始。

1. 转到 GitHub 页查看示例：[https://github.com/Microsoft/Windows-appsample-photo-lab](https://github.com/Microsoft/Windows-appsample-photo-lab)。
2. 接下来，需要克隆或下载示例。 选择“克隆或下载”  按钮。 一个子菜单将出现。
    ![PhotoLab 示例在 GitHub 页上的“克隆或下载”菜单](../design/basics/images/xaml-basics/clone-repo.png)

    **如果你不熟悉 GitHub:**

    a. 选择“下载 ZIP”  ，在本地保存文件。 这将下载一个包含你需要的所有项目文件的 .zip 文件。

    b. 将该文件解压缩。 使用文件资源管理器浏览到刚下载的 .zip 文件，右键单击它，然后选择“全部解压缩”。

    c. 浏览到示例的本地副本，转到 `Windows-appsample-photo-lab-master\xaml-basics-starting-points\data-binding` 目录。

    **如果你熟悉 GitHub：**

    a. 在本地克隆 repo 的主分支。

    b. 浏览到 `Windows-appsample-photo-lab\xaml-basics-starting-points\data-binding` 目录。

3. 在 Visual Studio 中双击 `Photolab.sln` 打开解决方案。

## <a name="part-1-replace-the-placeholders"></a>第 1 部分：替换占位符

此处，在数据模板 XAML 中创建一次性绑定，以显示实际图像和图像元数据，而不是占位符内容。

一次性绑定适用于不变的只读数据，这意味着它们具有高性能并且易于创建，可让你在 `GridView` 和 `ListView` 控件中显示大数据集。

### <a name="replace-the-placeholders-with-one-time-bindings"></a>用一次性绑定替换占位符

1. 打开 `xaml-basics-starting-points\data-binding` 文件夹，然后在 Visual Studio 中启动 `PhotoLab.sln` 文件。

2. 请确保将“解决方案平台”设置为 x86 或 x64 而不是 ARM，然后运行该应用。 这将在添加绑定之前显示具有 UI 占位符的应用的状态。

    ![运行具有占位符图像和文本的应用](../design/basics/images/xaml-basics/gallery-with-placeholder-templates.png)

3. 打开 MainPage.xaml 并搜索名为 ImageGridView_DefaultItemTemplate 的 `DataTemplate`。 你将更新此模板以使用数据绑定。

    **之前：**

    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate">
    ```

    `ImageGridView` 使用 `x:Key` 值选择此模板以显示数据对象。

4. 将 `x:DataType` 值添加到模板中。

    **之后：**

    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate"
                  x:DataType="local:ImageFileInfo">
    ```

    `x:DataType` 指示模板类型。 在本例中，它是 `ImageFileInfo` 类的模板（其中 `local:` 表示本地命名空间，如文件顶部附近的 xmlns 声明所定义）。

    在数据模板中使用 `x:Bind` 表达式时 `x:DataType` 是必需的，如下所述。

5. 在 `DataTemplate` 中，找到名为 `ItemImage` 的 `Image` 元素并替换其 `Source` 值，如下所示。

    **之前：**

    ```xaml
    <Image x:Name="ItemImage"
           Source="/Assets/StoreLogo.png"
           Stretch="Uniform" />
    ```

    **之后：**

    ```xaml
    <Image x:Name="ItemImage"
           Source="{x:Bind ImageSource}"
           Stretch="Uniform" />
    ```

    `x:Name` 标识 XAML 元素，以便你可以在 XAML 中的其他位置和代码隐藏中引用它。

    `x:Bind` 表达式通过从 data-object 属性获取值来为 UI 属性提供值。 在模板中，指示的属性是 `x:DataType` 所设置为的任何项的属性。 因此在本例中，数据源是 `ImageFileInfo.ImageSource` 属性。

    > [!NOTE]
    > `x:Bind` 值还会告诉编辑器数据类型，因此你可以使用 IntelliSense，而不是在 `x:Bind` 表达式中键入属性名称。 对刚刚粘贴的代码试用一下：将光标放在 `x:Bind` 之后，然后按空格键查看可绑定到的属性的列表。

6. 以相同方式替换其他 UI 控件的值。 （使用 IntelliSense 来尝试执行此操作，而不是复制/粘贴！）

    **之前：**

    ```xaml
    <TextBlock Text="Placeholder" ... />
    <StackPanel ... >
        <TextBlock Text="PNG file" ... />
        <TextBlock Text="50 x 50" ... />
    </StackPanel>
    <muxc:RatingControl Value="3" ... />
    ```

    **之后：**

    ```xaml
    <TextBlock Text="{x:Bind ImageTitle}" ... />
    <StackPanel ... >
        <TextBlock Text="{x:Bind ImageFileType}" ... />
        <TextBlock Text="{x:Bind ImageDimensions}" ... />
    </StackPanel>
    <muxc:RatingControl Value="{x:Bind ImageRating}" ... />
    ```

运行应用以查看其到目前为止的外观。 没有其他占位符！ 我们已经开了个好头。

![运行具有实际图像和文本而不是占位符的应用](../design/basics/images/xaml-basics/gallery-with-populated-templates.png)

> [!Note]
> 如果你想进一步试验，请尝试向数据模板添加新的 TextBlock，并使用 x:Bind IntelliSense 技巧来查找要显示的属性。

## <a name="part-2-use-binding-to-connect-the-gallery-ui-to-the-images"></a>第 2 部分：使用绑定将库 UI 连接至图像

在此，你将在页面 XAML 中创建一次性绑定以将库视图连接到图像集合，并替换在代码隐藏部分中执行此操作的现有过程代码。 你还将创建一个“删除”按钮以查看从集合中删除图像时库视图如何变化  。 同时，你将学习如何将事件绑定到事件处理程序，以超载传统事件处理程序提供的灵活性。

到目前为止所涉及的所有绑定都在数据模板内部，并且都引用 `x:DataType` 值所指示的类的属性。 页面中的其他 XAML 情况如何？

数据模板之外的 `x:Bind` 表达式始终绑定到页面本身。 这意味着可以引用在代码隐藏中放置的或在 XAML 声明的任何内容，包括自定义属性和页面上其他 UI 控件的属性（前提是它们具有 `x:Name` 值）。

在 PhotoLab 示例中，这种绑定的一种用法是将主 `GridView` 控件直接连接到图像集合，而不是在代码隐藏中进行。 在后面，你将看到其他示例。

### <a name="bind-the-main-gridview-control-to-the-images-collection"></a>将主要 GridView 控件绑定到图像集合

1. 在 MainPage.xaml.cs 中，找到 `GetItemsAsync` 方法并删除用于设置 `ItemsSource` 的代码。

    **之前：**

    ```csharp
    ImageGridView.ItemsSource = Images;
    ```

    **之后：**

    ```csharp
    // Replaced with XAML binding:
    // ImageGridView.ItemsSource = Images;
    ```

2. 在 MainPage.xaml 中，找到名为 `ImageGridView` 的 `GridView`，然后添加 `ItemsSource` 属性。 对于值，请使用 `x:Bind` 表达式来引用在代码隐藏中实现的 `Images` 属性。

    **之前：**

    ```xaml
    <GridView x:Name="ImageGridView"
    ```

    **之后：**

    ```xaml
    <GridView x:Name="ImageGridView"
              ItemsSource="{x:Bind Images}"
    ```

    `Images` 属性的类型为 `ObservableCollection<ImageFileInfo>`，因此在 `GridView` 中显示的各个项目属于 `ImageFileInfo` 类型。 这与第 1 部分中所描述的 `x:DataType` 值匹配。

到目前为止，我们所看到的所有绑定都是一次性只读绑定，这是普通 `x:Bind` 表达式的默认行为。 数据仅在初始化时才加载，这样可以实现高性能绑定 - 特别适合支持大数据集的多个复杂视图。

甚至连刚添加的 `ItemsSource` 绑定也是与不变属性值的一次性只读绑定，但这里有一个重要的区别。 `Images` 属性的不变值是初始化一次的集合的单一特定实例，如此处所示。

```csharp
private ObservableCollection<ImageFileInfo> Images { get; }
    = new ObservableCollection<ImageFileInfo>();
```

`Images` 属性值永远不会更改，但是由于属性的类型为 `ObservableCollection<T>`，所以集合的内容可能会发生更改，并且绑定将自动注意更改并更新 UI。

为了对此进行测试，我们将暂时添加一个删除当前所选图像的按钮。 此按钮不在最终版本中，因为选择图像会将你带入到详细信息页面中。 然而，`ObservableCollection<T>` 的行为在最终的 PhotoLab 示例中仍然很重要，因为 XAML 在页面构造函数中（通过 `InitializeComponent` 方法调用）进行了初始化，但后来在 `GetItemsAsync` 方法中填充了 `Images` 集合。

### <a name="add-a-delete-button"></a>添加删除按钮

1. 在 MainPage.xaml 中，查找名为“MainCommandBar”的 `CommandBar`，并在缩放按钮之前添加新按钮。 （缩放控件还不能用。 你将在本教程的下一个部分中安装好这些控件。）

    ```xaml
    <AppBarButton Icon="Delete"
                  Label="Delete selected image"
                  Click="{x:Bind DeleteSelectedImage}" />
    ```

    如果你已熟悉 XAML，则此 `Click` 值可能看起来不正常。 在以前版本的 XAML 中，必须将其设置为具有特定事件处理程序签名的方法，通常包括事件发件程序的参数和特定于事件的参数对象。 需要事件参数时，仍然可以使用此方法，但是通过使用 `x:Bind`，也可以连接到其他方法。 例如，如果不需要事件数据，则可以连接到没有参数的方法，跟我们在这里进行的操作一样。

    <!-- TODO add doc links about event binding - and doc links in general? -->

2. 在 MainPage.xaml.cs 中添加 `DeleteSelectedImage` 方法。

    ```csharp
    private void DeleteSelectedImage() =>
        Images.Remove(ImageGridView.SelectedItem as ImageFileInfo);
    ```

    此方法直接从 `Images` 集合中删除所选的图像。

现在，运行该应用并使用该按钮删除一些图像。 如你所见，UI 会自动更新，这是因为使用了数据绑定和 `ObservableCollection<T>` 类型。

> [!Note]
> 此代码仅从正在运行的应用的 `Images` 集合中删除 `ImageFileInfo` 实例。 它不会从计算机中删除图像文件。

## <a name="part-3-set-up-the-zoom-slider"></a>第 3 部分：设置缩放滑块

在此部分中，你将创建从数据模板中的控件到模板外的缩放滑块的单向绑定。 你还将了解到，可以将数据绑定与许多控件属性配合使用，而不仅仅是与 `TextBlock.Text` 和 `Image.Source` 等最明显的属性配合使用。

### <a name="bind-the-image-data-template-to-the-zoom-slider"></a>将图像数据模板绑定到缩放滑块

+ 找到名为 `ImageGridView_DefaultItemTemplate` 的 `DataTemplate`，并替换模板顶部 `Grid` 控件的 `**Height**` 和 `Width` 值。

    **之前**

    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate"
                  x:DataType="local:ImageFileInfo">
        <Grid Height="200"
              Width="200"
              Margin="{StaticResource LargeItemMargin}">
    ```

    **之后**

    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate"
                  x:DataType="local:ImageFileInfo">
        <Grid Height="{Binding Value, ElementName=ZoomSlider}"
              Width="{Binding Value, ElementName=ZoomSlider}"
              Margin="{StaticResource LargeItemMargin}">
    ```

你是否注意到这些是 `Binding` 表达式而非 `x:Bind` 表达式？ 这是执行数据绑定的旧方法，基本上已过时。 `x:Bind` 几乎可以执行 `Binding` 能够执行的每项操作，还可以执行其他操作。 但是，在数据模板中使用 `x:Bind` 时，它将绑定到 `x:DataType` 值中声明的类型。 那么，如何将模板中的内容绑定到页面 XAML 或代码隐藏部分中的内容呢？ 必须使用旧式的 `Binding` 表达式。

`Binding` 表达式无法识别 `x:DataType` 值，但这些 `Binding` 表达式具有工作原理几乎相同的 `ElementName` 值。 它们告诉绑定引擎，“绑定值”是对页面上指定元素的 `Value` 属性的绑定（即具有该 `x:Name` 值的元素）。 如果你想要绑定到代码隐藏中的属性，则它应该类似于 ```{Binding MyCodeBehindProperty, ElementName=page}```，其中 `page` 引用 XAML 内 `Page` 元素中设置的 `x:Name` 值。

> [!NOTE]
> 默认情况下，`Binding` 表达式是单向表达式，这意味着当绑定的属性值更改时，它们将自动更新 UI。
>
> 相反，`x:Bind` 的默认表达式是一次性表达式，这意味着对绑定属性所做的任何更改都将被忽略。 这是默认设置，因为这是最高性能选项，并且大多数绑定会绑定到静态只读数据。
>
> 由此得出的经验是，如果将 `x:Bind` 与可能会更改值的属性配合使用，请务必添加 `Mode=OneWay` 或 `Mode=TwoWay`。 你将在下一个部分中看到这方面的示例。

运行该应用并使用滑块更改图像模板尺寸。 如你所见，效果非常好，无需大多的代码。

![运行应用并显示缩放滑块](../design/basics/images/xaml-basics/gallery-with-zoom-control.png)

> [!NOTE]
> 作为一项挑战，请尝试将其他 UI 属性绑定到缩放滑块的 `Value` 属性，或者绑定到添加到缩放滑块后面的其他滑块。 例如，可以将 `TitleTextBlock` 的 `FontSize` 属性绑定到默认值为“24”的新滑块。 请务必设置合理的最小值和最大值。

## <a name="part-4-improve-the-zoom-experience"></a>第 4 部分：改善缩放体验

在此部分中，将自定义的 `ItemSize` 属性添加到代码隐藏中，并创建从图像模板到新属性的单向绑定。 `ItemSize` 值将根据缩放滑块以及“适应屏幕”切换键和窗口大小等其他因素进行更新，从而实现更优化的体验。

与内置控件属性不同，即使使用单向和双向绑定，自定义属性也不会自动更新 UI。 它们非常适用于一次性绑定，但是，如果你希望属性更改实际反映在 UI 中，则需要做一些工作  。

### <a name="create-the-itemsize-property-so-that-it-updates-the-ui"></a>创建 ItemSize 属性，使其更新 UI

1. 在 MainPage.xaml.cs 中，更改 `MainPage` 类的签名，使其实现 `INotifyPropertyChanged` 接口。

    **之前：**

    ```csharp
    public sealed partial class MainPage : Page
    ```

    **之后：**

    ```csharp
    public sealed partial class MainPage : Page, INotifyPropertyChanged
    ```

    这将通知绑定系统 `MainPage` 有一个 `PropertyChanged` 事件（已在后面部分中添加），绑定可以侦听该事件以更新 UI。

2. 将 `PropertyChanged` 事件添加到 `MainPage` 类。

    ```csharp
    public event PropertyChangedEventHandler PropertyChanged;
    ```

    此事件提供 `INotifyPropertyChanged` 接口所需的完整实现。 但是，为了使其产生作用，必须在自定义属性中显式引发事件。

3. 添加 `ItemSize` 属性并在其资源库中引发 `PropertyChanged` 事件。

    ```csharp
    public double ItemSize
    {
        get => _itemSize;
        set
        {
            if (_itemSize != value)
            {
                _itemSize = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(ItemSize)));
            }
        }
    }
    private double _itemSize;
    ```

    `ItemSize` 属性会公开专用 `_itemSize` 字段的值。 如果使用这样的支持字段，则可以使属性在引发可能不必要的 `PropertyChanged` 事件之前检查新值是否与旧值相同。

    事件本身由 `Invoke` 方法引发。 问号检查 `PropertyChanged` 事件是否为 Null - 即，是否还添加了任何事件处理程序。 每个单向或双向绑定都会在幕后添加事件处理程序，但是，如果没有绑定进行侦听，那么此时不会再发生任何情况。 然而，如果 `PropertyChanged` 不是 Null，则调用 `Invoke`，并引用事件源（由 `this` 关键字所表示的页面本身）和一个表示属性名称的 event-args 对象。 使用此信息，系统将向 `ItemSize` 属性的任何单向或双向绑定通知任何更改，以便它们可以更新绑定的 UI。

4. 在 MainPage.xaml 中，找到名为 `ImageGridView_DefaultItemTemplate` 的 `DataTemplate`，并替换模板顶部 `Grid` 控件的 `Height` 和 `Width` 值。 （如果你在本教程的上一部分中进行了控件到控件的绑定，则只需进行以下更改：将 `Value` 替换为 `ItemSize`，将 `ZoomSlider` 替换为 `page`。 请务必对 `Height` 和 `Width` 都执行此操作！）

    **之前**

    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate"
                  x:DataType="local:ImageFileInfo">
        <Grid Height="{Binding Value, ElementName=ZoomSlider}"
            Width="{Binding Value, ElementName=ZoomSlider}"
            Margin="{StaticResource LargeItemMargin}">
    ```

    **之后**

    ```xaml
    <DataTemplate x:Key="ImageGridView_DefaultItemTemplate"
                  x:DataType="local:ImageFileInfo">
        <Grid Height="{Binding ItemSize, ElementName=page}"
              Width="{Binding ItemSize, ElementName=page}"
              Margin="{StaticResource LargeItemMargin}">
    ```

现在，UI 可以响应 `ItemSize` 更改，你需要实际进行一些更改。 如前所述，`ItemSize` 值是利用各 UI 控件的当前状态计算的，但是每当这些控件改变状态时，必须执行此计算。 为此，将使用事件绑定，以便某些 UI 更改将调用一个更新 `ItemSize` 的帮助程序方法。

### <a name="update-the-itemsize-property-value"></a>更新 ItemSize 属性值

1. 向 MainPage.xaml.cs 添加 `DetermineItemSize` 方法。

    ```csharp
    private void DetermineItemSize()
    {
        if (FitScreenToggle != null
            && FitScreenToggle.IsOn == true
            && ImageGridView != null
            && ZoomSlider != null)
        {
            // The 'margins' value represents the total of the margins around the
            // image in the grid item. 8 from the ItemTemplate root grid + 8 from
            // the ItemContainerStyle * (Right + Left). If those values change,
            // this value needs to be updated to match.
            int margins = (int)this.Resources["LargeItemMarginValue"] * 4;
            double gridWidth = ImageGridView.ActualWidth -
                (int)this.Resources["DefaultWindowSidePaddingValue"];
            double ItemWidth = ZoomSlider.Value + margins;
            // We need at least 1 column.
            int columns = (int)Math.Max(gridWidth / ItemWidth, 1);

            // Adjust the available grid width to account for margins around each item.
            double adjustedGridWidth = gridWidth - (columns * margins);

            ItemSize = (adjustedGridWidth / columns);
        }
        else
        {
            ItemSize = ZoomSlider.Value;
        }
    }
    ```

2. 在 MainPage.xaml 中，导航到文件的顶部，并将 `SizeChanged` 事件绑定添加到 `Page` 元素中。

    **之前：**

    ```xaml
    <Page x:Name="page"
    ```

    **之后：**

    ```xaml
    <Page x:Name="page"
          SizeChanged="{x:Bind DetermineItemSize}"
    ```

3. 找到名为 `ZoomSlider` 的 `Slider`（在 `Page.Resources` 节）并添加 `ValueChanged` 事件绑定。

    **之前：**

    ```xaml
    <Slider x:Name="ZoomSlider"
    ```

    **之后：**

    ```xaml
    <Slider x:Name="ZoomSlider"
            ValueChanged="{x:Bind DetermineItemSize}"
    ```

4. 找到名为 `FitScreenToggle` 的 `ToggleSwitch` 并添加 `Toggled` 事件绑定。

    **之前：**

    ```xaml
    <ToggleSwitch x:Name="FitScreenToggle"
    ```

    **之后：**

    ```xaml
    <ToggleSwitch x:Name="FitScreenToggle"
                  Toggled="{x:Bind DetermineItemSize}"
    ```

运行该应用并使用缩放滑块和“适应屏幕”切换键更改图像模板尺寸  。 如你所见，最新更改可实现更优化的缩放/调整大小体验，同时可使代码保持有条不紊。

![运行应用并启用适应屏幕](../design/basics/images/xaml-basics/gallery-with-fit-to-screen.png)

> [!NOTE]
> 作为一项挑战，尝试在 `ZoomSlider` 后添加 `TextBlock`，并将 `Text` 属性绑定到 `ItemSize` 属性。 因为它不在数据模板中，所以可以像在以前的 `ItemSize` 绑定中那样使用 `x:Bind` 来替代 `Binding`。

## <a name="part-5-enable-user-edits"></a>第 5 部分：启用用户编辑

在此，你将创建双向绑定，以使用户能够更新值，包括图像标题、分级，以及各种视觉效果。

为达到此目的，你将更新现有的 `DetailPage`，它会提供单一图像查看器、缩放控件和 UI 编辑功能。

但是，首先需要附加 `DetailPage`，以便用户单击库视图中的图像时，该应用可以导航到它。

### <a name="attach-the-detailpage"></a>附加 DetailPage

1. 在 MainPage.xaml 中，查找名为 `ImageGridView` 的 `GridView`。 若要使项可单击，请将 `IsItemClickEnabled` 设置为 `True`，并添加一个 `ItemClick` 事件处理程序。

    > [!TIP]
    > 如果键入下面的更改，而不是复制/粘贴，则会看到一个 IntelliSense 弹出窗口，其中显示“\<New Event Handler\>”。 如果按下 Tab 键，它将用默认方法处理程序名称填充此值，并自动去掉下一步中所显示的方法。 以后，可以按 F12 在代码隐藏部分中导航到此方法。

    **之前：**

    ```xaml
    <GridView x:Name="ImageGridView">
    ```

    **之后：**

    ```xaml
    <GridView x:Name="ImageGridView"
              IsItemClickEnabled="True"
              ItemClick="ImageGridView_ItemClick">
    ```

    > [!NOTE]
    > 在此，我们将使用常规事件处理程序，而不是 x:Bind 表达式。 这是因为我们需要查看事件数据，如下所示。

2. 在 MainPage.xaml.cs 中，添加事件处理程序（如果使用了最后一步中的提示，则填充该程序）。

    ```csharp
    private void ImageGridView_ItemClick(object sender, ItemClickEventArgs e)
    {
        this.Frame.Navigate(typeof(DetailPage), e.ClickedItem);
    }
    ```

    此方法只导航到详细信息页面，并传递单击的项，此项是“DetailPage.OnNavigatedTo”为初始化页面而使用的 `ImageFileInfo` 对象。 不必在本教程中实现该方法，但可以看一看它的作用。

3. （可选）删除或注释掉以前播放点中添加的适用于当前所选图像的任何控件。 保留它们不会有任何损害，但现在如果不导航到详细信息页面，则很难选择图像。

现在已连接了两个页面，请运行该应用并四处看看。 一切都很正常，但是编辑窗格上的控件除外，当你尝试更改值时，它们不会响应。

如你所见，标题文本框确实会显示标题并允许键入更改。 必须将焦点更改为另一个控件才能提交更改，但屏幕左上角中的标题尚未更新。

我们已使用第 1 部分中介绍的普通 `x:Bind` 表达式绑定了所有控件。 回忆一下就能知道，这意味着它们都是一次绑定，这就解释了为什么没有注册对值所做的更改。 若要解决此问题，我们只需将其转变为双向绑定。

### <a name="make-the-editing-controls-interactive"></a>使编辑控件具有交互性

1. 在 DetailPage.xaml 中，查找名为“TitleTextBlock”的 `TextBlock` 以及它后面的“RatingControl”控件，并更新其 `x:Bind` 表达式以包括“Mode=TwoWay”  。

    **之前：**

    ```xaml
    <TextBlock x:Name="TitleTextBlock"
               Text="{x:Bind item.ImageTitle}"
               ... >
    <muxc:RatingControl Value="{x:Bind item.ImageRating}"
                            ... >
    ```

    **之后：**

    ```xaml
    <TextBlock x:Name="TitleTextBlock"
               Text="{x:Bind item.ImageTitle, Mode=TwoWay}"
               ... >
    <muxc:RatingControl Value="{x:Bind item.ImageRating, Mode=TwoWay}"
                            ... >
    ```

2. 对分级控件后面的所有效果滑块执行同样的操作。

    ```xaml
    <Slider Header="Exposure"    ... Value="{x:Bind item.Exposure, Mode=TwoWay}" ...
    <Slider Header="Temperature" ... Value="{x:Bind item.Temperature, Mode=TwoWay}" ...
    <Slider Header="Tint"        ... Value="{x:Bind item.Tint, Mode=TwoWay}" ...
    <Slider Header="Contrast"    ... Value="{x:Bind item.Contrast, Mode=TwoWay}" ...
    <Slider Header="Saturation"  ... Value="{x:Bind item.Saturation, Mode=TwoWay}" ...
    <Slider Header="Blur"        ... Value="{x:Bind item.Blur, Mode=TwoWay}" ...
    ```

如你所料，双向模式意味着每当任何一方有变化时，数据会进行双向传递。

像前面介绍的单向绑定一样，每当绑定的属性发生更改时，这些双向绑定将立即更新 UI，这要归功于 `ImageFileInfo` 类中的 `INotifyPropertyChanged` 实现。 然而，对于双向绑定，每当用户与控件交互时，值也将从 UI 移到绑定的属性。 XAML 这边不需要更多的内容。

运行该应用并试用编辑控件。 如你所见，进行更改时，会立即影响图像值，导航回主页面时，这些更改会持续存在。

## <a name="part-6-format-values-through-function-binding"></a>第 6 部分：通过函数绑定来设置值的格式

最后一个问题仍然存在。 移动效果滑块时，它们旁边的标签仍不会改变。

![具有默认标签值的效果滑块](../design/basics/images/xaml-basics/effect-sliders-before-label-fix.png)

本教程中的最后一部分是添加绑定以为显示设置滑块值的格式。

### <a name="bind-the-effect-slider-labels-and-format-the-values-for-display"></a>绑定效果滑块标签并为显示设置值的格式

1. 在 `Exposure` 滑块后面查找 `TextBlock`，并将 `Text` 值替换为此处所显示的绑定表达式。

    **之前：**

    ```xaml
    <Slider Header="Exposure" ... />
    <TextBlock ... Text="0.00" />
    ```

    **之后：**

    ```xaml
    <Slider Header="Exposure" ... />
    <TextBlock ... Text="{x:Bind item.Exposure.ToString('N', culture), Mode=OneWay}" />
    ```

    这称为函数绑定，因为所绑定到的是方法的返回值。 如果你在使用数据模板，则必须可通过页面的代码隐藏或 `x:DataType` 类型来访问该方法。 在本例中，方法是熟悉的 .NET `ToString` 方法，该方法可先通过页面的项目属性，再通过项目的 `Exposure` 属性进行访问。 （这说明了如何绑定到深入嵌套在连接链中的方法和属性。）

    函数绑定是为显示设置值格式的理想方法，因为可以将其他绑定源作为方法参数进行传递，并且绑定表达式将侦听对这些值所做的更改，正如使用单向模式所预料的那样。 在此示例中，“culture”参数是对代码隐藏中实现的不变字段的引用，但它完全可以作为一个引发 `PropertyChanged` 事件的属性。 在这种情况下，对属性值所做的任何更改都将导致 `x:Bind` 表达式调用具有新值的 `ToString`，然后使用结果来更新 UI。

2. 对标记其他效果滑块的 `TextBlock` 进行同样的操作。

    ```xaml
    <Slider Header="Temperature" ... />
    <TextBlock ... Text="{x:Bind item.Temperature.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Tint" ... />
    <TextBlock ... Text="{x:Bind item.Tint.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Contrast" ... />
    <TextBlock ... Text="{x:Bind item.Contrast.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Saturation" ... />
    <TextBlock ... Text="{x:Bind item.Saturation.ToString('N', culture), Mode=OneWay}" />

    <Slider Header="Blur" ... />
    <TextBlock ... Text="{x:Bind item.Blur.ToString('N', culture), Mode=OneWay}" />
    ```

现在，运行应用时一切正常，包括滑块标签。

![具有有效标签的效果滑块](../design/basics/images/xaml-basics/effect-sliders-after-label-fix.png)

## <a name="conclusion"></a>结论

本教程让你亲身感受了数据绑定，并介绍了一些可用的功能。 在我们结束之前，请注意一点：并非所有内容都可以绑定，有时尝试连接到的值与尝试绑定的属性不兼容。 虽然绑定的灵活性很大，但并非适用于各种情况。

例如，当控件没有要绑定到的合适属性时，无法通过绑定来解决问题，这与详细信息页面缩放功能的情况一样。 此缩放滑块需要与显示图像的 `ScrollViewer` 交互，但 `ScrollViewer` 只能通过其 `ChangeView` 方法进行更新。 在这种情况下，我们使用常规的事件处理程序来保持 `ScrollViewer` 和缩放滑块的同步；有关详细信息，请参阅 `DetailPage` 中的 `ZoomSlider_ValueChanged` 和 `MainImageScroll_ViewChanged` 方法。

尽管如此，绑定仍然是一种功能强大且灵活的方法，可用于简化代码并将 UI 逻辑与数据逻辑分开。 这样，便可以更加轻松地在此分界的任何一边进行调整，同时可减小在另一边引入错误的风险。

使用 `ImageFileInfo.ImageTitle` 属性就是 UI 和数据分离的一个示例。 此属性（和 `ImageRating` 属性）略微不同于在第 4 部分中创建的 `ItemSize` 属性，因为值存储在文件元数据（通过 `ImageProperties` 类型来公开）中，而不是字段中。 此外，如果文件元数据中没有标题，则 `ImageTitle` 会返回 `ImageName` 值（设置为文件名）。

```csharp
public string ImageTitle
{
    get => String.IsNullOrEmpty(ImageProperties.Title) ? ImageName : ImageProperties.Title;
    set
    {
        if (ImageProperties.Title != value)
        {
            ImageProperties.Title = value;
            var ignoreResult = ImageProperties.SavePropertiesAsync();
            OnPropertyChanged();
        }
    }
}
```

如你所见，资源库会更新 `ImageProperties.Title` 属性，然后调用 `SavePropertiesAsync` 以将新值写入文件。 （这是一种异步方法，但我们不能在属性中使用 `await` 关键字，你也不希望这么做，因为属性 getter 和资源库应该会立即完成操作。 因此，你将改为调用方法并忽略它所返回的 `Task` 对象。）

## <a name="going-further"></a>深入探索

现在，你已完成本实验，拥有了足够的绑定知识，可以自行解决问题。

你可能已经注意到，如果在详细信息页面上更改缩放级别，则它将在向后导航时自动重置，然后再次选择相同的图像。 你能搞清楚如何分别为每个图像保留和恢复缩放级别吗？ 祝好运！

你应该具有本教程中需要的所有信息，但是如果需要更多指导，则只需单击一下，即可获得数据绑定文档。 从这里开始：

+ [{x:Bind} 标记扩展](../xaml-platform/x-bind-markup-extension.md)
+ [深入了解数据绑定](./data-binding-in-depth.md)
