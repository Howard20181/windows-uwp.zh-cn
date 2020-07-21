---
title: 选择编程语言
ms.assetid: 6CA46432-BF03-4B20-9187-565B3503B497
description: 选择编程语言
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 10673184b44f9c4c52e2ee5ad3cf4ba4a1238497
ms.sourcegitcommit: c1226b6b9ec5ed008a75a3d92abb0e50471bb988
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86493302"
---
# <a name="getting-started-choosing-a-programming-language"></a>入门：选择编程语言

## <a name="choosing-a-programming-language"></a>选择编程语言

在执行下一步操作之前，你应知道在开发通用 Windows 平台 (UWP) 应用时可从中选择的编程语言。 尽管本文中的演练使用的是 C#，但你可以使用一种或多种编程语言开发 UWP 应用（请参阅[语言、工具和框架](https://docs.microsoft.com/previous-versions/windows/apps/dn465799(v=win.10))）。

可使用 C++、C#、Microsoft Visual Basic 和 JavaScript 进行开发。 JavaScript 使用用于 UI 布局的 HTML5 标记，其他语言使用一种称为*Extensible Application Markup Language (XAML)* 的标记语言来描述其 UI。

尽管我们在本文中以 C# 为重点，但其他语言也提供了你可能希望尝试的独特优势。 例如，如果应用的性能是首要关注事项，特别是对于密集图形，则 C++ 可能是正确选择。 Microsoft .NET 版本的 Visual Basic 非常适合 Visual Basic 应用的开发人员。 使用 HTML5 的 JavaScript 非常适合具有 Web 开发背景的开发人员。 有关更多信息，请参见下列内容之一：

-   [使用 c # 或 Visual Basic 创建 "Hello，World！" 应用](../get-started/create-a-hello-world-app-xaml-universal.md)
-   [使用 c + +/WinRT 创建 "Hello，World！" 应用](/windows/uwp/get-started/create-a-basic-windows-10-app-in-cppwinrt)
-   [使用 c + +/CX 创建 "Hello，World！" 应用](/windows/uwp/get-started/create-a-basic-windows-10-app-in-cpp)
-   [使用 JavaScript 创建 "Hello，World！" 应用](../get-started/create-a-hello-world-app-js-uwp.md)

**注意**   对于使用三维图形的应用程序，OpenGL 和 OpenGL ES 标准并不本机可用于 UWP 应用。 如果你不愿意将 OpenGL ES 代码重新写入到 Microsoft DirectX，你可能会有兴趣了解 **“角度”**。 角度是一个持续项目，旨在将 OpenGL API 调用转换为 DirectX API 调用，以将 OpenGL 转换为 DirectX。 若要了解更多信息，请参阅下列文章：
-   [夹角](https://bugs.chromium.org/p/angleproject/)
-   [使用 DirectX 创建第一个 UWP 应用](https://docs.microsoft.com/previous-versions/windows/apps/br229580(v=win.10))
-   [使用 DirectX 的 UWP 应用示例](https://docs.microsoft.com/samples/browse/?expanded=windows&products=windows-uwp&terms=directx)
-   [DirectX SDK 在哪里？](https://docs.microsoft.com/windows/desktop/directx-sdk--august-2009-)

## <a name="giving-c-a-go"></a>尝试 C#

作为 iOS 开发人员，你已习惯使用 Objective-C 和 Swift。 C# 是与 Objective-C 和 Swift 最相似的 Microsoft 编程语言。 对于大多数开发人员和大多数应用而言，我们认为 C# 是可供学习和使用的最容易、最快速的语言，因此本文的信息和演练将以该语言为主。 若要了解有关 C# 的详细信息，请参阅以下内容：

-   [使用 c # 或 Visual Basic 创建第一个 UWP 应用](../get-started/create-a-hello-world-app-xaml-universal.md)
-   [使用 C 的 UWP 应用示例#](https://docs.microsoft.com/samples/browse/?expanded=windows&products=windows-uwp&languages=csharp)
-   [Visual C#](https://msdn.microsoft.com/library/kx37x362.aspx)

下面是一个使用 Objective-C 和 C# 编写的类。 首先显示 Objective-C 版本，然后是 C# 版本。

```obj-c
// Objective-C header: SampleClass.h.

#import <Foundation/Foundation.h>

@interface SampleClass : NSObject {
    BOOL localVariable;
}

@property (nonatomic) BOOL localVariable;

-(int) addThis: (int) firstNumber andThis: (int) secondNumber;

@end
```

```obj-c
// Objective-C implementation.

#import "SampleClass.h"

@implementation SampleClass

@synthesize localVariable = _localVariable;

- (id)init {
    self = [super init];
    if (self) {
        localVariable = true;
    }
    return self;
}

-(int) addThis: (int) firstNumber andThis: (int) secondNumber {
    return firstNumber + secondNumber;
}

@end
```

```obj-c
// Objective-C usage.

SampleClass *mySampleClass = [[SampleClass alloc] init];
mySampleClass.localVariable = false;
int result = [mySampleClass addThis:1 andThis:2];
```

现在来看一下 C# 版本。 就像 Swift，你将看到标头和实现不在单独的文件中。

```csharp
// C# header and implementation.

using System;

namespace MyApp  // Defines this code' s scope.
{
    class SampleClass
    {
        private bool localVariable;

        public SampleClass() // Constructor.
        {
            localVariable = true;
        }

        public bool myLocalVariable // Property.
        {
            get
            {
                return localVariable;
            }
            set
            {
                localVariable = value; 
            }
        }

        public int AddTwoNumbers(int numberOne, int numberTwo)
        {
            return numberOne + numberTwo;
        }        
    }
}
```

```csharp
// C# usage.

SampleClass mySampleClass = new SampleClass();
mySampleClass.myLocalVariable = false;
int result = mySampleClass.AddTwoNumbers(1, 2);
```

C# 是一种简单易学的语言，并附带构成 .NET 的许多支持类和框架。 在任何时间，你都将愉快地编写代码，并且看不到方括号。

## <a name="next-step"></a>后续步骤

[入门：熟悉 Visual Studio 环境](getting-started-getting-around-in-visual-studio.md)
