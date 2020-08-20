---
title: Rozložení v ASP.NET Core
author: ardalis
description: Naučte se používat běžné rozložení, direktivy sdílení a spouštět společný kód před vykreslením zobrazení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 308e567e0480f83972ab7a55c7b957af83a164fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630689"
---
# <a name="layout-in-aspnet-core"></a>Rozložení v ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Dave Brock](https://twitter.com/daveabrock)

Stránky a zobrazení často sdílí vizuální a programové prvky. Tento článek ukazuje, jak:

* Použijte společná rozložení.
* Direktivy Share.
* Před vykreslováním stránek nebo zobrazení spouštějte běžný kód.

Tento dokument popisuje rozložení dvou různých přístupů k ASP.NET Core MVC: Razor stránky a řadiče se zobrazeními. Rozdíly jsou pro toto téma minimální:

* Razor Stránky jsou ve složce *stránky* .
* Řadiče se zobrazeními používají pro zobrazení složku *zobrazení* .

## <a name="what-is-a-layout"></a>Co je rozložení

Většina webových aplikací má společné rozložení, které uživatelům poskytuje konzistentní prostředí při přechodu ze stránky na stránku. Rozložení obvykle zahrnuje běžné prvky uživatelského rozhraní, jako jsou záhlaví aplikace, navigace nebo prvky nabídky a zápatí.

![Příklad rozložení stránky](layout/_static/page-layout.png)

Mnoho stránek v aplikaci také často používá běžné struktury HTML, jako jsou skripty a šablony stylů. Všechny tyto sdílené prvky mohou být definovány v souboru *rozložení* , na který lze následně odkazovat jakýmkoli zobrazením použitým v aplikaci. Rozložení omezují výskyt duplicitního kódu v zobrazeních.

Podle konvence je výchozím rozložením pro aplikaci ASP.NET Core název *_Layout. cshtml*. Soubory rozložení pro nové projekty ASP.NET Core vytvořené pomocí šablon jsou:

* Razor Stránky: *stránky/Shared/_Layout. cshtml*

  ![Složka stránky v Průzkumník řešení](layout/_static/rp-web-project-views.png)

* Kontroler se zobrazeními: *views/Shared/_Layout. cshtml*

  ![Složka zobrazení v Průzkumník řešení](layout/_static/mvc-web-project-views.png)

Rozložení definuje šablonu na nejvyšší úrovni pro zobrazení v aplikaci. Aplikace nevyžadují rozložení. Aplikace mohou definovat více než jedno rozložení s různými zobrazeními určujícími různá rozložení.

Následující kód ukazuje soubor rozložení pro šablonu vytvořenou v projektu pomocí kontroleru a zobrazení:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Určení rozložení

