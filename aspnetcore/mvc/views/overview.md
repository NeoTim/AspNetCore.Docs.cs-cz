---
title: Pohledy v ASP.NET Jádra MVC
author: ardalis
description: Zjistěte, jak zobrazení zvládnou prezentaci dat aplikace a interakci s uživatelem v ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: 70b8c2c01a28f99dd384351041a3b77d23f46a48
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384072"
---
# <a name="views-in-aspnet-core-mvc"></a>Pohledy v ASP.NET Jádra MVC

Podle [Steve Smith](https://ardalis.com/)

Tento dokument vysvětluje zobrazení používaná v aplikacích Core MVC ASP.NET. Informace o stránkách Razor naleznete v [tématu Úvod do stránek Razor Pages](xref:razor-pages/index).

Ve vzoru Model-View-Controller (MVC) *zobrazení* zpracovává prezentaci dat aplikace a interakci s uživatelem. Zobrazení je šablona HTML s vloženými [značkami Razor](xref:mvc/views/razor). Razor značky je kód, který spolupracuje se značkami HTML k vytvoření webové stránky, která je odeslána klientovi.

V ASP.NET Core MVC jsou zobrazení *.cshtml* soubory, které používají [programovací jazyk C#](/dotnet/csharp/) v razor značky. Soubory zobrazení jsou obvykle seskupeny do složek pojmenovaných pro každý řadič aplikace [.](xref:mvc/controllers/actions) Složky jsou uloženy ve složce *Zobrazení* v kořenovém adresáři aplikace:

![Složka Zobrazení v Průzkumníku řešení sady Visual Studio je otevřená s otevřenou složky Domů, která zobrazuje soubory About.cshtml, Contact.cshtml a Index.cshtml](overview/_static/views_solution_explorer.png)

Domácí *Home* ovladač je reprezentován složky *Home* uvnitř složky *Zobrazení.* Složka *Domů* obsahuje zobrazení webových stránek *O* *aplikaci*, Kontakt a *Rejstřík* (domovská stránka). Když uživatel požádá o jednu z těchto tří webových stránek, akce kontroleru v *řadiči plochy* určují, které ze tří zobrazení se používá k vytvoření a vrácení webové stránky uživateli.

Rozložení [layouts](xref:mvc/views/layout) slouží k zajištění konzistentních oddílů webových stránek a snížení opakování kódu. Rozložení často obsahují záhlaví, navigaci a prvky nabídky a zápatí. Záhlaví a zápatí obvykle obsahují standardní značky pro mnoho prvků metadat a odkazy na prostředky skriptu a stylu. Rozložení vám pomohou vyhnout se této standardní značky v zobrazeních.

[Částečná zobrazení](xref:mvc/views/partial) snižují duplikaci kódu správou opakovaně použitelných částí zobrazení. Částečné zobrazení je například užitečné pro biografii autora na webu blogu, který se zobrazuje v několika zobrazeních. Životopis autora je běžný obsah zobrazení a nevyžaduje spuštění kódu, aby bylo možné vytvořit obsah pro webovou stránku. Obsah životopisu autora je k dispozici pohledu pouze pomocí vazby modelu, takže použití částečného zobrazení pro tento typ obsahu je ideální.

[Komponenty zobrazení](xref:mvc/views/view-components) jsou podobné částečným zobrazením v tom, že umožňují snížit opakující se kód, ale jsou vhodné pro zobrazení obsahu, který vyžaduje spuštění kódu na serveru za účelem vykreslení webové stránky. Komponenty zobrazení jsou užitečné, když vykreslený obsah vyžaduje interakci s databází, například pro nákupní košík webu. Součásti zobrazení nejsou omezeny na vazbu modelu za účelem vytvoření výstupu webové stránky.

## <a name="benefits-of-using-views"></a>Výhody použití zobrazení

Zobrazení pomáhají vytvořit [oddělení problémů](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) v rámci aplikace MVC oddělením značek uživatelského rozhraní od jiných částí aplikace. Následující soc design dělá vaši aplikaci modulární, což poskytuje několik výhod:

* Aplikace se snadněji udržuje, protože je lépe organizovaná. Zobrazení jsou obvykle seskupena podle funkce aplikace. To usnadňuje vyhledání souvisejících zobrazení při práci na prvku.
* Části aplikace jsou volně spojeny. Zobrazení aplikace můžete vytvářet a aktualizovat odděleně od obchodní logiky a součástí přístupu k datům. Můžete upravit zobrazení aplikace, aniž byste museli aktualizovat další části aplikace.
* Je jednodušší otestovat části uživatelského rozhraní aplikace, protože zobrazení jsou samostatné jednotky.
* Vzhledem k lepší organizaci je méně pravděpodobné, že budete náhodně opakovat části uživatelského rozhraní.

## <a name="creating-a-view"></a>Vytvoření zobrazení

Zobrazení, která jsou specifická pro řadič, jsou vytvořena ve složce *Zobrazení/[ControllerName].* Zobrazení, která jsou sdílena mezi řadiči, jsou umístěna ve složce *Zobrazení nebo Sdílené.* Chcete-li vytvořit zobrazení, přidejte nový soubor a přiřazujte mu stejný název jako jeho přidružená akce řadiče s příponou *souboru .cshtml.* Chcete-li vytvořit zobrazení, které odpovídá akci *Informace* v řadiči *Domů,* vytvořte soubor *About.cshtml* ve složce *Zobrazení/Domov:*

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Značka holicího strojku* začíná symbolem. `@` Spusťte příkazy Jazyka C# umístěním kódu Jazyka C#`{ ... }`do bloků kódu [Razor,](xref:mvc/views/razor#razor-code-blocks) které jsou nastaveny složenými závorkami ( ). Podívejte se například na přiřazení `ViewData["Title"]` "O" na obrázku výše. Hodnoty v html můžete zobrazit tak, že `@` jednoduše na hodnotu odkazujete se symbolem. Podívejte se na `<h2>` `<h3>` obsah a prvky výše.

Obsah zobrazení zobrazený výše je pouze částí celé webové stránky, která je vykreslována uživateli. Zbývající rozložení stránky a další běžné aspekty zobrazení jsou určeny v jiných souborech zobrazení. Další informace naleznete v [tématu Rozložení](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Jak kontrolidy určují zobrazení

Zobrazení jsou obvykle vráceny z akcí jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), což je typ [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Vaše metoda akce můžete `ViewResult` vytvořit a vrátit přímo, ale to se běžně neprovádí. Vzhledem k tomu, že většina `View` řadičů dědí `ViewResult`z [řadiče](/dotnet/api/microsoft.aspnetcore.mvc.controller), jednoduše použijte pomocnou metodu k vrácení :

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Když se tato akce vrátí, zobrazení *About.cshtml* zobrazené v poslední části se vykreslí jako následující webová stránka:

![O stránce vykreslené v prohlížeči Edge](overview/_static/about-page.png)

`View` Pomocná metoda má několik přetížení. Volitelně můžete zadat:

* Explicitní zobrazení pro vrácení:

  ```csharp
  return View("Orders");
  ```

* [Model,](xref:mvc/models/model-binding) který má být předáván do pohledu:

  ```csharp
  return View(Orders);
  ```

* Pohled i model:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Zobrazit zjišťování

Když akce vrátí zobrazení, probíhá proces nazývaný *zjišťování zobrazení.* Tento proces určuje, který soubor zobrazení se používá na základě názvu zobrazení. 

Výchozí chování `View` metody (`return View();`) je vrátit zobrazení se stejným názvem jako metoda akce, ze které je volána. Název metody *About* `ActionResult` řadiče se například používá k vyhledání souboru zobrazení s názvem *About.cshtml*. Nejprve se běhový čas podívá do složky *Zobrazení/[Název_celého_zařízení]* pro zobrazení. Pokud tam nenajde odpovídající zobrazení, prohledá *sdílenou* složku pro zobrazení.

Nezáleží na tom, zda implicitně vrátíte `ViewResult` s `return View();` nebo `View` explicitně `return View("<ViewName>");`předat název zobrazení metodě s . V obou případech zobrazte zjišťování, které hledá odpovídající soubor zobrazení v tomto pořadí:

   1. *Zobrazení/\[Název_řadiče]/\[ViewName].cshtml*
   1. *Zobrazení/Sdílené/\[ViewName].cshtml*

Místo názvu zobrazení lze poskytnout cestu k souboru zobrazení. Pokud používáte absolutní cestu začínající v kořenovém adresáři aplikace (volitelně začínající na "/" nebo "~/"), musí být zadáno rozšíření *.cshtml:*

```csharp
return View("Views/Home/About.cshtml");
```

Relativní cestu můžete také použít k určení zobrazení v různých adresářích bez rozšíření *.cshtml.* Uvnitř `HomeController`aplikace můžete vrátit zobrazení *indexu* zobrazení *Správa* zobrazení s relativní cestou:

```csharp
return View("../Manage/Index");
```

Podobně můžete označit aktuální adresář specifický pro řadič s předponou "./":

```csharp
return View("./About");
```

[Částečné pohledy](xref:mvc/views/partial) a [součásti zobrazení](xref:mvc/views/view-components) používají podobné (ale ne identické) mechanismy zjišťování.

Výchozí konvenci pro umístění zobrazení v aplikaci můžete přizpůsobit pomocí vlastního [iViewLocationExpanderu](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Zjišťování zobrazení závisí na hledání souborů zobrazení podle názvu souboru. Pokud základní systém souborů rozlišuje malá a velká písmena, názvy zobrazení jsou pravděpodobně rozlišování velkých a malých písmen. Z důvodu kompatibility mezi operačními systémy můžete porovnat případ mezi názvy řadiče a akcí a přidruženými složkami zobrazení a názvy souborů. Pokud narazíte na chybu, že soubor zobrazení nelze nalézt při práci se systémem souborů rozlišující malá a velká písmena, zkontrolujte, zda se velikost písmen shoduje mezi požadovaným souborem zobrazení a skutečným názvem souboru zobrazení.

Postupujte podle osvědčených postupů při uspořádání struktury souborů pro vaše zobrazení tak, aby odrážely vztahy mezi řadiči, akcemi a zobrazeními pro zachování a srozumitelnost.

## <a name="passing-data-to-views"></a>Předávání dat zobrazením

Předávat data zobrazením pomocí několika přístupů:

* Data silného typu: model zobrazení
* Slabě zadaný data
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Data silného typu (model zobrazení)

Nejrobustnějším přístupem je určení typu [modelu](xref:mvc/models/model-binding) v pohledu. Tento model se běžně označuje jako *model pohledu*. Instance typu zobrazení modelu zobrazení do pohledu z akce.

Použití modelu zobrazení k předání dat do zobrazení umožňuje zobrazení využít výhod *kontroly silného* typu. *Silné psaní* (nebo *silné holčicí typ)* znamená, že každá proměnná a konstanta má explicitně definovaný typ (například `string`, `int`, nebo `DateTime`). Platnost typů používaných v zobrazení je kontrolována v době kompilace.

[Visual Studio](https://visualstudio.microsoft.com) a [Visual Studio kód](https://code.visualstudio.com/) seznam silně zadali členy třídy pomocí funkce s názvem [IntelliSense](/visualstudio/ide/using-intellisense). Chcete-li zobrazit vlastnosti modelu pohledu, zadejte název proměnné pro model`.`pohledu následovaný tečkou ( ). To vám pomůže psát kód rychleji s menším počtem chyb.

Zadejte model `@model` pomocí směrnice. Model používejte `@Model`s :

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Chcete-li poskytnout model pohledu, předává jej řadič jako parametr:

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

Neexistují žádná omezení pro typy modelů, které můžete poskytnout zobrazení. Doporučujeme používat plain old CLR object (POCO) viewmodels s malým nebo žádným chováním (metodami) definovanými. Třídy viewmodel jsou obvykle uloženy ve složce *Modely* nebo samostatné *viewmodels* složky v kořenovém adresáři aplikace. Model zobrazení *Adresa* použitý ve výše uvedeném příkladu je model zobrazení POCO uložený v souboru s názvem *Address.cs*:

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

Nic nebrání použití stejné třídy pro typy zobrazení modelu a typy obchodního modelu. Použití samostatných modelů však umožňuje, aby se vaše zobrazení lišila nezávisle na obchodní logice a části aplikace pro přístup k datům. Oddělení modelů a zobrazení také nabízí výhody zabezpečení, když modely používají [vazbu modelu](xref:mvc/models/model-binding) a [ověřování](xref:mvc/models/validation) dat odeslaných do aplikace uživatelem.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Slabě zadaný data (ViewData, ViewData atribut a ViewBag)

`ViewBag`*není k dispozici v nástroji Razor Pages.*

Kromě zobrazení silného typu mají zobrazení přístup k *slabě zadanému* (nazývanému také *volně zadaný)* shromažďování dat. Na rozdíl od silných typů *slabé typy* (nebo *volné typy)* znamená, že není explicitně deklarovat typ dat, které používáte. Můžete použít shromažďování slabě zadávaných dat pro předávání malých množství dat do a z řadiče a zobrazení.

| Předávání dat mezi ...                        | Příklad                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Řadič a zobrazení                             | Vyplnění rozevíracího seznamu daty.                                          |
| Zobrazení a [zobrazení rozložení](xref:mvc/views/layout)   | Nastavení ** \<názvu>** obsahu prvků v zobrazení rozložení ze souboru zobrazení.  |
| [Částečný pohled](xref:mvc/views/partial) a pohled | Widget, který zobrazuje data na základě webové stránky, kterou uživatel požadoval.      |

Tato kolekce lze odkazovat `ViewData` prostřednictvím nebo `ViewBag` vlastnosti na řadiče a zobrazení. Vlastnost `ViewData` je slovník slabě zadaných objektů. Vlastnost `ViewBag` je obálka `ViewData` kolem, který poskytuje `ViewData` dynamické vlastnosti pro základní kolekce. Poznámka: Hledání klíčů nerozlišují `ViewData` `ViewBag`malá a velká písmena pro obě a .

`ViewData`a `ViewBag` jsou dynamicky vyřešeny za běhu. Vzhledem k tomu, že nenabízejí kontrolu typu kompilace, jsou obvykle náchylnější k chybám než použití modelu zobrazení. Z tohoto důvodu někteří vývojáři dávají `ViewData` přednost `ViewBag`minimálně nebo nikdy použít a .

<a name="VD"></a>

**Viewdata**

`ViewData`je objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) přístupný `string` prostřednictvím klíčů. Řetězcová data mohou být uložena a použita přímo bez `ViewData` nutnosti přetypování, ale při jejich extrahování je nutné přetypovat jiné hodnoty objektů na určité typy. Data z `ViewData` řadičů můžete použít k zobrazením a zobrazením, včetně [částečných zobrazení](xref:mvc/views/partial) a [rozložení](xref:mvc/views/layout).

Následuje příklad, který nastavuje hodnoty pro `ViewData` pozdrav a adresu pomocí v akci:

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

**Atribut ViewData**

Dalším přístupem, který používá [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) je [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Vlastnosti na řadičích nebo modelech Razor Page označených `[ViewData]` atributem mají své hodnoty uložené a načtené ze slovníku.

V následujícím příkladu obsahuje domácí `Title` kontrolor `[ViewData]`vlastnost označenou . Metoda `About` nastaví název zobrazení Informace:

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

V rozložení se název čte ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**Zobrazit tašku**

`ViewBag`*není k dispozici v nástroji Razor Pages.*

`ViewBag`je objekt [DynamicViewData,](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) který poskytuje dynamický `ViewData`přístup k objektům uloženým v aplikaci . `ViewBag`může být pohodlnější pracovat s, protože nevyžaduje odlévání. Následující příklad ukazuje, `ViewBag` jak používat se `ViewData` stejným výsledkem jako použití výše:

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

**Současné použití viewdata a viewbagu**

`ViewBag`*není k dispozici v nástroji Razor Pages.*

Vzhledem `ViewBag` k tomu, `ViewData` `ViewData` a odkazovat `ViewData` na `ViewBag` stejné základní kolekce, můžete použít i a kombinovat mezi nimi při čtení a zápisu hodnoty.

Nastavte název `ViewBag` pomocí a `ViewData` popis pomocí v horní části zobrazení *About.cshtml:*

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Přečtěte si vlastnosti, `ViewData` `ViewBag`ale zvrátit použití a . V souboru *_Layout.cshtml* získejte `ViewData` název pomocí `ViewBag`a získejte popis pomocí :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Nezapomeňte, že struny nevyžadují `ViewData`obsazení pro . Můžete použít `@ViewData["Title"]` bez odlévání.

Použití `ViewData` obou `ViewBag` a současně funguje, stejně jako míchání a odpovídající čtení a zápis vlastností. Vykreslují se následující značky:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Souhrn rozdílů mezi ViewData a ViewBag**

 `ViewBag`není k dispozici na stránkách Razor.

* `ViewData`
  * Odvozuje z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), takže má vlastnosti slovníku, `Remove`které `Clear`mohou být užitečné, například `ContainsKey`, `Add`, a .
  * Klávesy ve slovníku jsou řetězce, takže mezery jsou povoleny. Příklad: `ViewData["Some Key With Whitespace"]`
  * V zobrazení, `string` které má být možné `ViewData`použít, musí být přetypován jakýkoli jiný typ než a .
* `ViewBag`
  * Odvozuje se od [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), takže umožňuje vytváření dynamických vlastností pomocí notace tečky (`@ViewBag.SomeKey = <value or object>`) a není vyžadovánžádný přetypování. Syntaxe `ViewBag` umožňuje rychlejší přidání do řadičů a zobrazení.
  * Jednodušší kontrola nulových hodnot. Příklad: `@ViewBag.Person?.Name`

**Kdy použít ViewData nebo ViewBag**

Oba `ViewData` `ViewBag` a jsou stejně platné přístupy pro předávání malých množství dat mezi správci a zobrazení. Volba, kterou z nich použít, je založena na preferencích. Můžete kombinovat `ViewData` a `ViewBag` objekty, ale kód je snazší číst a udržovat s jedním přístupem používá konzistentně. Oba přístupy jsou dynamicky vyřešeny za běhu a proto náchylné k vyvolání chyb za běhu. Některé vývojové týmy se jim vyhýbají.

### <a name="dynamic-views"></a>Dynamické pohledy

Pohledy, které nedeklarují typ modelu pomocí, `@model` ale které `return View(Address);`mají instanci modelu, která jim byla předána (například) mohou dynamicky odkazovat na vlastnosti instance:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Tato funkce nabízí flexibilitu, ale nenabízí ochranu kompilace nebo technologie IntelliSense. Pokud vlastnost neexistuje, generování webových stránek se nezdaří za běhu.

## <a name="more-view-features"></a>Další funkce zobrazení

[Pomocníci tagů](xref:mvc/views/tag-helpers/intro) usnadňují přidání chování na straně serveru ke stávajícím značkám HTML. Použití pomocníků značek se vyhne nutnosti psát vlastní kód nebo pomocníky ve vašich zobrazeních. Pomocné složky tagů se aplikují jako atributy prvků HTML a editory, kteří je nemohou zpracovat, je ignorují. To umožňuje upravovat a vykreslovat značky zobrazení v různých nástrojích.

Generování vlastních značek HTML lze dosáhnout pomocí mnoha vestavěných pomocníků HTML. Složitější logiku uživatelského rozhraní lze zpracovat [pomocí komponent view components](xref:mvc/views/view-components). Komponenty zobrazení poskytují stejné SoC, které nabízejí řadiče a zobrazení. Mohou eliminovat potřebu akcí a zobrazení, které se zabývají daty používanými běžnými prvky uživatelského rozhraní.

Stejně jako mnoho jiných aspektů ASP.NET Core, zobrazení podporují [vkládání závislostí](xref:fundamentals/dependency-injection), což umožňuje služby, které mají být [vloženy do zobrazení](xref:mvc/views/dependency-injection).
