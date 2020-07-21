---
title: Xbox One 开发人员模式停用
description: 如何停用开发人员模式。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 244124dd-d80a-4a72-91db-1c9c2fbc7c3c
ms.localizationpriority: medium
ms.openlocfilehash: 6e7b96e3b8b0cd6f47fdd97008aa8d90dc032fc4
ms.sourcegitcommit: bee98f7a468c97c707de76afc14e1707c25f74f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80531465"
---
# <a name="xbox-one-developer-mode-deactivation"></a>Xbox One 开发人员模式停用

如果你决定不再希望将主机用于开发，请使用以下步骤停用开发人员模式。

## <a name="switch-to-retail-mode"></a>切换到零售模式

首先，将你的 Xbox One 主机返回到零售模式。

1. 打开**开发人员主页**。

2. 选择**退出开发人员模式**。  主机将在零售模式下重新启动。  

   ![退出开发人员模式](images/devkit-deactivation-1.png)

现在，可使用以下方法之一停用你的主机。

## <a name="deactivate-your-console-using-the-dev-mode-activation-app"></a>使用“开发人员模式激活”应用停用主机

在主机上停用开发人员模式的首选方法是使用**开发人员模式激活**应用。 

1. 导航到**游戏和应用** > **应用**。
  
   ![激活步骤 3](images/devkit-deactivation-5.png)    
   
2.  打开“开发人员模式激活”应用。

3.  选择**激活**。
  
    ![停用主机](images/deactivation-app.png)

请参阅 [Xbox One 开发人员模式激活](devkit-activation.md)，了解有关**开发人员模式激活**应用的详细信息。 

## <a name="reset-your-console"></a>重置主机

也可以通过重置主机停用开发人员模式。  

> [!NOTE]
> 在重置主机时，将丢失所有本地保存的游戏数据。

若要重置主机，请执行以下步骤：

1.  转到**我的游戏和应用**。

2.  选择**应用**，然后选择**设置**。

3.  转到左侧窗格中的**系统**，然后选择右侧窗格中的**主机信息**。   
   
    ![主机信息和更新](images/devkit-deactivation-2.png)  
    
4.  选择**重置主机**。
    
    ![重置主机](images/devkit-deactivation-3.png)
    
5.  接下来，选择**重置和删除所有内容**。 此选项用于将主机重置为其原始零售状态。  将删除所有应用、游戏和本地保存数据。 请注意，如果选择另一选项**重置并保留我的游戏和应用**，将无法从开发人员计划中删除你的主机。  
   
    ![重置和删除所有内容](images/devkit-deactivation-4.png)

## <a name="deactivate-your-console-using-partner-center"></a>使用合作伙伴中心停用控制台

如果出于任何原因无法访问控制台，还可以使用合作伙伴中心在控制台上停用开发人员模式。

1. 在合作伙伴中心导航到 "[管理 Xbox one 控制台](https://partner.microsoft.com/xboxconfig/devices)" 页。 系统可能会提示你登录。

2. 通过匹配序列号、主机 ID 或设备 ID，在主机列表中找到要停用的主机。  

3. 单击**停用**。  
  
![使用 DevCenter 执行停用](images/devkit-deactivation-6.png)

如果你之前未将 Xbox One 主机返回到零售模式，请立即如[切换到零售模式](#switch-to-retail-mode)中所述执行此操作。

## <a name="see-also"></a>另请参阅
- [Xbox One 开发人员模式激活](devkit-activation.md)
- [Xbox one 上的 UWP](index.md)
