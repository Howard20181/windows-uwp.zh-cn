---
Description: 本文介绍如何创建可实现 IBasicVideoEffect 接口的 Windows 运行时组件，以允许你为视频流创建自定义效果。
MS-HAID: dev\_audio\_vid\_camera.custom\_video\_effects
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: 自定义视频效果
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 40a6bd32-a756-400f-ba34-2c5f507262c0
ms.localizationpriority: medium
ms.openlocfilehash: 1be4bf71d99bd6560ce4ed753b55dacdfcceb868
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257195"
---
# <a name="custom-video-effects"></a>自定义视频效果




本文介绍如何创建可实现 [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect) 接口的 Windows 运行时组件，用于为视频流创建自定义效果。 自定义效果可以与若干不同的 Windows 运行时 API 一起使用，包括提供设备相机的访问权限的 [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) 和允许你从媒体剪辑创建复杂合成的 [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition)。

## <a name="add-a-custom-effect-to-your-app"></a>将自定义效果添加到应用


自定义视频效果在实现 [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect) 接口的类中定义。 此类不能直接包含在应用的项目中。 必须改用 Windows 运行时组件来托管视频效果类。

**添加视频效果的 Windows 运行时组件**

1.  在 Microsoft Visual Studio 中，打开解决方案后，转到**文件**菜单并依次选择**添加-&gt;新建项目**。
2.  选择 **Windows 运行时组件(通用 Windows)** 项目类型。
3.  对于此示例，将项目命名为*VideoEffectComponent*。 此名称稍后将在代码中引用。
4.  单击**确定**。
5.  项目模板将创建一个名为 Class1.cs 的类。 在**解决方案资源管理器**中，右键单击 Class1.cs 的图标并选择**重命名**。
6.  将文件重命名为*ExampleVideoEffect.cs*。 Visual Studio 将显示一条提示，询问你是否想要更新对新名称的所有引用。 单击**是**。
7.  打开**ExampleVideoEffect.cs**，并更新类定义以实现 [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect) 接口。

[!code-cs[ImplementIBasicVideoEffect](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetImplementIBasicVideoEffect)]


你需要在效果类文件中包含以下命名空间，以便访问本文中示例所使用的所有类型。

[!code-cs[EffectUsing](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetEffectUsing)]


## <a name="implement-the-ibasicvideoeffect-interface-using-software-processing"></a>使用软件处理实现 IBasicVideoEffect 接口


你的视频效果必须实现 [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect) 接口的所有方法和属性。 本部分将引导你完成使用软件处理简单实现该接口的过程。

### <a name="close-method"></a>Close 方法

当效果关闭时，系统将对你的类调用 [**Close**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.close) 方法。 你应当使用此方法来处理你创建的任何资源。 该方法的参数是 [**MediaEffectClosedReason**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaEffectClosedReason)，该参数使你知道效果是否正常关闭、是否发生错误或者效果是否不支持所需的编码格式。

[!code-cs[Close](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetClose)]


### <a name="discardqueuedframes-method"></a>DiscardQueuedFrames 方法

当效果应重置时，将调用 [**DiscardQueuedFrames**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.discardqueuedframes) 方法。 此方法的一个典型方案是当你的效果之前存储了处理后的帧以供在处理当前帧时使用的情况。 当调用此方法时，你应当处理保存的上一组帧。 此方法可用于重置与之前的帧（而不仅仅是累计的视频帧）相关的任何状态。


[!code-cs[DiscardQueuedFrames](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetDiscardQueuedFrames)]



### <a name="isreadonly-property"></a>IsReadOnly 属性

[  **IsReadOnly**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.isreadonly) 属性使系统能够知道你的效果是否将写入效果的输出。 如果你的应用不修改视频帧（例如，一种仅执行视频帧分析的效果），你应将此属性设置为 true，这将使系统能够为你有效地将帧输入复制到帧输出。

> [!TIP]
> 当 [**IsReadOnly**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.isreadonly) 属性设置为 true 时，系统会在调用 [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe) 之前，将输入帧复制到输出帧。 将 **IsReadOnly** 属性设置为 true 不会限制在 **ProcessFrame** 中对效果的输出帧执行写入操作。


[!code-cs[IsReadOnly](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetIsReadOnly)]

