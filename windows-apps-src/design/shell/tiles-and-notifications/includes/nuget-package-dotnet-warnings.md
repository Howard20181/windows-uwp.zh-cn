---
ms.openlocfilehash: e4f09efa1bd6d6884e9a32b46aff1bf586a16e60
ms.sourcegitcommit: 6cd970686d1ea7176b7e6651f349a14551709820
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559386"
---
> [!IMPORTANT]
> 仍使用 packages.config .NET Framework 桌面应用必须迁移到 PackageReference，否则不会正确引用 Windows 10 Sdk。 在项目中，右键单击 "引用"，然后单击 "将 packages.config 迁移到 PackageReference"。
> 
> .NET Core 3.0 WPF 应用必须更新到 .NET Core 3.1，否则将不会存在 Api。
> 
> .NET 5 应用程序必须 [使用 Windows 10 tfm](https://docs.microsoft.com/dotnet/standard/frameworks#how-to-specify-a-target-framework)中的一个，否则 toast 发送和管理 api （如） `Show()` 将丢失。 将 TFM 设置为 `net5.0-windows10.0.17763.0` 或更高版本。
