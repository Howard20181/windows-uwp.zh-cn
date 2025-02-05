---
description: 使用 Microsoft Store 分析 API 中的此方法，可获取桌面应用程序中的错误堆栈跟踪。
title: 获取桌面应用程序中的错误的堆栈跟踪
ms.date: 06/05/2018
ms.topic: article
keywords: windows 10, uwp, Microsoft Store 服务, Microsoft Store 分析 API, 堆栈跟踪, 错误, 桌面应用程序
ms.localizationpriority: medium
ms.openlocfilehash: 319323ffbd8ed9aecda29cb012a47476f74c7811
ms.sourcegitcommit: b0a82c2a132212eb5fb72b67f0789cac1014642f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254172"
---
# <a name="get-the-stack-trace-for-an-error-in-your-desktop-application"></a>获取桌面应用程序中的错误的堆栈跟踪

在 Microsoft Store 分析 API 使用此方法，可获取已添加到 [Windows 桌面应用程序计划](/windows/desktop/appxpkg/windows-desktop-application-program)的桌面应用程序的错误堆栈跟踪。 此方法只能下载过去 30 天内发生的错误的堆栈跟踪。 在合作伙伴中心的桌面应用程序的 [运行状况报告](/windows/desktop/appxpkg/windows-desktop-application-program) 中也提供了堆栈跟踪。

在可以使用此方法之前，必须首先使用[获取桌面应用程序中的错误的详细信息](get-details-for-an-error-in-your-desktop-application.md)方法来检索与想要检索堆栈跟踪的错误相关联的 CAB 文件 ID 哈希。

## <a name="prerequisites"></a>必备条件

若要使用此方法，首先需要执行以下操作：

* 完成 Microsoft Store 分析 API 的所有[先决条件](access-analytics-data-using-windows-store-services.md#prerequisites)（如果尚未这样做）。
* [获取 Azure AD 访问令牌](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)，以供在此方法的请求标头中使用。 获取访问令牌后，在它到期前，你有 60 分钟的使用时间。 该令牌到期后，可以获取新的令牌。
* 获取与要检索堆栈跟踪的错误相关联的 CAB 文件的 ID 哈希。 若要获取此值，请使用 [获取桌面应用程序中的错误的详细信息](get-details-for-an-error-in-your-desktop-application.md)方法来检索应用中特定错误的详细信息，并使用该方法的响应正文中的 **cabIdHash** 值。

## <a name="request"></a>请求

### <a name="request-syntax"></a>请求语法

| 方法 | 请求 URI                                                                   |
|--------|-------------------------------------------------------------------------------|
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/stacktrace` |

### <a name="request-header"></a>请求头

| 标头        | 类型   | 说明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| 授权 | 字符串 | 必需。 Azure AD 访问令牌的格式为 **Bearer** &lt;*token*&gt; 。 |

### <a name="request-parameters"></a>请求参数

| 参数        | 类型   |  说明      |  必需  |
|---------------|--------|---------------|------|
| applicationId | 字符串 | 要为其获取堆栈跟踪的桌面应用程序的产品 ID。 若要获取桌面应用程序的产品 ID，请在合作伙伴中心 (（例如 **运行状况报告**）[中打开适用于桌面应用程序的分析报告](/windows/desktop/appxpkg/windows-desktop-application-program)，) 并从 URL 中检索产品 ID。 |  是  |
| cabIdHash | 字符串 | 获取与要检索堆栈跟踪的错误相关联的 CAB 文件的唯一 ID 哈希。 要获取此值，请使用 [获取桌面应用程序中的错误的详细信息](get-details-for-an-error-in-your-desktop-application.md)方法来检索应用程序中特定错误的详细信息，并使用该方法的响应正文中的 **cabIdHash** 值。 |  是  |

### <a name="request-example"></a>请求示例

以下示例演示如何使用此方法获取堆栈跟踪。 将 *applicationId* 和 *cabIdHash* 值替换为桌面应用程序的相应值。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/stacktrace?applicationId=10238467886765136388&cabIdHash=54ffb83a-e159-41d2-8158-f36f306cc01e HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>响应

### <a name="response-body"></a>响应正文

| 值      | 类型    | 说明                  |
|------------|---------|--------------------------------|
| “值”      | array   | 一组对象，其中每个包含堆栈跟踪数据的一个帧。 有关每个对象中的数据的详细信息，请参阅以下[堆栈跟踪值](#stack-trace-values)部分。 |
| @nextLink  | 字符串  | 如果存在其他数据页，则此字符串包含一个你可用来请求下一页数据的 URI。 例如，当请求的 **top** 参数设置为 10，但查询的错误超过 10 行时，就会返回此值。 |
| TotalCount | integer | 查询的数据结果中的行总数。          |

### <a name="stack-trace-values"></a>堆栈跟踪值

*Value* 数组中的元素包含以下值。

| “值”           | 类型    | 说明      |
|-----------------|---------|----------------|
| 级别            | 字符串  |  此元素在调用堆栈中表示的帧编号。  |
| image   | 字符串  |   可执行文件或库映像的名称，包含在此堆栈帧中调用的函数。           |
| 函数 | 字符串  |  在此堆栈帧中调用的函数名称。 仅当应用包含可执行文件或库的符号时才可用。              |
| offset     | string  |  相对于函数开始的当前指令的字节偏移量。      |

### <a name="response-example"></a>响应示例

以下示例举例说明此请求的 JSON 响应正文。

```json
{
  "Value": [
    {
      "level": "0",
      "image": "Contoso.ContosoApp",
      "function": "Contoso.ContosoApp.MainPage.DoWork",
      "offset": "0x25C"
    }
    {
      "level": "1",
      "image": "Contoso.ContosoApp",
      "function": "Contoso.ContosoApp.MainPage.Initialize",
      "offset": "0x26"
    }
    {
      "level": "2",
      "image": "Contoso.ContosoApp",
      "function": "Contoso.ContosoApp.Start",
      "offset": "0x66"
    }
  ],
  "@nextLink": null,
  "TotalCount": 3
}

```

## <a name="related-topics"></a>相关主题

* [运行状况报告](../publish/health-report.md)
* [使用 Microsoft Store 服务访问分析数据](access-analytics-data-using-windows-store-services.md)
* [获取桌面应用程序的错误报告数据](get-desktop-application-error-reporting-data.md)
* [获取桌面应用程序中的错误的详细信息](get-details-for-an-error-in-your-desktop-application.md)
* [下载桌面应用程序中错误的 CAB 文件](download-the-cab-file-for-an-error-in-your-desktop-application.md)