### <a name="setencodingproperties-method"></a>SetEncodingProperties 方法

系统会对你的效果调用 [**SetEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.setencodingproperties)，以便让你知道要应用该效果的视频流的编码属性。 此方法还提供了对用于硬件呈现的 Direct3D 设备的引用。 此设备的用法会在本文后面的硬件处理示例中介绍。

[!code-cs[SetEncodingProperties](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSetEncodingProperties)]


### <a name="supportedencodingproperties-property"></a>SupportedEncodingProperties 属性

系统将检查 [**SupportedEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedencodingproperties) 属性以确定你的效果支持哪些编码属性。 请注意，如果你的效果的使用者无法使用你指定的属性对视频进行编码，它将对你的效果调用 [**Close**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.close) 并且会从视频管道中删除你的效果。


[!code-cs[SupportedEncodingProperties](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSupportedEncodingProperties)]


> [!NOTE] 
> 如果从 [SupportedEncodingProperties**返回空的**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.VideoEncodingProperties)VideoEncodingProperties 对象的空白默认列表，系统将默认为 ARGB32 编码。

 

### <a name="supportedmemorytypes-property"></a>SupportedMemoryTypes 属性

系统将检查 [**SupportedMemoryTypes**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedmemorytypes) 属性以确定你的效果是否将访问软件内存或硬件 (GPU) 内存中的视频帧。 如果你返回 [**MediaMemoryTypes.Cpu**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaMemoryTypes)，系统会将使图像数据包含在 [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) 对象中的输入和输出帧传递给你的效果。 如果返回 **MediaMemoryTypes.Gpu**，系统会将使图像数据包含在 [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface) 对象中的输入和输出帧传递给效果。

[!code-cs[SupportedMemoryTypes](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSupportedMemoryTypes)]


> [!NOTE]
> 如果指定 [**MediaMemoryTypes.GpuAndCpu**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaMemoryTypes)，系统将使用 GPU 或系统内存（以对管道更高效者为准）。 在使用此值时，必须检查 [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe) 方法，以查看传入到该方法的 [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) 或 [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface) 是否包含数据，然后相应地处理帧。

 

### <a name="timeindependent-property"></a>TimeIndependent 属性

[  **TimeIndependent**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.timeindependent) 属性使系统能够知道你的效果不需要统一计时。 当设置为 true 时，系统可以使用优化功能增强效果性能。

[!code-cs[TimeIndependent](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetTimeIndependent)]

### <a name="setproperties-method"></a>SetProperties 方法

