---
title: Pomocná rutina značky prostředí v ASP.NET Core
author: pkellner
description: Pomocná rutina značky prostředí ASP.NET Core definované, včetně všech vlastností
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 379f58ed37329f047d53adf1dcfdfd2ad6a6ca4e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901002"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Pomocná rutina značky prostředí v ASP.NET Core

Podle [Peter Kellner](http://peterkellner.net), [Hisham Bin Ateya](https://twitter.com/hishambinateya), a [Luke Latham](https://github.com/guardrex)

Pomocná rutina značky prostředí podmíněně vykreslí obsah uzavřené na základě aktuálního [hostitelské prostředí](xref:fundamentals/environments). Pomocná rutina značky prostředí jeden atribut, `names`, je čárkou oddělený seznam názvů prostředí. Pokud se názvy zadané prostředí shodují v aktuálním prostředí, se uzavřené obsah zobrazí.

Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Atributy pomocné rutiny značky prostředí

### <a name="names"></a>názvy

`names` Přijímá jediný název hostitelské prostředí nebo čárkami oddělený seznam hostování názvy prostředí, které aktivují vykreslování uzavřené obsah.

Hodnoty prostředí jsou ve srovnání s aktuální hodnotu vrácenou příkazem [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). Porovnání ignoruje velikost písmen.

Následující příklad používá pomocná rutina značky prostředí. Pokud hostitelského prostředí je pracovní nebo produkční prostředí, je zobrazení obsahu:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>zahrnutí a vyloučení atributy

`include` & `exclude` atributy ovládacího prvku vykreslování uzavřené obsahu na základě zahrnuté ani vyloučené hostování prostředí názvů.

### <a name="include"></a>include

`include` Vlastnost vykazuje podobné chování jako `names` atribut. Uvedené v prostředí `include` hodnota atributu musí odpovídat aplikace hostitelské prostředí ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) k vykreslení obsahu `<environment>` značky.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

Rozdíl od `include` atribut obsah `<environment>` je vykreslen při hostování prostředí neodpovídá uvedené v prostředí `exclude` hodnotu atributu.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/environments>
