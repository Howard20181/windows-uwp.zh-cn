---
title: Xbox One 开发人员模式激活
description: 如何激活开发人员模式，以便你可以在零售模式和开发人员模式之间切换。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ade80769-17ae-46e9-9c2f-bf08ae5a51ee
ms.localizationpriority: medium
ms.openlocfilehash: 95b65e63c081734a560a852a5d064ef76c423ef6
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258727"
---
# <a name="xbox-one-developer-mode-activation"></a>Xbox One 开发人员模式激活

## <a name="how-developer-mode-works"></a>开发人员模式的工作原理
Xbox One 具有两种模式，即*零售* 模式 (**1**) 和*开发人员* 模式 (**2**)。 在零售模式中，Xbox One 主机处于可供任何客户或用户使用的状态：既可玩游戏，也可以用户身份运行应用。 在开发人员模式中，你可以开发适用于该主机的软件，不过既不能玩零售游戏也不能运行零售应用。

可以在任何零售 Xbox One 主机上启用开发人员模式。 开发人员模式启用后，可以在零售模式 (**2a**) 和开发人员模式 (**2b**) 之间来回切换。

![Xbox One 模式](images/dev-mode-flow.png)

## <a name="activate-developer-mode-on-your-retail-xbox-one-console"></a>在零售 Xbox One 主机上激活开发人员模式

1.  启动 Xbox One 主机。

2.  从 Xbox One 商店中搜索和安装**开发人员模式激活**应用。

    ![安装“开发人员模式激活”应用](images/devkit-activation-1.png)

3.  从 Microsoft Store 页面启动该应用。

    ![开发人员模式激活应用](images/devkit-activation-2.png)

4.  记下“开发人员模式激活”应用中显示的代码。

    ![激活步骤 5](images/activation-step-5.png)  
    
5.  [在合作伙伴中心注册应用开发人员帐户](https://developer.microsoft.com/store/register)。  这也是发布游戏的第一步。

6.  通过有效的当前合作伙伴中心应用开发者帐户登录到[合作伙伴中心](https://partner.microsoft.com/dashboard)。  如果左侧导航窗格中未显示多个选项，或者在 "**概述**" 部分中看不到 "**创建新应用**" 选项，以下步骤和激活链接_将无法工作_;请确保已完全注册了上一步中的应用开发人员帐户。

7.  请参阅[partner.microsoft.com/xboxconfig/devices](https://partner.microsoft.com/xboxconfig/devices)。

8.  输入“开发人员模式激活”应用中显示的激活代码。 与你的帐户关联的激活次数有限制。 激活开发人员模式后，合作伙伴中心将指示你已使用与你的帐户关联的某个激活。

    ![激活步骤 8](images/activation-step-8-rs2.png)    
    
9.  单击**同意并激活**。 这将导致页面重新加载，并且你将看到你的设备已填充到表中。 可以在 [Xbox One 开发人员模式激活计划](https://docs.microsoft.com/legal/windows/agreements/xbox-one-developer-mode-activation)中找到 Xbox One 开发人员模式激活计划协议的条款。

10. 输入激活代码之后，主机将显示激活过程的进度屏幕。  
    
11. 激活完成后，打开“开发人员模式激活”应用，然后单击**切换并重启**以转到开发人员模式。 请注意，此操作所需的时间比平常更长。

    ![激活步骤 12](images/activation-step-12.png)   

## <a name="switch-between-retail-and-developer-mode"></a>在零售模式和开发人员模式之间切换
在主机上启用了开发人员模式后，可使用**开发人员主页**在零售模式和开发人员模式之间切换。 若要了解有关启动和使用开发人员主页的详细信息，请参阅 [Xbox One 工具简介](introduction-to-xbox-tools.md)。

* 若要切换到零售模式，请打开**开发人员主页**。 在**快速操作**下选择**退出开发人员模式**。 这将在零售模式下重启主机。    

  ![激活步骤 13](images/activation-step-13-rs4.png)  
  
* 若要切换到开发人员模式，请使用“开发人员模式激活”应用。 打开该应用，然后选择**切换并重启**。 这将在开发人员模式下重新启动你的主机。  

  ![激活步骤 14](images/activation-step-12.png)  

## <a name="see-also"></a>另请参阅
- [Xbox One 开发人员模式停用](devkit-deactivation.md)
- [Xbox one 上的 UWP](index.md)
