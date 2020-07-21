---
description: 如何在 UWP 应用中使用联系人和日历信息。
title: 联系人和日历
ms.assetid: b7e53ab5-2828-4fb7-8656-2bec70b3467f
ms.date: 05/18/2018
ms.topic: article
keywords: windows 10, uwp, 联系人, 日历, 约会, 电子邮件
ms.localizationpriority: medium
ms.openlocfilehash: b2e3f0b1d93d2b2c32e117f61fd7514077ca3923
ms.sourcegitcommit: 90fe7a9a5bfa7299ad1b78bbef289850dfbf857d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756543"
---
# <a name="contacts-my-people-and-calendar"></a>联系人、我的人脉和日历


你可以让你的用户访问其联系人和约会，以便他们可以彼此共享内容、电子邮件、日历信息或消息，或共享你设计的任何功能。

若要查看你的应用访问联系人和约会的几种不同方法，请参阅以下主题：

| 主题 | 说明 |
|-------|-------------|
| [选择联系人](selecting-contacts.md) | [  <strong>Windows.ApplicationModel.Contacts</strong>](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts) 命名空间提供了多个用来选择联系人的选项。 下面，我们将向你介绍如何选择一个联系人或多个联系人，并且还介绍如何将联系人选取器配置为仅检索应用所需的联系人信息。 |
| [发送电子邮件](sending-email.md) | 显示如何启动撰写电子邮件对话框以允许用户发送电子邮件。 你可以在显示该对话框之前，使用数据预填充电子邮件的字段。 该消息将在用户点击发送按钮后发出。 |
| [发送短信](sending-an-sms-message.md) | 本主题向你展示如何启动撰写短信对话框以允许用户发送短信。 你可以在显示该对话框之前，使用数据预填充短信的字段。 该消息将在用户点击发送按钮后发出。 |
| [管理约会](managing-appointments.md) | 通过 [<strong>Windows.ApplicationModel.Appointments</strong>](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Appointments) 命名空间，你可以在用户的日历应用中创建和管理约会。 我们将在此处向你介绍如何创建约会、将其添加到日历应用、在日历应用中替换它以及从日历应用中删除它。 我们还将介绍如何显示日历应用的时间跨度以及创建一个约会循环对象。 |
| [将你的应用与联系人卡片上的操作关联起来](integrating-with-contacts.md) | 介绍如何使应用显示在联系人卡片或微型联系人卡片上的操作旁边。 用户可以选择你的应用执行某项操作，如打开个人资料页面、打电话或发送消息。 |
| [为应用程序添加“我的人脉”支持](my-people-support.md) | 介绍了如何为应用程序添加“我的人脉”支持，以及如何在任务栏上固定和取消固定联系人。 |
| [“我的人脉”共享](my-people-sharing.md) | 介绍了如何添加“我的人脉”支持，这让用户能够通过将文件从文件资源管理器拖到“我的人脉”固定区域，来与他们固定的联系人分享内容。 |
| [“我的人脉”通知](my-people-notifications.md) | 介绍了如何创建和使用“我的人脉”通知（一种从固定的联系人发送的新型 Toast 通知）。 |

 

## <a name="related-topics"></a>相关主题

* [约会 API 示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Appointments)
* [联系人管理器 API 示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/master/Official%20Windows%20Platform%20Sample/Contact%20manager%20API%20sample)
* [联系人选取器应用示例](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/ContactPicker)
* [处理联系人操作示例](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/master/Official%20Windows%20Platform%20Sample/Windows%208.1%20Store%20app%20samples/99866-Windows%208.1%20Store%20app%20samples/Handling%20Contact%20Actions)
* [联系人卡片集成示例](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ContactCardIntegration)
