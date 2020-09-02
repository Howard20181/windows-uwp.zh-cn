---
Description: 本文介绍如何创建可实现 IBasicAudioEffect 接口的 Windows 运行时组件，以允许你为音频流创建自定义效果。
title: 自定义音频效果
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 360faf3f-7e73-4db4-8324-3391f801d827
ms.localizationpriority: medium
ms.openlocfilehash: e52aa4ebde6f988daad9c1712845e07ee553d7a2
ms.sourcegitcommit: c3ca68e87eb06971826087af59adb33e490ce7da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89363960"
---
# <a name="custom-audio-effects"></a>自定义音频效果

本文介绍如何创建可实现 [**IBasicAudioEffect**](/uwp/api/Windows.Media.Effects.IBasicAudioEffect) 接口的 Windows 运行时组件，以为音频流创建自定义效果。 自定义效果可以与若干不同的 Windows 运行时 API 一起使用，包括提供设备相机的访问权限的 [MediaCapture](/uwp/api/Windows.Media.Capture.MediaCapture)、允许你从媒体剪辑创建复杂合成的 [**MediaComposition**](/uwp/api/Windows.Media.Editing.MediaComposition) 和允许你快速汇编各种音频输入、输出和子混合节点图的 [**AudioGraph**](/uwp/api/Windows.Media.Audio.AudioGraph)。

## <a name="add-a-custom-effect-to-your-app"></a>将自定义效果添加到应用


自定义音频效果在实现 [**IBasicAudioEffect**](/uwp/api/Windows.Media.Effects.IBasicAudioEffect) 接口的类中定义。 此类不能直接包含在应用的项目中。 必须改用 Windows 运行时组件托管音频效果类。

**为音频效果添加 Windows 运行时组件**

1.  在 Microsoft Visual Studio 中打开解决方案后，请在 " **文件** " 菜单中选择 " **添加- &gt; 新建项目**"。
2.  ** (通用 Windows) **项目类型中选择 "Windows 运行时" 组件。
3.  对于此示例，将项目命名为 *AudioEffectComponent*。 此名称稍后将在代码中引用。
4.  单击 **“确定”** 。
5.  项目模板将创建一个名为 Class1.cs 的类。 在 **解决方案资源管理器**中，右键单击 Class1.cs 的图标，然后选择 " **重命名**"。
6.  将文件重命名为 *ExampleAudioEffect.cs*。 Visual Studio 将显示一条提示，询问你是否想要更新对新名称的所有引用。 单击 **“是”** 。
7.  打开 **ExampleAudioEffect.cs** 并更新类定义以实现 [**IBasicAudioEffect**](/uwp/api/Windows.Media.Effects.IBasicAudioEffect) 接口。


:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetImplementIBasicAudioEffect":::

你需要在效果类文件中包含以下命名空间，以便访问本文中示例所使用的所有类型。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetEffectUsing":::

## <a name="implement-the-ibasicaudioeffect-interface"></a>实现 IBasicAudioEffect 接口

你的音频效果必须实现 [**IBasicAudioEffect**](/uwp/api/Windows.Media.Effects.IBasicAudioEffect) 接口的所有方法和属性。 本部分将引导你完成简单实现该接口以创建基本回声效果的过程。

### <a name="supportedencodingproperties-property"></a>SupportedEncodingProperties 属性

系统将检查 [**SupportedEncodingProperties**](/uwp/api/windows.media.effects.ibasicaudioeffect.supportedencodingproperties) 属性以确定你的效果支持哪些编码属性。 请注意，如果你的效果的使用者无法使用你指定的属性对音频进行编码，系统将对你的效果调用 [**Close**](/uwp/api/windows.media.effects.ibasicaudioeffect.close) 并且会从音频管道中删除你的效果。 在此示例中，[**AudioEncodingProperties**](/uwp/api/Windows.Media.MediaProperties.AudioEncodingProperties) 对象将创建并添加到返回的列表，以支持 44.1 kHz 和 48 kHz、32 位浮点的单声道编码。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetSupportedEncodingProperties":::

### <a name="setencodingproperties-method"></a>SetEncodingProperties 方法

系统会对你的效果调用 [**SetEncodingProperties**](/uwp/api/windows.media.effects.ibasicvideoeffect.setencodingproperties)，以便让你知道要应用该效果的音频流的编码属性。 为了实现回声效果，此示例使用缓冲区存储 1 秒的音频数据。 使用此方法，有机会根据编码音频的采样率，将缓冲区大小初始化为一秒音频中的采样数。 延迟效果还使用整数计数器跟踪在延迟缓冲区中的当前位置。 因为在将效果添加到音频管道时会调用 **SetEncodingProperties**，因此此时是将该值初始化为 0 的好时机。 你可能还需要捕获传递到此方法中的 **AudioEncodingProperties** 对象，以在效果中的其他位置使用。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetDeclareEchoBuffer":::
:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetSetEncodingProperties":::


