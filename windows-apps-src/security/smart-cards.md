---
title: 智能卡
description: 本主题介绍了通用 Windows 平台 (UWP) 应用如何使用智能卡将用户连接到安全网络服务，包括如何访问物理智能卡读卡器、创建虚拟智能卡、与智能卡通信、对用户进行身份验证、重置用户 PIN 及移除智能卡或断开智能卡连接。
ms.assetid: 86524267-50A0-4567-AE17-35C4B6D24745
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 安全性
ms.localizationpriority: medium
ms.openlocfilehash: 5c792cde951b2bf01585256f51f67a545d96510d
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89170831"
---
# <a name="smart-cards"></a>智能卡




本主题介绍了通用 Windows 平台 (UWP) 应用如何使用智能卡将用户连接到安全网络服务，包括如何访问物理智能卡读卡器、创建虚拟智能卡、与智能卡通信、对用户进行身份验证、重置用户 PIN 及移除智能卡或断开智能卡连接。 

## <a name="configure-the-app-manifest"></a>配置应用清单


必须先在项目 Package.appxmanifest 文件中设置“共享的用户证书”**** 功能，应用才可以使用智能卡或虚拟智能卡对用户进行身份验证。

## <a name="access-connected-card-readers-and-smart-cards"></a>访问连接的卡读取器和智能卡


你可以通过向 [**SmartCardReader.FromIdAsync**](/uwp/api/windows.devices.smartcards.smartcardreader.fromidasync) 方法传递设备 ID（在 [**DeviceInformation**](/uwp/api/Windows.Devices.Enumeration.DeviceInformation) 中指定）来查询读取器和连接的智能卡。 若要访问当前连接到返回的读取器设备的智能卡，请调用 [**SmartCardReader.FindAllCardsAsync**](/uwp/api/windows.devices.smartcards.smartcardreader.findallcardsasync)。

```cs
string selector = SmartCardReader.GetDeviceSelector();
DeviceInformationCollection devices =
    await DeviceInformation.FindAllAsync(selector);

foreach (DeviceInformation device in devices)
{
    SmartCardReader reader =
        await SmartCardReader.FromIdAsync(device.Id);

    // For each reader, we want to find all the cards associated
    // with it.  Then we will create a SmartCardListItem for
    // each (reader, card) pair.
    IReadOnlyList<SmartCard> cards =
        await reader.FindAllCardsAsync();
}
```

你还应当通过实现处理插卡上的应用行为的方法使你的应用能够观察 [**CardAdded**](/uwp/api/windows.devices.smartcards.smartcardreader.cardadded) 事件。

```cs
private void reader_CardAdded(SmartCardReader sender, CardAddedEventArgs args)
{
  // A card has been inserted into the sender SmartCardReader.
}
```

然后你可以将每个返回的 [**SmartCard**](/uwp/api/Windows.Devices.SmartCards.SmartCard) 对象传递到 [**SmartCardProvisioning**](/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) 以访问使你的应用可以访问并自定义其配置的方法。

## <a name="create-a-virtual-smart-card"></a>创建虚拟智能卡


若要使用 [**SmartCardProvisioning**](/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) 创建虚拟智能卡，你的应用将首先需要提供一个昵称、一个管理员密钥和一个 [**SmartCardPinPolicy**](/uwp/api/Windows.Devices.SmartCards.SmartCardPinPolicy)。 昵称一般向应用提供，但你的应用将仍需要提供一个管理员密钥并生成一个当前 **SmartCardPinPolicy** 的实例，然后才能将所有三个值传递到 [**RequestVirtualSmartCardCreationAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcardcreationasync)。

1.  新建 [**SmartCardPinPolicy**](/uwp/api/Windows.Devices.SmartCards.SmartCardPinPolicy) 的实例
2.  通过调用由该服务或管理工具提供的管理密钥值上的 [**CryptographicBuffer.GenerateRandom**](/uwp/api/windows.security.cryptography.cryptographicbuffer.generaterandom) 来生成管理密钥值。
3.  将这些值与 *FriendlyNameText* 字符串一起传递到 [**RequestVirtualSmartCardCreationAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcardcreationasync)。

```cs
SmartCardPinPolicy pinPolicy = new SmartCardPinPolicy();
pinPolicy.MinLength = 6;

IBuffer adminkey = CryptographicBuffer.GenerateRandom(24);

SmartCardProvisioning provisioning = await
     SmartCardProvisioning.RequestVirtualSmartCardCreationAsync(
          "Card friendly name",
          adminkey,
          pinPolicy);
```

[**RequestVirtualSmartCardCreationAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcardcreationasync) 返回关联的 [**SmartCardProvisioning**](/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) 对象之后，将设置虚拟智能卡并且为使用做好准备。

>[!NOTE]
>若要使用 UWP 应用创建虚拟智能卡，运行该应用的用户必须是 administrators 组的成员。 如果用户不是 administrators 组的成员，则虚拟智能卡的创建将失败。

## <a name="handle-authentication-challenges"></a>处理身份验证质询


要使用智能卡或虚拟智能卡进行身份验证，你的应用必须提供行为以完成存储在卡上的管理密钥数据和由身份验证服务器或管理工具维护的管理密钥数据之间的质询。

