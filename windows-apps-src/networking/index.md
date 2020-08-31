---
ms.assetid: 7bb9fd81-8ab5-4f8d-a854-ce285b0669a4
description: 查看文章链接列表，在这些文章中了解可供通用 Windows 平台 (UWP) 开发人员使用的网络和 Web 服务技术。
title: 网络和 Web 服务
ms.date: 11/26/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 169412c21714f2777db3e6078a6528dc863277ea
ms.sourcegitcommit: 45dec3dc0f14934b8ecf1ee276070b553f48074d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2020
ms.locfileid: "89094604"
---
# <a name="networking-and-web-services"></a>网络和 Web 服务

以下网络和 Web 服务技术可供通用 Windows 平台 (UWP) 开发人员使用。

| 主题 | 说明 |
| - | - |
| [网络基础知识](networking-basics.md) | 针对任何支持网络的应用的必做事项。 |
| [选择哪一种网络技术？](which-networking-technology.md) | 适用于 UWP 开发人员的网络技术概述，以及关于如何选择适合自己应用的技术的建议。 |
| [后台中的网络通信](network-communications-in-the-background.md) | 要在网络通信不在后台运行的情况下继续网络通信，应用可以使用后台任务以及套接字代理或控制通道触发器。 |
| [套接字](sockets.md) | 套接字是实现许多网络协议所基于的低级数据传输技术。 UWP 为客户端-服务器或对等应用程序提供 TCP 和 UDP 套接字类，无论连接长期存在还是不需要建立连接。 |
| [WebSockets](websockets.md) | WebSocket 提供了一种机制，用于使用 HTTP 通过 Web 在客户端与服务器之间进行既快捷又安全的双向通信，同时支持 UTF-8 和二进制消息。 |
| [HttpClient](httpclient.md) | 依据 HTTP 2.0 和 HTTP 1.1 协议，使用 [Windows.Web.Http](https://docs.microsoft.com/uwp/api/Windows.Web.Http) 命名空间 API 发送和接收信息。 |
| [RSS/Atom 源](web-feeds.md) | 使用综合源检索或创建最新和最热门的 Web 内容，这些源通过 [Windows.Web.Syndication](https://docs.microsoft.com/uwp/api/Windows.Web.Syndication) 命名空间中的功能根据 RSS 和 Atom 标准生成。 |
| [后台传输](background-transfers.md) | 使用后台传输 API 以便在网络上可靠地复制文件。 |