### <a name="setproperties-method"></a>SetProperties 方法

[**SetProperties**](/uwp/api/windows.media.imediaextension.setproperties) 方法允许正在使用你的效果的应用调整效果参数。 属性将作为属性名称和值的 [**IPropertySet**](/uwp/api/Windows.Foundation.Collections.IPropertySet) 映射传递。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetSetProperties":::

根据 **Mix** 属性的值，在此简单示例中，将混合当前音频采样与延迟缓冲区中的值。 已声明了某个属性，并已使用 TryGetValue 来获取通过调用应用设置的值。 如果未设置任何值，使用默认值 0.5。 请注意，此属性为只读。 必须使用 **SetProperties** 设置属性值。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetMixProperty":::

### <a name="processframe-method"></a>ProcessFrame 方法

[**ProcessFrame**](/uwp/api/windows.media.effects.ibasicaudioeffect.processframe) 方法是你的效果修改流音频数据的位置。 针对每一帧调用一次该方法，并将 [**ProcessAudioFrameContext**](/uwp/api/Windows.Media.Effects.ProcessAudioFrameContext) 对象传递给它。 此对象包含一个输入 [**AudioFrame**](/uwp/api/Windows.Media.AudioFrame) 对象（包含要处理的传入帧）和一个你要向其写入音频数据（将传递到剩余的音频管道）的输出 **AudioFrame** 对象。 音频帧是表示一小段音频数据的音频采样的缓冲区。

访问 **AudioFrame** 的数据缓冲区需要 COM 互操作，所以你应该在效果类文件中包括 **System.Runtime.InteropServices** 命名空间，然后针对你的效果将以下代码添加到该命名空间内，以便导入用于访问音频缓冲区的接口。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetComImport":::

> [!NOTE]
> 因为这种方法访问本机非托管的图像缓冲区，所以需要将项目配置为允许不安全的代码。
> 1.  在解决方案资源管理器中，右键单击 AudioEffectComponent 项目，然后选择 " **属性**"。
> 2.  选择“**生成**”选项卡。
> 3.  选中 " **允许不安全代码** " 复选框。

 

现在，你可以将 **ProcessFrame** 方法实现添加到你的效果。 首先，此方法将从输入和输出音频帧中获取 [**AudioBuffer**](/uwp/api/Windows.Media.AudioBuffer) 对象。 请注意，输出帧和输入帧均已打开，分别用于写入和读取。 接下来，通过调用 [**CreateReference**](/uwp/api/windows.graphics.imaging.bitmapbuffer.createreference) 为每个缓冲区获取 [**IMemoryBufferReference**](/uwp/api/Windows.Foundation.IMemoryBufferReference)。 然后，通过将 **IMemoryBufferReference** 转换为上述定义的 COM 互操作接口 **IMemoryByteAccess**，接着调用 **GetBuffer**，获取实际数据缓冲区。

现在已获取数据缓冲区，你可以从输入缓冲区进行读取，并对输出缓冲区进行写入。 针对 inputbuffer 中的每个采样，获取值并将其乘以 1 - **Mix**，以设置效果的原始信号值。 接下来，从回声缓冲区中的当前位置检索采样并将其乘以 **Mix**，以设置效果的已处理值。 输出采样设置为原始值和已处理值的总和。 最后，每个输入采样存储在回声缓冲区中并递增当前采样索引。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetProcessFrame":::



### <a name="close-method"></a>Close 方法

应关闭效果时，系统将在你的类上调用 [**Close**](/uwp/api/windows.media.effects.ibasicaudioeffect.close) [**Close**](/uwp/api/windows.media.effects.ibasicaudioeffect.close) 方法。 你应当使用此方法来处理你创建的任何资源。 该方法的参数是 [**MediaEffectClosedReason**](/uwp/api/Windows.Media.Effects.MediaEffectClosedReason)，该参数使你知道效果是否正常关闭、是否发生错误或者效果是否不支持所需的编码格式。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetClose":::

### <a name="discardqueuedframes-method"></a>DiscardQueuedFrames 方法

