---
title: Pomocná rutina značky prostředí v ASP.NET Core
author: pkellner
description: Pomocná rutina značky prostředí ASP.NET Core definované, včetně všech vlastností
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: e2e038fe69da696b67f7aef61795e23dc8512fdf
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856134"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Pomocná rutina značky prostředí v ASP.NET Core

Podle [Peter Kellner](https://peterkellner.net), [Hisham Bin Ateya](https://twitter.com/hishambinateya), a [Luke Latham](https://github.com/guardrex)

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
