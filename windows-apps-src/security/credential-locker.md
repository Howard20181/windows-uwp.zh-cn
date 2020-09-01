---
title: 凭据保险箱
description: 本文介绍了通用 Windows 平台 (UWP) 应用可如何使用凭据保险箱安全存储和检索用户凭据，并使用用户的 Microsoft 帐户在设备间漫游用户凭据。
ms.assetid: 7BCC443D-9E8A-417C-B275-3105F5DED863
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, 安全性
ms.localizationpriority: medium
ms.openlocfilehash: 1a9f47a0f48d00c898bdb6df71f5cbd858b03bc4
ms.sourcegitcommit: 7b2febddb3e8a17c9ab158abcdd2a59ce126661c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89167261"
---
# <a name="credential-locker"></a>凭据保险箱




本文介绍了通用 Windows 平台 (UWP) 应用可如何使用凭据保险箱安全存储和检索用户凭据，并使用用户的 Microsoft 帐户在设备间漫游用户凭据。

例如，你有一个连接到服务以访问受保护资源（如媒体文件或社交网络）的应用。 你的服务需要每位用户的登录信息。 你已在应用中构建了 UI，此 UI 可获取每位用户的用户名和密码，然后使用这些信息使用户登录到服务中。 使用保险箱 API 可为用户存储用户名和密码，并在他们下次打开应用时轻松地检索它们并自动使用户登录（不论他们在什么设备上）。

存储在 CredentialLocker 中的用户凭据*不会*过期，*不会*受 [**ApplicationData.RoamingStorageQuota**](/uwp/api/windows.storage.applicationdata.roamingstoragequota) 影响，并且*不会*因传统漫游数据等不活动而被清除。 但是，CredentialLocker 中的每个应用最多只能存储 20 个凭据。

对于域帐户来说，凭据保险箱的工作方式略有不同。 如果有凭据与 Microsoft 帐户一起存储，并且将该帐户与域帐户关联（如作时使用的帐户），凭据将漫游至该域帐户。 但是，任何在使用域帐户登录时添加的新凭据不会漫游。 这确保了该域的私有凭据不会暴露在域之外。

## <a name="storing-user-credentials"></a>存储用户凭据


1.  使用来自 [**Windows.Security.Credentials**](/uwp/api/Windows.Security.Credentials) 命名空间的 [**PasswordVault**](/uwp/api/Windows.Security.Credentials.PasswordVault) 对象获取对凭据保险箱的引用。
2.  创建包含你的应用的标识符、用户名和密码的 [**PasswordCredential**](/uwp/api/Windows.Security.Credentials.PasswordCredential) 对象，并将该对象传递至 [**PasswordVault.Add**](/uwp/api/windows.security.credentials.passwordvault.add) 方法以将凭据添加到保险箱。

```cs
var vault = new Windows.Security.Credentials.PasswordVault();
vault.Add(new Windows.Security.Credentials.PasswordCredential(
    "My App", username, password));
```

## <a name="retrieving-user-credentials"></a>检索用户凭据


在你拥有对 [**PasswordVault**](/uwp/api/Windows.Security.Credentials.PasswordVault) 对象的引用之后，你有多个选项从凭据保险箱检索用户凭据。

-   你可以使用 [**PasswordVault.RetrieveAll**](/uwp/api/windows.security.credentials.passwordvault.retrieveall) 方法检索用户在保险箱中为你的应用提供的所有凭据。

-   如果你知道存储的凭据的用户名，你可以使用 [**PasswordVault.FindAllByUserName**](/uwp/api/windows.security.credentials.passwordvault.findallbyusername) 方法检索该用户名的所有凭据。

-   如果你知道存储的凭据的资源名，你可以使用 [**PasswordVault.FindAllByResource**](/uwp/api/windows.security.credentials.passwordvault.findallbyresource) 方法检索该资源名的所有凭据。

-   最后，如果你同时知道一个凭据的用户名和资源名，你可以使用 [**PasswordVault.Retrieve**](/uwp/api/windows.security.credentials.passwordvault.retrieve) 方法仅检索该凭据。

让我们来查看一个示例，在此示例中我们已在一个应用中全局存储了资源名，并且，如果找到了它们的凭据，我们将使用户自动登录。 如果我们找到了同一个用户的多个凭据，我们将要求用户选择一个默认凭据以在登录时使用。

```cs
private string resourceName = "My App";
private string defaultUserName;

private void Login()
{
    var loginCredential = GetCredentialFromLocker();

    if (loginCredential != null)
    {
        // There is a credential stored in the locker.
        // Populate the Password property of the credential
        // for automatic login.
        loginCredential.RetrievePassword();
    }
    else
    {
        // There is no credential stored in the locker.
        // Display UI to get user credentials.
        loginCredential = GetLoginCredentialUI();
    }

    // Log the user in.
    ServerLogin(loginCredential.UserName, loginCredential.Password);
}


private Windows.Security.Credentials.PasswordCredential GetCredentialFromLocker()
{
    Windows.Security.Credentials.PasswordCredential credential = null;

    var vault = new Windows.Security.Credentials.PasswordVault();
    var credentialList = vault.FindAllByResource(resourceName);
    if (credentialList.Count > 0)
    {
        if (credentialList.Count == 1)
        {
            credential = credentialList[0];
        }
        else
        {
            // When there are multiple usernames,
            // retrieve the default username. If one doesn't
            // exist, then display UI to have the user select
            // a default username.

            defaultUserName = GetDefaultUserNameUI();

            credential = vault.Retrieve(resourceName, defaultUserName);
        }
    }

    return credential;
}
```

## <a name="deleting-user-credentials"></a>删除用户凭据


在凭据保险箱中删除用户凭据是一个快捷的两步式过程。

1.  使用来自 [**Windows.Security.Credentials**](/uwp/api/Windows.Security.Credentials) 命名空间的 [**PasswordVault**](/uwp/api/Windows.Security.Credentials.PasswordVault) 对象获取对凭据保险箱的引用。

2.  将希望删除的凭据传递至 [**PasswordVault.Remove**](/uwp/api/windows.security.credentials.passwordvault.remove) 方法。

```cs
var vault = new Windows.Security.Credentials.PasswordVault();
vault.Remove(new Windows.Security.Credentials.PasswordCredential(
    "My App", username, password));
```

## <a name="best-practices"></a>最佳做法


将凭据保险箱仅用于存储密码，而不要将其用于存储较大的数据 blob。

仅当满足以下条件时才将密码保存在凭据保险箱中：

-   用户已成功登录。
-   用户已选择保存密码。

永远不要使用应用数据或漫游设置在纯文本中存储凭据。