以下代码显示了如何支持服务的智能卡身份验证或者物理或虚拟卡详细信息修改。 如果使用卡上的管理员密钥生成的数据（“challenge”）与由服务器或管理工具提供的管理员密钥数据（“adminkey”）相同，则身份验证成功。

```cs
static class ChallengeResponseAlgorithm
{
    public static IBuffer CalculateResponse(IBuffer challenge, IBuffer adminkey)
    {
        if (challenge == null)
            throw new ArgumentNullException("challenge");
        if (adminkey == null)
            throw new ArgumentNullException("adminkey");

        SymmetricKeyAlgorithmProvider objAlg = SymmetricKeyAlgorithmProvider.OpenAlgorithm(SymmetricAlgorithmNames.TripleDesCbc);
        var symmetricKey = objAlg.CreateSymmetricKey(adminkey);
        var buffEncrypted = CryptographicEngine.Encrypt(symmetricKey, challenge, null);
        return buffEncrypted;
    }
}
```

你将看到这个在本主题的剩余部分中被多次引用的代码是我们审查如何完成身份验证操作以及如何将更改应用到智能卡和虚拟智能卡信息。

## <a name="verify-smart-card-or-virtual-smart-card-authentication-response"></a>验证智能卡或虚拟智能卡身份验证响应


现在我们已定义了身份验证质询的逻辑，我们可以与读取器进行通信以访问智能卡，或者访问虚拟智能卡进行身份验证。

1.  要开始质询，请从与智能卡关联的 [**SmartCardProvisioning**](/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) 对象调用 [**GetChallengeContextAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.getchallengecontextasync)。 这将生成一个 [**SmartCardChallengeContext**](/uwp/api/Windows.Devices.SmartCards.SmartCardChallengeContext) 的实例，此实例包含卡的 [**Challenge**](/uwp/api/windows.devices.smartcards.smartcardchallengecontext.challenge) 值。

2.  接着，将由服务或管理工具提供的卡的质询值和管理员密钥传递到我们在之前示例中定义的 **ChallengeResponseAlgorithm** 中。

3.  如果身份验证成功，[**VerifyResponseAsync**](/uwp/api/windows.devices.smartcards.smartcardchallengecontext.verifyresponseasync) 将返回 **true**。

```cs
bool verifyResult = false;
SmartCard card = await rootPage.GetSmartCard();
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

using (SmartCardChallengeContext context =
       await provisioning.GetChallengeContextAsync())
{
    IBuffer response = ChallengeResponseAlgorithm.CalculateResponse(
        context.Challenge,
        rootPage.AdminKey);

    verifyResult = await context.VerifyResponseAsync(response);
}
```

## <a name="change-or-reset-a-user-pin"></a>更改或重置用户 PIN


更改与智能卡关联的 PIN 的步骤：

1.  访问该卡并生成关联的 [**SmartCardProvisioning**](/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) 对象。
2.  调用 [**RequestPinChangeAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestpinchangeasync) 来向用户显示 UI 以完成此操作。
3.  如果 PIN 成功更改，调用将返回 **true**。

```cs
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

bool result = await provisioning.RequestPinChangeAsync();
```

请求 PIN 重置的步骤：

1.  调用 [**RequestPinResetAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestpinresetasync) 以启动操作。 此调用包括一个表示智能卡和 PIN 重置请求的 [**SmartCardPinResetHandler**](/uwp/api/windows.devices.smartcards.smartcardpinresethandler) 方法。
2.  [**SmartCardPinResetHandler**](/uwp/api/windows.devices.smartcards.smartcardpinresethandler) 提供我们的 **ChallengeResponseAlgorithm**（包装在 [**SmartCardPinResetDeferral**](/uwp/api/Windows.Devices.SmartCards.SmartCardPinResetDeferral) 调用中）用于比较卡的质询值和由服务或管理工具提供的管理员密钥的信息，以便对请求进行身份验证。

3.  如果质询成功，[**RequestPinResetAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestpinresetasync) 调用将完成；如果成功重置 PIN，将返回 **true**。

```cs
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

bool result = await provisioning.RequestPinResetAsync(
    (pinResetSender, request) =>
    {
        SmartCardPinResetDeferral deferral =
            request.GetDeferral();

        try
        {
            IBuffer response =
                ChallengeResponseAlgorithm.CalculateResponse(
                    request.Challenge,
                    rootPage.AdminKey);
            request.SetResponse(response);
        }
        finally
        {
            deferral.Complete();
        }
    });
}
```

## <a name="remove-a-smart-card-or-virtual-smart-card"></a>删除智能卡或虚拟智能卡


当删除物理智能卡时，[**CardRemoved**](/uwp/api/windows.devices.smartcards.smartcardreader.cardremoved) 事件将在删除卡时引发。

使用将应用在卡或读取器删除上的行为定义为事件处理程序的方法，将此事件的引发与卡读取器关联。 此行为可以与向用户提供卡已删除的通知同样简单。

```cs
reader = card.Reader;
reader.CardRemoved += HandleCardRemoved;
```

虚拟智能卡的删除以编程方式处理，方法是先检索卡，然后从 [**SmartCardProvisioning**](/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) 返回的对象调用 [**RequestVirtualSmartCardDeletionAsync**](/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcarddeletionasync)。

```cs
bool result = await SmartCardProvisioning
    .RequestVirtualSmartCardDeletionAsync(card);
```