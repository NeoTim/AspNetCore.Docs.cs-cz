---
title: Přehled ASP.NET Core MVC
author: ardalis
description: Zjistěte, jak ASP.NET Core MVC je bohatou architekturu pro vytváření webových aplikací a rozhraní API pomocí Model-View-Controller návrhu.
ms.author: riande
ms.date: 01/08/2018
uid: mvc/overview
ms.openlocfilehash: eb636616ab952f506993ae5f4692e5e966bb20f4
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516297"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="bdeff-103">Přehled ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="bdeff-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="bdeff-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bdeff-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bdeff-105">ASP.NET Core MVC je bohatou architekturu pro vytváření webových aplikací a rozhraní API pomocí Model-View-Controller návrhu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="bdeff-106">Co je vzor MVC?</span><span class="sxs-lookup"><span data-stu-id="bdeff-106">What is the MVC pattern?</span></span>

<span data-ttu-id="bdeff-107">Vzor architektury Model-View-Controller (MVC) rozděluje aplikace do tří hlavních skupin součástí: Modely, zobrazení a Kontrolerů.</span><span class="sxs-lookup"><span data-stu-id="bdeff-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="bdeff-108">Tento model pomáhá zajistit [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="bdeff-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="bdeff-109">Použití tohoto modelu, jsou uživatelské požadavky směrovány do Kontroleru, který je zodpovědný za práci s modelem k provádění akcí uživatele a/nebo načíst výsledky dotazů.</span><span class="sxs-lookup"><span data-stu-id="bdeff-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="bdeff-110">Kontroler vybere zobrazení tak, aby pro uživatele a poskytuje s daty modelu, které vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="bdeff-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="bdeff-111">Následující diagram znázorňuje tři hlavní komponenty a ty, které odkazují na ty ostatní:</span><span class="sxs-lookup"><span data-stu-id="bdeff-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Vzor MVC](overview/_static/mvc.png)

<span data-ttu-id="bdeff-113">Tento vymezení odpovědnosti umožňuje škálovat aplikace z hlediska složitost, protože je to snazší pro kódování, ladění a testování (model, zobrazení nebo řadič) něco, co má jednu úlohu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="bdeff-114">Je obtížnější aktualizace, testování a ladění kódu, který má závislosti rozdělené mezi dva nebo více z těchto tří oblastí.</span><span class="sxs-lookup"><span data-stu-id="bdeff-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="bdeff-115">Například logiku uživatelského rozhraní obvykle Chcete-li změnit častěji než obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="bdeff-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="bdeff-116">Pokud prezentaci kódu a obchodní logiky jsou sloučeny do jednoho objektu, musí být objekt, který obsahuje logiku upravena pokaždé, když se změní uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bdeff-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="bdeff-117">To často představuje chyby a vyžaduje opakované obchodní logiky po každé změně minimální uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bdeff-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="bdeff-118">Zobrazení a kontroler závisí na modelu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="bdeff-119">Ale model závisí na zobrazení ani kontroleru.</span><span class="sxs-lookup"><span data-stu-id="bdeff-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="bdeff-120">Toto je jedna z klíčových výhod tohoto oddělení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="bdeff-121">Toto oddělení umožňuje nezávisle na vizuální prezentace modelu, který má být sestaví a otestují.</span><span class="sxs-lookup"><span data-stu-id="bdeff-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="bdeff-122">Model odpovědnosti</span><span class="sxs-lookup"><span data-stu-id="bdeff-122">Model Responsibilities</span></span>

<span data-ttu-id="bdeff-123">Model v aplikaci MVC představuje stav aplikace a veškeré obchodní logiky nebo operací, které by měl provádět ji.</span><span class="sxs-lookup"><span data-stu-id="bdeff-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="bdeff-124">Obchodní logika by měl zapouzdřené v modelu, spolu s žádné implementační logika pro uchování stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdeff-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="bdeff-125">Zobrazení silného typu obvykle používají typy ViewModel navržené tak, aby obsahovala data pro zobrazení v tomto zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="bdeff-126">Správce vytvoří a naplní tyto instance ViewModel z modelu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="bdeff-127">Zobrazit odpovědnosti</span><span class="sxs-lookup"><span data-stu-id="bdeff-127">View Responsibilities</span></span>

