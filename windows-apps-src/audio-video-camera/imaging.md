---
ms.assetid: 3FD2AA71-EF67-47B2-9332-3FFA5D3703EA
description: 本文介绍了如何使用 BitmapDecoder 和 BitmapEncoder 加载和保存图像文件，以及如何使用 SoftwareBitmap 对象表示位图图像。
title: 创建、编辑和保存位图图像
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d503e6849a1a7b17678f856649f6b8194dc16722
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89157441"
---
# <a name="create-edit-and-save-bitmap-images"></a>创建、编辑和保存位图图像



本文说明如何使用 [**BitmapDecoder**](/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) 和 [**BitmapEncoder**](/uwp/api/Windows.Graphics.Imaging.BitmapEncoder) 加载和保存映像文件，以及如何使用 [**SoftwareBitmap**](/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) 对象表示位图图像。

**SoftwareBitmap** 类是一个通用 API，可从多个源（包括图像文件、[**WriteableBitmap**](/uwp/api/Windows.UI.Xaml.Media.Imaging.WriteableBitmap) 对象、Direct3D 图面和代码）中进行创建。 **SoftwareBitmap** 允许你在不同的像素格式和 alpha 模式之间轻松转换，并允许对像素数据的低级别访问。 此外，**SoftwareBitmap** 是可供 Windows 的多个功能使用的通用接口，包括：

-   [**CapturedFrame**](/uwp/api/Windows.Media.Capture.CapturedFrame) 允许你获取由相机捕获的帧以作为 **SoftwareBitmap**。

-   [**VideoFrame**](/uwp/api/Windows.Media.VideoFrame) 允许你获取 **VideoFrame** 的 **SoftwareBitmap** 表示形式。

-   [**FaceDetector**](/uwp/api/Windows.Media.FaceAnalysis.FaceDetector) 允许你在 **SoftwareBitmap** 中检测人脸。

本文中的示例代码使用以下命名空间中的 API。

[!code-cs[Namespaces](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetNamespaces)]

## <a name="create-a-softwarebitmap-from-an-image-file-with-bitmapdecoder"></a>使用 BitmapDecoder 从图像文件中创建 SoftwareBitmap

若要从文件中创建 [**SoftwareBitmap**](/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap)，请获取包含图像数据的 [**StorageFile**](/uwp/api/Windows.Storage.StorageFile) 的实例。 此示例使用 [**FileOpenPicker**](/uwp/api/Windows.Storage.Pickers.FileOpenPicker) 以允许用户选择图像文件。

[!code-cs[PickInputFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetPickInputFile)]

调用 **StorageFile** 对象的 [**OpenAsync**](/uwp/api/windows.storage.istoragefile.openasync) 方法，以获取包含图像数据的随机访问流。 调用静态方法 [**BitmapDecoder.CreateAsync**](/uwp/api/windows.graphics.imaging.bitmapdecoder.createasync) 以获取指定流的 [**BitmapDecoder**](/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) 类的实例。 调用 [**GetSoftwareBitmapAsync**](/uwp/api/windows.graphics.imaging.bitmapdecoder.getsoftwarebitmapasync) 以获取包含图像的 [**SoftwareBitmap**](/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) 对象。

[!code-cs[CreateSoftwareBitmapFromFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateSoftwareBitmapFromFile)]

## <a name="save-a-softwarebitmap-to-a-file-with-bitmapencoder"></a>将 SoftwareBitmap 保存到具有 BitmapEncoder 的文件

若要将 **SoftwareBitmap** 保存到某个文件，请获取该图像将保存到的 **StorageFile** 的实例。 此示例使用 [**FileSavePicker**](/uwp/api/Windows.Storage.Pickers.FileSavePicker) 以允许用户选择输出文件。

[!code-cs[PickOutputFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetPickOutputFile)]

调用 **StorageFile** 对象的 [**OpenAsync**](/uwp/api/windows.storage.istoragefile.openasync) 方法，以获取该图像将写入的随机访问流。 调用静态方法 [**BitmapEncoder.CreateAsync**](/uwp/api/windows.graphics.imaging.bitmapencoder.createasync) 以获取指定流的 [**BitmapEncoder**](/uwp/api/Windows.Graphics.Imaging.BitmapEncoder) 类的实例。 **CreateAsync** 的第一个参数是表示编解码器的 GUID，应该使用该编解码器对图像进行编码。 **BitmapEncoder** 类公开一个包含受编码器支持的每个编解码器的 ID（例如 [**JpegEncoderId**](/uwp/api/windows.graphics.imaging.bitmapencoder.jpegencoderid)）的属性。