[  **SetProperties**](https://docs.microsoft.com/uwp/api/windows.media.imediaextension.setproperties) 方法允许正在使用你的效果的应用调整效果参数。 属性将作为属性名称和值的 [**IPropertySet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IPropertySet) 映射传递。


[!code-cs[SetProperties](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSetProperties)]


此简单示例将根据指定值使每个视频帧中的像素变暗。 已声明了某个属性，并已使用 TryGetValue 来获取通过调用应用设置的值。 如果未设置任何值，使用默认值 0.5。

[!code-cs[FadeValue](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetFadeValue)]


### <a name="processframe-method"></a>ProcessFrame 方法

[  **ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe) 方法是你的效果修改视频图像数据的位置。 针对每一帧调用一次该方法，并将 [**ProcessVideoFrameContext**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.ProcessVideoFrameContext) 对象传递给它。 此对象包含一个输入 [**VideoFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame) 对象（包含要处理的传入帧）和一个你要向其写入图像数据（将传递到剩余的视频管道）的输出 **VideoFrame** 对象。 其中每个 **VideoFrame** 对象都有一个 [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.softwarebitmap) 属性和一个 [**Direct3DSurface**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.direct3dsurface) 属性，但是使用其中哪个对象，则由你从 [**SupportedMemoryTypes**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedmemorytypes) 属性返回的值确定。

此示例介绍如何使用软件处理简单地实现 **ProcessFrame** 方法。 有关使用 [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) 对象的详细信息，请参阅[图像处理](imaging.md)。 使用硬件处理的 **ProcessFrame** 实现示例将在本文后面介绍。

访问 **SoftwareBitmap** 的数据缓冲区需要 COM 互操作，所以应当在效果类文件中包含 **System.Runtime.InteropServices** 命名空间。

[!code-cs[COMUsing](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetCOMUsing)]


在该命名空间内为效果添加以下代码，以导入访问该图像缓冲区的接口。

[!code-cs[COMImport](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetCOMImport)]


> [!NOTE]
> 因为这种方法访问本机非托管的图像缓冲区，所以需要将项目配置为允许不安全的代码。
> 1.  在“解决方案资源管理器”中，右键单击 VideoEffectComponent 项目，并选择**属性**。
> 2.  选择**生成**选项卡。
> 3.  选中**允许不安全代码**复选框。

 

现在，你可以添加 **ProcessFrame** 方法实现。 首先，此方法将从输入和输出软件位图中获取 [**BitmapBuffer**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapBuffer) 对象。 请注意，输出帧和输入帧均已打开，分别用于写入和读取。 接下来，通过调用 [**CreateReference**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IMemoryBufferReference) 为每个缓冲区获取 [**IMemoryBufferReference**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapbuffer.createreference)。 然后，通过将 **IMemoryBufferReference** 转换为上述定义的 COM 互操作接口 **IMemoryByteAccess**，接着调用 **GetBuffer**，获取实际数据缓冲区。

现在已获取数据缓冲区，你可以从输入缓冲区进行读取，并对输出缓冲区进行写入。 通过调用 [**GetPlaneDescription**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapbuffer.getplanedescription) 获取缓冲区的布局，它提供了有关缓冲区的宽度、步幅和初始偏移的信息。 每像素位数由与之前通过 [**SetEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.setencodingproperties) 方法设置的编码属性确定。 缓冲区格式信息用于查找每个像素的缓冲区中的索引。 源缓冲区的像素值已复制到目标缓冲区中，并且颜色值乘以为此效果定义的 FadeValue 属性，以使其按指定的量变暗。

[!code-cs[ProcessFrameSoftwareBitmap](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetProcessFrameSoftwareBitmap)]


## <a name="implement-the-ibasicvideoeffect-interface-using-hardware-processing"></a>使用硬件处理实现 IBasicVideoEffect 接口


使用硬件 (GPU) 处理创建自定义视频效果与使用上述软件处理几乎完全相同。 本部分介绍使用硬件处理的效果中的几点不同之处。 此示例使用 Win2D Windows 运行时 API。 有关使用 Win2D 的详细信息，请参阅 [Win2D 文档](https://microsoft.github.io/Win2D/html/Introduction.htm)。

使用以下步骤将 Win2D NuGet 程序包添加到你创建的项目，如本文开头的**向应用添加自定义效果**部分中所述。

**将 Win2D NuGet 包添加到效果项目**

1.  在**解决方案资源管理器**中，右键单击 **VideoEffectComponent** 项目，并选择**管理 NuGet 程序包**。
2.  在窗口顶部，选择**浏览**选项卡。
3.  在搜索框中，输入 **Win2D**。
4.  选择 **Win2D.uwp**，然后选择右侧窗格中的**安装**。
5.  **查看更改**对话框将向你显示要安装的程序包。 单击**确定**。
6.  接受程序包许可证。

除了基本项目设置中包含的命名空间外，还需要包含 Win2D 提供的以下命名空间。

[!code-cs[UsingWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetUsingWin2D)]


由于此效果将使用 GPU 内存来处理图像数据，因此应当从 [**SupportedMemoryTypes**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaMemoryTypes) 属性返回 [**MediaMemoryTypes.Gpu**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedmemorytypes)。

[!code-cs[SupportedMemoryTypesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSupportedMemoryTypesWin2D)]


使用 [**SupportedEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedencodingproperties) 属性来设置效果将支持的编码属性。 当处理 Win2D 时，必须使用 ARGB32 编码。

[!code-cs[SupportedEncodingPropertiesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSupportedEncodingPropertiesWin2D)]


使用 [**SetEncodingProperties**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert) 方法，从传入到该方法中的IDirect3DDevice[**创建新的 Win2D**CanvasDevice](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DDevice) 对象。

[!code-cs[SetEncodingPropertiesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSetEncodingPropertiesWin2D)]


[  **SetProperties**](https://docs.microsoft.com/uwp/api/windows.media.imediaextension.setproperties) 实现与之前的软件处理示例相同。 此示例使用 **BlurAmount** 属性配置 Win2D 模糊效果。

[!code-cs[SetPropertiesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSetPropertiesWin2D)]

[!code-cs[BlurAmountWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetBlurAmountWin2D)]


最后一步是实现实际处理图像数据的 [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe) 方法。

使用 Win2D API，从输入帧的Direct3DSurface[**属性创建**CanvasBitmap](https://docs.microsoft.com/uwp/api/windows.media.videoframe.direct3dsurface)。 从输出帧的 **Direct3DSurface** 创建一个 **CanvasRenderTarget**，从此呈现目标创建一个 **CanvasDrawingSession**。 新的 Win2D **GaussianBlurEffect** 已使用我们的效果通过SetProperties[**公开的**BlurAmount](https://docs.microsoft.com/uwp/api/windows.media.imediaextension.setproperties) 属性进行了初始化。 最后，调用 **CanvasDrawingSession.DrawImage** 方法，使用模糊效果将输入位图绘制到呈现目标。

[!code-cs[ProcessFrameWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetProcessFrameWin2D)]


## <a name="adding-your-custom-effect-to-your-app"></a>向应用添加自定义效果


若要从应用中使用你的视频效果，必须向应用添加对效果项目的引用。

1.  在“解决方案资源管理器”中，在你的项目下，右键单击**引用**，然后选择**添加引用**。
2.  展开**项目**选项卡、选择**解决方案**，然后选中效果项目名称对应的复选框。 对于此示例，名称为 *VideoEffectComponent*。
3.  单击**确定**。

### <a name="add-your-custom-effect-to-a-camera-video-stream"></a>向相机视频流添加自定义效果

你可以按照[简单的相机预览访问](simple-camera-preview-access.md)文章中的步骤，从相机设置一个简单的预览流。 按照这些步骤操作将为你提供一个已初始化的 [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) 对象，该对象用于访问相机的视频流。

若要将你的自定义视频效果添加到相机流，首先要创建一个新的 [**VideoEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.VideoEffectDefinition) 对象，从而为你的效果传入命名空间和类名称。 接下来，调用 **MediaCapture** 对象的 [**AddVideoEffect**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.addvideoeffectasync) 方法，以将你的效果添加到指定的流。 此示例使用 [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType) 值来指定应将效果添加到预览流。 如果你的应用支持视频捕获，你也可以使用 **MediaStreamType.VideoRecord** 将效果添加到捕获流。 **AddVideoEffect** 将返回表示你的自定义效果的 [**IMediaExtension**](https://docs.microsoft.com/uwp/api/Windows.Media.IMediaExtension) 对象。 你可以使用 SetProperties 方法设置效果的配置。

添加效果后，将调用 [**StartPreviewAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.startpreviewasync) 来启动预览流。

[!code-cs[AddVideoEffectAsync](./code/VideoEffect_Win10/cs/VideoEffect_Win10/MainPage.xaml.cs#SnippetAddVideoEffectAsync)]



### <a name="add-your-custom-effect-to-a-clip-in-a-mediacomposition"></a>向 MediaComposition 中的一段剪辑添加自定义效果

有关从视频剪辑创建媒体合成的一般指南，请参阅[媒体合成和编辑](media-compositions-and-editing.md)。 以下代码片段展示了使用自定义视频效果创建简单媒体合成的过程。 通过调用 [**CreateFromFileAsync**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaClip)、传入用户通过 [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromfileasync) 选择的视频文件创建一个 [**MediaClip**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) 对象，并将剪辑添加到新的 [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition)。 接下来，创建新的 [**VideoEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.VideoEffectDefinition) 对象，从而将效果的命名空间和类名称传入到构造函数。 最后，将效果定义添加到 [MediaClip**对象的**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.videoeffectdefinitions)VideoEffectDefinitions 集合。


[!code-cs[AddEffectToComposition](./code/VideoEffect_Win10/cs/VideoEffect_Win10/MainPage.xaml.cs#SnippetAddEffectToComposition)]


## <a name="related-topics"></a>相关主题
* [简单的相机预览访问](simple-camera-preview-access.md)
* [媒体合成和编辑](media-compositions-and-editing.md)
* [Win2D 文档](https://microsoft.github.io/Win2D/html/Introduction.htm)
* [媒体播放](media-playback.md)