<span data-ttu-id="bdeff-128">Zobrazení jsou zodpovědný za prezentování obsah prostřednictvím uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bdeff-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="bdeff-129">Používají [zobrazovací modul Razor](#razor-view-engine) pro vložení kódu .NET v kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="bdeff-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="bdeff-130">By měl být minimální logiku v zobrazeních a jakékoli logiky v nich by se měly týkat předkládání obsahu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="bdeff-131">Pokud zjistíte, třeba provádět spoustu logiky v zobrazení souborů k zobrazení dat z komplexní model, zvažte použití [zobrazení komponenty](views/view-components.md), ViewModel, nebo zobrazit šablonu pro zjednodušení zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="bdeff-132">Odpovědnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="bdeff-132">Controller Responsibilities</span></span>

<span data-ttu-id="bdeff-133">Kontrolery jsou komponenty, které zpracovávají interakci s uživatelem, pracují s modelem a konečně vybírají vykreslené zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="bdeff-134">V aplikaci MVC zobrazení pouze zobrazuje informace; kontroler zpracovává a reaguje na vstup uživatele a interakce.</span><span class="sxs-lookup"><span data-stu-id="bdeff-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="bdeff-135">Ve vzoru MVC kontroleru je počáteční vstupní bod a je zodpovědný za výběrem kterém modelu typy pro práci s a které zobrazení k vykreslení (proto jeho název – určuje způsob reakce aplikace na daný požadavek).</span><span class="sxs-lookup"><span data-stu-id="bdeff-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="bdeff-136">Kontrolery by neměl příliš složité podle příliš mnoho odpovědností.</span><span class="sxs-lookup"><span data-stu-id="bdeff-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="bdeff-137">Pokud chcete zachovat logice kontroleru stal zbytečně složité, nabízená oznámení obchodní logiku z kontroleru a do modelu domény.</span><span class="sxs-lookup"><span data-stu-id="bdeff-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="bdeff-138">Pokud zjistíte, že vaše akce kontroleru často provádějí stejné typy akcí, přesunout tyto běžné akce do [filtry](#filters).</span><span class="sxs-lookup"><span data-stu-id="bdeff-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="bdeff-139">Co je ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="bdeff-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="bdeff-140">Rozhraní ASP.NET Core MVC je jednoduchý, open source, s možností intenzivního testování prezentační platforma optimalizovaná pro použití s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdeff-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="bdeff-141">ASP.NET Core MVC nabízí na základě vzorů způsob tvorby dynamických webů, která umožňuje jasně oddělit oblasti zájmu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="bdeff-142">Dává vám plnou kontrolu nad značkami, podporuje vývoj TDD skvěle a využívá nejnovější webové standardy.</span><span class="sxs-lookup"><span data-stu-id="bdeff-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="bdeff-143">Funkce</span><span class="sxs-lookup"><span data-stu-id="bdeff-143">Features</span></span>

<span data-ttu-id="bdeff-144">ASP.NET Core MVC zahrnuje následující položky:</span><span class="sxs-lookup"><span data-stu-id="bdeff-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="bdeff-145">Směrování</span><span class="sxs-lookup"><span data-stu-id="bdeff-145">Routing</span></span>](#routing)
* [<span data-ttu-id="bdeff-146">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="bdeff-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="bdeff-147">Ověření modelu</span><span class="sxs-lookup"><span data-stu-id="bdeff-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="bdeff-148">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="bdeff-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="bdeff-149">Filtry</span><span class="sxs-lookup"><span data-stu-id="bdeff-149">Filters</span></span>](#filters)
* [<span data-ttu-id="bdeff-150">Oblasti</span><span class="sxs-lookup"><span data-stu-id="bdeff-150">Areas</span></span>](#areas)
* [<span data-ttu-id="bdeff-151">Webová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="bdeff-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="bdeff-152">Testovatelnosti</span><span class="sxs-lookup"><span data-stu-id="bdeff-152">Testability</span></span>](#testability)
* [<span data-ttu-id="bdeff-153">Zobrazovací modul Razor</span><span class="sxs-lookup"><span data-stu-id="bdeff-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="bdeff-154">Zobrazení se silnými typy</span><span class="sxs-lookup"><span data-stu-id="bdeff-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="bdeff-155">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="bdeff-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="bdeff-156">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdeff-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="bdeff-157">Směrování</span><span class="sxs-lookup"><span data-stu-id="bdeff-157">Routing</span></span>

<span data-ttu-id="bdeff-158">ASP.NET Core MVC je postavený na [směrování ASP.NET Core](../fundamentals/routing.md), výkonné komponenta mapování adres URL, která vám umožní vytvářet aplikace, které mají srozumitelné a dohledatelné adresy URL.</span><span class="sxs-lookup"><span data-stu-id="bdeff-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="bdeff-159">To vám umožní definovat vaší aplikace vzory mapování adres URL, které fungují dobře u optimalizace pro vyhledávací weby (SEO) a pro generování odkazů, bez ohledu na způsob uspořádání souborů na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="bdeff-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="bdeff-160">Můžete definovat pomocí syntaxe šablony vhodné trasy, která podporuje hodnotu omezení trasy, výchozí nastavení a volitelné hodnoty trasy.</span><span class="sxs-lookup"><span data-stu-id="bdeff-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="bdeff-161">*Směrování na základě úmluvy* umožňuje můžete globálně zadat adresu URL, která formátuje aplikace přijímá a jak každou z těchto formátů mapuje na metodu konkrétní akce na zadaný kontroler.</span><span class="sxs-lookup"><span data-stu-id="bdeff-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="bdeff-162">Po přijetí příchozího požadavku modulu Směrování analyzuje adresu URL a odpovídá jednomu z definovaných formátech adres URL a pak zavolá metodu akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="bdeff-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="bdeff-163">*Atribut směrování* umožňuje zadat informace o směrování podle upravení kontrolery a akce s atributy, které definují trasy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdeff-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="bdeff-164">To znamená, že vedle kontroleru a akce, které jsou přidružené jsou umístěné vaše definice trasy.</span><span class="sxs-lookup"><span data-stu-id="bdeff-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

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

### <a name="model-binding"></a><span data-ttu-id="bdeff-165">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="bdeff-165">Model binding</span></span>

<span data-ttu-id="bdeff-166">ASP.NET Core MVC [vazby modelu](models/model-binding.md) převede dat o žádostech klientů (hodnot formuláře, data směrování, parametry řetězce dotazu, hlavičky protokolu HTTP) na objekty, které dokáže zpracovat kontroleru.</span><span class="sxs-lookup"><span data-stu-id="bdeff-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="bdeff-167">V důsledku toho nemusí být proveďte práci ve zjištění příchozí žádosti o data; logice kontroleru už má data jako parametry pro její metody akce.</span><span class="sxs-lookup"><span data-stu-id="bdeff-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="bdeff-168">Ověření modelu</span><span class="sxs-lookup"><span data-stu-id="bdeff-168">Model validation</span></span>

<span data-ttu-id="bdeff-169">Podporuje ASP.NET Core MVC [ověření](models/validation.md) pomocí upravení váš objekt modelu s atributy ověření dat poznámky.</span><span class="sxs-lookup"><span data-stu-id="bdeff-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="bdeff-170">Atributy ověření nebyly zvoleny na straně klienta před hodnoty jsou odeslány na server, jakož i na serveru před akce kontroleru je volána.</span><span class="sxs-lookup"><span data-stu-id="bdeff-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

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

<span data-ttu-id="bdeff-171">Akce kontroleru:</span><span class="sxs-lookup"><span data-stu-id="bdeff-171">A controller action:</span></span>

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

<span data-ttu-id="bdeff-172">Rozhraní framework zpracovává ověřování data žádosti na straně klienta i na serveru.</span><span class="sxs-lookup"><span data-stu-id="bdeff-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="bdeff-173">Ověřovací logiku určenou pro typy modelu se přidá do vykreslené zobrazení jako nerušivý poznámky a je požadováno v prohlížeči pomocí [jQuery ověření](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="bdeff-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="bdeff-174">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="bdeff-174">Dependency injection</span></span>

<span data-ttu-id="bdeff-175">Má integrovanou podporu pro ASP.NET Core [injektáž závislostí (DI)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="bdeff-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="bdeff-176">V ASP.NET Core MVC [řadiče](controllers/dependency-injection.md) můžete žádost o potřebné služby prostřednictvím jejich konstruktory, takže se budou dodržovat [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="bdeff-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="bdeff-177">Vaše aplikace může také používat [injektáž závislostí v zobrazení souborů](views/dependency-injection.md), použije `@inject` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="bdeff-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

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

### <a name="filters"></a><span data-ttu-id="bdeff-178">Filtry</span><span class="sxs-lookup"><span data-stu-id="bdeff-178">Filters</span></span>

<span data-ttu-id="bdeff-179">[Filtry](controllers/filters.md) pomoci vývojářům zapouzdření vyskytující aspekty, jako je zpracování výjimek nebo autorizace.</span><span class="sxs-lookup"><span data-stu-id="bdeff-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="bdeff-180">Filtry povolit spuštění vlastní logiku provedení před instrumentací a následného zpracování metody akce a může být nakonfigurován pro spouštění v určitých bodech, v rámci spuštění kanálu pro daný požadavek.</span><span class="sxs-lookup"><span data-stu-id="bdeff-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="bdeff-181">Filtry lze použít u řadičů nebo akce, jako atributy (nebo je možné spustit globálně).</span><span class="sxs-lookup"><span data-stu-id="bdeff-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="bdeff-182">Několik filtrů (například `Authorize`) jsou zahrnuty v rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="bdeff-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="bdeff-183">`[Authorize]` je atribut, který se používá k vytvoření filtry autorizace MVC.</span><span class="sxs-lookup"><span data-stu-id="bdeff-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="bdeff-184">Oblasti</span><span class="sxs-lookup"><span data-stu-id="bdeff-184">Areas</span></span>

<span data-ttu-id="bdeff-185">[Oblasti](controllers/areas.md) poskytují způsob, jak rozdělit velké aplikace ASP.NET Core MVC Web na menší funkční seskupení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="bdeff-186">Oblast je struktury MVC uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdeff-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="bdeff-187">V projektu aplikace MVC logické komponenty, jako jsou modelu, Kontroleru a zobrazení jsou uloženy v různých složkách a aplikace MVC používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="bdeff-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="bdeff-188">Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace.</span><span class="sxs-lookup"><span data-stu-id="bdeff-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="bdeff-189">Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a vyhledávání atd. Každá z těchto jednotek mít své vlastní logickou součástí zobrazení, kontrolerů a modely.</span><span class="sxs-lookup"><span data-stu-id="bdeff-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="bdeff-190">Webová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="bdeff-190">Web APIs</span></span>

<span data-ttu-id="bdeff-191">Kromě toho, že skvělé platforma pro vytváření webů, ASP.NET Core MVC má skvělou podporu pro vytváření webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="bdeff-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="bdeff-192">Můžete vytvářet služby, které se širokou škálou klientů včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="bdeff-193">Rozhraní zahrnuje podporu pro vyjednávání obsahu HTTP s integrovanou podporou pro [formátování dat](xref:web-api/advanced/formatting) jako JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="bdeff-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="bdeff-194">Zápis [vlastní formátovací moduly](xref:web-api/advanced/custom-formatters) přidání podpory pro vlastní formáty.</span><span class="sxs-lookup"><span data-stu-id="bdeff-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="bdeff-195">Použijte generování povolení podpory pro hypermédiích.</span><span class="sxs-lookup"><span data-stu-id="bdeff-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="bdeff-196">Snadno povolit podporu pro [prostředků mezi zdroji (CORS) pro sdílení obsahu](http://www.w3.org/TR/cors/) tak, aby vaše webová rozhraní API mohou být sdíleny napříč více webových aplikací.</span><span class="sxs-lookup"><span data-stu-id="bdeff-196">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="bdeff-197">Testovatelnosti</span><span class="sxs-lookup"><span data-stu-id="bdeff-197">Testability</span></span>

<span data-ttu-id="bdeff-198">Použití rozhraní framework rozhraní a vkládání závislostí usnadňují skvěle se hodí pro testování částí a funkcí (třeba TestHost a InMemory zprostředkovatele pro Entity Framework), které obsahuje rozhraní [integrační testy](xref:test/integration-tests) rychlý a Snadné také.</span><span class="sxs-lookup"><span data-stu-id="bdeff-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="bdeff-199">Další informace o [testování logice kontroleru](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="bdeff-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="bdeff-200">Zobrazovací modul Razor</span><span class="sxs-lookup"><span data-stu-id="bdeff-200">Razor view engine</span></span>

<span data-ttu-id="bdeff-201">[ASP.NET Core MVC zobrazení](views/overview.md) použít [zobrazovací modul Razor](views/razor.md) k vykreslení zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bdeff-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="bdeff-202">Razor je šablona compact, výrazový a plynulé značkovací jazyk pro definování zobrazení pomocí vloženého kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="bdeff-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="bdeff-203">Razor se používá k dynamickému generování webového obsahu na serveru.</span><span class="sxs-lookup"><span data-stu-id="bdeff-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="bdeff-204">Čistě je možné kombinovat kódu serveru s obsahem na straně klienta a kód.</span><span class="sxs-lookup"><span data-stu-id="bdeff-204">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="bdeff-205">Pomocí zobrazovací modul Razor můžete definovat [rozložení](views/layout.md), [částečná zobrazení](views/partial.md) a nahraditelné oddílů.</span><span class="sxs-lookup"><span data-stu-id="bdeff-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="bdeff-206">Zobrazení se silnými typy</span><span class="sxs-lookup"><span data-stu-id="bdeff-206">Strongly typed views</span></span>

<span data-ttu-id="bdeff-207">Zobrazení MVC Razor může být silného typu závislosti na modelu.</span><span class="sxs-lookup"><span data-stu-id="bdeff-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="bdeff-208">Řadiče můžete předat model silného typu zobrazení povolení zobrazení kontrolu typu a podporu technologie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="bdeff-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="bdeff-209">Například následující zobrazení vykreslí model typu `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="bdeff-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="bdeff-210">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="bdeff-210">Tag Helpers</span></span>

<span data-ttu-id="bdeff-211">[Pomocné rutiny značky](views/tag-helpers/intro.md) povolit kód na straně serveru k účasti na vytváření a vykreslování prvků HTML v souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="bdeff-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="bdeff-212">Pomocné rutiny značek můžete použít k definování vlastní značky (například `<environment>`) nebo chcete změnit chování existující značky (například `<label>`).</span><span class="sxs-lookup"><span data-stu-id="bdeff-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="bdeff-213">Pomocné rutiny značek svázat konkrétní prvky podle názvu elementu a jeho atributy.</span><span class="sxs-lookup"><span data-stu-id="bdeff-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="bdeff-214">Poskytují výhod vykreslování na straně serveru stále zachováním HTML prostředí pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="bdeff-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="bdeff-215">Existuje mnoho integrovaných pomocných rutin značek pro běžné úlohy – například vytváření formulářů, odkazy, načítání prostředků a další – a ještě větší počet je dostupný ve veřejných úložištích GitHub a jako NuGet balíčky.</span><span class="sxs-lookup"><span data-stu-id="bdeff-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="bdeff-216">Pomocné rutiny značek jsou vytvořené v jazyce C# a cílí na základě název elementu, atributu nebo nadřazené značky elementů HTML.</span><span class="sxs-lookup"><span data-stu-id="bdeff-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="bdeff-217">Například předdefinované LinkTagHelper slouží k vytvoření odkazu `Login` akce `AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="bdeff-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="bdeff-218">`EnvironmentTagHelper` Je možné zahrnout jiné skripty v zobrazení (například raw nebo minifikovaný) založených na prostředí modulu runtime, vývoj, přípravném nebo produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="bdeff-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

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

<span data-ttu-id="bdeff-219">Pomocné rutiny značek poskytuje vývojové prostředí podporou HTML a bohaté prostředí IntelliSense pro vytváření značky HTML a syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="bdeff-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="bdeff-220">Většina integrovaných pomocných rutin značek cílit na stávající elementy HTML a poskytovat na straně serveru atributy pro element.</span><span class="sxs-lookup"><span data-stu-id="bdeff-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="bdeff-221">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="bdeff-221">View Components</span></span>

<span data-ttu-id="bdeff-222">[Zobrazení komponenty](views/view-components.md) umožňují balíček logiky vykreslování a znovu ji použít v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bdeff-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="bdeff-223">Jsou podobné [částečná zobrazení](views/partial.md), ale s přidružené logiky.</span><span class="sxs-lookup"><span data-stu-id="bdeff-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="bdeff-224">Kompatibilita – verze</span><span class="sxs-lookup"><span data-stu-id="bdeff-224">Compatibility version</span></span>

<span data-ttu-id="bdeff-225"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda umožňuje aplikacím vyjádřit výslovný souhlas nebo výslovný nesouhlas s potenciálně rozbíjející změny chování zavedení v ASP.NET Core MVC 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="bdeff-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="bdeff-226">Další informace naleznete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="bdeff-226">For more information, see <xref:mvc/compatibility-version>.</span></span>
