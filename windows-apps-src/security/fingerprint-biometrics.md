---
title: 指纹生物识别
description: 本文介绍了如何将指纹生物识别添加到通用 Windows 平台 (UWP) 应用中。
ms.assetid: 55483729-5F8A-401A-8072-3CD611DDFED2
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 安全性
ms.localizationpriority: medium
ms.openlocfilehash: bcb82280d80467157faa8aa5195ad7b9d9a7336b
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89167211"
---
# <a name="fingerprint-biometrics"></a>指纹生物识别




本文介绍了如何将指纹生物识别添加到通用 Windows 平台 (UWP) 应用中。 在用户必须同意特定操作时将指纹身份验证请求囊括在内，将提升应用的安全性。 例如，可在授权应用内购买或对受限资源的访问权限之前要求指纹身份验证。 使用 UserConsentVerifier 命名空间中的[**UserConsentVerifier**](/uwp/api/Windows.Security.Credentials.UI.UserConsentVerifier)类管理指纹身份[**验证。**](/uwp/api/Windows.Security.Credentials.UI)

## <a name="check-the-device-for-a-fingerprint-reader"></a>检查设备以寻找指纹读取器


若要查找设备是否安装了指纹读取器，请调用 [**UserConsentVerifier.CheckAvailabilityAsync**](/uwp/api/windows.security.credentials.ui.userconsentverifier.checkavailabilityasync)。 即使设备支持指纹身份验证，应用仍应当在“设置”中向用户提供相应选项，以便启用或禁用指纹身份验证。

```cs
public async System.Threading.Tasks.Task<string> CheckFingerprintAvailability()
{
    string returnMessage = "";

    try
    {
        // Check the availability of fingerprint authentication.
        var ucvAvailability = await Windows.Security.Credentials.UI.UserConsentVerifier.CheckAvailabilityAsync();

        switch (ucvAvailability)
        {
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.Available:
                returnMessage = "Fingerprint verification is available.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.DeviceBusy:
                returnMessage = "Biometric device is busy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.DeviceNotPresent:
                returnMessage = "No biometric device found.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.DisabledByPolicy:
                returnMessage = "Biometric verification is disabled by policy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.NotConfiguredForUser:
                returnMessage = "The user has no fingerprints registered. Please add a fingerprint to the " +
                                "fingerprint database and try again.";
                break;
            default:
                returnMessage = "Fingerprints verification is currently unavailable.";
                break;
        }
    }
    catch (Exception ex)
    {
        returnMessage = "Fingerprint authentication availability check failed: " + ex.ToString();
    }

    return returnMessage;
}
```

## <a name="request-consent-and-return-results"></a>请求同意并返回结果


若要请求来自指纹扫描的用户同意，请调用 [**UserConsentVerifier.RequestVerificationAsync**](/uwp/api/windows.security.credentials.ui.userconsentverifier.requestverificationasync) 方法。 要使指纹身份验证起效，用户必须预先将指纹“签名”添加到指纹数据库中。

当你调用 [**UserConsentVerifier.RequestVerificationAsync**](/uwp/api/windows.security.credentials.ui.userconsentverifier.requestverificationasync) 时，将向用户呈现一个请求指纹扫描的模式对话框。 你可以向 **UserConsentVerifier.RequestVerificationAsync** 方法提供一条消息，此消息将作为模式对话框的一部分显示给用户，如下图所示。

```cs
private async System.Threading.Tasks.Task<string> RequestConsent(string userMessage)
{
    string returnMessage;

    if (String.IsNullOrEmpty(userMessage))
    {
        userMessage = "Please provide fingerprint verification.";
    }

    try
    {
        // Request the logged on user's consent via fingerprint swipe.
        var consentResult = await Windows.Security.Credentials.UI.UserConsentVerifier.RequestVerificationAsync(userMessage);

        switch (consentResult)
        {
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.Verified:
                returnMessage = "Fingerprint verified.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.DeviceBusy:
                returnMessage = "Biometric device is busy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.DeviceNotPresent:
                returnMessage = "No biometric device found.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.DisabledByPolicy:
                returnMessage = "Biometric verification is disabled by policy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.NotConfiguredForUser:
                returnMessage = "The user has no fingerprints registered. Please add a fingerprint to the " +
                                "fingerprint database and try again.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.RetriesExhausted:
                returnMessage = "There have been too many failed attempts. Fingerprint authentication canceled.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.Canceled:
                returnMessage = "Fingerprint authentication canceled.";
                break;
            default:
                returnMessage = "Fingerprint authentication is currently unavailable.";
                break;
        }
    }
    catch (Exception ex)
    {
        returnMessage = "Fingerprint authentication failed: " + ex.ToString();
    }

    return returnMessage;
}
```