---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC.
ms.author: riande
ms.date: 02/14/2017
uid: mvc/controllers/areas
ms.openlocfilehash: 19e818fa198936ea1bee0da8039e88a3c0abbf6b
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410609"
---
# <a name="areas-in-aspnet-core"></a>Oblasti v ASP.NET Core

Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC. Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area`do `controller` a `action`.

Oblasti poskytují způsob, jak rozdělit velké aplikace ASP.NET Core MVC Web na menší funkční seskupení. Oblast je v podstatě struktury MVC uvnitř aplikace. V projektu aplikace MVC logické komponenty, jako jsou modelu, Kontroleru a zobrazení jsou uloženy v různých složkách a aplikace MVC používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace. Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a vyhledávání atd. Každá z těchto jednotek mít své vlastní logickou součástí zobrazení, kontrolerů a modely. V tomto scénáři použijete k oblasti fyzicky rozdělení komponent firmy ve stejném projektu.

Oblast je definovat jako menších funkční jednotek v projektu aplikace ASP.NET Core MVC s vlastní sadou řadiče, zobrazení a modely.

Zvažte použití oblastí v MVC projektu při:

* Vaše aplikace je proveden součástí více vysoké úrovně funkčnosti, která by měla být oddělena logicky

* Chcete rozdělit projekt MVC tak, aby každá funkční oblast je možné pracovat nezávisle na sobě

Oblasti funkce:

* Aplikace ASP.NET Core MVC může mít libovolný počet oblastí.

* Každá oblast má vlastní řadiče, modely a zobrazení.

* Oblasti umožňují organizovat velké projekty MVC do více základní součásti, které je možné pracovat nezávisle na sobě.

* Oblasti podporují víc řadičích se stejným názvem, za předpokladu, že mají různé *oblasti*.

Pojďme se podívat na příklad, který ukazuje, jak vytvořit a použít oblasti. Řekněme, že máte aplikace ze storu, která má dvě odlišné skupiny kontrolerů a zobrazení: Produkty a služby. Složka je obvykle strukturu pro, používat MVC vypadá níže:

* Název projektu
  * Oblasti
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Zobrazení
        * Domů
          * Index.cshtml
        * Správa
          * Index.cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Index.cshtml

MVC se pokusí pro vykreslení zobrazení v oblasti, ve výchozím nastavení, pokusí se vás pod rouškou v následujících umístěních:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

Toto jsou výchozí umístění, které lze změnit prostřednictvím `AreaViewLocationFormats` na `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.

Například v níže uvedeného kódu namísto nutnosti název složky jako "Oblasti", byl změněn na "Kategorie".

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

Poznámka je struktura *zobrazení* složky je pouze jeden, který je považován za důležité tady a obsah ostatních složek, jako jsou *řadiče* a *modely* nemá **není** záleží. Například nemusí mít *řadiče* a *modely* složky vůbec. Tento postup funguje, protože obsah *řadiče* a *modely* je jenom kód, který získá kompilovány do knihovny DLL tam, kde jako obsah *zobrazení* není až do požadavek, který zobrazení byly provedeny.

Jakmile jste definovali hierarchii složek, je třeba sdělit MVC, že je každý kontroler přidružené k oblasti. Můžete to udělat pomocí upravení názvu kontroleru se `[Area]` atribut.

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

Nastavte definici trasy, která funguje s nově vytvořený oblasti. [Trasy na akce kontroleru](routing.md) článek obsahuje podrobnosti o tom, jak vytvořit trasu definic, včetně použití konvenční trasy a trasy atributů. V tomto příkladu použijeme konvenční trasy. Chcete-li to provést, otevřete *Startup.cs* soubor a upravit ji tak, že přidáte `areaRoute` s názvem definice trasy níže.

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(
         name: "areaRoute",
         template: "{area:exists}/{controller=Home}/{action=Index}/{id?}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

Přejdete na adresu `http://<yourApp>/products`, `Index` metody akce `HomeController` v `Products` oblasti, který bude vyvolán.

## <a name="link-generation"></a>Generování odkazů

* Generování odkazů z akce v rámci oblasti na základě kontroleru další akce v rámci stejné kontroleru.

  Řekněme, že je cesta aktuální žádosti jako `/Products/Home/Create`

  HtmlHelper syntaxe: `@Html.ActionLink("Go to Product's Home Page", "Index")`

  Taghelperu. syntaxe: `<a asp-action="Index">Go to Product's Home Page</a>`

  Všimněte si, že jsme nemusí poskytnout hodnoty "oblasti" a "controller" Zde jsou už k dispozici v rámci aktuálního požadavku. Tento druh hodnoty se nazývají `ambient` hodnoty.

* Generování odkazů z akce v rámci oblasti na základě kontroleru další akci na jiný kontroler

  Řekněme, že je cesta aktuální žádosti jako `/Products/Home/Create`

  HtmlHelper syntaxe: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`

  Taghelperu. syntaxe: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`

  Všimněte si, že tady se používá okolí hodnotu "oblasti", ale explicitně zadaná hodnota 'controller' výše.

* Generování odkazů z akce v rámci oblasti kontroleru další akci na základě různých kontroleru a do jiné oblasti.

  Řekněme, že je cesta aktuální žádosti jako `/Products/Home/Create`

  HtmlHelper syntaxe: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`

  Taghelperu. syntaxe: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`

  Všimněte si, že tady žádné okolí hodnoty jsou použity.

* Generování odkazů z akce v rámci řadič oblasti založené na jinou akci na jiný kontroler a **není** v oblasti.

  HtmlHelper syntaxe: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`

  Taghelperu. syntaxe: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`

  Protože chceme, aby ke generování odkazů do jiné oblasti na základě akce kontroleru, jsme prázdný okolí hodnotu "oblasti" zde.

## <a name="publishing-areas"></a>Publikování oblasti

Všechny `*.cshtml` a `wwwroot/**` souborů k publikování do výstupu, kdy `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí *.csproj* souboru.
