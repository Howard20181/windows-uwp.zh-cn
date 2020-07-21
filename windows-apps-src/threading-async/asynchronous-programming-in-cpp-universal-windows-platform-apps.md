---
ms.assetid: 34C00F9F-2196-46A3-A32F-0067AB48291B
description: 本文介绍了在 Visual C++ 组件扩展 (C++/CX) 中，通过使用在 ppltasks.h 中的 concurrency 命名空间中定义的 task 类来使用异步方法的推荐方式。
title: 使用 C++ 进行异步编程
ms.date: 05/14/2018
ms.topic: article
keywords: Windows 10, uwp, 线程, 异步, C++
ms.localizationpriority: medium
ms.openlocfilehash: 73268a2aab0212277fb1caa158220cb7dbc180bf
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/02/2020
ms.locfileid: "82730062"
---
# <a name="asynchronous-programming-in-ccx"></a>使用 C++/CX 异步编程
> [!NOTE]
> 本主题旨在帮助你维护 C++/CX 应用程序。 不过，我们建议你使用 [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md) 编写新应用程序。 C++/WinRT 是 Windows 运行时 (WinRT) API 的完全标准新式 C++17 语言投影，以基于标头文件的库的形式实现，旨在为你提供对新式 Windows API 的一流访问。

本文介绍了在 Visual C++ 组件扩展 (C++/CX) 中，通过使用在 ppltasks.h 中的 `concurrency` 命名空间中定义的 `task` 类来使用异步方法的推荐方式。

## <a name="universal-windows-platform-uwp-asynchronous-types"></a>通用 Windows 平台 (UWP) 异步类型
通用 Windows 平台 (UWP) 功能具有一个定义完善的用于调用异步方法的模型，并提供了使用此类方法所需的类型。 如果你对 UWP 异步模型不熟悉，请在阅读本文其余部分之前阅读[异步编程][AsyncProgramming]。

尽管可以直接在 c + + 中使用异步 Windows 运行时 Api，但首选的方法是使用[**任务类**][task-class]及其相关的类型和函数，这些类型和函数包含在[**并发**][concurrencyNamespace]命名空间中`<ppltasks.h>`并在中定义。 **concurrency::task** 是一个通用类型，但当使用 **/ZW** 编译器开关（对于通用 Windows 平台 (UWP) 应用和组件而言必不可少）时，task 类将封装 UWP 异步类型，以便更容易地完成以下工作：

-   将多个异步和同步操作结合在一起

-   在任务链中处理异常

-   在任务链中执行取消

-   确保个别任务在相应的线程上下文或单元中运行

本文提供了有关如何将 **task** 类与 UWP 异步 API 一起使用的基本指南。 有关**任务**及其相关方法（包括[**创建\_任务**][createTask]）的完整文档，请参阅[任务并行（并发运行时）][taskParallelism]。 

## <a name="consuming-an-async-operation-by-using-a-task"></a>通过任务使用异步操作
以下示例说明如何利用任务类来使用返回 [**IAsyncOperation**][IAsyncOperation] 接口且其操作会生成一个值的 **async** 方法。 下面是基本步骤：

1.  调用 `create_task` 方法并将其传递到 **IAsyncOperation^** 对象。

2.  对任务调用成员函数 [**task::then**][taskThen] 并提供一个将在异步操作完成时调用的 lambda。

``` cpp
#include <ppltasks.h>
using namespace concurrency;
using namespace Windows::Devices::Enumeration;
...
void App::TestAsync()
{    
    //Call the *Async method that starts the operation.
    IAsyncOperation<DeviceInformationCollection^>^ deviceOp =
        DeviceInformation::FindAllAsync();

    // Explicit construction. (Not recommended)
    // Pass the IAsyncOperation to a task constructor.
    // task<DeviceInformationCollection^> deviceEnumTask(deviceOp);

    // Recommended:
    auto deviceEnumTask = create_task(deviceOp);

    // Call the task's .then member function, and provide
    // the lambda to be invoked when the async operation completes.
    deviceEnumTask.then( [this] (DeviceInformationCollection^ devices )
    {       
        for(int i = 0; i < devices->Size; i++)
        {
            DeviceInformation^ di = devices->GetAt(i);
            // Do something with di...          
        }       
    }); // end lambda
    // Continue doing work or return...
}
```

