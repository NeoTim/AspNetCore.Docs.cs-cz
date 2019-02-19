---
title: Rozložení v ASP.NET Core
author: ardalis
description: Zjistěte, jak používat společné rozložení, sdílet direktivy a spustit běžné kód před vykreslení zobrazení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/18/2018
uid: mvc/views/layout
ms.openlocfilehash: 1bd225c804b333efea834a46b7d9ba46b1bb69d8
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410570"
---
# <a name="layout-in-aspnet-core"></a>Rozložení v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/) a [společnosti Dave Brock](https://twitter.com/daveabrock)

Zobrazení stránky a často sdílení visual a programové prvků. Tento článek ukazuje, jak:

* Použití běžných rozložení.
* Sdílet direktivy.
* Spusťte společný kód před vykreslování stránky nebo zobrazení.

Tento dokument popisuje rozložení pro dva různé přístupy k ASP.NET Core MVC: A řadiče se zobrazeními Razor Pages. Pro toto téma jsou minimální rozdíly:

* Stránky Razor jsou v *stránky* složky.
* Kontrolery se používá k zobrazení *zobrazení* složku pro zobrazení.

## <a name="what-is-a-layout"></a>Co je rozložení

Většina webových aplikací mají společné rozložení, který poskytuje uživatelům konzistentní prostředí při práci ze stránky na stránku. Rozložení obvykle zahrnuje společné prvky uživatelského rozhraní, jako je například aplikace záhlaví, navigaci nebo prvky nabídky a zápatí.

![Příklad rozložení stránky](layout/_static/page-layout.png)

Společné struktury HTML, jako jsou skripty a šablony stylů také často používá mnoho stránek v rámci aplikace. Všechny tyto sdílené prvky mohou být definovány v *rozložení* soubor, který může odkazovat ve všech zobrazeních použít v aplikaci. Rozložení snížit duplicitního kódu v zobrazeních.

Podle konvence je výchozí rozložení aplikace ASP.NET Core s názvem *_Layout.cshtml*. Soubor rozložení pro nové projekty ASP.NET Core, které jsou vytvořené pomocí šablon:

* Stránky Razor: *Pages/Shared/_Layout.cshtml*

  ![stránky složku v Průzkumníku řešení](layout/_static/rp-web-project-views.png)

* Kontroler zobrazení: *Views/Shared/_Layout.cshtml*

 ![zobrazení složky v Průzkumníku řešení](layout/_static/mvc-web-project-views.png)

Rozložení definuje šablonu nejvyšší úrovně pro zobrazení v aplikaci. Aplikace nevyžadují rozložení. Aplikace můžete definovat více než jedno rozložení s různá zobrazení zadání různá rozložení.

Následující kód ukazuje soubor rozložení pro vytvoření projektu s kontroler a zobrazení šablony:

[!code-html[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Určení rozložení

Zobrazení Razor je k dispozici `Layout` vlastnost. Jednotlivá zobrazení zadat rozložení tak, že nastavíte tuto vlastnost:

[!code-html[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Zadané rozložení můžete použít úplnou cestu (například */Pages/Shared/_Layout.cshtml* nebo */Views/Shared/_Layout.cshtml*) nebo částečný název (Příklad: `_Layout`). Když částečný název zadán, bude hledat rozložení souboru pomocí jeho procesu zjišťování standardní zobrazovací modul Razor. Složky, pokud existuje metoda obslužné rutiny (nebo řadič) je nejprve prohledán, za nímž následuje *Shared* složky. Tento proces zjišťování je stejný jako ten, který používá ke zjišťování [částečná zobrazení](partial.md).

Ve výchozím nastavení, musí volat každou rozložení `RenderBody`. Všude, kde volání `RenderBody` je umístěn, bude vykreslen obsah zobrazení.

<a name="layout-sections-label"></a>

### <a name="sections"></a>Oddíly

Rozložení můžete volitelně odkazovat na jeden nebo více *oddíly*, voláním `RenderSection`. Oddíly umožňují organizovat umístění některých prvků stránky. Každé volání `RenderSection` můžete určit, zda je tento oddíl požadované nebo volitelné:

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

Pokud není nalezen požadovaný oddíl, je vyvolána výjimka. Jednotlivá zobrazení zadejte obsah, který mohl být vykreslen v rámci oddílu pomocí `@section` syntaxi Razor. Pokud na stránce nebo zobrazení určující sekci, je nutné vykreslit (nebo dojde k chybě).

Příklad `@section` definice v zobrazení pro stránky Razor:

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

V předchozím kódu *scripts/main.js* se přidá do `scripts` části na stránku nebo zobrazení. Další stránky nebo zobrazení ve stejné aplikaci nemusí potřebovat tento skript a nebude nadefinoval oddíl skripty.

Následující kód používá [pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) k vykreslení *_ValidationScriptsPartial.cshtml*:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

Předchozí kód generovaný serverem [generování uživatelského rozhraní Identity](xref:security/authentication/scaffold-identity).

Oddíly definované v stránku nebo zobrazení jsou k dispozici pouze v její okamžitý rozložení stránky. Nelze se odkazovat z částečných zobrazení, zobrazení komponenty nebo jiných částí systému zobrazení.

### <a name="ignoring-sections"></a>Oddíly se ignoruje

Ve výchozím nastavení text a všechny části na stránce obsahu musí všechny zobrazovat na stránce rozložení. Zobrazovací modul Razor vynucuje Díky sledování, zda byl vykreslen text a každý oddíl.

Chcete-li dát pokyn zobrazovací modul, aby ignoroval text nebo oddíly, zavolejte `IgnoreBody` a `IgnoreSection` metody.

Text a každý oddíl na stránce Razor musí být buď vykreslen nebo ignorovat.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Import sdílených direktivy

Zobrazení a stránky můžete použít direktivy Razor pro import obory názvů a použití [injektáž závislostí](dependency-injection.md). Direktivy, které sdílí mnoho zobrazení je možné zadat běžný *_ViewImports.cshtml* souboru. `_ViewImports` Soubor podporuje následující direktivy:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

Tento soubor nepodporuje další funkce Razor, jako je například funkce a definice části.

Ukázka `_ViewImports.cshtml` souboru:

[!code-html[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

*_ViewImports.cshtml* soubor pro aplikaci ASP.NET Core MVC je obvykle umístěn ve *stránky* (nebo *zobrazení*) složky. A *_ViewImports.cshtml* souboru je možné použít v jakékoli složce, v takovém případě se použijí jenom u ke stránkám nebo zobrazení v této složce a jejích podsložkách. `_ViewImports` soubory se zpracovávají spouštění na kořenové úrovni a potom pro každou složku dovedou až k pozici stránky nebo zobrazení samotný. `_ViewImports` bylo nastaveno na kořenové úrovni může přepsat na úrovni složek.

Například předpokládejme, že:

* Kořenové úrovni *_ViewImports.cshtml* soubor obsahuje `@model MyModel1` a `@addTagHelper *, MyTagHelper1`.
* Podsložky *_ViewImports.cshtml* soubor obsahuje `@model MyModel2` a `@addTagHelper *, MyTagHelper2`.

Stránky a zobrazení v podsložce bude mít přístup k oběma pomocných rutin značek a `MyModel2` modelu.

Pokud je položek víc *_ViewImports.cshtml* soubory se nacházejí v hierarchii souborů kombinované chování direktivy jsou:

* `@addTagHelper`, `@removeTagHelper`: všech spuštění, v pořadí
* `@tagHelperPrefix`: na ten nejbližší do zobrazení přepíše všechny ostatní
* `@model`: na ten nejbližší do zobrazení přepíše všechny ostatní
* `@inherits`: na ten nejbližší do zobrazení přepíše všechny ostatní
* `@using`: všechny jsou zahrnuty; duplicity se ignorují.
* `@inject`: pro každou vlastnost na ten nejbližší do zobrazení přepíše všechny ostatní se stejným názvem vlastnosti

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Spuštění kódu před každou zobrazení

Kód, který je potřeba spustit před každou zobrazení nebo stránky musí být umístěné ve *soubor _ViewStart.cshtml* souboru. Podle konvence *soubor _ViewStart.cshtml* soubor se nachází v *stránky* (nebo *zobrazení*) složky. Příkazy uvedené v *soubor _ViewStart.cshtml* jsou spouštěny před každou úplné zobrazení (nikoli rozložení a není částečná zobrazení). Stejně jako [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *soubor _ViewStart.cshtml* jsou hierarchická. Pokud *soubor _ViewStart.cshtml* soubor je definován ve složce zobrazení nebo stránky se spustí po definovanému v kořenovém adresáři *stránky* (nebo *zobrazení*) složku (pokud existuje).

Ukázka *soubor _ViewStart.cshtml* souboru:

[!code-html[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Výše uvedeného souboru Určuje, zda budou používat všechna zobrazení *_Layout.cshtml* rozložení.

*Soubor _ViewStart.cshtml* a *_ViewImports.cshtml* jsou **není** obvykle umístěné v */stránek/Shared* (nebo   */zobrazení/Shared*) složka. Verze těchto souborů úrovni aplikace by měl být umístěn v přímo */stránky* (nebo */zobrazení*) složky.
