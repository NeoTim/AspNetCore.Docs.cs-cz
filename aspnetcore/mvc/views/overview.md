---
title: Zobrazení ve ASP.NET Core MVC
author: ardalis
description: Přečtěte si, jak zobrazení zpracovávají data prezentace aplikace a interakce uživatele v ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020870"
---
# <a name="views-in-aspnet-core-mvc"></a>Zobrazení ve ASP.NET Core MVC

[Steve Smith](https://ardalis.com/)

Tento dokument vysvětluje zobrazení používaná v aplikacích ASP.NET Core MVC. Informace o Razor stránkách naleznete v tématu [Úvod do Razor stránek](xref:razor-pages/index).

Ve vzoru MVC (Model-View-Controller) zpracovává *zobrazení* datovou prezentaci aplikace a interakci s uživatelem. Zobrazení je šablona HTML s vloženým [ Razor kódem](xref:mvc/views/razor). Razoroznačení je kód, který komunikuje s označením HTML a vytvoří webovou stránku, která je odeslána klientovi.

V ASP.NET Core MVC jsou zobrazení soubory *. cshtml* , které používají [programovací jazyk C#](/dotnet/csharp/) v Razor kódu. Soubory zobrazení jsou obvykle seskupeny do složek pojmenovaných pro každý z [řadičů](xref:mvc/controllers/actions)aplikace. Složky se ukládají do složky *zobrazení* v kořenovém adresáři aplikace:

![Složka zobrazení v Průzkumník řešení sady Visual Studio je otevřená s otevřenou domovskou složkou, která zobrazuje informace o souborech. cshtml, Contact. cshtml a index. cshtml.](overview/_static/views_solution_explorer.png)

*Domovský* kontroler je reprezentován *domovskou* složkou ve složce *zobrazení* . *Domovská* složka obsahuje zobrazení webových stránek *About*, *Contact*a *index* (Domovská stránka). Když si uživatel vyžádá jednu z těchto tří webových stránek, akce kontroleru v rámci *domovského* kontroleru určují, které ze tří zobrazení se použije k sestavení a vrácení webové stránky uživateli.

Pomocí [rozložení](xref:mvc/views/layout) můžete poskytovat konzistentní oddíly webové stránky a snižovat opakování kódu. Rozložení často obsahují prvky záhlaví, navigace a nabídky a zápatí. Záhlaví a zápatí obvykle obsahuje často používaný kód pro mnoho prvků metadat a odkazy na prostředky skriptu a stylu. Rozložení umožňují vyhnout se tomuto standardnímu označení ve vašich zobrazeních.

[Částečná zobrazení](xref:mvc/views/partial) omezují duplicity kódu tím, že spravují opakovaně použitelné části zobrazení. Například částečné zobrazení je užitečné v případě, že autorský biografing na webu blogu, který se zobrazuje v několika zobrazeních. Biografická biografie je běžné zobrazení obsahu a nevyžaduje spuštění kódu, aby bylo možné vytvořit obsah pro webovou stránku. Vytváření biografických obsahu je k dispozici pro zobrazení podle samotného modelu, takže použití částečného zobrazení pro tento typ obsahu je ideální.

[Zobrazení komponent](xref:mvc/views/view-components) je podobné jako u částečných zobrazení v tom, že umožňují snížit opakující se kód, ale jsou vhodné pro zobrazení obsahu, který vyžaduje spuštění kódu na serveru, aby bylo možné webovou stránku vykreslit. Zobrazit součásti jsou užitečné, když vykreslený obsah vyžaduje interakci s databází, například pro nákupní košík webu. Zobrazit součásti nejsou omezeny na vazbu modelu, aby bylo možné vydávat výstup webové stránky.

## <a name="benefits-of-using-views"></a>Výhody používání zobrazení

Zobrazení usnadňují vytvoření [oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) v rámci aplikace MVC oddělením značek uživatelského rozhraní od jiných částí aplikace. Následující návrh SoC vám nabízí modulární aplikaci, která poskytuje několik výhod:

* Aplikaci je možné spravovat snadněji, protože je lépe organizovaná. Zobrazení jsou obecně seskupena podle funkcí aplikace. To usnadňuje hledání souvisejících zobrazení při práci na funkci.
* Části aplikace se volně odpojí. Zobrazení aplikace můžete sestavovat a aktualizovat odděleně od obchodní logiky a komponent pro přístup k datům. Můžete upravit zobrazení aplikace, aniž by bylo nutné aktualizovat ostatní části aplikace.
* Je snazší otestovat části aplikace uživatelského rozhraní, protože zobrazení jsou samostatné jednotky.
* Kvůli lepší organizaci je méně pravděpodobný, že nechtěně zopakujete části uživatelského rozhraní.

## <a name="creating-a-view"></a>Vytvoření zobrazení

Zobrazení, která jsou specifická pro kontroler, se vytvářejí ve složce *views/[Controller]* . Zobrazení, která jsou sdílená mezi řadiči, se nacházejí v *zobrazeních nebo sdílených* složkách. Chcete-li vytvořit zobrazení, přidejte nový soubor a pojmenujte ho stejným názvem jako jeho přidružená akce kontroleru s příponou souboru *. cshtml* . Chcete-li vytvořit zobrazení, které odpovídá akci *o* akci v rámci *domovského* kontroleru, vytvořte soubor *About. cshtml* v *zobrazeních/domovské* složce:

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* označení začíná `@` symbolem. Příkazy jazyka C# spustíte tak, že umístíte kód C# do [ Razor bloků kódu](xref:mvc/views/razor#razor-code-blocks) nastavených na složené závorky ( `{ ... }` ). Podívejte se například na téma přiřazení "About", které se `ViewData["Title"]` zobrazí výše. Hodnoty v jazyce HTML můžete zobrazit pouhým odkazem na hodnotu `@` symbolem. Podívejte se na obsah `<h2>` `<h3>` výše uvedených prvků a.

Výše uvedený obsah zobrazení je pouze součástí celé webové stránky, která je uživateli vykreslena. Zbývající rozložení stránky a další běžné aspekty zobrazení jsou uvedeny v jiných souborech zobrazení. Další informace najdete v [tématu věnovaném rozložení](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Jak řadiče určují zobrazení

Zobrazení jsou obvykle vrácena z akcí jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), což je typ [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Vaše metoda akce může vytvořit a vrátit `ViewResult` přímo, ale to se obvykle neprovádí. Vzhledem k tomu, že většina řadičů dědí z [kontroleru](/dotnet/api/microsoft.aspnetcore.mvc.controller), jednoduše použijete `View` pomocnou metodu, která vrátí `ViewResult` :

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Když se tato akce vrátí, zobrazení *About. cshtml* zobrazené v poslední části se vykreslí jako následující webová stránka:

![O stránce vygenerované v prohlížeči Edge](overview/_static/about-page.png)

`View`Pomocná metoda má několik přetížení. Volitelně můžete zadat:

* Explicitní zobrazení, které se má vrátit:

  ```csharp
  return View("Orders");
  ```

* [Model](xref:mvc/models/model-binding) , který se má předat zobrazení:

  ```csharp
  return View(Orders);
  ```

* Jak zobrazení, tak i model:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Zjišťování zobrazení

Když akce vrátí zobrazení, dojde k procesu s názvem *zjišťování zobrazení* . Tento proces určuje, který soubor zobrazení se použije v závislosti na názvu zobrazení. 

Výchozí chování `View` metody ( `return View();` ) je vrátit zobrazení se stejným názvem jako metoda akce, ze které je volána. Například *About* `ActionResult` název metody řadiče se používá k vyhledání souboru zobrazení s názvem *About. cshtml*. Za prvé, modul runtime pro zobrazení vyhledá složku views */[Controller]* . Pokud tam nenajde žádné zobrazení, vyhledá pro zobrazení *sdílenou* složku.

Nezáleží na tom, zda implicitně vrátíte `ViewResult` `return View();` nebo explicitně předáte název zobrazení do `View` metody pomocí `return View("<ViewName>");` . V obou případech zobrazení vyhledávání vyhledá v tomto pořadí soubor zobrazení s porovnáním:

   1. *Zobrazení/ \[ kontrolér]/ \[ viewName]. cshtml*
   1. *Zobrazení/Shared/ \[ viewName]. cshtml*

Místo názvu zobrazení je možné zadat cestu k souboru zobrazení. Pokud použijete absolutní cestu začínající v kořenu aplikace (volitelně začíná znakem "/" nebo "~/"), musí být zadáno rozšíření *. cshtml* :

```csharp
return View("Views/Home/About.cshtml");
```

Můžete také použít relativní cestu k určení zobrazení v různých adresářích bez přípony *. cshtml* . V `HomeController` nástroji můžete vrátit zobrazení *indexu* pro *správu* zobrazení s relativní cestou:

```csharp
return View("../Manage/Index");
```

Podobně můžete určit aktuální adresář specifický pro řadič s předponou "./":

```csharp
return View("./About");
```

[Částečně zobrazení](xref:mvc/views/partial) a [součásti zobrazení](xref:mvc/views/view-components) používají podobné (ale ne stejné) mechanismy zjišťování.

Můžete přizpůsobit výchozí konvenci pro způsob, jakým se v aplikaci nacházejí zobrazení pomocí vlastního [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).

Zjišťování zobrazení spoléhá na hledání souborů zobrazení podle názvu souboru. Pokud základní systém souborů rozlišuje velká a malá písmena, je pravděpodobné, že názvy zobrazení budou rozlišovat velká a malá písmena. V případě kompatibility mezi operačními systémy porovnává malá a velká písmena mezi názvy kontrolérů a akcí a přidružených složek zobrazení a názvů souborů. Pokud dojde k chybě, že soubor zobrazení nebyl nalezen při práci se systémem souborů s rozlišováním velkých a malých písmen, potvrďte, že velikost písmen mezi požadovaným souborem zobrazení a aktuálním názvem souboru zobrazení se neshoduje.

Dodržujte osvědčené postupy uspořádání struktury souborů pro vaše zobrazení tak, aby odrážely vztahy mezi řadiči, akcemi a zobrazeními pro udržovatelnost a přehlednost.

## <a name="passing-data-to-views"></a>Předávání dat do zobrazení

Předávání dat do zobrazení pomocí několika přístupů:

* Data silného typu: ViewModel
* Slabě zadaná data
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Data silného typu (ViewModel)

Nejrobustním přístupem je určit typ [modelu](xref:mvc/models/model-binding) v zobrazení. Tento model se běžně označuje jako *ViewModel*. Z akce předáte instanci typu ViewModel do zobrazení.

Použití ViewModel k předávání dat zobrazení umožňuje zobrazení výhod kontroly *silného* typu. *Silné psaní* (nebo *silného typu*) znamená, že každá proměnná a konstanta má explicitně definovaný typ (například `string` , `int` nebo `DateTime` ). Platnost typů použitých v zobrazení je kontrolována v době kompilace.

[Visual Studio](https://visualstudio.microsoft.com) a [Visual Studio Code](https://code.visualstudio.com/) seznam členů třídy silně typovaného typu pomocí funkce s názvem [IntelliSense](/visualstudio/ide/using-intellisense). Pokud chcete zobrazit vlastnosti ViewModel, zadejte název proměnné pro ViewModel následovaný tečkou ( `.` ). To vám pomůže psát kód rychleji s menším množstvím chyb.

Určete model pomocí `@model` direktivy. Použijte model s `@Model` :

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Aby se tento model poskytl zobrazení, kontroler ho předává jako parametr:

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

Pro typy modelů, které lze poskytnout zobrazení, neexistují žádná omezení. Doporučujeme použít prostý starý objekt CLR (POCO) ViewModels s malým nebo žádným chováním (metody) definované. Třídy ViewModel jsou obvykle uloženy ve složce *modely* nebo v samostatné složce *ViewModels* v kořenovém adresáři aplikace. *Adresa* ViewModel použitá v předchozím příkladu je POCO ViewModel uložená v souboru s názvem *Address.cs*:

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

Nic nebrání použití stejných tříd pro ViewModel typy a typy obchodních modelů. Použití samostatných modelů ale umožňuje, aby se vaše zobrazení měnila nezávisle na obchodních logicech a částech aplikace pro přístup k datům. Oddělení modelů a ViewModels také nabízí výhody zabezpečení, když modely používají [vazby modelů](xref:mvc/models/model-binding) a [ověřování](xref:mvc/models/validation) dat odesílaných do aplikace uživatelem.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Slabě zadaná data (ViewData, atribut ViewData a ViewBag)

`ViewBag`*není k dispozici v Razor Stránky.*

Kromě zobrazení se silným typem mají zobrazení zobrazení přístup k *slabě typované* (také nazývané *volně typované*) kolekci dat. Na rozdíl od silných typů, *slabých* typů (nebo *volných typů*) znamená, že explicitně nedeklarujete typ dat, která používáte. Pro přenos malých objemů dat do a z řadičů a zobrazení můžete použít kolekci slabého typu dat.

| Předávání dat mezi...                        | Příklad                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Kontroler a zobrazení                             | Naplnění rozevíracího seznamu daty.                                          |
| Zobrazení a [zobrazení rozložení](xref:mvc/views/layout)   | Nastavení **\<title>** obsahu prvku v zobrazení rozložení ze souboru zobrazení.  |
| [Částečné zobrazení](xref:mvc/views/partial) a zobrazení | Pomůcka, která zobrazuje data na základě webové stránky požadované uživatelem.      |

Na tuto kolekci lze odkazovat buď pomocí `ViewData` vlastností nebo `ViewBag` v řadičích a zobrazeních. `ViewData`Vlastnost je slovníkem slabě typových objektů. `ViewBag`Vlastnost je Obálka kolem `ViewData` , která poskytuje dynamické vlastnosti pro podkladovou `ViewData` kolekci. Poznámka: Vyhledání klíčů rozlišuje velká a malá písmena `ViewData` `ViewBag` .

`ViewData`a `ViewBag` jsou dynamicky vyřešeny za běhu. Vzhledem k tomu, že nenabízejí kontrolu typu při kompilaci, obě jsou obecně větší náchylnější k chybám než použití ViewModel. Z tohoto důvodu někteří vývojáři dávají přednost minimálnímu nebo nikdy nepoužívání `ViewData` a `ViewBag` .

<a name="VD"></a>

**ViewData**

`ViewData`je objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , ke kterému se přistupoval prostřednictvím `string` klíčů. Řetězcová data lze uložit a použít přímo bez nutnosti přetypování, ale `ViewData` při jejich extrakci je nutné přetypovat jiné hodnoty objektů na konkrétní typy. Můžete použít `ViewData` k předávání dat z řadičů do zobrazení a v rámci zobrazení, včetně [částečných zobrazení](xref:mvc/views/partial) a [rozložení](xref:mvc/views/layout).

Následuje příklad, který nastavuje hodnoty pro pozdrav a adresu pomocí `ViewData` akce:

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

**ViewData – atribut**

Dalším přístupem, který používá [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , je [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Vlastnosti v řadičích nebo Razor modelech stránek označených `[ViewData]` atributem mají jejich hodnoty uložené a načtené ze slovníku.

V následujícím příkladu obsahuje domovský kontroler `Title` vlastnost s označením `[ViewData]` . `About`Metoda nastaví název zobrazení informace o:

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

V rozložení je název čten ze slovníku ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*není k dispozici v Razor Stránky.*

`ViewBag`je objekt [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) , který poskytuje dynamický přístup k objektům uloženým v `ViewData` . `ViewBag`může být pohodlnější pro práci s, protože nevyžaduje přetypování. Následující příklad ukazuje, jak použít `ViewBag` se stejným výsledkem jako v použití `ViewData` výše:

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

**Použití ViewData a ViewBag současně**

`ViewBag`*není k dispozici v Razor Stránky.*

Vzhledem `ViewData` k tomu, že a `ViewBag` odkazují na stejnou základní `ViewData` kolekci, můžete `ViewData` `ViewBag` při čtení a zápisu hodnot použít jak a, tak i kombinaci a porovnávání.

Pomocí nadpisu `ViewBag` a popisu použijte `ViewData` v horní části o zobrazení o souboru *. cshtml* :

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Přečtěte si vlastnosti, ale zpět použijte `ViewData` a `ViewBag` . V souboru *_Layout. cshtml* Získejte název pomocí `ViewData` a získejte popis pomocí `ViewBag` :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Pamatujte, že řetězce nevyžadují přetypování pro `ViewData` . Můžete použít `@ViewData["Title"]` bez přetypování.

Použití obou `ViewData` i `ViewBag` ve stejnou dobu funguje, stejně jako kombinace a spárování se čtením a zápisem vlastností. Následující kód je vykreslen:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Shrnutí rozdílů mezi ViewData a ViewBag**

 `ViewBag`není na stránkách k dispozici Razor .

* `ViewData`
  * Je odvozen z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), takže má vlastnosti slovníku, které mohou být užitečné, například `ContainsKey` , `Add` , `Remove` a `Clear` .
  * Klíče ve slovníku jsou řetězce, takže je povolen prázdný znak. Příklad: `ViewData["Some Key With Whitespace"]`
  * Libovolný typ jiný než `string` musí být přetypování v zobrazení, které se má použít `ViewData` .
* `ViewBag`
  * Je odvozen z [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), takže umožňuje vytváření dynamických vlastností pomocí tečky Notation ( `@ViewBag.SomeKey = <value or object>` ) a není vyžadováno přetypování. Syntaxe nástroje `ViewBag` umožňuje rychlejší přidávání do řadičů a zobrazení.
  * Jednodušší pro kontrolu hodnot null. Příklad: `@ViewBag.Person?.Name`

**Kdy použít ViewData nebo ViewBag**

Oba `ViewData` i `ViewBag` jsou stejně platné přístupy k předávání malých objemů dat mezi řadiči a zobrazeními. Výběr, který má být použit, je založen na předvolbách. Můžete kombinovat objekty a porovnat `ViewData` je `ViewBag` , ale kód je snazší číst a udržovat s jedním způsobem použitým konzistentně. Oba přístupy se dynamicky vyřeší za běhu, a proto náchylné k chybám za běhu. Některé vývojové týmy je zabraňují.

### <a name="dynamic-views"></a>Dynamická zobrazení

Zobrazení, která nedeklarují typ modelu pomocí `@model` , ale které mají předané instance modelu (například), `return View(Address);` mohou dynamicky odkazovat na vlastnosti instance:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Tato funkce nabízí flexibilitu, ale nenabízí ochranu kompilace ani IntelliSense. Pokud vlastnost neexistuje, generování webové stránky se za běhu nezdařilo.

## <a name="more-view-features"></a>Další funkce zobrazení

[Pomocník značek](xref:mvc/views/tag-helpers/intro) usnadňuje přidávání chování na straně serveru do existujících značek HTML. Použití pomocníků značek zabraňuje nutnosti psát vlastní kód nebo pomocníky v rámci vašich zobrazení. Pomocníky značek se používají jako atributy pro prvky HTML a jsou ignorovány editory, které je nemohou zpracovat. To vám umožní upravovat a vykreslovat značky zobrazení v nejrůznějších nástrojích.

Generování vlastního kódu HTML lze dosáhnout pomocí mnoha vestavěných pomocníků HTML. Složitější logiku uživatelského rozhraní lze zpracovat pomocí [zobrazení komponent](xref:mvc/views/view-components). Zobrazit součásti poskytují stejné SoC, jaké řadiče a zobrazení nabízí. Můžou eliminovat nutnost akcí a zobrazení, které se týkají dat používaných společnými prvky uživatelského rozhraní.

Podobně jako mnoho dalších aspektů ASP.NET Core podporují [vkládání závislostí](xref:fundamentals/dependency-injection), což umožňuje vkládání služeb [do zobrazení](xref:mvc/views/dependency-injection).
