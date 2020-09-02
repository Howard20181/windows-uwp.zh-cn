---
title: 与远程应用服务通信
description: 使用项目“Rome”与在远程设备上运行的应用服务交换消息。
ms.assetid: a0261e7a-5706-4f9a-b79c-46a3c81b136f
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10，uwp，连接的设备，远程系统，罗马，project 罗马，后台任务，应用服务
ms.localizationpriority: medium
ms.openlocfilehash: 779205a47b85cf9f9a0aec9db910b97995dc2cd8
ms.sourcegitcommit: c3ca68e87eb06971826087af59adb33e490ce7da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89363910"
---
# <a name="communicate-with-a-remote-app-service"></a>与远程应用服务通信

除了在远程设备上使用 URI 启动应用，还可以在远程设备上运行*应用服务*并与之通信。 任何基于 Windows 的设备均可用作客户端或主设备。 这可使你使用几乎无限种方法与已连接的设备交互，而无需在前台显示应用。

## <a name="set-up-the-app-service-on-the-host-device"></a>在主设备上设置应用服务
为了在远程设备上运行应用服务，必须已经在该设备上安装了该应用服务的提供程序。 本指南将使用 [Windows 通用示例存储库](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices)提供的[随机数字生成器应用服务示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices) CSharp 版。 有关如何编写你自己的应用服务的说明，请参阅[创建和使用应用服务](how-to-create-and-consume-an-app-service.md)。

无论是使用已制定的应用服务还是编写自己的应用服务，你都将需要执行一些编辑操作，以使该服务与远程系统兼容。 在 Visual Studio 中，转到应用服务提供程序的项目（在示例中称为“AppServicesProvider”），然后选择其 _Package.appxmanifest_ 文件。 右键单击并选择 " **查看代码** " 以查看文件的全部内容。 在主**应用程序**元素内创建**extension**元素 (或查找该元素（如果该元素已存在) ）。 然后创建一个 **扩展** ，将该项目定义为应用服务并引用其父项目。

``` xml
...
<Extensions>
    <uap:Extension Category="windows.appService" EntryPoint="RandomNumberService.RandomNumberGeneratorTask">
        <uap3:AppService Name="com.microsoft.randomnumbergenerator"/>
    </uap:Extension>
</Extensions>
...
```

在 **AppService** 元素的旁边，添加 **SupportsRemoteSystems** 属性：

``` xml
...
<uap3:AppService Name="com.microsoft.randomnumbergenerator" SupportsRemoteSystems="true"/>
...
```

若要使用此 **uap3** 命名空间中的元素，您必须将命名空间定义添加到清单文件的顶部（如果它尚不存在）。

```xml
<?xml version="1.0" encoding="utf-8"?>
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3">
  ...
</Package>
```

然后生成应用服务提供程序项目，并将其部署到)  (的主机设备。

## <a name="target-the-app-service-from-the-client-device"></a>从客户端设备定向应用服务
从其中调用远程应用服务的设备需要具有远程系统功能的应用。 此功能可添加到在主设备上提供应用服务的相同应用（在此情况下，需要在两台设备上安装相同应用），或者在完全不同的应用中实现。

本节中的代码需要以下 **using** 语句以按原样运行：

:::code language="csharp" source="~/../snippets-windows/windows-uwp/launch-resume/RemoteAppService/cs/MainPage.xaml.cs" id="SnippetUsings":::


首先必须实例化 [**AppServiceConnection**](/uwp/api/Windows.ApplicationModel.AppService.AppServiceConnection) 对象，就像本地调用应用服务一样。 [创建和使用应用服务](how-to-create-and-consume-an-app-service.md)对此过程做了更为详细的介绍。 在此示例中，要定向的应用服务是随机数字生成器服务。

> [!NOTE]
> 假定在调用以下方法的代码中已通过某种方法获取了 [RemoteSystem](/uwp/api/Windows.System.RemoteSystems.RemoteSystem) 对象。 有关如何设置的说明，请参阅[启动远程应用](launch-a-remote-app.md)。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/launch-resume/RemoteAppService/cs/MainPage.xaml.cs" id="SnippetAppService":::

接下来，为计划的远程设备创建 [**RemoteSystemConnectionRequest**](/uwp/api/Windows.System.RemoteSystems.RemoteSystemConnectionRequest) 对象。 随后它将用于向该设备打开 **AppServiceConnection**。 请注意，在以下示例中，极大地简化了错误处理和报告以实现简便性。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/launch-resume/RemoteAppService/cs/MainPage.xaml.cs" id="SnippetRemoteConnection":::

此时，在远程计算机上，你应该拥有应用服务的开放连接。

## <a name="exchange-service-specific-messages-over-the-remote-connection"></a>通过远程连接交换特定于服务的消息

在此处，你可以 [**ValueSet**](/uwp/api/windows.foundation.collections.valueset) 对象的形式发送或接收通过该服务传递的消息（有关详细信息，请参阅[创建和使用应用服务](how-to-create-and-consume-an-app-service.md)）。 随机数字生成器服务采用将密钥 `"minvalue"` 和 `"maxvalue"` 用作输入的两个整数、随机选择这两个整数范围内的一个整数，并通过密钥 `"Result"` 将其返回到调用进程。

:::code language="csharp" source="~/../snippets-windows/windows-uwp/launch-resume/RemoteAppService/cs/MainPage.xaml.cs" id="SnippetSendMessage":::

此时你已连接到目标主设备上的应用服务、已在该设备上运行操作，并作为响应收到了客户端设备的数据。

## <a name="related-topics"></a>相关主题

[已连接的应用和设备 (项目罗马) 概述](connected-apps-and-devices.md)  
[启动远程应用](launch-a-remote-app.md)  
[创建和使用应用服务](how-to-create-and-consume-an-app-service.md)  
[远程系统 API 参考](/uwp/api/Windows.System.RemoteSystems)  
[远程系统示例](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/RemoteSystems)
