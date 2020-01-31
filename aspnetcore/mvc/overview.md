---
title: Přehled ASP.NET Core MVC
author: ardalis
description: Přečtěte si, jak ASP.NET Core MVC je bohatá architektura pro vytváření webových aplikací a rozhraní API pomocí vzorového vzoru pro zobrazení modelu.
ms.author: riande
ms.date: 01/28/2020
uid: mvc/overview
ms.openlocfilehash: a147c2aa01f1440f8ac59f73eb7be734193f802a
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869968"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="a23a0-103">Přehled ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a23a0-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="a23a0-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a23a0-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a23a0-105">ASP.NET Core MVC je bohatá architektura pro vytváření webových aplikací a rozhraní API pomocí vzorového vzoru pro zobrazení modelu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="a23a0-106">Co je vzor MVC?</span><span class="sxs-lookup"><span data-stu-id="a23a0-106">What is the MVC pattern?</span></span>

<span data-ttu-id="a23a0-107">Architektonický vzor architektury MVC (Model-View-Controller) odděluje aplikaci do tří hlavních skupin komponent: modelů, zobrazení a řadičů.</span><span class="sxs-lookup"><span data-stu-id="a23a0-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="a23a0-108">Tento model pomáhá dosáhnout [oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="a23a0-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="a23a0-109">Pomocí tohoto modelu jsou požadavky uživatelů směrovány na kontroler, který je zodpovědný za práci s modelem pro provádění akcí uživatele nebo načtení výsledků dotazů.</span><span class="sxs-lookup"><span data-stu-id="a23a0-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="a23a0-110">Kontroler zvolí zobrazení, které se zobrazí uživateli, a poskytne mu veškerá data modelu, která vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="a23a0-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="a23a0-111">Následující diagram znázorňuje tři hlavní komponenty a odkazy na ostatní:</span><span class="sxs-lookup"><span data-stu-id="a23a0-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![Vzor MVC](overview/_static/mvc.png)

<span data-ttu-id="a23a0-113">Tato změna odpovědnosti vám pomůže škálovat aplikace z hlediska složitosti, protože je snazší kód, ladit a testovat něco (model, zobrazení nebo kontroler) s jednou úlohou.</span><span class="sxs-lookup"><span data-stu-id="a23a0-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="a23a0-114">Je obtížnější aktualizovat, testovat a ladit kód, který obsahuje závislosti rozdělené do dvou nebo více těchto tří oblastí.</span><span class="sxs-lookup"><span data-stu-id="a23a0-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="a23a0-115">Například logika uživatelského rozhraní má za následek změnu častěji než obchodní logika.</span><span class="sxs-lookup"><span data-stu-id="a23a0-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="a23a0-116">Pokud je kód prezentace a obchodní logika kombinována v jednom objektu, objekt obsahující obchodní logiku musí být upraven pokaždé, když se změní uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a23a0-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="a23a0-117">To často zavádí chyby a vyžaduje znovu testování obchodní logiky po každé změně minimálního uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a23a0-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="a23a0-118">Zobrazení i kontroler jsou závislé na modelu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="a23a0-119">Model však závisí ani na zobrazení, ani na řadiči.</span><span class="sxs-lookup"><span data-stu-id="a23a0-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="a23a0-120">Toto je jedna z klíčových výhod oddělení.</span><span class="sxs-lookup"><span data-stu-id="a23a0-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="a23a0-121">Toto oddělení umožňuje sestavit a otestovat model nezávisle na vizuální prezentaci.</span><span class="sxs-lookup"><span data-stu-id="a23a0-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="a23a0-122">Závazky modelu</span><span class="sxs-lookup"><span data-stu-id="a23a0-122">Model Responsibilities</span></span>

<span data-ttu-id="a23a0-123">Model v aplikaci MVC představuje stav aplikace a jakékoli obchodní logiky nebo operace, které by je měla provést.</span><span class="sxs-lookup"><span data-stu-id="a23a0-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="a23a0-124">Obchodní logika by měla být zapouzdřená v modelu spolu s logikou implementace pro zachování stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23a0-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="a23a0-125">Zobrazení silného typu obvykle používají ViewModel typy navržené k zobrazení dat zobrazených v tomto zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a23a0-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="a23a0-126">Kontroler vytvoří a naplní tyto instance ViewModel z modelu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="a23a0-127">Zobrazit odpovědnosti</span><span class="sxs-lookup"><span data-stu-id="a23a0-127">View Responsibilities</span></span>

