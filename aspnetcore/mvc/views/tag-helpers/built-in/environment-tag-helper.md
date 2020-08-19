---
title: Pomocná značka prostředí v ASP.NET Core
author: pkellner
description: ASP.NET Core pomocná podpora značek prostředí, včetně všech vlastností
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: de37bf704ac377a15ef600b96ba78056c51ea7be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633861"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Pomocná značka prostředí v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net) a [Hisham bin Ateya](https://twitter.com/hishambinateya)

Pomocník pro tag prostředí podmíněně vykreslí svůj obsah na základě aktuálního [hostitelského prostředí](xref:fundamentals/environments). Jediný atribut pomocníka značky prostředí `names` je čárkami oddělený seznam názvů prostředí. Pokud se některý z poskytnutých názvů prostředí shoduje s aktuálním prostředím, zobrazí se obsah přiložený k obsahu.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .

## <a name="environment-tag-helper-attributes"></a>Pomocné atributy značek prostředí

### <a name="names"></a>názvy

`names` přijímá jeden název hostitelského prostředí nebo seznam názvů hostitelských prostředí oddělených čárkami, které aktivují vykreslování vloženého obsahu.

Hodnoty prostředí jsou porovnány s aktuální hodnotou vrácenou funkcí [IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*). Porovnání ignoruje velikost písmen.

V následujícím příkladu je použita pomocná pomůcka značek prostředí. Obsah se vykreslí, pokud je hostující prostředí pracovní nebo produkční:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>zahrnutí a vyloučení atributů

`include`& `exclude` ovládací prvek atributy vykreslovat vložený obsah na základě zahrnutých nebo vyloučených názvů hostitelských prostředí.

### <a name="include"></a>include

`include`Vlastnost vykazuje podobné chování `names` atributu. Prostředí uvedené v `include` hodnotě atributu se musí shodovat s hostitelským prostředím aplikace ([IHostingEnvironment. Environment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) pro vykreslení obsahu `<environment>` značky.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>vyloučení

Na rozdíl od `include` atributu je obsah značky vykreslen v případě, že se `<environment>` hostitelské prostředí neshoduje s prostředím uvedeným v `exclude` hodnotě atributu.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/environments>