使用 [**SetSoftwareBitmap**](/uwp/api/windows.graphics.imaging.bitmapencoder.setsoftwarebitmap) 方法设置将进行编码的图像。 你可以设置 [**BitmapTransform**](/uwp/api/Windows.Graphics.Imaging.BitmapTransform) 属性的值，以在对图像进行编码时将基本转换应用到该图像。 [**IsThumbnailGenerated**](/uwp/api/windows.graphics.imaging.bitmapencoder.isthumbnailgenerated) 属性可确定缩略图是否由编码器生成。 请注意，并非所有文件格式都支持缩略图，因此如果你使用此功能，你应该会收到由于缩略图不受支持而引发的不受支持的操作错误。

调用 [**FlushAsync**](/uwp/api/windows.graphics.imaging.bitmapencoder.flushasync) 以使编码器将图像数据写入指定的文件。

[!code-cs[SaveSoftwareBitmapToFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSaveSoftwareBitmapToFile)]

当你通过创建新的 [**BitmapPropertySet**](/uwp/api/Windows.Graphics.Imaging.BitmapPropertySet) 对象，并使用一个或多个表示编码器设置的 [**BitmapTypedValue**](/uwp/api/Windows.Graphics.Imaging.BitmapTypedValue) 对象填充它来创建 **BitmapEncoder** 时，你可以指定其他编码选项。 有关受支持的编码器选项的列表，请参阅 [BitmapEncoder 选项参考](bitmapencoder-options-reference.md)。

[!code-cs[UseEncodingOptions](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetUseEncodingOptions)]

## <a name="use-softwarebitmap-with-a-xaml-image-control"></a>使用带有 XAML 图像控件的 SoftwareBitmap

若要使用 [**Image**](/uwp/api/Windows.UI.Xaml.Controls.Image) 控件在 XAML 页面内显示图像，首先在 XAML 页面中定义 **Image** 控件。

