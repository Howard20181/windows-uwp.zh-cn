---
Description: 了解如何捕获和识别较长的连续听写语音输入。
title: 启用连续听写
ms.assetid: 383B3E23-1678-4FBB-B36E-6DE2DA9CA9DC
label: Continuous dictation
template: detail.hbs
keywords: 语音，语音，语音识别，自然语言，听写，输入，用户交互
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 73cfb6fd56b5ae06118ea01ef7ef0b3882db60da
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257996"
---
# <a name="continuous-dictation"></a>连续听写

了解如何捕获和识别较长的连续听写语音输入。

> **重要 API**：[**SpeechContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechContinuousRecognitionSession)、[**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession)

在[语音识别](speech-recognition.md)中，你已了解如何使用 [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizeasync) 对象的 [**RecognizeAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizewithuiasync) 或 [**RecognizeWithUIAsync**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) 方法捕获和识别相对较短的语音输入。例如，撰写短信 (SMS) 或进行提问时。

对于较长的连续语音识别会话（例如听写或电子邮件），则使用 [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) 的 [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) 属性以获取 [**SpeechContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechContinuousRecognitionSession) 对象。

> [!NOTE]
> 听写语言支持取决于运行应用的[设备](https://docs.microsoft.com/windows/uwp/design/devices/)。 对于 Pc 和笔记本电脑，只能识别 en-us，而 Xbox 和手机可以识别语音识别支持的所有语言。 有关详细信息，请参阅[指定语音识别器语言](specify-the-speech-recognizer-language.md)。

## <a name="set-up"></a>设置

若要管理连续听写会话，你的应用需要几个对象：

- [  **SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) 对象的示例。
- 对要在听写期间更新 UI 的 UI 调度程序的引用。
- 用于跟踪用户累积说出的字词的方式。

此处，我们将一个 [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) 实例声明为代码隐藏类的私有字段。 如果你希望连续听写在单个可扩展应用程序标记语言 (XAML) 页面之后持续，则你的应用需要将引用存储在其他位置。

```CSharp
private SpeechRecognizer speechRecognizer;
```

在听写期间，识别器会从后台线程引发事件。 由于后台线程不能直接在 XAML 中更新 UI，你的应用必须使用调度程序才能更新 UI 以响应识别事件。

此处，我们声明一个私有字段，它将在之后通过 UI 调度程序进行初始化。

```CSharp
// Speech events may originate from a thread other than the UI thread.
// Keep track of the UI thread dispatcher so that we can update the
// UI in a thread-safe manner.
private CoreDispatcher dispatcher;
```

若要跟踪用户说出的内容，你需要处理由语音识别器所引发的识别事件。 这些事件提供用户话语块的识别结果。

此处，我们使用 [**StringBuilder**](https://docs.microsoft.com/dotnet/api/system.text.stringbuilder) 对象保留在会话期间获取的所有识别结果。 新结果将在处理后追加到 **StringBuilder**。

```CSharp
private StringBuilder dictatedTextBuilder;
```

## <a name="initialization"></a>初始化

在连续语音识别初始化期间，你必须：

- 提取 UI 线程的调度程序（如果在连续识别事件处理程序中更新你的应用的 UI）。
- 初始化语音识别器。
- 编译内置的听写语法。
    **请注意**   语音识别至少需要一个约束才能定义可识别词汇。 如果未指定任何约束，将使用预定义的听写语法。 请参阅[语音识别](speech-recognition.md)。
- 为识别事件设置事件侦听器。

在此示例中，我们将在 [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) 页面事件中初始化语音识别。

1. 因为由语音识别器引发的事件在后台线程上发生，所以请创建一个对调度程序的引用以更新 UI 线程。 始终在 UI 线程上调用[**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) 。
```csharp
this.dispatcher = CoreWindow.GetForCurrentThread().Dispatcher;
```

2.  然后，我们初始化 [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) 实例。
```csharp
this.speechRecognizer = new SpeechRecognizer();
```

3.  我们再添加和编译语法，该语法定义所有可通过 [**SpeechRecognizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizer) 识别的字词和短语。

    如果未显式指定语法，则默认使用预定义听写语法。 通常，默认语法最适用于常规听写。

    此处，我们立即调用 [**CompileConstraintsAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.compileconstraintsasync) 而无需添加语法。

    
```csharp
SpeechRecognitionCompilationResult result =
      await speechRecognizer.CompileConstraintsAsync();
```

## <a name="handle-recognition-events"></a>处理识别事件

你可以通过调用 [**RecognizeAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizeasync) 或 [**RecognizeWithUIAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.recognizewithuiasync) 捕获单一、简要的话语或短语。 

但是，为了捕获较长的连续识别会话，我们将指定在用户说话时要在后台运行的事件侦听器，并定义处理程序以构建听写字符串。

然后，我们使用识别器的 [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) 属性来获取 [**SpeechContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechContinuousRecognitionSession) 对象，该对象提供用于管理连续识别会话的方法和事件。

两个事件尤其关键：

- [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated)，在识别器生成一些结果时发生。
- [**已完成**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.completed)，这在连续识别会话结束时发生。

当用户说话时引发 [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 事件。 识别器持续侦听用户，并定期引发一个传递语音输入块的事件。 你必须使用事件参数的 [**Result**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionresultgeneratedeventargs.result) 属性检查语音输入，并在事件处理程序中采取相应操作，例如将文本追加到 StringBuilder 对象。

作为 [**SpeechRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResult) 的实例，[**Result**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionresultgeneratedeventargs.result) 属性可用于确定是否希望接受语音输入。 [  **SpeechRecognitionResult**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResult) 为此提供了两个属性：

- [**状态**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.status)指示识别是否成功。 识别失败的原因有多种。
- [**置信度**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.confidence)表示识别器理解正确字词的相对置信度。

下面是支持连续识别的基本步骤：  

1. 此处，我们在 [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 页面事件中注册 [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) 连续识别事件的处理程序。
```csharp
speechRecognizer.ContinuousRecognitionSession.ResultGenerated +=
        ContinuousRecognitionSession_ResultGenerated;
```

2.  然后检查 [**Confidence**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.confidence) 属性。 如果 Confidence 的值是 [**Medium**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionConfidence) 或更好，我们便将文本追加到 StringBuilder。 我们还在收集输入时更新 UI。

    **请注意**  在无法直接更新 UI 的后台线程上引发[**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated)事件。 如果处理程序需要更新 UI （如 \[语音和 TTS 示例\] 这样做），则必须通过调度程序的[**RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync)方法将更新调度到 UI 线程。
```csharp
private async void ContinuousRecognitionSession_ResultGenerated(
      SpeechContinuousRecognitionSession sender,
      SpeechContinuousRecognitionResultGeneratedEventArgs args)
      {

        if (args.Result.Confidence == SpeechRecognitionConfidence.Medium ||
          args.Result.Confidence == SpeechRecognitionConfidence.High)
          {
            dictatedTextBuilder.Append(args.Result.Text + " ");

            await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              dictationTextBox.Text = dictatedTextBuilder.ToString();
              btnClearText.IsEnabled = true;
            });
          }
        else
        {
          await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              dictationTextBox.Text = dictatedTextBuilder.ToString();
            });
        }
      }
```

3.  然后处理 [**Completed**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.completed) 事件，该事件指示连续听写的结尾。

    当你调用 [**StopAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.stopasync) 或 [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) 方法时会话结束（在下一部分介绍）。 在发生错误或用户停止说话时，会话也可以结束。 检查事件参数的 [**Status**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionresult.status) 属性以确定会话结束的原因 ([**SpeechRecognitionResultStatus**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResultStatus))。

    此处，我们在 [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.completed) 页面事件中注册 [**Completed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) 连续识别事件的处理程序。
```csharp
speechRecognizer.ContinuousRecognitionSession.Completed +=
      ContinuousRecognitionSession_Completed;
```

4.  事件处理程序检查“Status”属性，以确定识别是否成功。 它还可处理用户已停止说话的情况。 通常，将 [**TimeoutExceeded**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognitionResultStatus) 视为成功的识别，因为这意味着用户已结束说话。 你应该在代码中对这种情况进行处理以提供良好体验。

    **请注意**  在无法直接更新 UI 的后台线程上引发[**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated)事件。 如果处理程序需要更新 UI （如 \[语音和 TTS 示例\] 这样做），则必须通过调度程序的[**RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync)方法将更新调度到 UI 线程。
```csharp
private async void ContinuousRecognitionSession_Completed(
      SpeechContinuousRecognitionSession sender,
      SpeechContinuousRecognitionCompletedEventArgs args)
      {
        if (args.Status != SpeechRecognitionResultStatus.Success)
        {
          if (args.Status == SpeechRecognitionResultStatus.TimeoutExceeded)
          {
            await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              rootPage.NotifyUser(
                "Automatic Time Out of Dictation",
                NotifyType.StatusMessage);

              DictationButtonText.Text = " Continuous Recognition";
              dictationTextBox.Text = dictatedTextBuilder.ToString();
            });
          }
          else
          {
            await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
              rootPage.NotifyUser(
                "Continuous Recognition Completed: " + args.Status.ToString(),
                NotifyType.StatusMessage);

              DictationButtonText.Text = " Continuous Recognition";
            });
          }
        }
      }
```

## <a name="provide-ongoing-recognition-feedback"></a>提供正在进行的识别反馈


当用户对话时，他们通常将依赖上下文才能完全理解所说内容。 同样，语音识别器通常需要上下文才能提供高可信度的识别结果。 例如，除非可从前后的词语中收集到更多的上下文，否则词语“包含”和“包涵”本身是无法区分的。 除非识别器已具有一定的置信度来确保字词已正确识别，否则它将不会引发 [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 事件。

这可能会导致不理想的用户体验，因为他们仍在继续说话，但在识别器不足以具有能够引发 [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 事件的置信度之前，不会提供任何结果。

处理 [**HypothesisGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.hypothesisgenerated) 事件以改进这种明显的响应缺乏问题。 只要识别器为要处理的字词生成一组新的潜在匹配，就会引发此事件。 事件参数提供包含当前匹配的 [**Hypothesis**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognitionhypothesisgeneratedeventargs.hypothesis) 属性。 在用户继续说话时向其展示这些匹配，向他们保证处理仍在进行。 当置信度较高并已确定识别结果时，使用ResultGenerated[**事件中提供的最终**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionresultgeneratedeventargs.result)Result[**替换临时的**Hypothesis](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 结果。

此处，我们将假设文本和一个省略号（“…”）追加到输出 [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) 的当前值。 从生成新的假设直到从 [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 事件获取最终结果后，文本框内容才会更新。

```CSharp
private async void SpeechRecognizer_HypothesisGenerated(
  SpeechRecognizer sender,
  SpeechRecognitionHypothesisGeneratedEventArgs args)
  {

    string hypothesis = args.Hypothesis.Text;
    string textboxContent = dictatedTextBuilder.ToString() + " " + hypothesis + " ...";

    await dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
      dictationTextBox.Text = textboxContent;
      btnClearText.IsEnabled = true;
    });
  }
```

## <a name="start-and-stop-recognition"></a>启动和停止识别


启动识别会话之前，检查语音识别器 [**State**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.state) 属性的值。 语音识别器必须处于 [**Idle**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechRecognition.SpeechRecognizerState) 状态。

在检查语音识别器的状态之后，我们通过调用语音识别器的 [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.startasync) 属性的 [**StartAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) 方法启动会话。

```CSharp
if (speechRecognizer.State == SpeechRecognizerState.Idle)
{
  await speechRecognizer.ContinuousRecognitionSession.StartAsync();
}
```

可以采用两种方法停止识别：

-   [**StopAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.stopasync)使任何挂起的识别事件完成（在完成所有挂起的识别操作之前，将继续引发[**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) ）。
-   [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync)立即终止识别会话并放弃所有挂起的结果。

在检查语音识别器的状态之后，我们通过调用语音识别器的 [**ContinuousRecognitionSession**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) 属性的 [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechrecognizer.continuousrecognitionsession) 方法停止会话。

```CSharp
if (speechRecognizer.State != SpeechRecognizerState.Idle)
{
  await speechRecognizer.ContinuousRecognitionSession.CancelAsync();
}
```

> [!NOTE]
> [  **ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 事件可在调用 [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) 后发生。  
> 由于多线程处理，当调用 [**CancelAsync**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 时，[**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.cancelasync) 事件可能仍保留在堆栈上。 如果如此，则仍引发 **ResultGenerated** 事件。  
> 如果在取消识别会话时设置任何私有字段，请始终在 [**ResultGenerated**](https://docs.microsoft.com/uwp/api/windows.media.speechrecognition.speechcontinuousrecognitionsession.resultgenerated) 处理程序中确认它们的值。 例如，如果在取消会话时将字段设置为 null，请勿假定字段在处理程序中进行初始化。

 

## <a name="related-articles"></a>相关文章


* [语音交互](speech-interactions.md)

**示例**
* [语音识别和语音合成示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SpeechRecognitionAndSynthesis)
 

 




