---
title: Zpracování chyb v Blazor aplikacích ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core Blazor způsob, jakým Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: cca4d8ce0c783f26f33cb7b2b1535a4bc53384d6
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424346"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="98ea5-103">Zpracování chyb v Blazor aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98ea5-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="98ea5-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="98ea5-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="98ea5-105">Tento článek popisuje Blazor , jak spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.</span><span class="sxs-lookup"><span data-stu-id="98ea5-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="98ea5-106">Podrobné chyby při vývoji</span><span class="sxs-lookup"><span data-stu-id="98ea5-106">Detailed errors during development</span></span>

<span data-ttu-id="98ea5-107">Když Blazor aplikace nefunguje správně během vývoje, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace.</span><span class="sxs-lookup"><span data-stu-id="98ea5-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="98ea5-108">Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky žlutý pruh:</span><span class="sxs-lookup"><span data-stu-id="98ea5-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="98ea5-109">Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="98ea5-110">V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="98ea5-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="98ea5-111">Uživatelské rozhraní tohoto prostředí pro zpracování chyb je součástí Blazor šablon projektu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="98ea5-112">V Blazor aplikaci WebAssembly, přizpůsobte si prostředí v souboru *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="98ea5-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="98ea5-113">V Blazor serverové aplikaci si přizpůsobte prostředí v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="98ea5-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="98ea5-114">`blazor-error-ui`Element je skrytý styly zahrnutými v Blazor šablonách (*wwwroot/CSS/Web. CSS*) a pak se zobrazí, když dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="98ea5-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="98ea5-115">Jak Blazor Serverová aplikace reaguje na neošetřené výjimky</span><span class="sxs-lookup"><span data-stu-id="98ea5-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="98ea5-116">Server je stavový rámec.</span><span class="sxs-lookup"><span data-stu-id="98ea5-116"> Server is a stateful framework.</span></span> <span data-ttu-id="98ea5-117">I když uživatelé pracují s aplikací, udržují připojení k serveru známému jako *okruh*.</span><span class="sxs-lookup"><span data-stu-id="98ea5-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="98ea5-118">Okruh obsahuje aktivní instance komponent a mnoho dalších aspektů stavu, například:</span><span class="sxs-lookup"><span data-stu-id="98ea5-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="98ea5-119">Poslední Vykreslený výstup komponent.</span><span class="sxs-lookup"><span data-stu-id="98ea5-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="98ea5-120">Aktuální sada delegátů zpracovávajících události, které mohou být aktivovány událostmi na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="98ea5-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="98ea5-121">Pokud uživatel otevře aplikaci na více kartách prohlížeče, má několik nezávislých okruhů.</span><span class="sxs-lookup"><span data-stu-id="98ea5-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="98ea5-122">zpracovává většinu neošetřených výjimek jako závažného okruhu, ve kterém se vyskytují.</span><span class="sxs-lookup"><span data-stu-id="98ea5-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="98ea5-123">Pokud je okruh ukončen z důvodu neošetřené výjimky, uživatel může pokračovat v interakci s aplikací pouze tak, že znovu načte stránku, aby vytvořila nový okruh.</span><span class="sxs-lookup"><span data-stu-id="98ea5-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="98ea5-124">Nejsou ovlivněny okruhy mimo tu, která je ukončená, což jsou okruhy pro ostatní uživatele nebo jiné karty prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="98ea5-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="98ea5-125">Tento scénář je podobný aplikaci klasické pracovní plochy, při které dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="98ea5-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="98ea5-126">Chybná aplikace se musí restartovat, ale ostatní aplikace to neovlivní.</span><span class="sxs-lookup"><span data-stu-id="98ea5-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="98ea5-127">Okruh se ukončí, když dojde k neošetřené výjimce z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="98ea5-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="98ea5-128">Neošetřená výjimka často opustí okruh v nedefinovaném stavu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="98ea5-129">Po neošetřené výjimce nelze zaručit normální operaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="98ea5-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="98ea5-130">V případě, že okruh pokračuje, mohou se v aplikaci zobrazit slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="98ea5-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="98ea5-131">Správa neošetřených výjimek v kódu pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="98ea5-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="98ea5-132">Aby aplikace pokračovala i po chybě, aplikace musí mít logiku zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="98ea5-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="98ea5-133">Pozdější části tohoto článku popisují možné zdroje neošetřených výjimek.</span><span class="sxs-lookup"><span data-stu-id="98ea5-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="98ea5-134">V produkčním prostředí nevykresluje zprávy o výjimkách rozhraní nebo trasování zásobníku v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="98ea5-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="98ea5-135">Vykreslování zpráv výjimek nebo trasování zásobníku může:</span><span class="sxs-lookup"><span data-stu-id="98ea5-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="98ea5-136">Vyzradit citlivé informace koncovým uživatelům.</span><span class="sxs-lookup"><span data-stu-id="98ea5-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="98ea5-137">Pomáhat uživateli se zlými úmysly zjišťovat slabiny v aplikaci, která může ohrozit zabezpečení aplikace, serveru nebo sítě.</span><span class="sxs-lookup"><span data-stu-id="98ea5-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="98ea5-138">Protokolovat chyby trvalého poskytovatele</span><span class="sxs-lookup"><span data-stu-id="98ea5-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="98ea5-139">Pokud dojde k neošetřené výjimce, je výjimka zaznamenána do <xref:Microsoft.Extensions.Logging.ILogger> instancí nakonfigurovaných v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="98ea5-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="98ea5-140">Ve výchozím nastavení se Blazor aplikace protokolují do výstupu konzoly s poskytovatelem protokolování konzoly.</span><span class="sxs-lookup"><span data-stu-id="98ea5-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="98ea5-141">Zvažte možnost protokolování do trvalého umístění u poskytovatele, který spravuje velikost protokolu a rotaci protokolů.</span><span class="sxs-lookup"><span data-stu-id="98ea5-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="98ea5-142">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="98ea5-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="98ea5-143">Během vývoje Blazor obvykle posílá kompletní informace o výjimkách do konzoly prohlížeče, aby bylo možné v ladění.</span><span class="sxs-lookup"><span data-stu-id="98ea5-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="98ea5-144">V produkčním prostředí jsou podrobné chyby v konzole prohlížeče ve výchozím nastavení zakázané, což znamená, že se do klientů neodesílají chyby, ale úplné podrobnosti o výjimce se pořád protokolují na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="98ea5-145">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="98ea5-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="98ea5-146">Musíte se rozhodnout, které incidenty se mají protokolovat, a úroveň závažnosti protokolovaných incidentů.</span><span class="sxs-lookup"><span data-stu-id="98ea5-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="98ea5-147">Nepřátelským uživatelům může být možné aktivovat chyby záměrně.</span><span class="sxs-lookup"><span data-stu-id="98ea5-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="98ea5-148">Například Neprotokolujte incident z chyby, kde `ProductId` je uvedena neznámá adresa URL komponenty, která zobrazuje podrobnosti o produktu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="98ea5-149">Ne všechny chyby by se měly považovat za incidenty s vysokou závažností pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="98ea5-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="98ea5-150">Další informace naleznete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="98ea5-150">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="98ea5-151">Místa, kde může dojít k chybám</span><span class="sxs-lookup"><span data-stu-id="98ea5-151">Places where errors may occur</span></span>