由 [**task::then**][taskThen] 函数创建并返回的任务称为*延续*。 用户提供的 lambda 输入参数（在此情况下）是任务操作在完成时产生的结果。 它与你在直接使用 **IAsyncOperation** 接口时通过调用 [**IAsyncOperation::GetResults**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperation_TResult_#Windows_Foundation_IAsyncOperation_1_GetResults) 检索到的值相同。

[**task::then**][taskThen] 方法立即返回，并且其委托在异步工作成功完成后才运行。 在本示例中，如果异步操作导致引发异常，或由于取消请求而以取消状态结束，则延续永远不会执行。 稍后，我们将介绍如何编写即使上一个任务被取消或失败也会执行的延续。

尽管你在本地堆栈上声明任务变量，但它仍然管理其生存期，这样在其所有操作完成并且对其的所有引用离开作用域之前都不会被删除（即使该方法在操作完成之前返回）。

## <a name="creating-a-chain-of-tasks"></a>创建任务链
在异步编程中，常见的做法是定义一个操作序列，也称作*任务链*，其中每个延续只有在前一个延续完成后才能执行。 在某些情况下，上一个（或*先行*）任务会产生一个延续接受为输入的值。 通过使用 [**task::then**][taskThen] 方法，你可以按照直观且简单的方式创建任务链；该方法返回一个 **task<T>**，其中 **T** 是 lambda 函数的返回类型。 可以将多个继续符组合到任务链中：`myTask.then(…).then(…).then(…);`

当延续创建一个新的异步操作时，任务链尤其有用；此类任务称为异步任务。 以下示例将介绍具有两个延续的任务链。 初始任务获取一个现有文件的句柄，当该操作完成后，第一个延续会启动一个新的异步操作来删除该文件。 当该操作完成后，第二个延续将运行，并且输出一条确认消息。

``` cpp
#include <ppltasks.h>
using namespace concurrency;
...
void App::DeleteWithTasks(String^ fileName)
{    
    using namespace Windows::Storage;
    StorageFolder^ localFolder = ApplicationData::Current->LocalFolder;
    auto getFileTask = create_task(localFolder->GetFileAsync(fileName));

    getFileTask.then([](StorageFile^ storageFileSample) ->IAsyncAction^ {       
        return storageFileSample->DeleteAsync();
    }).then([](void) {
        OutputDebugString(L"File deleted.");
    });
}
```

上一个示例说明了四个要点：

-   第一个延续将 [**IAsyncAction^**][IAsyncAction] 对象转换为 **task<void>** 并返回 **task**。

-   第二个延续执行无错误处理，因此接受 **void** 而不是 **task<void>** 作为输入。 它是一个基于值的延续。

-   第二个延续在 [**DeleteAsync**][deleteAsync] 操作完成后才执行。

-   因为第二个延续是基于值的，所以如果通过调用 [**DeleteAsync**][deleteAsync] 启动的操作引发异常，则第二个延续根本不会执行。

**注意**  创建任务链只是一种使用**task**类编写异步操作的方法。 还可以通过使用联接和选择运算符**&&** 和**||** 来编写操作。 有关详细信息，请参阅[任务并行度（并发运行时）][taskParallelism]。

## <a name="lambda-function-return-types-and-task-return-types"></a>Lambda 函数返回类型和任务返回类型
在任务延续中，lambda 函数的返回类型包含在 **task** 对象中。 如果该 lambda 返回 **double**，则延续任务的类型为 **task<double>**。 但是，任务对象的设计目的是为了不生成无需嵌套的返回类型。 如果 lambda 返回 **IAsyncOperation&lt;SyndicationFeed^&gt;^**，则延续返回 **task&lt;SyndicationFeed^&gt;**，而不是 **task&lt;task&lt;SyndicationFeed^&gt;&gt;** 或 **task&lt;IAsyncOperation&lt;SyndicationFeed^&gt;^&gt;^**。 此过程称为*异步解包*，并且它还确保延续内部的异步操作在调用下一个延续之前完成。

请注意，在上一个示例中，即使其 lambda 返回 [**IAsyncInfo**][IAsyncInfo] 对象，该任务仍然会返回 **task<void>**。 下表总结了在 lambda 函数和封闭任务之间发生的类型转换：

| | |
|--------------------------------------------------------|---------------------|
| lambda 返回类型                                     | `.then`返回类型 |
| TResult                                                | 任务<TResult> |
| IAsyncOperation<TResult>^                        | 任务<TResult> |
| IAsyncOperationWithProgress&lt;TResult, TProgress&gt;^ | 任务<TResult> |
|IAsyncAction^                                           | 任务<void>    |
| IAsyncActionWithProgress<TProgress>^             |任务<void>     |
| 任务<TResult>                                    |任务<TResult>  |


## <a name="canceling-tasks"></a>取消任务
为用户提供取消异步操作的选项通常是一个不错的方法。 另外，在某些情况下，你可能必须以编程方式从任务链外部取消操作。 尽管每\*个**异步**返回类型都有一个从[**IAsyncInfo**][IAsyncInfo]继承的[**Cancel**][IAsyncInfoCancel]方法，但将其公开给外部方法是一项麻烦。 在任务链中支持取消的首选方法是使用[**取消\_标记\_源**](https://docs.microsoft.com/cpp/parallel/concrt/reference/cancellation-token-source-class)来创建[**取消\_标记**](https://docs.microsoft.com/cpp/parallel/concrt/reference/cancellation-token-class)，然后将该标记传递给初始任务的构造函数。 如果异步任务是使用取消标记创建的，并且调用[**了\_取消\_标记源：： cancel**](https://docs.microsoft.com/cpp/parallel/concrt/reference/cancellation-token-source-class?view=vs-2017) ，则该任务会自动调用**\* IAsync**操作上的**cancel**并将取消请求向下传递。 下面的伪代码演示了基本方法。

``` cpp
//Class member:
cancellation_token_source m_fileTaskTokenSource;

// Cancel button event handler:
m_fileTaskTokenSource.cancel();

// task chain
auto getFileTask2 = create_task(documentsFolder->GetFileAsync(fileName),
                                m_fileTaskTokenSource.get_token());
//getFileTask2.then ...
```

当任务被取消时，任务[**\_已取消**][taskCanceled]异常向下传播到任务链。 基于值的延续将不执行，但是基于任务的延续将在调用 [**task::get**][taskGet] 时导致引发异常。 如果有错误处理继续符，请确保它显式捕获**任务\_取消**异常。 （此异常不是派生自 [**Platform::Exception**](https://docs.microsoft.com/cpp/cppcx/platform-exception-class)。）

取消是协作式操作。 如果延续要执行一些长时间的工作，而不仅是调用 UWP 方法，则需要负责定期检查取消令牌的状态，并且在其被取消后停止执行。 清除了在延续中分配的所有资源后，调用 " [**\_取消当前\_任务**](https://docs.microsoft.com/cpp/parallel/concrt/reference/concurrency-namespace-functions?view=vs-2017)" 取消该任务并将取消传播到其后的任何基于值的延续。 下面是另外一个示例：你可以创建一个任务链用于表示 [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker) 操作的结果。 如果用户选择**取消**按钮，则不会调用 [**IAsyncInfo::Cancel**][IAsyncInfoCancel] 方法。 相反，操作成功，但返回 **nullptr**。 如果输入为**nullptr**，则继续可以测试输入参数并调用 " **\_取消当前\_任务**"。

有关详细信息，请参阅 [PPL 中的取消](https://docs.microsoft.com/cpp/parallel/concrt/cancellation-in-the-ppl)

## <a name="handling-errors-in-a-task-chain"></a>在任务链中处理错误
如果要让一个延续即使在先行被取消或引发异常的情况下也能够执行，请将该延续的 lambda 函数的输入指定为 **task<TResult>** 或 **task<void>**，使该延续成为基于任务的延续，但前提是先行任务的 lambda 返回 [**IAsyncAction^**][IAsyncAction]。

若要在任务链中处理错误和取消，则无需使每个延续成为基于任务的延续，也不用将每个可能引发异常的操作封装在 `try…catch` 块中。 相反，你可以将基于任务的延续添加至任务链的末尾，并且在那里处理所有错误。 任何异常（包括[**任务\_取消**][taskCanceled]异常）将传播任务链，并绕过任何基于值的延续，使你能够在基于错误处理任务的继续处理。 我们可以重写上一个示例以使用基于任务的错误处理延续：

``` cpp
#include <ppltasks.h>
void App::DeleteWithTasksHandleErrors(String^ fileName)
{    
    using namespace Windows::Storage;
    using namespace concurrency;

    StorageFolder^ documentsFolder = KnownFolders::DocumentsLibrary;
    auto getFileTask = create_task(documentsFolder->GetFileAsync(fileName));

    getFileTask.then([](StorageFile^ storageFileSample)
    {       
        return storageFileSample->DeleteAsync();
    })

    .then([](task<void> t)
    {

        try
        {
            t.get();
            // .get() didn' t throw, so we succeeded.
            OutputDebugString(L"File deleted.");
        }
        catch (Platform::COMException^ e)
        {
            //Example output: The system cannot find the specified file.
            OutputDebugString(e->Message->Data());
        }

    });
}
```

在基于任务的延续中，我们调用成员函数 [**task::get**][taskGet] 以获取任务结果。 即使该操作是不产生任何结果的 [**IAsyncAction**][IAsyncAction]，我们仍然需要调用 **task::get**，因为 **task::get** 也会获取已经向下传输到该任务的所有异常。 如果输入任务正在存储某个异常，则该异常将在调用 **task::get** 时被引发。 如果不调用**任务：： get**，或不在链的末尾使用基于任务的继续符，或者不捕获引发的异常类型，则在删除对任务的所有引用后，将引发**\_未观察到\_任务异常**。

只捕获你可以处理的异常。 如果你的应用遇到无法恢复的错误，则最好让该应用崩溃，而不要让其继续在未知状态下运行。 此外，一般情况下，不要尝试捕获**\_未观察到任务\_异常**本身。 该异常主要用于诊断目的。 引发**未观察到\_任务\_异常**时，它通常指示代码中的 bug。 原因通常是应该处理的异常，或由代码中的某个其他错误导致的不可恢复的异常。

## <a name="managing-the-thread-context"></a>管理线程上下文
UWP 应用的 UI 在单线程单元 (STA) 中运行。 其 lambda 返回 [**IAsyncAction**][IAsyncAction] 或 [**IAsyncOperation**][IAsyncOperation] 的任务具有单元意识。 如果该任务在 STA 中创建，则默认情况下，除非你另外指定，否则该任务的所有要运行的延续也将在该 STA 中运行。 换句话说，整个任务链从父任务继承单元意识。 该行为可帮助简化与 UI 控件的交互，这些 UI 控件只能从 STA 访问。

例如，在 UWP 应用中，在任何表示 XAML 页面的类的成员函数中，可从 [**task::then**][taskThen] 方法内部填充 [**ListBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListBox) 控件，而无需使用 [**Dispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) 对象。

``` cpp
#include <ppltasks.h>
void App::SetFeedText()
{    
    using namespace Windows::Web::Syndication;
    using namespace concurrency;
    String^ url = "http://windowsteamblog.com/windows_phone/b/wmdev/atom.aspx";
    SyndicationClient^ client = ref new SyndicationClient();
    auto feedOp = client->RetrieveFeedAsync(ref new Uri(url));

    create_task(feedOp).then([this]  (SyndicationFeed^ feed)
    {
        m_TextBlock1->Text = feed->Title->Text;
    });
}
```

如果任务不返回 [**IAsyncAction**][IAsyncAction] 或 [**IAsyncOperation**][IAsyncOperation]，则它不具有单元意识，并且默认情况下，其延续在第一个可用的后台线程上运行。

你可以通过使用任务的重载[**：：，然后**][taskThen]使用任务[**\_延续\_上下文**](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-continuation-context-class)来重写任一类型任务的默认线程上下文。 例如，在某些情况下，在后台线程上计划具有单元意识的任务的延续或许是可取的。 在这种情况下，可以传递[**任务\_延续\_上下文：：使用\_任意**][useArbitrary]来计划多线程单元中下一个可用线程上的任务工作。 这可以改善延续的性能，因为其工作不必与 UI 线程上发生的其他工作同步。

下面的示例演示了指定[**任务\_延续\_上下文的方法：：使用任意\_**][useArbitrary]选项，还演示了如何使用默认延续上下文来同步非线程安全集合上的并发操作。 在此代码中，我们遍历 RSS 源的 URL 列表，并为每个 URL 启动一个异步操作以检索源数据。 我们无法控制检索订阅的顺序，而我们其实并不关心。 当每个 [**RetrieveFeedAsync**](https://docs.microsoft.com/uwp/api/windows.web.syndication.isyndicationclient.retrievefeedasync) 操作完成时，第一个延续接受 [**SyndicationFeed^**](https://docs.microsoft.com/uwp/api/Windows.Web.Syndication.SyndicationFeed) 对象并使用它来初始化应用定义的 `FeedData^` 对象。 由于其中的每个操作都独立于其他操作，因此可以通过指定**任务\_延续\_上下文：\_：使用任意**延续上下文，来加快操作速度。 但是，在初始化每个 `FeedData` 对象之后，我们必须将其添加到一个不属于线程安全集合的 [**Vector**](https://docs.microsoft.com/cpp/cppcx/platform-collections-vector-class) 中。 因此，我们将创建一个延续并[**指定\_任务\_延续上下文：：\_使用 current**](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-continuation-context-class?view=vs-2017)以确保对[**追加**](https://docs.microsoft.com/uwp/api/windows.foundation.collections.ivector_t_.append)的所有调用都出现在同一应用程序单线程单元（ASTA）环境中。 由于[**任务\_延续\_上下文：： use\_default**](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-continuation-context-class?view=vs-2017)是默认上下文，因此，我们不需要显式指定该上下文，但为了清楚起见，我们在此处执行此操作。

``` cpp
#include <ppltasks.h>
void App::InitDataSource(Vector<Object^>^ feedList, vector<wstring> urls)
{
                using namespace concurrency;
    SyndicationClient^ client = ref new SyndicationClient();

    std::for_each(std::begin(urls), std::end(urls), [=,this] (std::wstring url)
    {
        // Create the async operation. feedOp is an
        // IAsyncOperationWithProgress<SyndicationFeed^, RetrievalProgress>^
        // but we don't handle progress in this example.

        auto feedUri = ref new Uri(ref new String(url.c_str()));
        auto feedOp = client->RetrieveFeedAsync(feedUri);

        // Create the task object and pass it the async operation.
        // SyndicationFeed^ is the type of the return value
        // that the feedOp operation will eventually produce.

        // Then, initialize a FeedData object by using the feed info. Each
        // operation is independent and does not have to happen on the
        // UI thread. Therefore, we specify use_arbitrary.
        create_task(feedOp).then([this]  (SyndicationFeed^ feed) -> FeedData^
        {
            return GetFeedData(feed);
        }, task_continuation_context::use_arbitrary())

        // Append the initialized FeedData object to the list
        // that is the data source for the items collection.
        // This all has to happen on the same thread.
        // By using the use_default context, we can append
        // safely to the Vector without taking an explicit lock.
        .then([feedList] (FeedData^ fd)
        {
            feedList->Append(fd);
            OutputDebugString(fd->Title->Data());
        }, task_continuation_context::use_default())

        // The last continuation serves as an error handler. The
        // call to get() will surface any exceptions that were raised
        // at any point in the task chain.
        .then( [this] (task<void> t)
        {
            try
            {
                t.get();
            }
            catch(Platform::InvalidArgumentException^ e)
            {
                //TODO handle error.
                OutputDebugString(e->Message->Data());
            }
        }); //end task chain

    }); //end std::for_each
}
```

嵌套任务（即在延续内部创建的新任务）不继承初始任务的单元意识。

## <a name="handing-progress-updates"></a>处理进度更新
在操作完成之前，支持 [**IAsyncOperationWithProgress**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_) 或 [**IAsyncActionWithProgress**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncActionWithProgress_TProgress_) 的方法会在操作执行过程中定期提供进度更新。 进度报告独立于任务和延续概念。 你只需为对象的 [**Progress**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_) 属性提供委托。 委派的一个典型用途是更新 UI 中的进度栏。

## <a name="related-topics"></a>相关主题
* [使用 C++/CX 为 UWP 应用创建异步操作](https://docs.microsoft.com/cpp/parallel/concrt/creating-asynchronous-operations-in-cpp-for-windows-store-apps)
* [Visual C++ 语言参考](https://docs.microsoft.com/cpp/cppcx/visual-c-language-reference-c-cx)
* [异步编程][AsyncProgramming]
* [任务并行（并发运行时）][taskParallelism]
* [concurrency::task](/cpp/parallel/concrt/reference/task-class)

<!-- LINKS -->
[AsyncProgramming]: <https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps> "AsyncProgramming"
[concurrencyNamespace]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/concurrency-namespace> "并发命名空间"
[createTask]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/concurrency-namespace-functions#create_task> "CreateTask"
[deleteAsync]: <https://msdn.microsoft.com/library/windows/apps/BR227199> "DeleteAsync"
[IAsyncAction]: <https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncaction.aspx> "IAsyncAction"
[IAsyncOperation]: <https://msdn.microsoft.com/library/windows/apps/BR206598> "IAsyncOperation"
[IAsyncInfo]: <https://msdn.microsoft.com/library/windows/apps/BR206587> "IAsyncInfo"
[IAsyncInfoCancel]: <https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncinfo.cancel> "IAsyncInfoCancel"
[taskCanceled]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/task-canceled-class> "TaskCancelled"
[task-class]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/task-class#get> "Task 类"
[taskGet]: <https://msdn.microsoft.com/library/windows/apps/xaml/hh750017.aspx> "TaskGet"
[taskParallelism]: <https://docs.microsoft.com/cpp/parallel/concrt/task-parallelism-concurrency-runtime> "任务并行度"
[taskThen]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/task-class#then> "TaskThen"
[useArbitrary]: <https://msdn.microsoft.com/library/windows/apps/xaml/hh750036.aspx> "UseArbitrary"