[!code-xml[ImageControl](./code/ImagingWin10/cs/MainPage.xaml#SnippetImageControl)]

当前，**Image** 控件仅支持使用 BGRA8 编码和预乘 alpha 或不带 alpha 的通道的图像。 在尝试显示某个图像前，进行测试以确保它具有正确的格式，如果没有，则使用 **SoftwareBitmap** 静态 [**Convert**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert) 方法将该图像转换为受支持的格式。

创建新的 [**SoftwareBitmapSource**](/uwp/api/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource) 对象。 通过调用 [**SetBitmapAsync**](/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource.setbitmapasync) 设置源对象的内容，从而传入 **SoftwareBitmap**。 然后，可以将 **Image** 控件的 [**Source**](/uwp/api/windows.ui.xaml.controls.image.source) 属性设置为新创建的 **SoftwareBitmapSource**。

[!code-cs[SoftwareBitmapToWriteableBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmapToWriteableBitmap)]

还可以使用 **SoftwareBitmapSource** 将 **SoftwareBitmap** 设置为 [**ImageBrush**](/uwp/api/Windows.UI.Xaml.Media.ImageBrush) 的 [**ImageSource**](/uwp/api/windows.ui.xaml.media.imagebrush.imagesource)。

## <a name="create-a-softwarebitmap-from-a-writeablebitmap"></a>从 WriteableBitmap 创建 SoftwareBitmap

你可以通过调用 [**SoftwareBitmap.CreateCopyFromBuffer**](/uwp/api/windows.graphics.imaging.softwarebitmap.createcopyfrombuffer) 并提供 **WriteableBitmap** 的 **PixelBuffer** 属性来设置像素数据，从而从现有 **WriteableBitmap** 创建 **SoftwareBitmap**。 第二个参数允许你请求新创建的 **WriteableBitmap** 的像素格式。 可以使用 **WriteableBitmap** 的 [**PixelWidth**](/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.pixelwidth) 和 [**PixelHeight**](/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.pixelheight) 属性指定新图像的尺寸。

[!code-cs[WriteableBitmapToSoftwareBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetWriteableBitmapToSoftwareBitmap)]

## <a name="create-or-edit-a-softwarebitmap-programmatically"></a>以编程方式创建或编辑 SoftwareBitmap

到目前为止，本主题已解决处理图像文件的问题。 你还可以在代码中以编程方式创建一个新的 **SoftwareBitmap**，并使用相同技术访问和修改 **SoftwareBitmap** 的像素数据。

**SoftwareBitmap** 使用 COM 互操作来公开包含像素数据的原始缓冲区。

若要使用 COM 互操作，必须在项目中包含对 **System.Runtime.InteropServices** 命名空间的引用。

[!code-cs[InteropNamespace](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetInteropNamespace)]

通过在命名空间内添加以下代码，初始化 [**IMemoryBufferByteAccess**](/previous-versions/mt297505(v=vs.85)) COM 接口。

[!code-cs[COMImport](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCOMImport)]

使用所需的像素格式和大小创建一个新的 **SoftwareBitmap**。 或者，使用要编辑其像素数据的现有 **SoftwareBitmap**。 调用 [**SoftwareBitmap.LockBuffer**](/uwp/api/windows.graphics.imaging.softwarebitmap.lockbuffer) 以获取表示像素数据缓冲区的 [**BitmapBuffer**](/uwp/api/Windows.Graphics.Imaging.BitmapBuffer) 类的实例。 将 **BitmapBuffer** 转换为 **IMemoryBufferByteAccess** COM 接口，然后调用 [**IMemoryBufferByteAccess.GetBuffer**](/windows/desktop/WinRT/imemorybufferbyteaccess-getbuffer) 以使用数据填充字节数组。 使用 [**BitmapBuffer.GetPlaneDescription**](/uwp/api/windows.graphics.imaging.bitmapbuffer.getplanedescription) 方法获取 [**BitmapPlaneDescription**](/uwp/api/Windows.Graphics.Imaging.BitmapPlaneDescription) 对象，该对象将有助于针对每个像素将偏移计算到缓冲区中。

[!code-cs[CreateNewSoftwareBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateNewSoftwareBitmap)]

此方法访问以 Windows 运行时类型为基础的原始缓冲区，因此必须使用 **unsafe** 关键字进行声明。 你还必须使用 Microsoft Visual Studio 配置你的项目，以允许通过以下操作编译不安全的代码：打开项目的 **“属性”** 页面、单击 **“生成”** 属性页，然后选中 **“允许不安全代码”** 复选框。

## <a name="create-a-softwarebitmap-from-a-direct3d-surface"></a>从 Direct3D 图面创建 SoftwareBitmap

若要从 Direct3D 图面创建 **SoftwareBitmap** 对象，必须在项目中包含 [**Windows.Graphics.DirectX.Direct3D11**](/uwp/api/Windows.Graphics.DirectX.Direct3D11) 命名空间。

[!code-cs[Direct3DNamespace](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetDirect3DNamespace)]

调用 [**CreateCopyFromSurfaceAsync**](/uwp/api/windows.graphics.imaging.softwarebitmap.createcopyfromsurfaceasync) 以从图面创建一个新的 **SoftwareBitmap**。 顾名思义，新的 **SoftwareBitmap** 具有图像数据的单独副本。 修改 **SoftwareBitmap** 将不会对 Direct3D 图面产生任何影响。

[!code-cs[CreateSoftwareBitmapFromSurface](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateSoftwareBitmapFromSurface)]

## <a name="convert-a-softwarebitmap-to-a-different-pixel-format"></a>将 SoftwareBitmap 转换为不同的像素格式

**SoftwareBitmap** 类提供静态方法 [**Convert**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert)，该方法允许你轻松创建一个使用从现有 **SoftwareBitmap** 指定的像素格式和 alpha 模式的新 **SoftwareBitmap**。 请注意，新创建的位图具有图像数据的单独副本。 修改新位图将不会影响源位图。

[!code-cs[Convert](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetConvert)]

## <a name="transcode-an-image-file"></a>转换图像文件的代码

你可以将图像文件的代码直接从 [**BitmapDecoder**](/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) 转换为 [**BitmapEncoder**](/uwp/api/Windows.Graphics.Imaging.BitmapEncoder)。 从要转换代码的文件创建 [**IRandomAccessStream**](/uwp/api/Windows.Storage.Streams.IRandomAccessStream)。 从输入流创建一个新的 **BitmapDecoder**。 为编码器创建一个新的 [**InMemoryRandomAccessStream**](/uwp/api/Windows.Storage.Streams.InMemoryRandomAccessStream) 以写入并调用 [**BitmapEncoder.CreateForTranscodingAsync**](/uwp/api/windows.graphics.imaging.bitmapencoder.createfortranscodingasync)，从而传入内存中的流和解码器对象。 转换代码时不支持编码选项；相反，你应使用 [**CreateAsync**](/uwp/api/windows.graphics.imaging.bitmapencoder.createasync)。 输入图像文件中未在编码器上专门设置的任何属性都将写入到未更改的输出文件中。 调用 [**FlushAsync**](/uwp/api/windows.graphics.imaging.bitmapencoder.flushasync) 以使编码器对内存中的流进行编码。 最后，在开始处查找文件流和内存中的流，并调用 [**CopyAsync**](/uwp/api/windows.storage.streams.randomaccessstream.copyasync) 以将内存中的流写出到文件流。

[!code-cs[TranscodeImageFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetTranscodeImageFile)]

## <a name="related-topics"></a>相关主题

* [BitmapEncoder 选项参考](bitmapencoder-options-reference.md)
* [图像元数据](image-metadata.md)
 

 