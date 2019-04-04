---
title: Zobrazení v ASP.NET Core MVC
author: ardalis
description: Zjistěte, jak zpracovat zobrazení prezentace dat aplikace a interakce uživatelů v ASP.NET Core MVC.
ms.author: riande
ms.date: 04/03/2019
uid: mvc/views/overview
ms.openlocfilehash: 766996645bc6ef2b6be42d729baf5d57f55b6ddd
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012796"
---
# <a name="views-in-aspnet-core-mvc"></a>Zobrazení v ASP.NET Core MVC

Podle [Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)

Tento dokument popisuje zobrazení použít v aplikacích ASP.NET Core MVC. Informace o stránky Razor, naleznete v tématu [Úvod do stránky Razor](xref:razor-pages/index).

V modelu Model-View-Controller (MVC) *zobrazení* aplikace data prezentace a uživatelské interakce zpracovává. Zobrazení je šablonu HTML s vloženými [kód Razor](xref:mvc/views/razor). Kód Razor je kód, který komunikuje s kódu HTML, který vytvoří webovou stránku, která je odeslána do klienta.

V ASP.NET Core MVC se zobrazeními *.cshtml* soubory, které používají [programovací jazyk C#](/dotnet/csharp/) v kódu Razor. Obvykle jsou seskupené zobrazit soubory do složky s názvem pro jednotlivé aplikace [řadiče](xref:mvc/controllers/actions). Složky jsou uložené v *zobrazení* složku v kořenovém adresáři aplikace:

![Zobrazení složky řešení Průzkumníka Visual Studio je otevřený a nabízí domovské složky otevřete zobrazení soubory About.cshtml Contact.cshtml a Index.cshtml](overview/_static/views_solution_explorer.png)

*Domů* kontroleru je reprezentována *Domů* složky uvnitř *zobrazení* složky. *Domů* složka obsahuje zobrazení pro *o*, *kontakt*, a *Index* webové stránky (domovské stránky). Když si uživatel vyžádá jednu z těchto tří webových stránek, akce kontroleru v *Domů* řadič určit, který tři zobrazení, které se používá k sestavení a vrátit na webové stránce uživateli.

Použití [rozložení](xref:mvc/views/layout) k zajištění konzistentní webové části a omezení opakování kódu. Rozložení často obsahují záhlaví, prvky navigace a nabídky a zápatí. Záhlaví a zápatí obvykle obsahuje často používaný kód pro mnoho prvků metadata a odkazy na skript a styl prostředky. Rozložení umožňuje vyhnout se tento často používaný kód v zobrazení.

[Částečná zobrazení](xref:mvc/views/partial) snížení dojde k duplikaci kódu pomocí správy opakovaně použitelné součásti zobrazení. Například je užitečné pro Autor životopis na webu, blogu, který se zobrazí několik zobrazení částečného zobrazení. Vytváření životopis je běžné zobrazení obsahu a nevyžaduje, aby kód provést, aby vytvoření obsahu pro webovou stránku. Autor životopis obsah je dostupný zobrazením vazby modelu samostatně, takže použití částečné zobrazení pro tento typ obsahu je ideální.

[Zobrazení komponenty](xref:mvc/views/view-components) jsou podobná částečné zobrazení, umožňují omezení opakování kódu, ale jsou vhodné pro zobrazení obsahu, který vyžaduje kód pro spuštění na serveru k vykreslení na webovou stránku. Zobrazení komponenty jsou užitečné, když vykreslovaný obsah vyžaduje interakci databáze, například pro webovou stránku nákupní košík. Komponenty zobrazení nejsou omezeni na vazby modelu, aby bylo možné generovat výstup webové stránky.

## <a name="benefits-of-using-views"></a>Výhody použití zobrazení

Zobrazení nápovědy k navázání [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) v rámci aplikace MVC oddělením značky uživatelského rozhraní od jiných částí aplikace. Podle SoC návrh umožňuje aplikaci modulární, který poskytuje několik výhod:

* Aplikace je snazší Údržba, protože je líp uspořádané. Zobrazení jsou obecně seskupené podle funkcí aplikace. Díky tomu je snazší najít související zobrazení při práci na funkci.
* Součástí aplikace jsou volně vázány. Můžete vytvářet a aktualizovat zobrazení aplikace nezávisle na součásti obchodní logiku a data access. Zobrazení aplikace můžete změnit bez nutnosti nutně aktualizovat ostatní části aplikace.
* Je snazší testování části uživatelského rozhraní aplikace, protože zobrazení jsou samostatné jednotky.
* Kvůli lepší organizaci je méně pravděpodobné, že omylem opakováním části uživatelského rozhraní.

## <a name="creating-a-view"></a>Vytvoření zobrazení

Zobrazení, které jsou specifické pro určitý kontroler vytvářejí *zobrazení / [parametr ControllerName]* složky. Zobrazení, která jsou sdílena mezi řadiče jsou umístěny v *zobrazení/Shared* složky. Vytvoření zobrazení, přidejte nový soubor a poskytněte stejný název jako akcí k přidruženému kontroleru se *.cshtml* příponu souboru. Vytvoření zobrazení, která odpovídá *o* v akci *Domů* vytvoření kontroleru, *About.cshtml* ve *zobrazení Domů*složky:

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* značek začíná `@` symbol. Spusťte příkazy jazyka C# tak, že C# kódu v rámci [bloky kódu Razor](xref:mvc/views/razor#razor-code-blocks) nastavit ve složených závorkách (`{ ... }`). Příklad najdete v sekci přiřazení "O" `ViewData["Title"]` uvedené výše. Můžete jednoduše odkazující na hodnotu s zobrazit hodnoty v kódu HTML `@` symbol. Zobrazit obsah `<h2>` a `<h3>` prvků uvedených výše.

Zobrazit obsah výše uvedené je pouze část celé webové stránky, které je vykresleno pro uživatele. Zbývající rozložení stránky a dalších běžných aspektů zobrazení jsou uvedeny v dalších souborů zobrazení. Další informace najdete v tématu [rozložení tématu](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Jak určit kontrolerů zobrazení

Zobrazení jsou obvykle vrácená z akce, jako je [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), což je typ [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Můžete vytvořit a vrátí metodu akce `ViewResult` přímo, ale neprovádí běžně. Protože většina řadičů dědit z [řadič](/dotnet/api/microsoft.aspnetcore.mvc.controller), jednoduše použít `View` pomocnou metodu se vraťte `ViewResult`:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Po návratu tato akce *About.cshtml* zobrazení je znázorněno v předchozí části se vykreslí jako na následující webové stránce:

![O stránku zpracovanou v prohlížeči Microsoft Edge](overview/_static/about-page.png)

`View` Pomocná metoda má několik přetížení. Volitelně můžete zadat:

* Explicitní zobrazení vrátit:

  ```csharp
  return View("Orders");
  ```

* A [modelu](xref:mvc/models/model-binding) k předání do zobrazení:

  ```csharp
  return View(Orders);
  ```

* Zobrazení a modelu:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Zobrazení zjišťování

Po návratu akce zobrazení, proces se nazývá *zobrazení zjišťování* probíhá. Tento proces Určuje soubor, který zobrazení se používá na základě názvu zobrazení. 

Výchozí chování `View` – metoda (`return View();`) je vrátit zobrazení s názvem, ze kterého je volána metoda akce. Například *o* `ActionResult` název metody kontroleru se používá k zobrazení souboru s názvem vyhledat *About.cshtml*. Nejprve, modul runtime hledá v *zobrazení / [parametr ControllerName]* složku pro zobrazení. Pokud se nenajde odpovídající zobrazení, hledá *Shared* složku pro zobrazení.

Nebude vadit, když se implicitně vrátit `ViewResult` s `return View();` nebo explicitně předávat název zobrazení, který má `View` metodu s `return View("<ViewName>");`. V obou případech zobrazení zjišťování vyhledá odpovídající soubor zobrazení v tomto pořadí:

   1. *Views/\[ControllerName]/\[ViewName].cshtml*
   1. *Views/Shared/\[ViewName].cshtml*

Místo názvu zobrazení lze zadat cestu k souboru zobrazení. Pokud se používá absolutní cesta od kořenové aplikace (volitelně můžete od verze "/" nebo "~ /"), *.cshtml* rozšíření je nutné zadat:

```csharp
return View("Views/Home/About.cshtml");
```

Můžete také použít relativní cestu k určení zobrazení v různých adresářích bez *.cshtml* rozšíření. Uvnitř `HomeController`, můžete se vrátit *Index* zobrazení vaší *spravovat* zobrazení s relativní cestou:

```csharp
return View("../Manage/Index");
```

Podobně můžete určit aktuální adresář konkrétní řadič se ". /" předpona:

```csharp
return View("./About");
```

[Částečná zobrazení](xref:mvc/views/partial) a [zobrazení součástí](xref:mvc/views/view-components) pomocí mechanismů zjišťování podobný (ale nejsou identické).

Můžete přizpůsobit výchozí konvence pro způsob zobrazení se nachází v rámci aplikace s použitím vlastní [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Zobrazení zjišťování spoléhá na hledání zobrazit soubory podle názvu souboru. Pokud podkladový systém souborů je velká a malá písmena, zobrazit názvy jsou pravděpodobně malá a velká písmena. Z důvodu kompatibility v operačních systémech rozlišovat velikost písmen mezi kontroleru a akce názvy a přidružené zobrazení složek a souborů. Pokud narazíte na chybu při práci se systémem souborů s rozlišením velkých nebyl nalezen soubor zobrazení, potvrzení, že malých a velkých písmen odpovídá mezi požadované zobrazení souboru a název souboru skutečné zobrazení.

Dodržíte osvědčený postup uspořádání struktura souborů pro zobrazení tak, aby odrážely vztahy mezi kontrolerů, akce a zobrazení udržovatelnost a srozumitelnější.

## <a name="passing-data-to-views"></a>Předání dat pro zobrazení

Předejte data do zobrazení pomocí několik přístupů:

* Data se silnými typy: viewmodel
* Slabě typovaná dat
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Dat silného typu (viewmodel)

Se nejrobustnější zadává [modelu](xref:mvc/models/model-binding) typ v zobrazení. Tento model se často označuje jako *viewmodel*. Předejte instanci typu viewmodel do zobrazení z akce.

Použití viewmodel k předávání dat k zobrazení umožňuje zobrazit výhod *silné* kontroly typu. *Silné typování* (nebo *silného typu*) znamená, že všechny proměnné a konstanty má typ, který explicitně definovány (například `string`, `int`, nebo `DateTime`). Platnost typy používané v zobrazení je zaškrtnuté políčko v době kompilace.

[Visual Studio](https://www.visualstudio.com/vs/) a [Visual Studio Code](https://code.visualstudio.com/) seznam členů tříd se silnými typy použití funkce volána [IntelliSense](/visualstudio/ide/using-intellisense). Pokud chcete zobrazit vlastnosti viewmodel, zadejte název proměnné pro viewmodel následovaných tečkou (`.`). To vám umožňuje napsat kód rychleji s menším množstvím chyb.

Zadat pomocí modelu `@model` směrnice. Použití modelu s `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

K poskytnutí modelu pro zobrazení, předává kontroleru je jako parametr:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Neexistují žádná omezení na typy modelů, které zadáte do zobrazení. Doporučujeme používat modely viewmodels prostý staré CLR objektů POCO s minimální nebo nulovou chování (metody) definované. Obvykle jsou tříd viewmodel buď uloženy v *modely* složku nebo samostatné *modely ViewModels* složku v kořenovém adresáři aplikace. *Adresu* viewmodel použitých v příkladu výše je viewmodel POCO, uloží se do souboru s názvem *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Nic neumožňuje použití stejné třídy pro viewmodel typů a typů modelu vaší firmy. Použití samostatných modelů však umožňuje zobrazení se liší od obchodní logiku a data nezávisle na sobě přístupu k částem vaší aplikace. Oddělení modely a modely viewmodels také nabízí výhody zabezpečení při použití modelů [vazby modelu](xref:mvc/models/model-binding) a [ověření](xref:mvc/models/validation) pro data odesílaná do aplikace uživatelem.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Slabě typované data (ViewData, atribut ViewData a objekt ViewBag)

`ViewBag` *není k dispozici v stránky Razor.*

Kromě zobrazení se silnými typy, zobrazení je k dispozici přístup k *slabě typované* (také nazývané *volně psaný*) shromažďování dat o. Na rozdíl od silné typy *slabé typy* (nebo *dojde ke snížení typy*) znamená, že je nemusíte deklarovat explicitně typu dat, které používáte. Shromažďování dat o slabě typované můžete použít pro předávání malých objemů dat do a z kontrolerů a zobrazení.

| Předávání dat mezi...                        | Příklad                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Kontrolerem a zobrazením                             | Naplnění rozevíracího seznamu s daty.                                          |
| Zobrazení a [rozložení zobrazení](xref:mvc/views/layout)   | Nastavení  **\<title >** obsahu elementu v zobrazení rozložení ze zobrazení souboru.  |
| [Částečné zobrazení](xref:mvc/views/partial) a zobrazení | Widget, který zobrazuje data založená na webové stránce, která uživatel si vyžádal.      |

Tato kolekce může být odkazováno prostřednictvím buď `ViewData` nebo `ViewBag` vlastnosti kontrolerů a zobrazení. `ViewData` Vlastnost je slovník slabě typované objekty. `ViewBag` Vlastnost představuje obálku kolem `ViewData` poskytující dynamické vlastnosti pro základní `ViewData` kolekce. Poznámka: Klíč hledání rozlišují velikost písmen u obou `ViewData` a `ViewBag`.

`ViewData` a `ViewBag` jsou vyřešeny dynamicky za běhu. Protože nenabízejí kontrolu typu za kompilace, obě jsou obecně více náchylné než při použití viewmodel. Z tohoto důvodu někteří vývojáři dáváte přednost minimálně nebo vůbec `ViewData` a `ViewBag`.

<a name="VD"></a>

**ViewData**

`ViewData` je [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) přistupuje prostřednictvím objektu `string` klíče. Řetězcová data můžete ukládat a používat přímo bez nutnosti přetypování, ale musíte přetypovat jiné `ViewData` hodnot na konkrétní typy objektu při jejich extrakci. Můžete použít `ViewData` k předání dat z řadiče, zobrazeními a v zobrazeních, včetně [částečná zobrazení](xref:mvc/views/partial) a [rozložení](xref:mvc/views/layout).

Tady je příklad, který nastavuje hodnoty pozdrav a adresy `ViewData` v akci:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Práce s daty v zobrazení:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**Atribut viewData**

Další možností, které používá [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) je [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Upravené vlastnosti v kontrolerech a modelech stránky Razor pomocí `[ViewData]` hodnoty uložené a načten ze slovníku.

V následujícím příkladu obsahuje kontroler Home `Title` vlastnost upravené pomocí `[ViewData]`. `About` Metody nastavuje název o zobrazení:

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

V zobrazení o přístup `Title` vlastnost jako vlastnost modelu:

```cshtml
<h1>@Model.Title</h1>
```

Název je v rozložení pro čtení ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**Objekt ViewBag**

`ViewBag` *není k dispozici v stránky Razor.*

`ViewBag` je [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) poskytující dynamické přístup k objektům, které jsou uložené v `ViewData`. `ViewBag` může být vhodnější pro práci s, protože nevyžaduje přetypování. Následující příklad ukazuje, jak používat `ViewBag` s stejný výsledek jako při použití `ViewData` výše:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Použití slovníku ViewData a objekt ViewBag současně**

`ViewBag` *není k dispozici v stránky Razor.*

Protože `ViewData` a `ViewBag` odkazovat na stejnou základní `ViewData` kolekce, je možné použít jak `ViewData` a `ViewBag` a kombinovat a párovat mezi nimi při čtení a zápisu hodnoty.

Sada s použitím názvu `ViewBag` a popis použití `ViewData` v horní části *About.cshtml* zobrazení:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Čtení vlastností, ale reverse použití `ViewData` a `ViewBag`. V *_Layout.cshtml* soubor, pomocí názvu získat `ViewData` a získat popis použití `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Mějte na paměti, že řetězce nevyžadují přetypování pro `ViewData`. Můžete použít `@ViewData["Title"]` bez přetypování.

Použitím `ViewData` a `ViewBag` ve stejný čas funguje, jako kombinace a porovnávání čtení a zápis do vlastnosti. Vykreslení následující značky:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Souhrnné informace o rozdílech mezi ViewData a objekt ViewBag**

 `ViewBag` není k dispozici v stránky Razor.

* `ViewData`
  * Je odvozen od [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), takže má slovníkové vlastnosti, které mohou být užitečné, například `ContainsKey`, `Add`, `Remove`, a `Clear`.
  * Klíče ve slovníku jsou řetězce, proto je povolen prázdný znak. Příklad: `ViewData["Some Key With Whitespace"]`
  * Jakýkoli typ jiný než `string` musíte přetypovat v zobrazení a použití `ViewData`.
* `ViewBag`
  * Je odvozen od [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), takže umožňuje vytváření dynamických vlastností pomocí zápisu s tečkou (`@ViewBag.SomeKey = <value or object>`), a není nutná žádná přetypování. Syntaxe `ViewBag` umožňuje rychlejší pro přidání do kontrolerů a zobrazení.
  * Jednodušší kontrola hodnot null. Příklad: `@ViewBag.Person?.Name`

**Použití slovníku ViewData nebo objekt ViewBag**

Obě `ViewData` a `ViewBag` jsou rovnoměrně platný přístupy k předání malé množství dat mezi kontrolerů a zobrazení. Volba používané podle předvoleb. Můžete kombinovat a párovat `ViewData` a `ViewBag` objekty, ale kód je jednodušší číst a spravovat s jedním z přístupů používány konzistentně. Oba přístupy jsou vyřešené dynamicky za běhu a proto náchylná k způsobující chyby za běhu. Některé vývojové týmy jim vyhnout.

### <a name="dynamic-views"></a>Dynamické zobrazení

Zobrazení, která není deklarovat modelu zadejte pomocí `@model` , ale dostatek instanci modelu předaný k nim (například `return View(Address);`) můžete odkazovat na instanci vlastnosti dynamicky:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Tato funkce nabízí flexibilitu, ale nenabízí ochrany kompilace ani IntelliSense. Pokud vlastnost neexistuje, webové stránky generování nezdaří za běhu.

## <a name="more-view-features"></a>Další zobrazení funkcí

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují snadno přidat chování na straně serveru k existující značky HTML. Použití pomocných rutin značek Polly nemusíte psát vlastní kód nebo pomocné rutiny v rámci zobrazení. Pomocné rutiny značek se použijí jako atributy prvků HTML a jsou ignorovány ve editorů, které nelze zpracovat. To umožňuje upravit a vykreslení zobrazení kódu v různých nástrojů.

Generování vlastní značky HTML lze nastavit pomocí mnoho integrovaných pomocných rutin HTML. Složitější logiku uživatelského rozhraní může být zpracována [komponenty zobrazení](xref:mvc/views/view-components). Zobrazení komponenty poskytují stejné SoC tohoto řadiče a nabídnout zobrazení. Odstraňují potřebu akcemi a zobrazeními, které pracují s daty používá společné prvky uživatelského rozhraní.

Stejně jako mnoho dalších aspektů ASP.NET Core, zobrazení podporu [injektáž závislostí](xref:fundamentals/dependency-injection), umožní službám být [vloženy do zobrazení](xref:mvc/views/dependency-injection).
