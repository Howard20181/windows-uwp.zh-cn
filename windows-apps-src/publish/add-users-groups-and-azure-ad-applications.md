---
Description: 你可以将用户、组和 Azure AD 应用程序添加到你的合作伙伴中心帐户。
title: 向合作伙伴中心帐户添加用户、组和 Azure AD 应用程序
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10，uwp，azure ad 应用程序，aad，用户，组，多个用户，多用户
ms.localizationpriority: medium
ms.openlocfilehash: 41467f51e02f3cc700e3759f33d6fd6eea3ac7a6
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210703"
---
# <a name="add-users-groups-and-azure-ad-applications-to-your-partner-center-account"></a>向合作伙伴中心帐户添加用户、组和 Azure AD 应用程序

[合作伙伴中心](https://partner.microsoft.com/dashboard)（在 "**帐户设置**" 下）的 "**用户**" 部分允许你使用 Azure Active Directory 向合作伙伴中心帐户添加用户。 为每个用户分配一个角色（或自定义权限集），并且该角色定义他们的帐户访问权限。 你还可以添加[用户组](#groups)和[Azure AD 应用程序](#azure-ad-applications)，以便向他们授予对你的合作伙伴中心帐户的访问权限。

将用户添加到帐户后，你可以[编辑帐户详细信息](#edit)、更改[角色和权限](set-custom-permissions-for-account-users.md)或[删除用户](#remove)。

> [!IMPORTANT]
> 若要将用户添加到你的帐户，你必须首先[将合作伙伴中心帐户与组织的 Azure Active Directory 租户相关联](associate-azure-ad-with-partner-center.md)。 

添加用户时，需要通过为其分配[角色或自定义权限集](set-custom-permissions-for-account-users.md)来指定他们对合作伙伴中心帐户的访问权限。 

请记住，所有合作伙伴中心用户（包括组和 Azure AD 应用程序）必须在[与合作伙伴中心帐户关联的 Azure AD 租户](associate-azure-ad-with-partner-center.md)中具有活动帐户。 您可以在一个租户中一次性完成用户管理工作；但你必须使用你想要在其中添加或编辑用户的租户的管理员帐户登录。 在合作伙伴中心创建新用户时，还会在你登录到的 Azure AD 租户中为该用户创建一个帐户，并在合作伙伴中心对用户的名称进行更改，以便在组织的 Azure AD 租户中进行相同的更改。

> [!NOTE]
> 如果你的组织使用[目录集成](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)?redirectedfrom=MSDN)将本地目录服务与你的 Azure AD 同步，则你将无法在合作伙伴中心创建新的用户、组或 Azure AD 应用程序。 你（或你的本地目录中的另一个管理员）需要直接在本地目录中创建它们，然后才能在合作伙伴中心内查看并添加它们。


<span id="users" />

## <a name="add-users-to-your-partner-center-account"></a>将用户添加到合作伙伴中心帐户

若要将用户添加到合作伙伴中心帐户，请在 "**帐户设置**" 中，选择 "**用户**" 页，然后选择 "**添加用户"。** 你必须使用你想要在其中工作的 Azure AD 租户的管理员帐户登录。 

### <a name="add-existing-users"></a>添加现有用户 

你可以选择已存在于你的组织的租户中的用户，并向他们授予对你的合作伙伴中心帐户的访问权限。 

<span id="from-directory" />

1.  选择齿轮图标（位于 "合作伙伴中心" 的右上角附近），然后选择 "**开发人员设置**"。 在“设置”菜单中，选择“用户”。
2.  从**用户**页面，选择**添加用户**。 
3.  从出现的列表中选择一个或多个用户。 你可以使用搜索框来搜索特定用户。
    > [!TIP]
    > 如果选择将多个用户添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个用户，请针对每个角色或自定义权限集重复以下步骤。
4.  当你完成选择用户时，请单击**添加选定项**。
5.  在**角色**部分中，为选定的用户指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。
6.  单击 **“保存”** 。

### <a name="additional-methods-for-adding-users"></a>其他用户添加方法

如果你使用管理员帐户登录，并且该帐户对你所使用的 Azure AD 租户具有[全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限，则可以使用其他选项将用户添加到合作伙伴中心帐户。 你需要选择其中一个选项：

-   **添加现有用户**：选择已存在于你的组织的目录中的用户，并使用上面介绍的方法向他们授予对你的合作伙伴中心帐户的访问权限。
-   **创建新用户**：创建全新用户帐户以添加到组织的目录和合作伙伴中心帐户
-   **邀请外部用户**：向当前不在你的组织目录中的用户发送电子邮件邀请。 他们将被邀请访问你的合作伙伴中心帐户，并会在你的 Azure AD 租户中为他们创建新的[来宾用户](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)帐户。

<span id="new-user" />

### <a name="create-new-users"></a>创建新用户

> [!IMPORTANT]
> 你必须使用 Azure AD 租户中的全局管理员帐户登录才能创建新用户。

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加用户**"，然后选择 "**创建新用户**"。
2.  输入新用户的名字、姓氏和用户名。
3.  如果希望新用户在组织目录中拥有[全局管理员帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)，请选中标有**使此用户成为 Azure AD 中的全局管理员，并且全权管理所有目录资源**的框。 这可使该用户完整访问公司 Azure AD 中的所有管理功能。 他们将能够在组织的目录中添加和管理用户（尽管不是在合作伙伴中心，除非你向帐户授予适当的[角色/权限](set-custom-permissions-for-account-users.md)）。 如果选中此框，需要为用户提供**密码恢复电子邮件**。
4.  如果你选中了**使此用户成为 Azure AD 中的全局管理员**框，请输入用户在需要恢复密码时可以使用的电子邮件。
5.  在**组成员身份**部分中，选择任何希望新用户加入的组。
6.  在**角色**部分中，为用户指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。
7.  单击 **“保存”** 。
8.  在确认页上，你将看到新用户的登录信息，包括临时密码。 请确保记下此信息，并将其提供给新用户，因为在离开此页面后可能无法访问临时密码。


<span id="email" />

### <a name="invite-outside-users"></a>邀请外部用户

> [!IMPORTANT]
> 你必须使用 Azure AD 租户中的全局管理员帐户登录才能邀请外部用户。

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加用户**"，然后选择 "**通过电子邮件邀请用户**"。
1.  输入一个或多个电子邮件地址（最多十个），用逗号或分号隔开。
2.  在**角色**部分中，为用户指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。
3.  单击 **“保存”** 。

你邀请的用户将获得加入你帐户的电子邮件邀请，并且会在你的 Azure AD 租户中为其创建新的[来宾用户](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)帐户。 每个用户需要接受邀请才能访问帐户。

如果需要重新发送邀请，请在**用户**页上查找用户，然后选择他们的电子邮件地址（或者显示**邀请挂起**的文本）。 然后在页面底部单击**重新发送邀请**。

> [!IMPORTANT]
> 你邀请加入你的合作伙伴中心帐户的外部用户可以为其分配与其他用户相同的角色和权限。 但是，外部用户无法在 Visual Studio 中执行某些任务，例如将应用与 Microsoft Store 关联，或创建要上传到 Microsoft Store 的程序包。 如果用户需要执行这些任务，请选择**创建新用户**而不是**邀请外部用户**。 （如果你不希望将这些用户添加到现有 Azure AD 租户，可以[创建新租户](../publish/associate-azure-ad-with-partner-center.md#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)，然后在该租户中为他们创建新用户帐户。） 


### <a name="changing-a-users-directory-password"></a>更改用户的目录密码

如果在创建用户帐户时已经提供了**密码恢复电子邮件**，那么用户可以在有需要时自行更改密码。 你还可以按照以下步骤更新用户密码（如果你使用 Azure AD 租户中的全局管理员帐户登录以更改用户密码）。 请注意，这将更改用户的 Azure AD 租户中的密码，以及他们用于访问合作伙伴中心的密码。 

1.  从 "**用户**" 页（在 "**帐户设置**" 下）中，选择要编辑的用户帐户的名称。
2.  选择页面底部的 "**重置密码**" 按钮。
3.  将显示一个确认页，用于显示用户的登录信息，包括临时密码。

    > [!IMPORTANT]
    >  一定要打印或复制此信息并将其提供给用户，因为离开此页面后，你将无法访问临时密码。

<span id="groups" />

## <a name="add-groups-to-your-partner-center-account"></a>向合作伙伴中心帐户添加组

可以将组织的目录中的组添加到合作伙伴中心帐户。 当你执行此操作时，属于该组成员的每个用户都将能够访问它，并且具有与组的分配角色关联的权限。

### <a name="add-groups-from-your-organizations-directory"></a>从组织的目录添加组

1.  选择齿轮图标（位于 "合作伙伴中心" 的右上角附近），然后选择 "**开发人员设置**"。 在“设置”菜单中，选择“用户”。
2. 在 "**用户**" 页中，选择 "**添加组**"。
2.  从出现的列表中选择一个或多个组。 你可以使用搜索框来搜索特定组。
    > [!TIP]
    > 如果选择将多个组添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个组，请针对每个角色或自定义权限集重复以下步骤。

3.  完成选择组后，请单击**添加选定项**。
4.  在**角色**部分中，为选定的组指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。 组的所有成员都能够访问你的合作伙伴中心帐户，其中包含你应用于组的权限，而不考虑与其个人帐户关联的角色/权限。
5.  单击 **“保存”** 。


### <a name="create-a-new-group-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account"></a>在组织的目录中创建新的组帐户，并将其添加到合作伙伴中心帐户

如果要授予合作伙伴中心对全新组的访问权限，则可以在 "**用户**" 部分中创建新组。 请注意，新组将在你组织的目录中创建，而不是在你的合作伙伴中心帐户中。

1.  在 "**用户**" 页（在 "**开发人员设置**" 下）中，单击 "**添加组**"。
2.  在下一页上，选择 "**新建组**"。
3.  输入新组的显示名称。
4.  为组指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。 组的所有成员都能够访问你的合作伙伴中心帐户，其中包含你应用于组的权限，而不考虑与其个人帐户关联的角色/权限。
5.  从出现的列表中选择要分配给新组的用户。 你可以使用搜索框来搜索特定用户。
6.  完成选择用户后，请单击**添加选定项**将其添加到新组中。
7.  单击 **“保存”** 。


<span id="azure-ad-applications" />

## <a name="add-azure-ad-applications-to-your-partner-center-account"></a>将 Azure AD 应用程序添加到合作伙伴中心帐户

你可以允许作为组织 Azure AD 一部分的应用程序或服务访问你的合作伙伴中心帐户。 这些 Azure AD 应用程序用户帐户可用于调用 [Microsoft Store 服务](../monetize/using-windows-store-services.md)提供的 REST API。


### <a name="add-azure-ad-applications-from-your-organizations-directory"></a>从组织的目录中添加 Azure AD 应用程序

1.  1.  选择齿轮图标（位于 "合作伙伴中心" 的右上角附近），然后选择 "**开发人员设置**"。 在“设置”菜单中，选择“用户”。
2. 从**用户**页面，选择**添加 Azure AD 应用程序**。
3.  从出现的列表中选择一个或多个 Azure AD 应用程序。 你可以使用搜索框来搜索特定的 Azure AD 应用程序。
    > [!TIP]
    > 如果选择多个 Azure AD 应用程序添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个 Azure AD 应用程序，请针对每个角色或自定义权限集重复以下步骤。

4.  完成选择 Azure AD 应用程序后，请单击**添加选定项**。
5.  在**角色**部分中，为选定的 Azure AD 应用程序指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。
6.  单击 **“保存”** 。


### <a name="create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account"></a>在组织的目录中创建新的 Azure AD 应用程序帐户，并将其添加到合作伙伴中心帐户

如果要向合作伙伴中心授予全新 Azure AD 应用程序帐户的访问权限，则可以在 "**用户**" 部分中创建一个。 请注意，这将在你的组织的目录中创建一个新帐户，而不只是在你的合作伙伴中心帐户中。

> [!TIP]
> 如果主要使用此 Azure AD 应用程序进行合作伙伴中心身份验证，并且不需要用户直接访问该应用程序，则可以输入**回复 URL**和**应用 ID URI**的任何有效地址，前提是你的目录中的任何其他 Azure AD 应用程序都不会使用这些值。

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加 Azure AD 应用程序**"。
2.  在下一页上，选择 "**新建" Azure AD 应用程序**"。
3.  输入新的 Azure AD 应用程序的**回复 URL**。 这是用户可以登录并使用 Azure AD 应用程序的 URL（有时也称为“应用 URL”或“登录 URL”）。 **回复 URL**不能超过 256 个字符，且在你目录中必须是唯一的。
4.  输入新 Azure AD 应用程序的**应用程序 ID URI**。 这是 Azure AD 应用程序在向 Azure AD 发送单一登录请求时显示的该应用程序的逻辑标识符。 请注意，每个 Azure AD 应用程序的**应用 ID URI** 在目录中必须唯一，且长度不得超过 256 个字符。 有关**应用 ID URI** 的详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#changing-the-application-registration-to-support-multi-tenant)。
5.  在**角色**部分中，为 Azure AD 应用程序指定[角色或自定义权限](set-custom-permissions-for-account-users.md)。
6.  单击 **“保存”** 。

添加或创建 Azure AD 应用程序后，可以返回到**用户**部分，然后选择应用程序名称查看应用程序的设置，包括租户 ID、客户端 ID、回复 URL 和应用 ID URI。

> [!NOTE]
> 如果要使用 [Microsoft Store 服务](../monetize/using-windows-store-services.md)提供的 REST API，将需要此页面上显示的租户 ID 和客户端 ID 值，以获取可用于对服务调用进行身份验证的 Azure AD 访问令牌。   

<span id="manage-keys" />

### <a name="manage-keys-for-an-azure-ad-application"></a>管理 Azure AD 应用程序的密钥

如果 Azure AD 应用程序在 Microsoft Azure AD 中读写数据，它将需要密钥。 可以通过在合作伙伴中心编辑其信息，为 Azure AD 应用程序创建密钥。 还可以删除不再需要的密钥。

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 Azure AD 应用程序的名称。
    > [!TIP]
    > 单击 Azure AD 应用程序的名称时，将看到 Azure AD 应用程序的所有活动密钥，包括创建密钥的日期和过期时间。 若要删除不再需要的密钥，请单击“删除”。

2.  若要添加新项，请选择 "**添加新项**"。
3.  你将看到一个显示 **Client ID** 和 **Key** 值的屏幕。
    > [!IMPORTANT]
    > 一定要打印或复制此信息，因为离开此页面后将无法再次访问。

4.  如果要创建更多密钥，请选择 "**添加其他密钥**"。

<span id="edit" />

## <a name="edit-a-user-group-or-azure-ad-application"></a>编辑用户、组或 Azure AD 应用程序

将用户、组和/或 Azure AD 应用程序添加到你的合作伙伴中心帐户后，你可以对其帐户信息进行更改。 

> [!IMPORTANT]
> 对[角色或权限](set-custom-permissions-for-account-users.md)所做的更改只会影响合作伙伴中心访问。 所有其他更改（如更改用户的名称或组成员身份，或 Azure AD 应用程序的回复 URL 和应用 ID URI）将反映在组织的 Azure AD 租户以及你的合作伙伴中心帐户中。 

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择要编辑的用户、组或 Azure AD 应用程序帐户的名称。
2.  进行所需更改。 你可以编辑的项目如下所示：
    -   对于**用户**，你可以编辑用户的名字、姓氏或用户名。 你还可以在**组成员身份**部分中选择或取消选择组，以更新其组成员身份。
    -   对于**组**，你可以编辑组的名称。 （若要更新组成员身份，请编辑要添加或从组中删除的用户，并对**组成员身份**部分进行更改。）
    -   对于 **Azure AD 应用程序**，你可以输入**回复 URL** 或**应用 ID URI** 的新值。
    请记住，这些更改将在你组织的目录中以及你的合作伙伴中心帐户中进行。
3.  对于与合作伙伴中心访问权限相关的更改，请选择或取消选择要应用的角色，或选择 "**自定义权限**" 并进行所需的更改。 这些更改仅影响合作伙伴中心访问，不会更改组织的 Azure AD 租户内的任何权限。
3.  单击 **“保存”** 。


## <a name="view-history-for-account-users"></a>查看帐户用户的历史记录

作为帐户所有者，你可以查看已添加到帐户中的任何其他用户的详细浏览历史记录。

在 "**用户**" 页（在 "**帐户设置**" 下）中，为想要查看其浏览历史记录的用户选择 "**上次活动**" 下显示的链接。 你可以查看此用户在过去 30 天内访问过的所有网页的 URL。

<span id="remove" />

## <a name="remove-users-groups-and-azure-ad-applications"></a>删除用户、组和 Azure AD 应用程序

若要从合作伙伴中心帐户中删除用户、组或 Azure AD 应用程序，请选择 "**用户**" 页上按名称显示的 "**删除**" 链接。 确认你要删除该帐户后，该用户、组或 Azure AD 应用程序将无法再访问你的合作伙伴中心帐户（除非你稍后再次添加）。

> [!IMPORTANT]
> 删除用户、组或 Azure AD 应用程序意味着该应用程序将无法再访问你的合作伙伴中心帐户。 它**不会**从你的组织的目录中删除用户、组或 Azure AD 应用程序。

 

