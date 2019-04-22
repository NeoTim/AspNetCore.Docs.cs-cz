---
title: Přehled ASP.NET Core MVC
author: ardalis
description: Zjistěte, jak ASP.NET Core MVC je bohatou architekturu pro vytváření webových aplikací a rozhraní API pomocí Model-View-Controller návrhu.
ms.author: riande
ms.date: 01/08/2018
uid: mvc/overview
ms.openlocfilehash: eb636616ab952f506993ae5f4692e5e966bb20f4
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516297"
---
# <a name="overview-of-aspnet-core-mvc"></a>Přehled ASP.NET Core MVC

Podle [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC je bohatou architekturu pro vytváření webových aplikací a rozhraní API pomocí Model-View-Controller návrhu.

## <a name="what-is-the-mvc-pattern"></a>Co je vzor MVC?

Vzor architektury Model-View-Controller (MVC) rozděluje aplikace do tří hlavních skupin součástí: Modely, zobrazení a Kontrolerů. Tento model pomáhá zajistit [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Použití tohoto modelu, jsou uživatelské požadavky směrovány do Kontroleru, který je zodpovědný za práci s modelem k provádění akcí uživatele a/nebo načíst výsledky dotazů. Kontroler vybere zobrazení tak, aby pro uživatele a poskytuje s daty modelu, které vyžaduje.

Následující diagram znázorňuje tři hlavní komponenty a ty, které odkazují na ty ostatní:

![Vzor MVC](overview/_static/mvc.png)

Tento vymezení odpovědnosti umožňuje škálovat aplikace z hlediska složitost, protože je to snazší pro kódování, ladění a testování (model, zobrazení nebo řadič) něco, co má jednu úlohu. Je obtížnější aktualizace, testování a ladění kódu, který má závislosti rozdělené mezi dva nebo více z těchto tří oblastí. Například logiku uživatelského rozhraní obvykle Chcete-li změnit častěji než obchodní logiku. Pokud prezentaci kódu a obchodní logiky jsou sloučeny do jednoho objektu, musí být objekt, který obsahuje logiku upravena pokaždé, když se změní uživatelské rozhraní. To často představuje chyby a vyžaduje opakované obchodní logiky po každé změně minimální uživatelské rozhraní.

> [!NOTE]
> Zobrazení a kontroler závisí na modelu. Ale model závisí na zobrazení ani kontroleru. Toto je jedna z klíčových výhod tohoto oddělení. Toto oddělení umožňuje nezávisle na vizuální prezentace modelu, který má být sestaví a otestují.

### <a name="model-responsibilities"></a>Model odpovědnosti

Model v aplikaci MVC představuje stav aplikace a veškeré obchodní logiky nebo operací, které by měl provádět ji. Obchodní logika by měl zapouzdřené v modelu, spolu s žádné implementační logika pro uchování stavu aplikace. Zobrazení silného typu obvykle používají typy ViewModel navržené tak, aby obsahovala data pro zobrazení v tomto zobrazení. Správce vytvoří a naplní tyto instance ViewModel z modelu.

### <a name="view-responsibilities"></a>Zobrazit odpovědnosti

Zobrazení jsou zodpovědný za prezentování obsah prostřednictvím uživatelského rozhraní. Používají [zobrazovací modul Razor](#razor-view-engine) pro vložení kódu .NET v kódu HTML. By měl být minimální logiku v zobrazeních a jakékoli logiky v nich by se měly týkat předkládání obsahu. Pokud zjistíte, třeba provádět spoustu logiky v zobrazení souborů k zobrazení dat z komplexní model, zvažte použití [zobrazení komponenty](views/view-components.md), ViewModel, nebo zobrazit šablonu pro zjednodušení zobrazení.

### <a name="controller-responsibilities"></a>Odpovědnosti kontroleru

Kontrolery jsou komponenty, které zpracovávají interakci s uživatelem, pracují s modelem a konečně vybírají vykreslené zobrazení. V aplikaci MVC zobrazení pouze zobrazuje informace; kontroler zpracovává a reaguje na vstup uživatele a interakce. Ve vzoru MVC kontroleru je počáteční vstupní bod a je zodpovědný za výběrem kterém modelu typy pro práci s a které zobrazení k vykreslení (proto jeho název – určuje způsob reakce aplikace na daný požadavek).

> [!NOTE]
> Kontrolery by neměl příliš složité podle příliš mnoho odpovědností. Pokud chcete zachovat logice kontroleru stal zbytečně složité, nabízená oznámení obchodní logiku z kontroleru a do modelu domény.

>[!TIP]
> Pokud zjistíte, že vaše akce kontroleru často provádějí stejné typy akcí, přesunout tyto běžné akce do [filtry](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Co je ASP.NET Core MVC

Rozhraní ASP.NET Core MVC je jednoduchý, open source, s možností intenzivního testování prezentační platforma optimalizovaná pro použití s ASP.NET Core.

ASP.NET Core MVC nabízí na základě vzorů způsob tvorby dynamických webů, která umožňuje jasně oddělit oblasti zájmu. Dává vám plnou kontrolu nad značkami, podporuje vývoj TDD skvěle a využívá nejnovější webové standardy.

## <a name="features"></a>Funkce

ASP.NET Core MVC zahrnuje následující položky:

* [Směrování](#routing)
* [Vazby modelu](#model-binding)
* [Ověření modelu](#model-validation)
* [Injektáž závislostí](../fundamentals/dependency-injection.md)
* [Filtry](#filters)
* [Oblasti](#areas)
* [Webová rozhraní API](#web-apis)
* [Testovatelnosti](#testability)
* [Zobrazovací modul Razor](#razor-view-engine)
* [Zobrazení se silnými typy](#strongly-typed-views)
* [Pomocné rutiny značek](#tag-helpers)
* [Komponenty zobrazení](#view-components)

### <a name="routing"></a>Směrování

ASP.NET Core MVC je postavený na [směrování ASP.NET Core](../fundamentals/routing.md), výkonné komponenta mapování adres URL, která vám umožní vytvářet aplikace, které mají srozumitelné a dohledatelné adresy URL. To vám umožní definovat vaší aplikace vzory mapování adres URL, které fungují dobře u optimalizace pro vyhledávací weby (SEO) a pro generování odkazů, bez ohledu na způsob uspořádání souborů na webovém serveru. Můžete definovat pomocí syntaxe šablony vhodné trasy, která podporuje hodnotu omezení trasy, výchozí nastavení a volitelné hodnoty trasy.

*Směrování na základě úmluvy* umožňuje můžete globálně zadat adresu URL, která formátuje aplikace přijímá a jak každou z těchto formátů mapuje na metodu konkrétní akce na zadaný kontroler. Po přijetí příchozího požadavku modulu Směrování analyzuje adresu URL a odpovídá jednomu z definovaných formátech adres URL a pak zavolá metodu akce k přidruženému kontroleru.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Atribut směrování* umožňuje zadat informace o směrování podle upravení kontrolery a akce s atributy, které definují trasy vaší aplikace. To znamená, že vedle kontroleru a akce, které jsou přidružené jsou umístěné vaše definice trasy.

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

ASP.NET Core MVC [vazby modelu](models/model-binding.md) převede dat o žádostech klientů (hodnot formuláře, data směrování, parametry řetězce dotazu, hlavičky protokolu HTTP) na objekty, které dokáže zpracovat kontroleru. V důsledku toho nemusí být proveďte práci ve zjištění příchozí žádosti o data; logice kontroleru už má data jako parametry pro její metody akce.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a>Ověření modelu

Podporuje ASP.NET Core MVC [ověření](models/validation.md) pomocí upravení váš objekt modelu s atributy ověření dat poznámky. Atributy ověření nebyly zvoleny na straně klienta před hodnoty jsou odeslány na server, jakož i na serveru před akce kontroleru je volána.

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

Rozhraní framework zpracovává ověřování data žádosti na straně klienta i na serveru. Ověřovací logiku určenou pro typy modelu se přidá do vykreslené zobrazení jako nerušivý poznámky a je požadováno v prohlížeči pomocí [jQuery ověření](https://jqueryvalidation.org/).

### <a name="dependency-injection"></a>Injektáž závislostí

Má integrovanou podporu pro ASP.NET Core [injektáž závislostí (DI)](../fundamentals/dependency-injection.md). V ASP.NET Core MVC [řadiče](controllers/dependency-injection.md) můžete žádost o potřebné služby prostřednictvím jejich konstruktory, takže se budou dodržovat [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).

Vaše aplikace může také používat [injektáž závislostí v zobrazení souborů](views/dependency-injection.md), použije `@inject` – direktiva:

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

[Filtry](controllers/filters.md) pomoci vývojářům zapouzdření vyskytující aspekty, jako je zpracování výjimek nebo autorizace. Filtry povolit spuštění vlastní logiku provedení před instrumentací a následného zpracování metody akce a může být nakonfigurován pro spouštění v určitých bodech, v rámci spuštění kanálu pro daný požadavek. Filtry lze použít u řadičů nebo akce, jako atributy (nebo je možné spustit globálně). Několik filtrů (například `Authorize`) jsou zahrnuty v rozhraní framework. `[Authorize]` je atribut, který se používá k vytvoření filtry autorizace MVC.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Oblasti

[Oblasti](controllers/areas.md) poskytují způsob, jak rozdělit velké aplikace ASP.NET Core MVC Web na menší funkční seskupení. Oblast je struktury MVC uvnitř aplikace. V projektu aplikace MVC logické komponenty, jako jsou modelu, Kontroleru a zobrazení jsou uloženy v různých složkách a aplikace MVC používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace. Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a vyhledávání atd. Každá z těchto jednotek mít své vlastní logickou součástí zobrazení, kontrolerů a modely.

### <a name="web-apis"></a>Webová rozhraní API

Kromě toho, že skvělé platforma pro vytváření webů, ASP.NET Core MVC má skvělou podporu pro vytváření webových rozhraní API. Můžete vytvářet služby, které se širokou škálou klientů včetně prohlížečů a mobilních zařízení.

Rozhraní zahrnuje podporu pro vyjednávání obsahu HTTP s integrovanou podporou pro [formátování dat](xref:web-api/advanced/formatting) jako JSON nebo XML. Zápis [vlastní formátovací moduly](xref:web-api/advanced/custom-formatters) přidání podpory pro vlastní formáty.

Použijte generování povolení podpory pro hypermédiích. Snadno povolit podporu pro [prostředků mezi zdroji (CORS) pro sdílení obsahu](http://www.w3.org/TR/cors/) tak, aby vaše webová rozhraní API mohou být sdíleny napříč více webových aplikací.

### <a name="testability"></a>Testovatelnosti

Použití rozhraní framework rozhraní a vkládání závislostí usnadňují skvěle se hodí pro testování částí a funkcí (třeba TestHost a InMemory zprostředkovatele pro Entity Framework), které obsahuje rozhraní [integrační testy](xref:test/integration-tests) rychlý a Snadné také. Další informace o [testování logice kontroleru](controllers/testing.md).

### <a name="razor-view-engine"></a>Zobrazovací modul Razor

[ASP.NET Core MVC zobrazení](views/overview.md) použít [zobrazovací modul Razor](views/razor.md) k vykreslení zobrazení. Razor je šablona compact, výrazový a plynulé značkovací jazyk pro definování zobrazení pomocí vloženého kódu jazyka C#. Razor se používá k dynamickému generování webového obsahu na serveru. Čistě je možné kombinovat kódu serveru s obsahem na straně klienta a kód.

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

Pomocí zobrazovací modul Razor můžete definovat [rozložení](views/layout.md), [částečná zobrazení](views/partial.md) a nahraditelné oddílů.

### <a name="strongly-typed-views"></a>Zobrazení se silnými typy

Zobrazení MVC Razor může být silného typu závislosti na modelu. Řadiče můžete předat model silného typu zobrazení povolení zobrazení kontrolu typu a podporu technologie IntelliSense.

Například následující zobrazení vykreslí model typu `IEnumerable<Product>`:

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

[Pomocné rutiny značky](views/tag-helpers/intro.md) povolit kód na straně serveru k účasti na vytváření a vykreslování prvků HTML v souborech Razor. Pomocné rutiny značek můžete použít k definování vlastní značky (například `<environment>`) nebo chcete změnit chování existující značky (například `<label>`). Pomocné rutiny značek svázat konkrétní prvky podle názvu elementu a jeho atributy. Poskytují výhod vykreslování na straně serveru stále zachováním HTML prostředí pro úpravy.

Existuje mnoho integrovaných pomocných rutin značek pro běžné úlohy – například vytváření formulářů, odkazy, načítání prostředků a další – a ještě větší počet je dostupný ve veřejných úložištích GitHub a jako NuGet balíčky. Pomocné rutiny značek jsou vytvořené v jazyce C# a cílí na základě název elementu, atributu nebo nadřazené značky elementů HTML. Například předdefinované LinkTagHelper slouží k vytvoření odkazu `Login` akce `AccountsController`:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper` Je možné zahrnout jiné skripty v zobrazení (například raw nebo minifikovaný) založených na prostředí modulu runtime, vývoj, přípravném nebo produkčním prostředí:

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

Pomocné rutiny značek poskytuje vývojové prostředí podporou HTML a bohaté prostředí IntelliSense pro vytváření značky HTML a syntaxe Razor. Většina integrovaných pomocných rutin značek cílit na stávající elementy HTML a poskytovat na straně serveru atributy pro element.

### <a name="view-components"></a>Komponenty zobrazení

[Zobrazení komponenty](views/view-components.md) umožňují balíček logiky vykreslování a znovu ji použít v celé aplikaci. Jsou podobné [částečná zobrazení](views/partial.md), ale s přidružené logiky.

## <a name="compatibility-version"></a>Kompatibilita – verze

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda umožňuje aplikacím vyjádřit výslovný souhlas nebo výslovný nesouhlas s potenciálně rozbíjející změny chování zavedení v ASP.NET Core MVC 2.1 nebo novější.

Další informace naleznete v tématu <xref:mvc/compatibility-version>.