<span data-ttu-id="a23a0-128">Zobrazení jsou zodpovědná za prezentaci obsahu prostřednictvím uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a23a0-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="a23a0-129">Používají [zobrazovací modul Razor](#razor-view-engine) k vložení kódu .NET do kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="a23a0-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="a23a0-130">V zobrazeních by měla být minimální logika a každá logika v nich by se měla vztahovat k prezentaci obsahu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="a23a0-131">Pokud zjistíte, že je potřeba provést skvělou možnost logiky v zobrazení souborů, abyste mohli zobrazit data ze složitého modelu, zvažte použití šablony [View Component](views/view-components.md), ViewModel nebo View pro zjednodušení zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a23a0-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="a23a0-132">Odpovědnosti řadiče</span><span class="sxs-lookup"><span data-stu-id="a23a0-132">Controller Responsibilities</span></span>

<span data-ttu-id="a23a0-133">Řadiče jsou komponenty, které zpracovávají interakci uživatele, pracují s modelem a nakonec vykreslí zobrazení, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="a23a0-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="a23a0-134">V aplikaci MVC zobrazení pouze zobrazuje informace, zatímco kontroler zpracovává vstup uživatele a interakci s uživatelem a reaguje na ně.</span><span class="sxs-lookup"><span data-stu-id="a23a0-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="a23a0-135">Ve vzoru MVC je kontroler počátečním vstupním bodem a zodpovídá za výběr typů modelů, se kterými se má pracovat, a které zobrazení se má vykreslit (takže jeho název určuje, jak aplikace reaguje na daný požadavek).</span><span class="sxs-lookup"><span data-stu-id="a23a0-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="a23a0-136">Řadiče by neměly být příliš komplikované o příliš mnoho zodpovědností.</span><span class="sxs-lookup"><span data-stu-id="a23a0-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="a23a0-137">Aby se logika kontroléru stala nepřesnou složitou, měla by být nabízena obchodní logika z kontroleru a do doménového modelu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="a23a0-138">Pokud zjistíte, že akce kontroleru často provádějí stejné druhy akcí, přesuňte tyto běžné akce do [filtrů](#filters).</span><span class="sxs-lookup"><span data-stu-id="a23a0-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="a23a0-139">Co je ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a23a0-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="a23a0-140">Rozhraní ASP.NET Core MVC je odlehčené Open Source rozhraní s vysokou testovatelné prezentací optimalizované pro použití s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a23a0-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="a23a0-141">ASP.NET Core MVC poskytuje vzory založené na vzorcích pro vytváření dynamických webů, které umožňují čistě oddělit se o takové obavy.</span><span class="sxs-lookup"><span data-stu-id="a23a0-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="a23a0-142">Poskytuje plnou kontrolu nad značkou, podporuje vývoj s použitím TDD a používá nejnovější webové standardy.</span><span class="sxs-lookup"><span data-stu-id="a23a0-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="a23a0-143">Funkce</span><span class="sxs-lookup"><span data-stu-id="a23a0-143">Features</span></span>

<span data-ttu-id="a23a0-144">ASP.NET Core MVC zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="a23a0-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="a23a0-145">Směrování</span><span class="sxs-lookup"><span data-stu-id="a23a0-145">Routing</span></span>](#routing)
* [<span data-ttu-id="a23a0-146">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="a23a0-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="a23a0-147">Ověření modelu</span><span class="sxs-lookup"><span data-stu-id="a23a0-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="a23a0-148">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="a23a0-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="a23a0-149">Filtry</span><span class="sxs-lookup"><span data-stu-id="a23a0-149">Filters</span></span>](#filters)
* [<span data-ttu-id="a23a0-150">Oblasti</span><span class="sxs-lookup"><span data-stu-id="a23a0-150">Areas</span></span>](#areas)
* [<span data-ttu-id="a23a0-151">Webová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a23a0-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="a23a0-152">Testovatelnosti</span><span class="sxs-lookup"><span data-stu-id="a23a0-152">Testability</span></span>](#testability)
* [<span data-ttu-id="a23a0-153">Modul zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="a23a0-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="a23a0-154">Zobrazení silného typu</span><span class="sxs-lookup"><span data-stu-id="a23a0-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="a23a0-155">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="a23a0-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="a23a0-156">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="a23a0-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="a23a0-157">Směrování</span><span class="sxs-lookup"><span data-stu-id="a23a0-157">Routing</span></span>

<span data-ttu-id="a23a0-158">ASP.NET Core MVC je postavená na [směrování ASP.NET Core](../fundamentals/routing.md), což je výkonná součást pro mapování adres URL, která umožňuje vytvářet aplikace s srozumitelnými a Prohledávatelnými adresami URL.</span><span class="sxs-lookup"><span data-stu-id="a23a0-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="a23a0-159">To vám umožní definovat vzory názvů adres URL vaší aplikace, které dobře fungují pro optimalizaci vyhledávačů (SEO) a pro vytváření odkazů, bez ohledu na to, jak se soubory na webovém serveru uspořádají.</span><span class="sxs-lookup"><span data-stu-id="a23a0-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="a23a0-160">Trasy můžete definovat pomocí vhodné syntaxe šablony směrování, která podporuje omezení hodnoty směrování, výchozí hodnoty a volitelné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a23a0-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="a23a0-161">*Směrování založené na konvencích* umožňuje globálně definovat formáty adres URL, které vaše aplikace přijímá, a způsob, jakým se každý z těchto formátů mapuje na konkrétní metodu akce v daném kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a23a0-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="a23a0-162">Po přijetí příchozího požadavku směrovací modul analyzuje adresu URL a porovná ji s jedním z definovaných formátů adres URL a potom zavolá metodu akce přidruženého kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a23a0-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="a23a0-163">*Směrování atributů* umožňuje zadat informace o směrování tím, že upravení řadiče a akce s atributy, které definují trasy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23a0-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="a23a0-164">To znamená, že definice tras jsou umístěny vedle kontroleru a akce, ke kterým jsou přidruženy.</span><span class="sxs-lookup"><span data-stu-id="a23a0-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

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

### <a name="model-binding"></a><span data-ttu-id="a23a0-165">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="a23a0-165">Model binding</span></span>

<span data-ttu-id="a23a0-166">ASP.NET Core [vazby modelu](models/model-binding.md) MVC převede data požadavků klienta (hodnoty formulářů, směrovat data, parametry řetězce dotazu, hlavičky HTTP) do objektů, které může řadič zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a23a0-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="a23a0-167">V důsledku toho vaše logika kontroleru nemusí dělat práci s zjišťováním příchozích dat požadavků. má pouze data jako parametry svých metod akcí.</span><span class="sxs-lookup"><span data-stu-id="a23a0-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="a23a0-168">Ověření modelu</span><span class="sxs-lookup"><span data-stu-id="a23a0-168">Model validation</span></span>

<span data-ttu-id="a23a0-169">ASP.NET Core MVC podporuje [ověřování](models/validation.md) tím, že upravení objekt modelu s atributy ověření poznámky k datům.</span><span class="sxs-lookup"><span data-stu-id="a23a0-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="a23a0-170">Atributy ověřování jsou zkontrolovány na straně klienta před odesláním hodnot na server a také na serveru před voláním akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a23a0-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

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

<span data-ttu-id="a23a0-171">Akce kontroleru:</span><span class="sxs-lookup"><span data-stu-id="a23a0-171">A controller action:</span></span>

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

<span data-ttu-id="a23a0-172">Rozhraní zpracovává ověřování dat žádostí na straně klienta i serveru.</span><span class="sxs-lookup"><span data-stu-id="a23a0-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="a23a0-173">Logika ověřování zadaná u typů modelů je přidána do vykreslených zobrazení jako nenáročná anotace a je vyhodnocena v prohlížeči s [ověřováním jQuery](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="a23a0-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="a23a0-174">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="a23a0-174">Dependency injection</span></span>

<span data-ttu-id="a23a0-175">ASP.NET Core obsahuje integrovanou podporu pro [vkládání závislostí (di)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="a23a0-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="a23a0-176">V ASP.NET Core MVC můžou [řadiče](controllers/dependency-injection.md) požadovat služby potřebné prostřednictvím svých konstruktorů, takže by mohli postupovat podle [principu explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="a23a0-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="a23a0-177">Aplikace může také používat [vkládání závislostí v souborech zobrazení](views/dependency-injection.md)pomocí direktivy `@inject`:</span><span class="sxs-lookup"><span data-stu-id="a23a0-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

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

### <a name="filters"></a><span data-ttu-id="a23a0-178">Filtry</span><span class="sxs-lookup"><span data-stu-id="a23a0-178">Filters</span></span>

<span data-ttu-id="a23a0-179">[Filtry](controllers/filters.md) , které vývojářům pomůžou zapouzdřit věci mimo průřez, jako je zpracování výjimek nebo autorizace.</span><span class="sxs-lookup"><span data-stu-id="a23a0-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="a23a0-180">Filtry umožňují spuštění vlastní logiky před a po zpracování pro metody akcí a lze je nakonfigurovat tak, aby běžely v určitých bodech v rámci kanálu spuštění pro daný požadavek.</span><span class="sxs-lookup"><span data-stu-id="a23a0-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="a23a0-181">Filtry lze použít u řadičů nebo akcí jako atributů (nebo je lze spustit globálně).</span><span class="sxs-lookup"><span data-stu-id="a23a0-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="a23a0-182">V rámci rozhraní je zahrnuto několik filtrů (například `Authorize`).</span><span class="sxs-lookup"><span data-stu-id="a23a0-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="a23a0-183">`[Authorize]` je atribut, který slouží k vytváření ověřovacích filtrů MVC.</span><span class="sxs-lookup"><span data-stu-id="a23a0-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="a23a0-184">Oblasti</span><span class="sxs-lookup"><span data-stu-id="a23a0-184">Areas</span></span>

<span data-ttu-id="a23a0-185">[Oblasti](controllers/areas.md) představují způsob, jak rozdělit velkou webovou aplikaci ASP.NET Core MVC do menších skupin funkcí.</span><span class="sxs-lookup"><span data-stu-id="a23a0-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="a23a0-186">Oblast je struktura MVC uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="a23a0-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="a23a0-187">V projektu MVC jsou logické komponenty, jako je model, kontrolér a zobrazení, uchovávány v různých složkách a MVC používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="a23a0-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="a23a0-188">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="a23a0-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="a23a0-189">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako jsou rezervace, fakturace a hledání atd. Každá z těchto jednotek má vlastní zobrazení logických komponent, řadičů a modelů.</span><span class="sxs-lookup"><span data-stu-id="a23a0-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="a23a0-190">Webová rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a23a0-190">Web APIs</span></span>

<span data-ttu-id="a23a0-191">Kromě toho, že se jedná o skvělou platformu pro tvorbu webů, ASP.NET Core MVC má skvělou podporu pro vytváření webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a23a0-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="a23a0-192">Můžete vytvářet služby, které dosáhnou široké škály klientů včetně prohlížečů a mobilních zařízení.</span><span class="sxs-lookup"><span data-stu-id="a23a0-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="a23a0-193">Rozhraní zahrnuje podporu pro vyjednávání obsahu HTTP s integrovanou podporou pro [formátování dat](xref:web-api/advanced/formatting) jako JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="a23a0-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="a23a0-194">Zápis [vlastních formátovacích modulů](xref:web-api/advanced/custom-formatters) pro přidání podpory pro vlastní formáty.</span><span class="sxs-lookup"><span data-stu-id="a23a0-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="a23a0-195">Pokud chcete povolit podporu pro multimédia, použijte generaci odkazů.</span><span class="sxs-lookup"><span data-stu-id="a23a0-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="a23a0-196">Umožňuje snadno povolit podporu pro [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) , aby vaše webová rozhraní API mohla být sdílená napříč více webovými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="a23a0-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="a23a0-197">Testovatelnosti</span><span class="sxs-lookup"><span data-stu-id="a23a0-197">Testability</span></span>

<span data-ttu-id="a23a0-198">Použití rozhraní a vkládání závislostí v rozhraní je vhodné pro testování částí a rozhraní zahrnuje funkce (jako je TestHost a inMemory Provider pro Entity Framework), které umožňují provádět i rychlé a snadné [testy integrace](xref:test/integration-tests) .</span><span class="sxs-lookup"><span data-stu-id="a23a0-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="a23a0-199">Přečtěte si další informace o [testování logiky kontroleru](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="a23a0-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="a23a0-200">Modul zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="a23a0-200">Razor view engine</span></span>

<span data-ttu-id="a23a0-201">[ASP.NET Core zobrazení MVC](views/overview.md) používají k vykreslování zobrazení [modul zobrazení Razor](views/razor.md) .</span><span class="sxs-lookup"><span data-stu-id="a23a0-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="a23a0-202">Razor je kompaktní, přehledové a kapalné jazyky pro označování zobrazení pomocí vloženého C# kódu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="a23a0-203">Syntaxe Razor slouží k dynamickému generování webového obsahu na serveru.</span><span class="sxs-lookup"><span data-stu-id="a23a0-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="a23a0-204">Kód serveru můžete vyčistit pomocí obsahu a kódu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a23a0-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="a23a0-205">Pomocí zobrazovacího modulu Razor můžete definovat [rozložení](views/layout.md), [částečná zobrazení](views/partial.md) a nahraditelné oddíly.</span><span class="sxs-lookup"><span data-stu-id="a23a0-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="a23a0-206">Zobrazení silného typu</span><span class="sxs-lookup"><span data-stu-id="a23a0-206">Strongly typed views</span></span>

<span data-ttu-id="a23a0-207">Zobrazení Razor v MVC lze silně napsat na základě vašeho modelu.</span><span class="sxs-lookup"><span data-stu-id="a23a0-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="a23a0-208">Řadiče můžou předat model silného typu k zobrazením, která umožňují, aby vaše zobrazení měla kontrolu typů a podporu technologie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="a23a0-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="a23a0-209">Například následující zobrazení vykresluje model typu `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="a23a0-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="a23a0-210">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="a23a0-210">Tag Helpers</span></span>

<span data-ttu-id="a23a0-211">[Pomocník značek](views/tag-helpers/intro.md) povolit kód na straně serveru, který se účastní vytváření a vykreslování prvků HTML v souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="a23a0-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="a23a0-212">Můžete použít pomocníka značek k definování vlastních značek (například `<environment>`) nebo k úpravě chování existujících značek (například `<label>`).</span><span class="sxs-lookup"><span data-stu-id="a23a0-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="a23a0-213">Přihlaste se k určitým prvkům na základě názvu elementu a jeho atributů pomocí rutiny tag.</span><span class="sxs-lookup"><span data-stu-id="a23a0-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="a23a0-214">Poskytují výhody vykreslování na straně serveru a zároveň zachovává prostředí pro úpravy HTML.</span><span class="sxs-lookup"><span data-stu-id="a23a0-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="a23a0-215">K dispozici je mnoho vestavěných pomocníků značek pro běžné úkoly, jako je vytváření formulářů, odkazů, načítání assetů a ještě více dostupných ve veřejných úložištích GitHub a jako balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="a23a0-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="a23a0-216">Pomocníky značek jsou vytvořeny v C#a jsou cíleny na prvky HTML na základě názvu elementu, názvu atributu nebo nadřazené značky.</span><span class="sxs-lookup"><span data-stu-id="a23a0-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="a23a0-217">Například integrovaný LinkTagHelper lze použít k vytvoření odkazu na akci `Login` `AccountsController`:</span><span class="sxs-lookup"><span data-stu-id="a23a0-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="a23a0-218">`EnvironmentTagHelper` lze použít k zahrnutí různých skriptů do vašich zobrazení (například RAW nebo minifikovaného) na základě běhového prostředí, jako je vývoj, příprava nebo produkce:</span><span class="sxs-lookup"><span data-stu-id="a23a0-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

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

<span data-ttu-id="a23a0-219">Značky pomocníků poskytují prostředí pro vývoj ve formátu HTML a bohatou technologii IntelliSense pro vytváření značek HTML a Razor.</span><span class="sxs-lookup"><span data-stu-id="a23a0-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="a23a0-220">Většina vestavěných pomocníků značek cílí na existující prvky HTML a poskytují atributy na straně serveru pro element.</span><span class="sxs-lookup"><span data-stu-id="a23a0-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="a23a0-221">Zobrazit součásti</span><span class="sxs-lookup"><span data-stu-id="a23a0-221">View Components</span></span>

<span data-ttu-id="a23a0-222">[Zobrazit komponenty](views/view-components.md) umožňují zabalit logiku vykreslování a znovu ji použít v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a23a0-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="a23a0-223">Jsou podobné [částečným zobrazením](views/partial.md), ale s přidruženou logikou.</span><span class="sxs-lookup"><span data-stu-id="a23a0-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="a23a0-224">Kompatibilita – verze</span><span class="sxs-lookup"><span data-stu-id="a23a0-224">Compatibility version</span></span>

<span data-ttu-id="a23a0-225">Metoda <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> umožňuje aplikaci, aby se odhlásila nebo výslovný souhlas při změnách chování, které se zavedly v ASP.NET Core MVC 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="a23a0-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="a23a0-226">Další informace najdete v tématu <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="a23a0-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a23a0-227">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a23a0-227">Additional resources</span></span>

* <span data-ttu-id="a23a0-228">[MyTested. AspNetCore. Mvc-Fluent test Library pro ASP.NET Core Mvc](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc) &ndash; knihovnu testování jednotek se silnými typy a poskytuje rozhraní Fluent pro testování MVC a webových aplikací API.</span><span class="sxs-lookup"><span data-stu-id="a23a0-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc) &ndash; Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="a23a0-229">(*Společnost Microsoft nespravuje ani nepodporuje.* )</span><span class="sxs-lookup"><span data-stu-id="a23a0-229">(*Not maintained or supported by Microsoft.*)</span></span>
* [<span data-ttu-id="a23a0-230">Integrace součástí Razor do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="a23a0-230">Integrate Razor components into Razor Pages and MVC apps</span></span>](xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps)