Razor zobrazení mají `Layout` vlastnost. Jednotlivá zobrazení určují rozložení nastavením této vlastnosti:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Zadané rozložení může použít úplnou cestu (například */Pages/Shared/_Layout. cshtml* nebo */views/Shared/_Layout. cshtml*) nebo částečný název (například: `_Layout` ). Pokud je k dispozici částečný název, Razor modul zobrazení vyhledá soubor rozložení pomocí standardního procesu zjišťování. Složka, ve které existuje metoda obslužné rutiny (nebo kontrolér), je nejdříve prohledána a za ní následuje *sdílená* složka. Tento proces zjišťování je stejný jako proces, který se používá ke zjišťování [částečných zobrazení](xref:mvc/views/partial#partial-view-discovery).

Ve výchozím nastavení musí každé rozložení volat `RenderBody` . Všude `RenderBody` , kde je umístěno volání, se vykreslí obsah zobrazení.

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>Oddíly

Rozložení může volitelně odkazovat na jeden nebo více *oddílů*voláním `RenderSection` . Oddíly poskytují způsob, jak uspořádat, kde by měly být umístěny určité prvky stránky. Každé volání, které umožňuje `RenderSection` určit, zda je tato část povinná nebo volitelná:

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

Pokud není požadovaný oddíl nalezen, je vyvolána výjimka. Jednotlivá zobrazení určují obsah, který se má vykreslit v rámci oddílu pomocí `@section` Razor syntaxe. Pokud stránka nebo zobrazení definují oddíl, je nutné jej vykreslit (nebo dojde k chybě).

Příklad `@section` definice v Razor zobrazení stránky:

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

V předchozím kódu se *skripty/main.js* přidaly do `scripts` oddílu na stránce nebo zobrazení. Jiné stránky nebo zobrazení ve stejné aplikaci pravděpodobně nevyžadují tento skript a nedefinují oddíl Scripts.

Následující kód používá [pomocníka částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) k vykreslování  *_ValidationScriptsPartial. cshtml*:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

Předchozí kód byl vygenerován pomocí [generování uživatelského Identity rozhraní ](xref:security/authentication/scaffold-identity).

Oddíly definované na stránce nebo zobrazení jsou k dispozici pouze na stránce jejího okamžitého rozložení. Nelze na ně odkazovat z částečných, zobrazení součástí nebo jiných částí systému zobrazení.

### <a name="ignoring-sections"></a>Ignorují se oddíly.

Ve výchozím nastavení musí být tělo a všechny oddíly na stránce obsahu vykresleny na stránce rozložení. RazorModul zobrazení tento modul vynutil sledováním, zda byl text a jednotlivé oddíly vykresleny.

Chcete-li, aby modul zobrazení mohl ignorovat tělo nebo oddíly, zavolejte `IgnoreBody` `IgnoreSection` metody a.

Tělo a každý oddíl Razor stránky musí být vykresleny nebo ignorovány.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Import sdílených direktiv

Zobrazení a stránky můžou pomocí Razor direktiv importovat obory názvů a používat [vkládání závislostí](dependency-injection.md). Direktivy sdílené pomocí mnoha zobrazení mohou být zadány v běžném souboru *_ViewImports. cshtml* . `_ViewImports`Soubor podporuje následující direktivy:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

Soubor nepodporuje jiné Razor funkce, jako jsou třeba definice funkcí a oddílů.

Vzorový `_ViewImports.cshtml` soubor:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

Soubor *_ViewImports. cshtml* pro aplikaci ASP.NET Core MVC je obvykle umístěn ve složce *Pages* (nebo *views*). *_ViewImports soubor. cshtml* lze umístit do jakékoli složky. v takovém případě bude použito pouze na stránky nebo zobrazení v této složce a jejích podsložkách. `_ViewImports` soubory jsou zpracovávány na kořenové úrovni a následně pro každou složku, která je v umístění stránky nebo samotného zobrazení. `_ViewImports` nastavení zadaná na kořenové úrovni lze přepsat na úrovni složky.

Předpokládejme například:

* *_ViewImports soubor. cshtml* na kořenové úrovni zahrnuje `@model MyModel1` a `@addTagHelper *, MyTagHelper1` .
* Podsložka  *_ViewImports soubor. cshtml* obsahuje `@model MyModel2` a `@addTagHelper *, MyTagHelper2` .

Stránky a zobrazení v podsložce budou mít přístup k oběma pomocníkům značek i k `MyModel2` modelu.

Pokud je v hierarchii souborů nalezeno více *_ViewImports. cshtml* souborů, kombinované chování direktiv jsou:

* `@addTagHelper`, `@removeTagHelper` : všechny spuštěné, v pořadí
* `@tagHelperPrefix`: nejbližší z nich přepisuje všechny ostatní.
* `@model`: nejbližší z nich přepisuje všechny ostatní.
* `@inherits`: nejbližší z nich přepisuje všechny ostatní.
* `@using`: vše je zahrnuto; duplicity se ignorují.
* `@inject`: pro každou vlastnost, která je nejblíže k zobrazení, potlačí všechny ostatní se stejným názvem vlastnosti.

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Spuštění kódu před každým zobrazením

Kód, který musí být spuštěn před každým zobrazením nebo stránkou, by měl být umístěn v souboru *_ViewStart. cshtml* . Podle konvence je soubor *_ViewStart. cshtml* umístěný ve složce *Pages* (nebo *views*). Příkazy uvedené v *_ViewStart. cshtml* jsou spouštěny před všemi úplnými zobrazeními (nikoli rozloženími, nikoli v částečných zobrazeních). Podobně jako [ViewImports. cshtml](xref:mvc/views/layout#viewimports)je *_ViewStart. cshtml* hierarchicky. Je-li soubor *_ViewStart. cshtml* definován ve složce zobrazení nebo stránky, bude spuštěn po jednom definovaném kořenu složky *stránky* (nebo *zobrazení*) (pokud existuje).

Ukázkový *_ViewStart soubor. cshtml* :

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Výše uvedený soubor určuje, že všechna zobrazení budou používat rozvržení *_Layout. cshtml* .

*_ViewStart. cshtml* a *_ViewImports. cshtml* nejsou **obvykle umístěny** ve složce */Pages/Shared* (nebo */views/Shared*). Verze těchto souborů na úrovni aplikace by měly být umístěné přímo ve složce */Pages* (nebo */views*).
