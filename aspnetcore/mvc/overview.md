---
title: Přehled ASP.NET Core MVC
author: ardalis
description: Přečtěte si, jak ASP.NET Core MVC je bohatá architektura pro vytváření webových aplikací a rozhraní API pomocí vzorového vzoru pro zobrazení modelu.
ms.author: riande
ms.date: 02/12/2020
uid: mvc/overview
ms.openlocfilehash: 2911399f6ed4e14345171c908c4306b9c3e33805
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447409"
---
# <a name="overview-of-aspnet-core-mvc"></a>Přehled ASP.NET Core MVC

[Steve Smith](https://ardalis.com/)

ASP.NET Core MVC je bohatá architektura pro vytváření webových aplikací a rozhraní API pomocí vzorového vzoru pro zobrazení modelu.

## <a name="what-is-the-mvc-pattern"></a>Co je vzor MVC?

Architektonický vzor architektury MVC (Model-View-Controller) odděluje aplikaci do tří hlavních skupin komponent: modelů, zobrazení a řadičů. Tento model pomáhá dosáhnout [oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Pomocí tohoto modelu jsou požadavky uživatelů směrovány na kontroler, který je zodpovědný za práci s modelem pro provádění akcí uživatele nebo načtení výsledků dotazů. Kontroler zvolí zobrazení, které se zobrazí uživateli, a poskytne mu veškerá data modelu, která vyžaduje.

Následující diagram znázorňuje tři hlavní komponenty a odkazy na ostatní:

![Vzor MVC](overview/_static/mvc.png)

Tato změna odpovědnosti vám pomůže škálovat aplikace z hlediska složitosti, protože je snazší kód, ladit a testovat něco (model, zobrazení nebo kontroler) s jednou úlohou. Je obtížnější aktualizovat, testovat a ladit kód, který obsahuje závislosti rozdělené do dvou nebo více těchto tří oblastí. Například logika uživatelského rozhraní má za následek změnu častěji než obchodní logika. Pokud je kód prezentace a obchodní logika kombinována v jednom objektu, objekt obsahující obchodní logiku musí být upraven pokaždé, když se změní uživatelské rozhraní. To často zavádí chyby a vyžaduje znovu testování obchodní logiky po každé změně minimálního uživatelského rozhraní.

> [!NOTE]
> Zobrazení i kontroler jsou závislé na modelu. Model však závisí ani na zobrazení, ani na řadiči. Toto je jedna z klíčových výhod oddělení. Toto oddělení umožňuje sestavit a otestovat model nezávisle na vizuální prezentaci.

### <a name="model-responsibilities"></a>Závazky modelu

Model v aplikaci MVC představuje stav aplikace a jakékoli obchodní logiky nebo operace, které by je měla provést. Obchodní logika by měla být zapouzdřená v modelu spolu s logikou implementace pro zachování stavu aplikace. Zobrazení silného typu obvykle používají ViewModel typy navržené k zobrazení dat zobrazených v tomto zobrazení. Kontroler vytvoří a naplní tyto instance ViewModel z modelu.

### <a name="view-responsibilities"></a>Zobrazit odpovědnosti

Zobrazení jsou zodpovědná za prezentaci obsahu prostřednictvím uživatelského rozhraní. Používají [zobrazovací modul Razor](#razor-view-engine) k vložení kódu .NET do kódu HTML. V zobrazeních by měla být minimální logika a každá logika v nich by se měla vztahovat k prezentaci obsahu. Pokud zjistíte, že je potřeba provést skvělou možnost logiky v zobrazení souborů, abyste mohli zobrazit data ze složitého modelu, zvažte použití šablony [View Component](views/view-components.md), ViewModel nebo View pro zjednodušení zobrazení.

### <a name="controller-responsibilities"></a>Odpovědnosti řadiče

Řadiče jsou komponenty, které zpracovávají interakci uživatele, pracují s modelem a nakonec vykreslí zobrazení, které se má vykreslit. V aplikaci MVC zobrazení pouze zobrazuje informace, zatímco kontroler zpracovává vstup uživatele a interakci s uživatelem a reaguje na ně. Ve vzoru MVC je kontroler počátečním vstupním bodem a zodpovídá za výběr typů modelů, se kterými se má pracovat, a které zobrazení se má vykreslit (takže jeho název určuje, jak aplikace reaguje na daný požadavek).

> [!NOTE]
> Řadiče by neměly být příliš komplikované o příliš mnoho zodpovědností. Aby se logika kontroléru stala nepřesnou složitou, měla by být nabízena obchodní logika z kontroleru a do doménového modelu.

>[!TIP]
> Pokud zjistíte, že akce kontroleru často provádějí stejné druhy akcí, přesuňte tyto běžné akce do [filtrů](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Co je ASP.NET Core MVC

Rozhraní ASP.NET Core MVC je odlehčené Open Source rozhraní s vysokou testovatelné prezentací optimalizované pro použití s ASP.NET Core.

ASP.NET Core MVC poskytuje vzory založené na vzorcích pro vytváření dynamických webů, které umožňují čistě oddělit se o takové obavy. Poskytuje plnou kontrolu nad značkou, podporuje vývoj s použitím TDD a používá nejnovější webové standardy.

## <a name="features"></a>Funkce

ASP.NET Core MVC zahrnuje následující:

* [Směrování](#routing)
* [Vazby modelu](#model-binding)
* [Ověření modelu](#model-validation)
* [Vkládání závislostí](../fundamentals/dependency-injection.md)
* [Filtry](#filters)
* [Oblasti](#areas)
* [Webová rozhraní API](#web-apis)
* [Testovatelnosti](#testability)
* [Modul zobrazení Razor](#razor-view-engine)
* [Zobrazení silného typu](#strongly-typed-views)
* [Pomocné rutiny značek](#tag-helpers)
* [Zobrazit součásti](#view-components)

### <a name="routing"></a>Směrování

ASP.NET Core MVC je postavená na [směrování ASP.NET Core](../fundamentals/routing.md), což je výkonná součást pro mapování adres URL, která umožňuje vytvářet aplikace s srozumitelnými a Prohledávatelnými adresami URL. To vám umožní definovat vzory názvů adres URL vaší aplikace, které dobře fungují pro optimalizaci vyhledávačů (SEO) a pro vytváření odkazů, bez ohledu na to, jak se soubory na webovém serveru uspořádají. Trasy můžete definovat pomocí vhodné syntaxe šablony směrování, která podporuje omezení hodnoty směrování, výchozí hodnoty a volitelné hodnoty.

*Směrování založené na konvencích* umožňuje globálně definovat formáty adres URL, které vaše aplikace přijímá, a způsob, jakým se každý z těchto formátů mapuje na konkrétní metodu akce v daném kontroleru. Po přijetí příchozího požadavku směrovací modul analyzuje adresu URL a porovná ji s jedním z definovaných formátů adres URL a potom zavolá metodu akce přidruženého kontroleru.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Směrování atributů* umožňuje zadat informace o směrování tím, že upravení řadiče a akce s atributy, které definují trasy vaší aplikace. To znamená, že definice tras jsou umístěny vedle kontroleru a akce, ke kterým jsou přidruženy.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a>Vazby modelu

ASP.NET Core [vazby modelu](models/model-binding.md) MVC převede data požadavků klienta (hodnoty formulářů, směrovat data, parametry řetězce dotazu, hlavičky HTTP) do objektů, které může řadič zpracovat. V důsledku toho vaše logika kontroleru nemusí dělat práci s zjišťováním příchozích dat požadavků. má pouze data jako parametry svých metod akcí.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>Ověření modelu

ASP.NET Core MVC podporuje [ověřování](models/validation.md) tím, že upravení objekt modelu s atributy ověření poznámky k datům. Atributy ověřování jsou zkontrolovány na straně klienta před odesláním hodnot na server a také na serveru před voláním akce kontroleru.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Akce kontroleru:

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

Rozhraní zpracovává ověřování dat žádostí na straně klienta i serveru. Logika ověřování zadaná u typů modelů je přidána do vykreslených zobrazení jako nenáročná anotace a je vyhodnocena v prohlížeči s [ověřováním jQuery](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Injektáž závislosti

ASP.NET Core obsahuje integrovanou podporu pro [vkládání závislostí (di)](../fundamentals/dependency-injection.md). V ASP.NET Core MVC můžou [řadiče](controllers/dependency-injection.md) požadovat služby potřebné prostřednictvím svých konstruktorů, takže by mohli postupovat podle [principu explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

Aplikace může také používat [vkládání závislostí v souborech zobrazení](views/dependency-injection.md)pomocí direktivy `@inject`:

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filtry

[Filtry](controllers/filters.md) , které vývojářům pomůžou zapouzdřit věci mimo průřez, jako je zpracování výjimek nebo autorizace. Filtry umožňují spuštění vlastní logiky před a po zpracování pro metody akcí a lze je nakonfigurovat tak, aby běžely v určitých bodech v rámci kanálu spuštění pro daný požadavek. Filtry lze použít u řadičů nebo akcí jako atributů (nebo je lze spustit globálně). V rámci rozhraní je zahrnuto několik filtrů (například `Authorize`). `[Authorize]` je atribut, který slouží k vytváření ověřovacích filtrů MVC.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Oblasti

[Oblasti](controllers/areas.md) představují způsob, jak rozdělit velkou webovou aplikaci ASP.NET Core MVC do menších skupin funkcí. Oblast je struktura MVC uvnitř aplikace. V projektu MVC jsou logické komponenty, jako je model, kontrolér a zobrazení, uchovávány v různých složkách a MVC používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně. Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako jsou rezervace, fakturace a hledání atd. Každá z těchto jednotek má vlastní zobrazení logických komponent, řadičů a modelů.

### <a name="web-apis"></a>Webová rozhraní API

Kromě toho, že se jedná o skvělou platformu pro tvorbu webů, ASP.NET Core MVC má skvělou podporu pro vytváření webových rozhraní API. Můžete vytvářet služby, které dosáhnou široké škály klientů včetně prohlížečů a mobilních zařízení.

Rozhraní zahrnuje podporu pro vyjednávání obsahu HTTP s integrovanou podporou pro [formátování dat](xref:web-api/advanced/formatting) jako JSON nebo XML. Zápis [vlastních formátovacích modulů](xref:web-api/advanced/custom-formatters) pro přidání podpory pro vlastní formáty.

Pokud chcete povolit podporu pro multimédia, použijte generaci odkazů. Umožňuje snadno povolit podporu pro [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) , aby vaše webová rozhraní API mohla být sdílená napříč více webovými aplikacemi.

### <a name="testability"></a>Testovatelnosti

Použití rozhraní a vkládání závislostí v rozhraní je vhodné pro testování částí a rozhraní zahrnuje funkce (jako je TestHost a inMemory Provider pro Entity Framework), které umožňují provádět i rychlé a snadné [testy integrace](xref:test/integration-tests) . Přečtěte si další informace o [testování logiky kontroleru](controllers/testing.md).

### <a name="razor-view-engine"></a>Modul zobrazení Razor

[ASP.NET Core zobrazení MVC](views/overview.md) používají k vykreslování zobrazení [modul zobrazení Razor](views/razor.md) . Razor je kompaktní, přehledové a kapalné jazyky pro označování zobrazení pomocí vloženého C# kódu. Syntaxe Razor slouží k dynamickému generování webového obsahu na serveru. Kód serveru můžete vyčistit pomocí obsahu a kódu na straně klienta.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

Pomocí zobrazovacího modulu Razor můžete definovat [rozložení](views/layout.md), [částečná zobrazení](views/partial.md) a nahraditelné oddíly.

### <a name="strongly-typed-views"></a>Zobrazení silného typu

Zobrazení Razor v MVC lze silně napsat na základě vašeho modelu. Řadiče můžou předat model silného typu k zobrazením, která umožňují, aby vaše zobrazení měla kontrolu typů a podporu technologie IntelliSense.

Například následující zobrazení vykresluje model typu `IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Pomocné rutiny značek

[Pomocník značek](views/tag-helpers/intro.md) povolit kód na straně serveru, který se účastní vytváření a vykreslování prvků HTML v souborech Razor. Můžete použít pomocníka značek k definování vlastních značek (například `<environment>`) nebo k úpravě chování existujících značek (například `<label>`). Přihlaste se k určitým prvkům na základě názvu elementu a jeho atributů pomocí rutiny tag. Poskytují výhody vykreslování na straně serveru a zároveň zachovává prostředí pro úpravy HTML.

K dispozici je mnoho vestavěných pomocníků značek pro běžné úkoly, jako je vytváření formulářů, odkazů, načítání assetů a ještě více dostupných ve veřejných úložištích GitHub a jako balíčky NuGet. Pomocníky značek jsou vytvořeny v C#a jsou cíleny na prvky HTML na základě názvu elementu, názvu atributu nebo nadřazené značky. Například integrovaný LinkTagHelper lze použít k vytvoření odkazu na akci `Login` `AccountsController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper` lze použít k zahrnutí různých skriptů do vašich zobrazení (například RAW nebo minifikovaného) na základě běhového prostředí, jako je vývoj, příprava nebo produkce:

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Značky pomocníků poskytují prostředí pro vývoj ve formátu HTML a bohatou technologii IntelliSense pro vytváření značek HTML a Razor. Většina vestavěných pomocníků značek cílí na existující prvky HTML a poskytují atributy na straně serveru pro element.

### <a name="view-components"></a>Zobrazit součásti

[Zobrazit komponenty](views/view-components.md) umožňují zabalit logiku vykreslování a znovu ji použít v celé aplikaci. Jsou podobné [částečným zobrazením](views/partial.md), ale s přidruženou logikou.

## <a name="compatibility-version"></a>Kompatibilita – verze

Metoda <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> umožňuje aplikaci, aby se odhlásila nebo výslovný souhlas při změnách chování, které se zavedly v ASP.NET Core MVC 2,1 nebo novější.

Další informace naleznete v tématu <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Další zdroje

* [MyTested. AspNetCore. Mvc-Fluent test Library pro ASP.NET Core Mvc](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc) &ndash; knihovnu testování jednotek se silnými typy a poskytuje rozhraní Fluent pro testování MVC a webových aplikací API. (*Společnost Microsoft nespravuje ani nepodporuje.* )
* <xref:blazor/integrate-components>