<span data-ttu-id="98ea5-152">Rozhraní a kód aplikace mohou aktivovat neošetřené výjimky v žádném z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="98ea5-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="98ea5-153">Instance komponenty</span><span class="sxs-lookup"><span data-stu-id="98ea5-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="98ea5-154">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="98ea5-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="98ea5-155">Logika vykreslování</span><span class="sxs-lookup"><span data-stu-id="98ea5-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="98ea5-156">Obslužné rutiny událostí</span><span class="sxs-lookup"><span data-stu-id="98ea5-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="98ea5-157">Vyřazení součásti</span><span class="sxs-lookup"><span data-stu-id="98ea5-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="98ea5-158">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="98ea5-158">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="98ea5-159">[BlazorRevykreslování serveru](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="98ea5-159">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="98ea5-160">Předchozí neošetřené výjimky jsou popsány v následujících částech tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="98ea5-161">Instance komponenty</span><span class="sxs-lookup"><span data-stu-id="98ea5-161">Component instantiation</span></span>

<span data-ttu-id="98ea5-162">Při Blazor vytváření instance komponenty:</span><span class="sxs-lookup"><span data-stu-id="98ea5-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="98ea5-163">Je vyvolán konstruktor součásti.</span><span class="sxs-lookup"><span data-stu-id="98ea5-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="98ea5-164">Jsou vyvolány konstruktory jakékoli nejednoznačné služby DI Services dodávané do konstruktoru komponenty prostřednictvím [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) direktivy nebo [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) atributu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="98ea5-165">BlazorServerový okruh se nezdařil, pokud kterýkoli z spouštěného konstruktoru nebo setter pro jakoukoliv `[Inject]` vlastnost vyvolá neošetřenou výjimku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="98ea5-166">Výjimka je závažná, protože architektura nemůže vytvořit instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="98ea5-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="98ea5-167">Pokud logika konstruktoru může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="98ea5-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="98ea5-168">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="98ea5-168">Lifecycle methods</span></span>

<span data-ttu-id="98ea5-169">Během životnosti komponenty Blazor vyvolá následující [metody životního cyklu](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="98ea5-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="98ea5-170">Pokud jakákoli metoda životního cyklu vyvolá výjimku synchronně nebo asynchronně, je výjimka závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="98ea5-171">Pro součásti, které se zabývat chybami v metodách životního cyklu, přidejte logiku zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="98ea5-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="98ea5-172">V následujícím příkladu, který `OnParametersSetAsync` volá metodu pro získání produktu:</span><span class="sxs-lookup"><span data-stu-id="98ea5-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="98ea5-173">Výjimka vyvolaná v `ProductRepository.GetProductByIdAsync` metodě je zpracována `try-catch` příkazem.</span><span class="sxs-lookup"><span data-stu-id="98ea5-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="98ea5-174">Po `catch` spuštění bloku:</span><span class="sxs-lookup"><span data-stu-id="98ea5-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="98ea5-175">`loadFailed`je nastaven na `true` , který se používá k zobrazení chybové zprávy uživateli.</span><span class="sxs-lookup"><span data-stu-id="98ea5-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="98ea5-176">Chyba je zaznamenána do protokolu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="98ea5-177">Logika vykreslování</span><span class="sxs-lookup"><span data-stu-id="98ea5-177">Rendering logic</span></span>

<span data-ttu-id="98ea5-178">Deklarativní označení v `.razor` souboru komponenty je zkompilováno do metody jazyka C# s názvem `BuildRenderTree` .</span><span class="sxs-lookup"><span data-stu-id="98ea5-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="98ea5-179">Když komponenta vykreslí, `BuildRenderTree` provede a sestaví strukturu dat popisující prvky, text a podřízené komponenty vykreslené komponenty.</span><span class="sxs-lookup"><span data-stu-id="98ea5-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="98ea5-180">Logika vykreslování může vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="98ea5-181">Příklad tohoto scénáře nastane `@someObject.PropertyName` , pokud je vyhodnocen `@someObject` , ale je `null` .</span><span class="sxs-lookup"><span data-stu-id="98ea5-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="98ea5-182">Neošetřená výjimka vyvolaná logikou vykreslování je závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="98ea5-183">Chcete-li zabránit výjimce odkazu s hodnotou null v logice vykreslování, vyhledejte `null` objekt před přístupem k jeho členům.</span><span class="sxs-lookup"><span data-stu-id="98ea5-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="98ea5-184">V následujícím příkladu `person.Address` nejsou k vlastnostem k dispozici, pokud `person.Address` je `null` :</span><span class="sxs-lookup"><span data-stu-id="98ea5-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="98ea5-185">Předchozí kód předpokládá, že `person` není `null` .</span><span class="sxs-lookup"><span data-stu-id="98ea5-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="98ea5-186">Struktura kódu často zaručuje, že objekt existuje v době, kdy je komponenta vykreslena.</span><span class="sxs-lookup"><span data-stu-id="98ea5-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="98ea5-187">V těchto případech není nutné kontrolovat `null` v logice vykreslování.</span><span class="sxs-lookup"><span data-stu-id="98ea5-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="98ea5-188">V předchozím příkladu `person` může být zaručeno, že existuje, protože `person` je vytvořena při vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="98ea5-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="98ea5-189">Obslužné rutiny událostí</span><span class="sxs-lookup"><span data-stu-id="98ea5-189">Event handlers</span></span>

<span data-ttu-id="98ea5-190">Kód na straně klienta vyvolá volání kódu jazyka C# při vytváření obslužných rutin událostí pomocí:</span><span class="sxs-lookup"><span data-stu-id="98ea5-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="98ea5-191">Další `@on...` atributy</span><span class="sxs-lookup"><span data-stu-id="98ea5-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="98ea5-192">Kód obslužné rutiny události může v těchto scénářích vyvolat neošetřenou výjimku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="98ea5-193">Pokud obslužná rutina události vyvolá neošetřenou výjimku (například databázový dotaz neuspěje), je výjimka závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="98ea5-194">Pokud aplikace volá kód, který může selhat z externích důvodů, Zachyťte výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="98ea5-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="98ea5-195">Pokud uživatelský kód neprovede soutisk a zpracuje výjimku, rozhraní zaprotokoluje výjimku a ukončí okruh.</span><span class="sxs-lookup"><span data-stu-id="98ea5-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="98ea5-196">Vyřazení součásti</span><span class="sxs-lookup"><span data-stu-id="98ea5-196">Component disposal</span></span>

<span data-ttu-id="98ea5-197">Součást může být odebrána z uživatelského rozhraní, například proto, že uživatel přešel na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="98ea5-198">Při odebrání součásti, která <xref:System.IDisposable?displayProperty=fullName> je implementována z uživatelského rozhraní, rozhraní zavolá metodu komponenty <xref:System.IDisposable.Dispose%2A> .</span><span class="sxs-lookup"><span data-stu-id="98ea5-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="98ea5-199">Pokud `Dispose` metoda komponenty vyvolá neošetřenou výjimku, je výjimka závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="98ea5-200">Pokud logika vyřazení může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="98ea5-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="98ea5-201">Další informace o vyřazení součástí najdete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="98ea5-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="98ea5-202">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="98ea5-202">JavaScript interop</span></span>

<span data-ttu-id="98ea5-203">`IJSRuntime.InvokeAsync<T>`umožňuje kódu .NET provádět asynchronní volání prostředí JavaScript Runtime v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="98ea5-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="98ea5-204">Následující podmínky se vztahují na zpracování chyb pomocí `InvokeAsync<T>` :</span><span class="sxs-lookup"><span data-stu-id="98ea5-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="98ea5-205">Pokud volání `InvokeAsync<T>` neproběhne synchronně, dojde k výjimce .NET.</span><span class="sxs-lookup"><span data-stu-id="98ea5-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="98ea5-206">Volání `InvokeAsync<T>` může selhat například proto, že zadané argumenty nemohou být serializovány.</span><span class="sxs-lookup"><span data-stu-id="98ea5-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="98ea5-207">Kód pro vývojáře musí zachytit výjimku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-207">Developer code must catch the exception.</span></span> <span data-ttu-id="98ea5-208">Pokud kód aplikace v metodě obslužné rutiny události nebo životní cyklus komponenty nezpracovává výjimku, je výsledná výjimka závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="98ea5-209">Pokud se volání `InvokeAsync<T>` asynchronně nezdařilo, rozhraní .NET se nepovede <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="98ea5-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="98ea5-210">Volání `InvokeAsync<T>` může selhat například proto, že kód na straně JavaScriptu vyvolá výjimku nebo vrátí hodnotu `Promise` , která se dokončila jako `rejected` .</span><span class="sxs-lookup"><span data-stu-id="98ea5-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="98ea5-211">Kód pro vývojáře musí zachytit výjimku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-211">Developer code must catch the exception.</span></span> <span data-ttu-id="98ea5-212">Při použití operátoru [await](/dotnet/csharp/language-reference/keywords/await) zvažte zabalení volání metody v příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="98ea5-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="98ea5-213">V opačném případě selhání kódu způsobí neošetřenou výjimku, která je závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="98ea5-214">Ve výchozím nastavení volání `InvokeAsync<T>` musí být dokončena v určitou dobu nebo jinak vyprší časový limit volání. Výchozí doba časového limitu je jedna minuta.</span><span class="sxs-lookup"><span data-stu-id="98ea5-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="98ea5-215">Časový limit chrání kód proti ztrátě v připojení k síti nebo kódu JavaScriptu, který nikdy neposílá zpět zprávu o dokončení.</span><span class="sxs-lookup"><span data-stu-id="98ea5-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="98ea5-216">Pokud vyprší časový limit volání, výsledný výsledek `Task` se nezdařil <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="98ea5-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="98ea5-217">Depeše a zpracovává výjimku pomocí protokolování.</span><span class="sxs-lookup"><span data-stu-id="98ea5-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="98ea5-218">Podobně kód JavaScriptu může iniciovat volání metod .NET, které jsou označeny [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) atributem.</span><span class="sxs-lookup"><span data-stu-id="98ea5-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="98ea5-219">Pokud tyto metody rozhraní .NET vyvolají neošetřenou výjimku:</span><span class="sxs-lookup"><span data-stu-id="98ea5-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="98ea5-220">Výjimka není považována za závažnou pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="98ea5-221">Na straně JavaScriptu `Promise` se zamítlo.</span><span class="sxs-lookup"><span data-stu-id="98ea5-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="98ea5-222">Máte možnost použít kód pro zpracování chyb na straně .NET nebo na straně JavaScriptu volání metody.</span><span class="sxs-lookup"><span data-stu-id="98ea5-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="98ea5-223">Další informace najdete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="98ea5-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor<span data-ttu-id="98ea5-224">Předvykreslování serveru</span><span class="sxs-lookup"><span data-stu-id="98ea5-224"> Server prerendering</span></span>

Blazor<span data-ttu-id="98ea5-225">komponenty mohou být předem vykresleny pomocí [pomocníka tag komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby byly vykreslené značky HTML vráceny jako součást počátečního požadavku HTTP uživatele.</span><span class="sxs-lookup"><span data-stu-id="98ea5-225"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="98ea5-226">Funguje to takto:</span><span class="sxs-lookup"><span data-stu-id="98ea5-226">This works by:</span></span>

* <span data-ttu-id="98ea5-227">Vytváření nového okruhu pro všechny předem vykreslené komponenty, které jsou součástí stejné stránky.</span><span class="sxs-lookup"><span data-stu-id="98ea5-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="98ea5-228">Generování počátečního kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="98ea5-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="98ea5-229">Okruh se zpracovává, `disconnected` dokud prohlížeč uživatele nevytvoří SignalR připojení zpátky ke stejnému serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="98ea5-230">Po navázání spojení se v okruhu obnoví interakce mezi aktivitami a kód HTML značek se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="98ea5-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="98ea5-231">Pokud nějaká komponenta vyvolá neošetřenou výjimku při předvykreslování, například během metody životního cyklu nebo v logice vykreslování:</span><span class="sxs-lookup"><span data-stu-id="98ea5-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="98ea5-232">Výjimka je pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="98ea5-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="98ea5-233">Výjimka vyvolá zásobník volání z `Component` pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="98ea5-233">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="98ea5-234">Proto se celý požadavek HTTP nezdařil, pokud není výjimka výslovně zachycena kódem vývojáře.</span><span class="sxs-lookup"><span data-stu-id="98ea5-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="98ea5-235">Za běžných okolností, když se předvykreslování nepovede, pokračuje sestavení a vykreslení komponenty nesmysl, protože pracovní komponenta se nedá vykreslit.</span><span class="sxs-lookup"><span data-stu-id="98ea5-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="98ea5-236">Chcete-li tolerovat chyby, ke kterým může dojít při předvykreslování, musí být logika zpracování chyb umístěna v rámci součásti, která může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="98ea5-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="98ea5-237">Použijte příkazy [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s ošetřením a protokolováním chyb.</span><span class="sxs-lookup"><span data-stu-id="98ea5-237">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="98ea5-238">Místo balení `Component` pomocníka značek v `try-catch` příkazu umístěte logiku zpracování chyb do komponenty vygenerované `Component` pomocníkem značek.</span><span class="sxs-lookup"><span data-stu-id="98ea5-238">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="98ea5-239">Pokročilé scénáře</span><span class="sxs-lookup"><span data-stu-id="98ea5-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="98ea5-240">Rekurzivní vykreslování</span><span class="sxs-lookup"><span data-stu-id="98ea5-240">Recursive rendering</span></span>

<span data-ttu-id="98ea5-241">Komponenty lze rekurzivně vnořovat.</span><span class="sxs-lookup"><span data-stu-id="98ea5-241">Components can be nested recursively.</span></span> <span data-ttu-id="98ea5-242">To je užitečné pro reprezentaci rekurzivních datových struktur.</span><span class="sxs-lookup"><span data-stu-id="98ea5-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="98ea5-243">Například `TreeNode` Komponenta může vykreslovat více `TreeNode` komponent pro každý podřízený uzel.</span><span class="sxs-lookup"><span data-stu-id="98ea5-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="98ea5-244">Při rekurzivním vykreslování Vyhněte se vzorům kódování, které vedou k nekonečné rekurzi:</span><span class="sxs-lookup"><span data-stu-id="98ea5-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="98ea5-245">Neprovádějte rekurzivní vykreslování struktury dat, která obsahuje cyklus.</span><span class="sxs-lookup"><span data-stu-id="98ea5-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="98ea5-246">Například nevykreslují uzel stromu, jehož podřízené položky zahrnují sám sebe.</span><span class="sxs-lookup"><span data-stu-id="98ea5-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="98ea5-247">Nevytvářejte řetěz rozložení, které obsahují cyklus.</span><span class="sxs-lookup"><span data-stu-id="98ea5-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="98ea5-248">Nevytvářejte například rozložení, jehož rozložení je samotné.</span><span class="sxs-lookup"><span data-stu-id="98ea5-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="98ea5-249">Neumožněte koncovému uživateli narušovat invariantní rekurzivních dat (pravidla) prostřednictvím škodlivých vstupů dat nebo volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="98ea5-250">Nekonečná smyčka během vykreslování:</span><span class="sxs-lookup"><span data-stu-id="98ea5-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="98ea5-251">Způsobí, že proces vykreslování bude trvale pokračovat.</span><span class="sxs-lookup"><span data-stu-id="98ea5-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="98ea5-252">Je ekvivalentní vytvořit neukončenou smyčku.</span><span class="sxs-lookup"><span data-stu-id="98ea5-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="98ea5-253">V těchto scénářích dojde k Blazor chybě ovlivněného okruhu serveru a vlákno se obvykle pokouší:</span><span class="sxs-lookup"><span data-stu-id="98ea5-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="98ea5-254">Spotřebujte tolik času procesoru povolený operačním systémem, a to po dobu neurčitou.</span><span class="sxs-lookup"><span data-stu-id="98ea5-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="98ea5-255">Spotřebovává neomezený objem paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="98ea5-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="98ea5-256">Spotřebovávání neomezené paměti je ekvivalentem k situaci, kdy neukončená smyčka přidá položky do kolekce při každé iteraci.</span><span class="sxs-lookup"><span data-stu-id="98ea5-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="98ea5-257">Aby se zabránilo nekonečným vzorům rekurzování, ujistěte se, že kód rekurzivního vykreslování obsahuje vhodné podmínky</span><span class="sxs-lookup"><span data-stu-id="98ea5-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="98ea5-258">Vlastní logika stromu vykreslování</span><span class="sxs-lookup"><span data-stu-id="98ea5-258">Custom render tree logic</span></span>

<span data-ttu-id="98ea5-259">Většina Blazor komponent je implementována jako soubory *. Razor* a je zkompilována k vytvoření logiky, která pracuje s a `RenderTreeBuilder` vykreslí výstup.</span><span class="sxs-lookup"><span data-stu-id="98ea5-259">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="98ea5-260">Vývojář může logiku ručně implementovat `RenderTreeBuilder` pomocí procedurálního kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="98ea5-260">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="98ea5-261">Další informace naleznete v tématu <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="98ea5-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="98ea5-262">Použití logiky tvůrce stromu ručního vykreslování je považováno za pokročilý a nebezpečný scénář, nedoporučuje se pro obecný vývoj komponent.</span><span class="sxs-lookup"><span data-stu-id="98ea5-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="98ea5-263">Pokud `RenderTreeBuilder` je napsán kód, vývojář musí zaručit správnost kódu.</span><span class="sxs-lookup"><span data-stu-id="98ea5-263">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="98ea5-264">Vývojář například musí zajistit, aby:</span><span class="sxs-lookup"><span data-stu-id="98ea5-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="98ea5-265">Volání `OpenElement` a `CloseElement` jsou správně vyvážená.</span><span class="sxs-lookup"><span data-stu-id="98ea5-265">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="98ea5-266">Atributy se přidávají jenom na správných místech.</span><span class="sxs-lookup"><span data-stu-id="98ea5-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="98ea5-267">Nesprávná logika tvůrce stromu ručního vykreslování může způsobit libovolné nedefinované chování, včetně havárií, zablokování serveru a ohrožení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="98ea5-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="98ea5-268">Zvažte ruční vykreslování logiky tvůrce stromu na stejné úrovni složitosti a se stejnou úrovní *nebezpečí* při psaní kódu sestavení nebo instrukcí jazyka MSIL ručně.</span><span class="sxs-lookup"><span data-stu-id="98ea5-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
