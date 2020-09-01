---
ms.assetid: 9146212C-8480-4C16-B74C-D7F08C7086AF
description: 本文向你演示了如何枚举 MIDI（乐器数字接口）设备以及从通用 Windows 应用发送和接收 MIDI 消息。
title: MIDI
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 871a8d1d8458decbd0b398847d7d9de57234ee3d
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89163761"
---
# <a name="midi"></a>MIDI



本文向你演示了如何枚举 MIDI（乐器数字接口）设备以及从通用 Windows 应用发送和接收 MIDI 消息。 Windows 10 支持 MIDI over USB (符合类和大多数专有驱动程序) 、通过蓝牙 LE (Windows 10 周年 Edition 和更高版本) ，以及通过以太网和路由的 midi 提供的免费第三方产品。

## <a name="enumerate-midi-devices"></a>枚举 MIDI 设备

在枚举和使用 MIDI 设备之前，将以下命名空间添加到你的项目。

[!code-cs[Using](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetUsing)]

将 [**ListBox**](/uwp/api/Windows.UI.Xaml.Controls.ListBox) 控件添加到你的 XAML 页面，该控件允许用户选择附加到系统的 MIDI 输入设备之一。 添加另一个控件以列出 MIDI 输出设备。

[!code-xml[MidiListBoxes](./code/MIDIWin10/cs/MainPage.xaml#SnippetMidiListBoxes)]

[**FindAllAsync**](/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) 方法的 [**DeviceInformation**](/uwp/api/Windows.Devices.Enumeration.DeviceInformation) 类用于枚举许多不同类型的由 Windows 识别的设备。 若要指明你仅希望使用该方法查找 MIDI 输入设备，请使用 [**MidiInPort.GetDeviceSelector**](/uwp/api/windows.devices.midi.midiinport.getdeviceselector) 返回的选择器字符串。 **FindAllAsync** 将返回包含通过系统注册的每个 MIDI 输入设备的 **DeviceInformation** 的 [**DeviceInformationCollection**](/uwp/api/Windows.Devices.Enumeration.DeviceInformationCollection)。 如果返回的集合不包含任何项，则没有可用的 MIDI 输入设备。 如果集合中包含项，循环浏览 **DeviceInformation** 对象，并将每台设备的名称添加到 MIDI 输入设备 **ListBox**。

[!code-cs[EnumerateMidiInputDevices](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetEnumerateMidiInputDevices)]

枚举 MIDI 输出设备与枚举输入设备的工作原理完全相同，不同之处在于调用 **FindAllAsync** 时，你应该指定由 [**MidiOutPort.GetDeviceSelector**](/uwp/api/windows.devices.midi.midioutport.getdeviceselector) 返回的选择器字符串。

[!code-cs[EnumerateMidiOutputDevices](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetEnumerateMidiOutputDevices)]



## <a name="create-a-device-watcher-helper-class"></a>创建设备观察程序帮助程序类

[**Windows.Devices.Enumeration**](/uwp/api/Windows.Devices.Enumeration) 命名空间提供了 [**DeviceWatcher**](/uwp/api/Windows.Devices.Enumeration.DeviceWatcher)，它可在设备已在系统中添加或删除时，或者设备的信息已更新时通知你的应用。 因为支持 MIDI 的应用通常会关注输入和输出设备，所以此示例将创建可实现 **DeviceWatcher** 模式的帮助程序类，以便可针对 MIDI 输入和 MIDI 输出设备使用相同的代码，从而避免重复。

将新类添加到你的项目以作为设备观察程序。 在此示例中，该类名为 **MyMidiDeviceWatcher**。 此部分中的剩余代码将用于实现帮助程序类。

将某些成员变量添加到类中：

-   监视设备更改的 [**DeviceWatcher**](/uwp/api/Windows.Devices.Enumeration.DeviceWatcher) 对象。
-   包含一个实例的 MIDI 输入端口选择器字符串和另一个实例的 MIDI 输出端口选择器的设备选择器字符串。
-   使用可用设备的名称填充的 [**ListBox**](/uwp/api/Windows.UI.Xaml.Controls.ListBox) 控件。
-   从除 UI 线程之外的某个线程更新 UI 所需的 [**CoreDispatcher**](/uwp/api/Windows.UI.Core.CoreDispatcher)。

[!code-cs[WatcherVariables](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherVariables)]

添加用于从帮助程序类外部访问当前设备列表的 [**DeviceInformationCollection**](/uwp/api/Windows.Devices.Enumeration.DeviceInformationCollection) 属性。

[!code-cs[DeclareDeviceInformationCollection](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetDeclareDeviceInformationCollection)]

在类构造函数中，调用方将传入 MIDI 设备选择器字符串、用于列出设备的 **ListBox**，以及更新 UI 所需的 **Dispatcher**。

调用 [**DeviceInformation.CreateWatcher**](/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher) 以创建 **DeviceWatcher** 类的新实例，从而传入 MIDI 设备选择器字符串。

为观察程序的事件处理程序注册处理程序。

[!code-cs[WatcherConstructor](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherConstructor)]

**DeviceWatcher** 具有以下事件：

-   [**Added**](/uwp/api/windows.devices.enumeration.devicewatcher.added) - 在新设备添加到系统中时引发。
-   [**Removed**](/uwp/api/windows.devices.enumeration.devicewatcher.removed) - 在从系统中删除设备时引发。
-   [**Updated**](/uwp/api/windows.devices.enumeration.devicewatcher.updated) - 在更新与现有设备关联的信息时引发。
-   [**EnumerationCompleted**](/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted) - 在观察程序完成其所请求的设备类型枚举时引发。

在以上每个事件的事件处理程序中，将调用帮助程序方法 **UpdateDevices**，以通过当前设备列表更新 **ListBox**。 因为未在 UI 线程上调用 **UpdateDevices** 更新 UI 元素和这些事件处理程序，因此每个调用都必须打包在对 [**RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) 的调用中，这将导致指定代码在 UI 线程上运行。

[!code-cs[WatcherEventHandlers](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherEventHandlers)]

**UpdateDevices** 帮助程序方法调用 [**DeviceInformation.FindAllAsync**](/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) 并通过已返回设备的名称更新 **ListBox**，如本文的前面部分所述。

[!code-cs[WatcherUpdateDevices](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherUpdateDevices)]

添加多个方法，若要启动观察程序，使用 **DeviceWatcher** 对象的 [**Start**](/uwp/api/windows.devices.enumeration.devicewatcher.start) 方法；若要停止观察程序，使用 [**Stop**](/uwp/api/windows.devices.enumeration.devicewatcher.stop) 方法。

[!code-cs[WatcherStopStart](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherStopStart)]

提供析构函数以取消注册观察程序事件处理程序，并将设备观察程序设置为 null。

[!code-cs[WatcherDestructor](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherDestructor)]

## <a name="create-midi-ports-to-send-and-receive-messages"></a>创建 MIDI 端口以发送和接收消息

在页面的代码隐藏中，声明成员变量以保留 **MyMidiDeviceWatcher** 帮助程序类的两个实例：一个用于输入设备，一个用于输出设备。

[!code-cs[DeclareDeviceWatchers](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetDeclareDeviceWatchers)]

创建观察程序帮助程序类的新实例，从而传入设备选择器字符串、要填充的 **ListBox**，以及可通过页面的 **Dispatcher** 属性访问的 **CoreDispatcher** 对象。 然后，调用该方法以启动每个对象的 **DeviceWatcher**。

在启动每个 **DeviceWatcher** 后不久，它将完成枚举连接到系统的当前设备，并引发其 [**EnumerationCompleted**](/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted) 事件，这将导致使用当前 MIDI 设备更新每个 **ListBox**。

[!code-cs[StartWatchers](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetStartWatchers)]

当用户在 MIDI 输入 **ListBox** 中选择某一项时，将引发 [**SelectionChanged**](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) 事件。 在此事件的处理程序中，访问帮助程序类的 **DeviceInformationCollection** 属性以获取当前设备列表。 如果列表中存在条目，使用对应于 **ListBox** 控件 [**SelectedIndex**](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex) 的索引选择 **DeviceInformation** 对象。

通过调用 [**MidiInPort.FromIdAsync**](/uwp/api/windows.devices.midi.midiinport.fromidasync) 创建表示所选输入设备的 [**MidiInPort**](/uwp/api/Windows.Devices.Midi.MidiInPort) 对象，从而传入所选设备的 [**Id**](/uwp/api/windows.devices.enumeration.deviceinformation.id) 属性。

为 [**MessageReceived**](/uwp/api/windows.devices.midi.midiinport.messagereceived) 事件注册处理程序，该事件将在通过指定的设备接收 MIDI 消息时引发。

[!code-cs[DeclareMidiPorts](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetDeclareMidiPorts)]

[!code-cs[InPortSelectionChanged](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetInPortSelectionChanged)]

调用 **MessageReceived** 处理程序时，该消息将包含在 **MidiMessageReceivedEventArgs** 的 [**Message**](/uwp/api/Windows.Devices.Midi.MidiMessageReceivedEventArgs) 属性中。 消息对象的 [**Type**](/uwp/api/windows.devices.midi.imidimessage.type) 是 [**MidiMessageType**](/uwp/api/Windows.Devices.Midi.MidiMessageType) 枚举中的值，用于指示已接收的消息类型。 消息数据取决于消息类型。 此示例可查看该消息是否是消息相关注释；如果是，则输出该消息的 MIDI 通道、注释和速度。

[!code-cs[MessageReceived](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetMessageReceived)]

输出设备 **ListBox** 的 [**SelectionChanged**](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) 处理程序与输入设备的处理程序的工作原理相同，不同之处在于未注册任何事件处理程序。

[!code-cs[OutPortSelectionChanged](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetOutPortSelectionChanged)]

创建输出设备后，通过针对要发送的消息类型创建新的 [**IMidiMessage**](/uwp/api/Windows.Devices.Midi.IMidiMessage)，可以发送一条消息。 在此示例中，该消息为 [**NoteOnMessage**](/uwp/api/Windows.Devices.Midi.MidiNoteOnMessage)。 调用 [**IMidiOutPort**](/uwp/api/Windows.Devices.Midi.IMidiOutPort) 对象的 [**SendMessage**](/uwp/api/windows.devices.midi.imidioutport.sendmessage) 方法以发送消息。

[!code-cs[SendMessage](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetSendMessage)]

当你的应用停用时，请务必清理你的应用资源。 取消注册你的事件处理程序，并将 MIDI 输入端口和输出端口对象设置为 null。 停止设备观察程序并将其设置为 null。

[!code-cs[CleanUp](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetCleanUp)]

## <a name="using-the-built-in-windows-general-midi-synth"></a>使用内置的 Windows 通用 MIDI 合成器

当你使用上面所述的技术枚举输出 MIDI 设备时，你的应用会发现一个名为“Microsoft GS 波形表合成器”的 MIDI 设备。 这是一款你可以从应用中使用的内置通用 MIDI 合成器。 但是，如果没有将该内置合成器的 SDK 扩展包含在你的项目中，将无法为此设备创建 MIDI 输出端口。

**将通用 MIDI 合成器 SDK 扩展包含在应用项目中**

1.  在 **“解决方案资源管理器”** 中，在你的项目下，右键单击 **“引用”**，然后选择 **“添加引用...”**
2.  展开 **“通用 Windows”** 节点。
3.  选择“扩展”。
4.  从扩展列表中，选择“适用于通用 Windows 应用的 Microsoft 通用 MIDI DLS”****。
    > [!NOTE] 
    > 如果存在多个版本的扩展，请务必选择与应用所面向的目标匹配的版本。 你可以在项目“属性”的“应用程序”**** 选项卡上查看应用所面向的 SDK 版本。

 

 