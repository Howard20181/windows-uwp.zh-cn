---
title: Device Portal 网络凭据 API 参考
description: 了解如何以编程方式添加、删除或更新网络凭据。
ms.localizationpriority: medium
ms.topic: article
ms.date: 02/08/2017
ms.openlocfilehash: 7ba9e25031590da02881276ff9a10a9f952c78ec
ms.sourcegitcommit: b0a82c2a132212eb5fb72b67f0789cac1014642f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254212"
---
# <a name="network-credentials-api-reference"></a>网络凭据 API 参考

你可以使用此 REST API 添加、删除或更新开发工具包上已存储的网络凭据。

## <a name="get-existing-credentials"></a>获取现有凭据

**请求**

你可以获取已存储的共享列表以及拥有该网络共享凭据的用户的用户名。

| 方法 | 请求 URI |
|--------|-------------|
| GET | /ext/networkcredential |

**URI 参数**

- 无

**请求标头**

- 无

**请求正文**   

- 无

**响应**   

以下格式的 JSON 数组：

* 凭据
  * NetworkPath - 网络共享的路径。
  * Username - 已存储凭据的用户名。

**状态代码**

此 API 具有以下预期状态代码。

| HTTP 状态代码 | 说明 |
|------------------|-------------|
| 200 | Success |
| 4XX | 错误代码 |
| 5XX | 错误代码 |

## <a name="add-or-update-stored-credentials-for-a-user"></a>为用户添加或更新已存储的凭据

**请求**

| 方法 | 请求 URI |
|--------|-------------|
| POST | /ext/networkcredential |

**URI 参数**

可以在请求 URI 上指定以下附加参数：

| URI 参数      | 说明     |
| ------------------ |-----------------|
| NetworkPath        | 添加凭据以访问的共享网络路径。 |

**请求标头**

- 无

**请求正文**

以下 JSON 元素：
* NetworkPath - 网络共享的路径。
* Username - 用于将凭据存储在其下的用户名。
* Password - 此用户的新密码或更新后的密码。

**响应**   

- 无  

**状态代码**

此 API 具有以下预期状态代码。

| HTTP 状态代码 | 说明 |
|------------------|-------------|
| 204 | 成功 |
| 4XX | 错误代码 |
| 5XX | 错误代码 |

## <a name="remove-stored-credentials-for-a-share"></a>删除已存储的共享凭据。

**请求**

| 方法 | 请求 URI |
|--------|-------------|
| DELETE | /ext/networkcredential |

**URI 参数**

可以在请求 URI 上指定以下附加参数：

| URI 参数      | 说明     |
| ------------------ |-----------------|
| NetworkPath        | 你将从其中删除已存储的凭据的共享网络路径。 |

**请求标头**

- 无

**请求正文**

- 无

**响应**

- 无

**状态代码**

此 API 具有以下预期状态代码。

| HTTP 状态代码 | 说明 |
|------------------|-------------|
| 204 | 凭据请求已成功。 |
| 4XX | 错误代码 |
| 5XX | 错误代码 |

**可用设备系列**

* Windows Xbox