当效果应重置时，将调用 [**DiscardQueuedFrames**](/uwp/api/windows.media.effects.ibasicvideoeffect.discardqueuedframes) 方法。 此方法的一个典型方案是当你的效果之前存储了处理后的帧以供在处理当前帧时使用的情况。 当调用此方法时，你应当处理保存的上一组帧。 此方法可用于重置与之前的帧（而不仅仅是累计的音频帧）相关的任何状态。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetDiscardQueuedFrames":::

### <a name="timeindependent-property"></a>TimeIndependent 属性

TimeIndependent [**TimeIndependent**](/uwp/api/windows.media.effects.ibasicvideoeffect.timeindependent) 属性使系统能够知道你的效果不需要统一计时。 当设置为 true 时，系统可以使用优化功能增强效果性能。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetTimeIndependent":::

### <a name="useinputframeforoutput-property"></a>UseInputFrameForOutput 属性

将 [**UseInputFrameForOutput**](/uwp/api/windows.media.effects.ibasicaudioeffect.useinputframeforoutput) 属性设置为 **true**，告知你的系统会将其输出写入 [**ProcessAudioFrameContext**](/uwp/api/Windows.Media.Effects.ProcessAudioFrameContext)（传递到 [**ProcessFrame**](/uwp/api/windows.media.effects.ibasicaudioeffect.processframe)）的 [**InputFrame**](/uwp/api/windows.media.effects.processaudioframecontext.inputframe) 的音频缓冲区，而不会写入 [**OutputFrame**](/uwp/api/windows.media.effects.processaudioframecontext.outputframe)。 

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/AudioEffectComponent/ExampleAudioEffect.cs" id="SnippetUseInputFrameForOutput":::


## <a name="adding-your-custom-effect-to-your-app"></a>向应用添加自定义效果


若要从应用中使用你的音频效果，必须向应用添加对效果项目的引用。

1.  在解决方案资源管理器中，右键单击 " **引用** "，然后选择 " **添加引用**"。
2.  展开 " **项目** " 选项卡，选择 " **解决方案**"，然后选中效果项目名称对应的复选框。 对于此示例，名称为 *AudioEffectComponent*。
3.  单击 **“确定”**

如果音频效果类声明为不同的命名空间，请确保将该命名空间包含在代码文件中。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/MainPage.xaml.cs" id="SnippetUsingAudioEffectComponent":::

### <a name="add-your-custom-effect-to-an-audiograph-node"></a>将自定义效果添加到 AudioGraph 节点
有关使用音频图的常规信息，请参阅[音频图](audio-graphs.md)。 以下代码片段显示了如何将本文中所示的回声效果示例添加到音频图节点。 首先，创建 [**PropertySet**](/uwp/api/Windows.Foundation.Collections.PropertySet)，设置由效果定义的 **Mix** 属性值。 接下来，调用 [**AudioEffectDefinition**](/uwp/api/Windows.Media.Effects.AudioEffectDefinition) 构造函数，从而传入自定义效果类型的完整类名称和属性集。 最后，将效果定义添加到现有 [**FileInputNode**](/uwp/api/windows.media.audio.createaudiofileinputnoderesult.fileinputnode) 的 [**EffectDefinitions**](/uwp/api/windows.media.audio.audiofileinputnode.effectdefinitions) 属性，以使发出的音频由自定义效果处理。 

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioGraph/cs/MainPage.xaml.cs" id="SnippetAddCustomEffect":::

在自定义效果添加到节点后，可通过调用 [**DisableEffectsByDefinition**](/uwp/api/windows.media.audio.audiofileinputnode.disableeffectsbydefinition) 和传入 **AudioEffectDefinition** 对象禁用它。 有关在你的应用中使用音频图的详细信息，请参阅[音频图](audio-graphs.md)。

### <a name="add-your-custom-effect-to-a-clip-in-a-mediacomposition"></a>向 MediaComposition 中的一段剪辑添加自定义效果

以下代码片段演示了向视频剪辑添加自定义音频效果以及媒体合成中的后台音频轨。 有关从视频剪辑创建媒体合成和添加后台音频跟踪的一般指南，请参阅[媒体合成和编辑](media-compositions-and-editing.md)。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/MediaEditing/cs/MainPage.xaml.cs" id="SnippetAddCustomAudioEffect":::



## <a name="related-topics"></a>相关主题
* [简单相机预览访问](simple-camera-preview-access.md)
* [媒体合成和编辑](media-compositions-and-editing.md)
* [Win2D 文档](https://microsoft.github.io/Win2D/html/Introduction.htm)
* [媒体播放](media-playback.md)

 
