---
title: 环境光
description: 了解环境照明如何为场景提供恒定照明，并了解如何使用 c + + 在 Direct3D 中设置环境照明。
ms.assetid: C34FA65A-3634-4A4B-B183-4CDA89F4DC95
keywords:
- 环境光
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: c21a674b0961836752c879bcea681b568f31053c
ms.sourcegitcommit: 5481bb34def681bc60fbfa42d9779053febec468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304459"
---
# <a name="ambient-lighting"></a>环境光

环境光为场景提供恒定照明。 它会无差别地点亮所有对象顶点，因为它不取决于任意其他照明因素，例如，顶点法线、光线方向、光线位置、范围或衰减。 环境光在所有方向都是恒定的，并且会无差别地为某个对象的所有像素上色。 这是一种快速计算，但会让对象看上去扁平且不逼真。

环境光是最快速的照明类型，但会产生最不逼真的结果。 Direct3D 包含一个单独的全局环境光，无需创造任何光线就能使用。 或者，你可以设置任意光对象来提供环境光。

通过以下公式介绍场景的环境光。

环境照明 = C ₐ \* \[ g ₐ + Sum (Atten<sub>i</sub> \* 点焊<sub>i</sub> \* L<sub>ai</sub>) \]

其中：

| 参数         | 默认值 | 类型          | 说明                                                                                                       |
|-------------------|---------------|---------------|-------------------------------------------------------------------------------------------------------------------|
| Cₐ                | (0,0,0,0)     | D3DCOLORVALUE | 材料环境颜色                                                                                            |
| Gₐ                | (0,0,0,0)     | D3DCOLORVALUE | 全局环境颜色                                                                                              |
| Atten<sub>i</sub> | (0,0,0,0)     | D3DCOLORVALUE | 第 i 道光的光线衰减。 请参阅[衰减和聚焦因素](attenuation-and-spotlight-factor.md)。 |
| 专色<sub>i</sub>  | (0,0,0,0)     | D3DVECTOR     | 第 i 道光的聚焦因素。 请参阅[衰减和聚焦因素](attenuation-and-spotlight-factor.md)。  |
| sum               | 不适用           | 不适用           | 环境光的总和                                                                                          |
| L<sub>ai</sub>    | (0,0,0,0)     | D3DVECTOR     | 第 i 道光的光环境颜色                                                                              |

 

Cₐ 的值为：

-   顶点 color1，如果 AMBIENTMATERIALSOURCE = D3DMCS \_ color1，则在顶点声明中提供第一个顶点颜色。
-   顶点 color2 （如果 AMBIENTMATERIALSOURCE = D3DMCS \_ color2）和第二个顶点颜色是在顶点声明中提供的。
-   材料环境颜色。

**注意**   如果使用了任一 AMBIENTMATERIALSOURCE 选项，并且未提供顶点颜色，则使用材料环境颜色。

 

若要使用材料环境颜色，请使用 SetMaterial，如以下示例代码所示。

Gₐ 是全局环境颜色。 它使用 SetRenderState (D3DRS \_ 环境) 进行设置。 Direct3D 场景中有一种全局环境颜色。 此参数不与 Direct3D 光对象关联。

L<sub>ai</sub> 是场景中第 i 道光的环境颜色。 每个 Direct3D 灯都有一组属性，其中一个属性是环境光。 sum(L<sub>ai</sub>) 术语是场景中所有环境光的总和。

## <a name="span-idexamplespanspan-idexamplespanspan-idexamplespanexample"></a><span id="Example"></span><span id="example"></span><span id="EXAMPLE"></span>实例


在此示例中，使用场景环境光和材料环境光为对象着色。

```cpp
#define GRAY_COLOR  0x00bfbfbf

Ambient.r = 0.75f;
Ambient.g = 0.0f;
Ambient.b = 0.0f;
Ambient.a = 0.0f;
```

根据公式，生成的对象顶点颜色是材料颜色和光颜色的组合。

下面两个图例显示了材料颜色（灰色）和光颜色（鲜红色）。

![灰色球体的图例](images/amb1.jpg)![红色球体的图例](images/lightred.jpg)

生成的场景显示在以下图例中。 场景中的唯一对象是一个球体。 环境光使用相同的颜色点亮所有对象顶点。 它不取决于顶点法线或光线方向。 因此，球体看起来像 2D 圆形，因为对象表面周围的着色没有差别。

![有环境光的球体图例](images/lighta.jpg)

若要为对象提供更逼真的外观，除环境光外，请应用漫射或镜面反射。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>相关主题


[照明的数学运算](mathematics-of-lighting.md)

 

 




