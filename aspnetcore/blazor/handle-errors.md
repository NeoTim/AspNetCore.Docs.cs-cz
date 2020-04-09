---
title: Zpracování chyb v Blazor aplikacích ASP.NET Core
author: guardrex
description: Zjistěte, Blazor jak Blazor ASP.NET Core spravuje neošetřené výjimky a jak vyvíjet aplikace, které detekují a zpracovávají chyby.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382272"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="9bf35-103">Zpracování chyb v Blazor aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9bf35-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="9bf35-104">Podle [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="9bf35-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="9bf35-105">Tento článek popisuje, jak Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.</span><span class="sxs-lookup"><span data-stu-id="9bf35-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="9bf35-106">Podrobné chyby během vývoje</span><span class="sxs-lookup"><span data-stu-id="9bf35-106">Detailed errors during development</span></span>

<span data-ttu-id="9bf35-107">Když Blazor aplikace během vývoje nefunguje správně, zobrazení podrobných informací o chybách z aplikace pomáhá při řešení potíží a řešení problému.</span><span class="sxs-lookup"><span data-stu-id="9bf35-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="9bf35-108">Když dojde k Blazor chybě, aplikace zobrazí zlatý pruh v dolní části obrazovky:</span><span class="sxs-lookup"><span data-stu-id="9bf35-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="9bf35-109">Během vývoje vás zlatá lišta přesměruje na konzolu prohlížeče, kde můžete vidět výjimku.</span><span class="sxs-lookup"><span data-stu-id="9bf35-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="9bf35-110">Ve výrobě zlatý pruh upozorní uživatele, že došlo k chybě a doporučuje aktualizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="9bf35-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="9bf35-111">Uživatelské rozhraní pro toto prostředí zpracování Blazor chyb je součástí šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="9bf35-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="9bf35-112">V Blazor aplikaci WebAssembly přizpůsobte prostředí v *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="9bf35-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="9bf35-113">V Blazor aplikaci Server přizpůsobte prostředí v souboru *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="9bf35-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="9bf35-114">Prvek `blazor-error-ui` je skryt styly Blazor obsaženými v šablonách *(wwwroot/css/site.css)* a poté zobrazen, když dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="9bf35-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="9bf35-115">Jak Blazor serverová aplikace reaguje na neošetřené výjimky</span><span class="sxs-lookup"><span data-stu-id="9bf35-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="9bf35-116">Server je stavové rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9bf35-116"> Server is a stateful framework.</span></span> <span data-ttu-id="9bf35-117">Zatímco uživatelé pracují s aplikací, udržují připojení k serveru známému jako *okruh*.</span><span class="sxs-lookup"><span data-stu-id="9bf35-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="9bf35-118">Obvod obsahuje instance aktivní součásti a mnoho dalších aspektů stavu, například:</span><span class="sxs-lookup"><span data-stu-id="9bf35-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="9bf35-119">Nejnovější vykreslený výstup komponent.</span><span class="sxs-lookup"><span data-stu-id="9bf35-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="9bf35-120">Aktuální sada delegátů zpracování událostí, které by mohly být vyvolány událostmi na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9bf35-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="9bf35-121">Pokud uživatel otevře aplikaci na více kartách prohlížeče, má více nezávislých obvodů.</span><span class="sxs-lookup"><span data-stu-id="9bf35-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="9bf35-122">zachází s většinou neošetřených výjimek jako fatální pro okruh, kde k nim dochází.</span><span class="sxs-lookup"><span data-stu-id="9bf35-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="9bf35-123">Pokud je okruh ukončen z důvodu neošetřené výjimky, uživatel může pokračovat v interakci s aplikací pouze opětovným načtením stránky a vytvořit nový okruh.</span><span class="sxs-lookup"><span data-stu-id="9bf35-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="9bf35-124">Obvody mimo ten, který je ukončen, což jsou obvody pro ostatní uživatele nebo jiné karty prohlížeče, nejsou ovlivněny.</span><span class="sxs-lookup"><span data-stu-id="9bf35-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="9bf35-125">Tento scénář je podobný desktopové&mdash;aplikaci, která havaruje, že havarovaná aplikace musí být restartována, ale ostatní aplikace nejsou ovlivněny.</span><span class="sxs-lookup"><span data-stu-id="9bf35-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="9bf35-126">Okruh je ukončen, pokud dojde k neošetřené výjimce z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="9bf35-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="9bf35-127">Neošetřená výjimka často ponechá okruh v nedefinovaném stavu.</span><span class="sxs-lookup"><span data-stu-id="9bf35-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="9bf35-128">Normální provoz aplikace nelze zaručit po neošetřené výjimce.</span><span class="sxs-lookup"><span data-stu-id="9bf35-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="9bf35-129">Pokud okruh pokračuje, mohou se v aplikaci objevit chyby zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9bf35-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="9bf35-130">Správa neošetřených výjimek v kódu vývojáře</span><span class="sxs-lookup"><span data-stu-id="9bf35-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="9bf35-131">Aby aplikace mohla pokračovat po chybě, musí mít logiku zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="9bf35-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="9bf35-132">Další části tohoto článku popisují potenciální zdroje neošetřených výjimek.</span><span class="sxs-lookup"><span data-stu-id="9bf35-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="9bf35-133">V produkčním prostředí nevykresluj zprávy o výjimkách rozhraní nebo trasování zásobníku v unovém rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9bf35-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="9bf35-134">Vykreslování zpráv výjimek nebo trasování zásobníku může:</span><span class="sxs-lookup"><span data-stu-id="9bf35-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="9bf35-135">Zpřístupňte citlivé informace koncovým uživatelům.</span><span class="sxs-lookup"><span data-stu-id="9bf35-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="9bf35-136">Pomozte uživateli se zlými úmysly odhalit slabiny v aplikaci, které mohou ohrozit zabezpečení aplikace, serveru nebo sítě.</span><span class="sxs-lookup"><span data-stu-id="9bf35-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="9bf35-137">Protokolovat chyby u trvalého zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="9bf35-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="9bf35-138">Pokud dojde k neošetřené výjimce, <xref:Microsoft.Extensions.Logging.ILogger> je výjimka zaznamenána do instancí nakonfigurovaných v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="9bf35-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="9bf35-139">Ve výchozím Blazor nastavení se aplikace přihlašují ke výstupu konzoly pomocí zprostředkovatele protokolování konzoly.</span><span class="sxs-lookup"><span data-stu-id="9bf35-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="9bf35-140">Zvažte protokolování do trvalejšího umístění s poskytovatelem, který spravuje velikost protokolu a střídání protokolů.</span><span class="sxs-lookup"><span data-stu-id="9bf35-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="9bf35-141">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="9bf35-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="9bf35-142">Během vývoje Blazor obvykle odešle úplné podrobnosti o výjimkách do konzole prohlížeče na pomoc při ladění.</span><span class="sxs-lookup"><span data-stu-id="9bf35-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="9bf35-143">V produkčním prostředí jsou podrobné chyby v konzole prohlížeče ve výchozím nastavení zakázány, což znamená, že chyby nejsou odesílány klientům, ale úplné podrobnosti výjimky jsou stále zaznamenány na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="9bf35-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="9bf35-144">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="9bf35-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="9bf35-145">Musíte se rozhodnout, které incidenty se mají protokolovat a úroveň závažnosti zaznamenaných incidentů.</span><span class="sxs-lookup"><span data-stu-id="9bf35-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="9bf35-146">Nepřátelští uživatelé mohou být schopni spustit chyby úmyslně.</span><span class="sxs-lookup"><span data-stu-id="9bf35-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="9bf35-147">Nezahlcovat například incident z chyby, kdy je v adrese URL komponenty, která zobrazuje podrobnosti o produktu, zadánneznámý `ProductId` případ.</span><span class="sxs-lookup"><span data-stu-id="9bf35-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="9bf35-148">Ne všechny chyby by měly být považovány za incidenty s vysokou závažností pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="9bf35-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="9bf35-149">Místa, kde může dojít k chybám</span><span class="sxs-lookup"><span data-stu-id="9bf35-149">Places where errors may occur</span></span>

<span data-ttu-id="9bf35-150">Architektura a kód aplikace může vyvolat neošetřené výjimky v některém z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="9bf35-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="9bf35-151">Vytvoření instance komponenty</span><span class="sxs-lookup"><span data-stu-id="9bf35-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="9bf35-152">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="9bf35-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="9bf35-153">Vykreslovací logika</span><span class="sxs-lookup"><span data-stu-id="9bf35-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="9bf35-154">Obslužné rutiny událostí</span><span class="sxs-lookup"><span data-stu-id="9bf35-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="9bf35-155">Likvidace součástí</span><span class="sxs-lookup"><span data-stu-id="9bf35-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="9bf35-156">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="9bf35-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="9bf35-157">[BlazorVykreslení serveru](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="9bf35-157">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="9bf35-158">Předchozí neošetřené výjimky jsou popsány v následujících částech tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="9bf35-158">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="9bf35-159">Vytvoření instance komponenty</span><span class="sxs-lookup"><span data-stu-id="9bf35-159">Component instantiation</span></span>

<span data-ttu-id="9bf35-160">Když Blazor vytvoříte instanci komponenty:</span><span class="sxs-lookup"><span data-stu-id="9bf35-160">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="9bf35-161">Konstruktor komponenty je vyvolán.</span><span class="sxs-lookup"><span data-stu-id="9bf35-161">The component's constructor is invoked.</span></span>
* <span data-ttu-id="9bf35-162">Konstruktory všech služeb di singleton DI dodávané konstruktoru [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) komponenty [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) prostřednictvím směrnice nebo atributu jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="9bf35-162">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="9bf35-163">Server Blazor okruh selže, když všechny provedené konstruktor `[Inject]` nebo setter pro libovolnou vlastnost vyvolá neošetřené výjimky.</span><span class="sxs-lookup"><span data-stu-id="9bf35-163">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="9bf35-164">Výjimka je závažná, protože rozhraní framework nelze vytvořit instanci součásti.</span><span class="sxs-lookup"><span data-stu-id="9bf35-164">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="9bf35-165">Pokud logika konstruktoru může vyvolat výjimky, aplikace by měla soutisk výjimky pomocí [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) prohlášení s zpracování chyb a protokolování.</span><span class="sxs-lookup"><span data-stu-id="9bf35-165">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="9bf35-166">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="9bf35-166">Lifecycle methods</span></span>

<span data-ttu-id="9bf35-167">Během životnosti komponenty Blazor vyvolá následující [metody životního cyklu](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="9bf35-167">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="9bf35-168">Pokud jakákoli metoda životního cyklu vyvolá výjimku synchronně nebo asynchronně, výjimka Blazor je závažná pro serverový obvod.</span><span class="sxs-lookup"><span data-stu-id="9bf35-168">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="9bf35-169">Pro součásti řešit chyby v metodách životního cyklu, přidejte logiku zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="9bf35-169">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="9bf35-170">V následujícím příkladu, kde `OnParametersSetAsync` volá metodu k získání produktu:</span><span class="sxs-lookup"><span data-stu-id="9bf35-170">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="9bf35-171">Výjimka vyvolána `ProductRepository.GetProductByIdAsync` v metodě `try-catch` je zpracována příkazem.</span><span class="sxs-lookup"><span data-stu-id="9bf35-171">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="9bf35-172">Při `catch` spuštění bloku:</span><span class="sxs-lookup"><span data-stu-id="9bf35-172">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="9bf35-173">`loadFailed`je nastavena na `true`, která slouží k zobrazení chybové zprávy uživateli.</span><span class="sxs-lookup"><span data-stu-id="9bf35-173">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="9bf35-174">Chyba je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="9bf35-174">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="9bf35-175">Vykreslovací logika</span><span class="sxs-lookup"><span data-stu-id="9bf35-175">Rendering logic</span></span>

<span data-ttu-id="9bf35-176">Deklarativní značky v souboru `.razor` komponenty jsou `BuildRenderTree`zkompilovány do metody C# s názvem .</span><span class="sxs-lookup"><span data-stu-id="9bf35-176">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="9bf35-177">Když komponenta vykreslí, `BuildRenderTree` spustí a vytvoří datovou strukturu popisující prvky, text a podřízené součásti vykreslené součásti.</span><span class="sxs-lookup"><span data-stu-id="9bf35-177">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="9bf35-178">Vykreslovací logika může vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="9bf35-178">Rendering logic can throw an exception.</span></span> <span data-ttu-id="9bf35-179">Příklad tohoto scénáře nastane, `@someObject.PropertyName` když `@someObject` `null`je vyhodnocena, ale je .</span><span class="sxs-lookup"><span data-stu-id="9bf35-179">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="9bf35-180">Neošetřená výjimka vyzdvižená logikou vykreslování je pro obvod serveru závažná. Blazor</span><span class="sxs-lookup"><span data-stu-id="9bf35-180">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="9bf35-181">Chcete-li zabránit výjimku nulového odkazu `null` v vykreslovací logice, zkontrolujte objekt před přístupem k jeho členům.</span><span class="sxs-lookup"><span data-stu-id="9bf35-181">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="9bf35-182">V následujícím příkladu nejsou vlastnosti `person.Address` `person.Address` přístupné, pokud je `null`:</span><span class="sxs-lookup"><span data-stu-id="9bf35-182">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="9bf35-183">Předchozí kód předpokládá, `person` že není `null`.</span><span class="sxs-lookup"><span data-stu-id="9bf35-183">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="9bf35-184">Struktura kódu často zaručuje, že objekt existuje v době vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="9bf35-184">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="9bf35-185">V těchto případech není nutné zkontrolovat `null` v logice vykreslování.</span><span class="sxs-lookup"><span data-stu-id="9bf35-185">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="9bf35-186">V předchozím příkladu může být zaručeno, že existuje, `person` protože `person` je vytvořen při vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="9bf35-186">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="9bf35-187">Obslužné rutiny událostí</span><span class="sxs-lookup"><span data-stu-id="9bf35-187">Event handlers</span></span>

<span data-ttu-id="9bf35-188">Kód na straně klienta aktivuje vyvolání kódu jazyka C# při vytváření obslužných rutin událostí pomocí:</span><span class="sxs-lookup"><span data-stu-id="9bf35-188">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="9bf35-189">Další `@on...` atributy</span><span class="sxs-lookup"><span data-stu-id="9bf35-189">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="9bf35-190">Kód obslužné rutiny události může vyvolat neošetřenou výjimku v těchto scénářích.</span><span class="sxs-lookup"><span data-stu-id="9bf35-190">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="9bf35-191">Pokud obslužná rutina události vyvolá neošetřenou výjimku (například Blazor databázový dotaz selže), výjimka je závažná pro serverový obvod.</span><span class="sxs-lookup"><span data-stu-id="9bf35-191">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="9bf35-192">Pokud aplikace volá kód, který může selhat z externích důvodů, soutisk výjimky pomocí [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) prohlášení s zpracování chyb a protokolování.</span><span class="sxs-lookup"><span data-stu-id="9bf35-192">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="9bf35-193">Pokud uživatelský kód není soutisk a zpracování výjimky, rozhraní protokoluje výjimku a ukončí okruh.</span><span class="sxs-lookup"><span data-stu-id="9bf35-193">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="9bf35-194">Likvidace součástí</span><span class="sxs-lookup"><span data-stu-id="9bf35-194">Component disposal</span></span>

<span data-ttu-id="9bf35-195">Komponenta může být odebrána z uživatelského rozhraní, například proto, že uživatel přejde na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="9bf35-195">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="9bf35-196">Když je komponenta, která implementuje, <xref:System.IDisposable?displayProperty=fullName> odebrána z <xref:System.IDisposable.Dispose*> ui, rozhraní nazývá metodu komponenty.</span><span class="sxs-lookup"><span data-stu-id="9bf35-196">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="9bf35-197">Pokud `Dispose` metoda komponenty vyvolá neošetřenou výjimku, výjimka Blazor je závažná pro obvod serveru.</span><span class="sxs-lookup"><span data-stu-id="9bf35-197">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="9bf35-198">Pokud logika likvidace může vyvolat výjimky, aplikace by měla soutisk výjimky pomocí [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) prohlášení s zpracování chyb a protokolování.</span><span class="sxs-lookup"><span data-stu-id="9bf35-198">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="9bf35-199">Další informace o likvidaci <xref:blazor/lifecycle#component-disposal-with-idisposable>součástí naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="9bf35-199">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="9bf35-200">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="9bf35-200">JavaScript interop</span></span>

<span data-ttu-id="9bf35-201">`IJSRuntime.InvokeAsync<T>`umožňuje kódu .NET provádět asynchronní volání modulu runtime javascriptu v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="9bf35-201">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="9bf35-202">Následující podmínky platí pro `InvokeAsync<T>`zpracování chyb s :</span><span class="sxs-lookup"><span data-stu-id="9bf35-202">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="9bf35-203">Pokud volání `InvokeAsync<T>` nezdaří synchronně, dojde k výjimce .NET.</span><span class="sxs-lookup"><span data-stu-id="9bf35-203">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="9bf35-204">Volání může `InvokeAsync<T>` selhat, například protože zadané argumenty nelze serializovat.</span><span class="sxs-lookup"><span data-stu-id="9bf35-204">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="9bf35-205">Kód vývojáře musí zachytit výjimku.</span><span class="sxs-lookup"><span data-stu-id="9bf35-205">Developer code must catch the exception.</span></span> <span data-ttu-id="9bf35-206">Pokud kód aplikace v obslužné rutině události nebo metodě životního cyklu Blazor komponenty nezpracovává výjimku, výsledná výjimka je pro obvod serveru závažná.</span><span class="sxs-lookup"><span data-stu-id="9bf35-206">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="9bf35-207">Pokud volání `InvokeAsync<T>` nezdaří asynchronně, .NET <xref:System.Threading.Tasks.Task> selže.</span><span class="sxs-lookup"><span data-stu-id="9bf35-207">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="9bf35-208">Volání může `InvokeAsync<T>` selhat, například proto, že kód na straně `Promise` JavaScript `rejected`vyvolá výjimku nebo vrátí, který byl dokončen jako .</span><span class="sxs-lookup"><span data-stu-id="9bf35-208">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="9bf35-209">Kód vývojáře musí zachytit výjimku.</span><span class="sxs-lookup"><span data-stu-id="9bf35-209">Developer code must catch the exception.</span></span> <span data-ttu-id="9bf35-210">Pokud používáte operátor [await,](/dotnet/csharp/language-reference/keywords/await) zvažte zabalení volání metody v příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s chybovým zpracováním a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="9bf35-210">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="9bf35-211">V opačném případě selhání kódu má za následek neošetřené výjimky, která je závažná pro server obvodu. Blazor</span><span class="sxs-lookup"><span data-stu-id="9bf35-211">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="9bf35-212">Ve výchozím nastavení `InvokeAsync<T>` musí být volání dokončeno v určité lhůtě, jinak je čas volání out. Výchozí časový limit je jedna minuta.</span><span class="sxs-lookup"><span data-stu-id="9bf35-212">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="9bf35-213">Časový čas chrání kód před ztrátou připojení k síti nebo kódu Jazyka JavaScript, který nikdy neodešle zpět zprávu o dokončení.</span><span class="sxs-lookup"><span data-stu-id="9bf35-213">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="9bf35-214">Pokud je časový čas volání, `Task` výsledné <xref:System.OperationCanceledException>selže s .</span><span class="sxs-lookup"><span data-stu-id="9bf35-214">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="9bf35-215">Soutisk a zpracování výjimky s protokolováním.</span><span class="sxs-lookup"><span data-stu-id="9bf35-215">Trap and process the exception with logging.</span></span>

<span data-ttu-id="9bf35-216">Podobně javascriptový kód může iniciovat volání [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) metod .NET označených atributem.</span><span class="sxs-lookup"><span data-stu-id="9bf35-216">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="9bf35-217">Pokud tyto metody .NET vyvolat neošetřené výjimky:</span><span class="sxs-lookup"><span data-stu-id="9bf35-217">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="9bf35-218">Výjimka není považována za závažnou Blazor pro serverový okruh.</span><span class="sxs-lookup"><span data-stu-id="9bf35-218">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="9bf35-219">JavaScript-strana `Promise` je odmítnuta.</span><span class="sxs-lookup"><span data-stu-id="9bf35-219">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="9bf35-220">Máte možnost použít kód zpracování chyb na straně .NET nebo javascriptové straně volání metody.</span><span class="sxs-lookup"><span data-stu-id="9bf35-220">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="9bf35-221">Další informace najdete v těchto článcích:</span><span class="sxs-lookup"><span data-stu-id="9bf35-221">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="9bf35-222">Předběžné vykreslování serveru</span><span class="sxs-lookup"><span data-stu-id="9bf35-222"> Server prerendering</span></span>

Blazor<span data-ttu-id="9bf35-223">součásti mohou být předem vykresleny pomocí [pomocníka pro značku komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) tak, aby jejich vykreslené značky HTML byly vráceny jako součást počátečního požadavku HTTP uživatele.</span><span class="sxs-lookup"><span data-stu-id="9bf35-223"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="9bf35-224">To funguje takto:</span><span class="sxs-lookup"><span data-stu-id="9bf35-224">This works by:</span></span>

* <span data-ttu-id="9bf35-225">Vytvoření nového okruhu pro všechny předvykreslované součásti, které jsou součástí stejné stránky.</span><span class="sxs-lookup"><span data-stu-id="9bf35-225">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="9bf35-226">Generování počátečního KÓDU HTML.</span><span class="sxs-lookup"><span data-stu-id="9bf35-226">Generating the initial HTML.</span></span>
* <span data-ttu-id="9bf35-227">Zacházet s `disconnected` okruhem jako do prohlížeče SignalR uživatele naváže připojení zpět na stejný server.</span><span class="sxs-lookup"><span data-stu-id="9bf35-227">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="9bf35-228">Po navázání připojení je obnovena interaktivita na okruhu a aktualizovány značky HTML komponent.</span><span class="sxs-lookup"><span data-stu-id="9bf35-228">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="9bf35-229">Pokud některé součásti vyvolá neošetřené výjimky během prerendering, například během metody životního cyklu nebo v logice vykreslování:</span><span class="sxs-lookup"><span data-stu-id="9bf35-229">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="9bf35-230">Výjimka je fatální pro obvod.</span><span class="sxs-lookup"><span data-stu-id="9bf35-230">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="9bf35-231">Výjimka je vyvolána zásobníku `Component` volání z pomocníka tageru.</span><span class="sxs-lookup"><span data-stu-id="9bf35-231">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="9bf35-232">Proto se nezdaří celý požadavek HTTP, pokud výjimka je explicitně zachycena kódem vývojáře.</span><span class="sxs-lookup"><span data-stu-id="9bf35-232">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="9bf35-233">Za normálních okolností při předběžném vykreslování selže, pokračování v sestavení a vykreslení komponenty nedává smysl, protože pracovní součást nelze vykreslit.</span><span class="sxs-lookup"><span data-stu-id="9bf35-233">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="9bf35-234">Chcete-li tolerovat chyby, které mohou nastat během předběžného vykreslování, musí být logika zpracování chyb umístěna uvnitř součásti, která může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="9bf35-234">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="9bf35-235">Pomocí příkazů [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) se zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="9bf35-235">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="9bf35-236">Místo zabalení `Component` pomocníka tagu do příkazu `try-catch` umístěte logiku zpracování `Component` chyb do komponenty vykreslené pomocníkem značek.</span><span class="sxs-lookup"><span data-stu-id="9bf35-236">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="9bf35-237">Pokročilé scénáře</span><span class="sxs-lookup"><span data-stu-id="9bf35-237">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="9bf35-238">Rekurzivní vykreslování</span><span class="sxs-lookup"><span data-stu-id="9bf35-238">Recursive rendering</span></span>

<span data-ttu-id="9bf35-239">Součásti mohou být vnořeny rekurzivně.</span><span class="sxs-lookup"><span data-stu-id="9bf35-239">Components can be nested recursively.</span></span> <span data-ttu-id="9bf35-240">To je užitečné pro rekurzivní datové struktury.</span><span class="sxs-lookup"><span data-stu-id="9bf35-240">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="9bf35-241">Komponenta `TreeNode` může například `TreeNode` vykreslit více součástí pro každou podřízené součásti uzlu.</span><span class="sxs-lookup"><span data-stu-id="9bf35-241">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="9bf35-242">Při rekurzivním vykreslování se vyhněte vzorcům kódování, které vedou k nekonečné rekurzi:</span><span class="sxs-lookup"><span data-stu-id="9bf35-242">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="9bf35-243">Neobnovujte datovou strukturu, která obsahuje cyklus.</span><span class="sxs-lookup"><span data-stu-id="9bf35-243">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="9bf35-244">Například nevykreslovat uzel stromu, jehož podřízené položky zahrnuje sám.</span><span class="sxs-lookup"><span data-stu-id="9bf35-244">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="9bf35-245">Nevytvářejte řetězec rozvržení, které obsahují cyklus.</span><span class="sxs-lookup"><span data-stu-id="9bf35-245">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="9bf35-246">Nevytvářejte například rozložení, jehož rozložení je samo o sobě.</span><span class="sxs-lookup"><span data-stu-id="9bf35-246">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="9bf35-247">Nedovolte koncovému uživateli porušovat rekurzní invarianty (pravidla) prostřednictvím zadávání škodlivých dat nebo volání interop javascriptu.</span><span class="sxs-lookup"><span data-stu-id="9bf35-247">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="9bf35-248">Nekonečné smyčky při vykreslování:</span><span class="sxs-lookup"><span data-stu-id="9bf35-248">Infinite loops during rendering:</span></span>

* <span data-ttu-id="9bf35-249">Způsobí, že proces vykreslování bude pokračovat navždy.</span><span class="sxs-lookup"><span data-stu-id="9bf35-249">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="9bf35-250">Je ekvivalentní k vytvoření neukončené smyčky.</span><span class="sxs-lookup"><span data-stu-id="9bf35-250">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="9bf35-251">V těchto scénářích Blazor se nezdaří ohrožený server obvod u tváře a podproces se obvykle pokusí:</span><span class="sxs-lookup"><span data-stu-id="9bf35-251">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="9bf35-252">Spotřebovávat tolik času procesoru, kolik je povoleno operačním systémem, po neomezenou dobu.</span><span class="sxs-lookup"><span data-stu-id="9bf35-252">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="9bf35-253">Spotřebovávat neomezené množství paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="9bf35-253">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="9bf35-254">Spotřebovávající neomezenou paměť je ekvivalentní scénář, kde neukončené smyčky přidá položky do kolekce na každé iteraci.</span><span class="sxs-lookup"><span data-stu-id="9bf35-254">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="9bf35-255">Chcete-li se vyhnout nekonečné rekurze vzory, ujistěte se, že rekurzivní vykreslování kód obsahuje vhodné podmínky zastavení.</span><span class="sxs-lookup"><span data-stu-id="9bf35-255">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="9bf35-256">Vlastní logika stromu vykreslení</span><span class="sxs-lookup"><span data-stu-id="9bf35-256">Custom render tree logic</span></span>

<span data-ttu-id="9bf35-257">Většina Blazor komponent jsou implementovány jako *.razor* soubory a jsou `RenderTreeBuilder` kompilovány k vytvoření logiky, která pracuje na vykreslení jejich výstup.</span><span class="sxs-lookup"><span data-stu-id="9bf35-257">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="9bf35-258">Vývojář může ručně `RenderTreeBuilder` implementovat logiku pomocí procedurálního kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="9bf35-258">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="9bf35-259">Další informace naleznete v tématu <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="9bf35-259">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="9bf35-260">Použití ruční logiky vytváření vykreslování stromu je považován za pokročilý a nebezpečný scénář, nedoporučuje se pro vývoj obecných součástí.</span><span class="sxs-lookup"><span data-stu-id="9bf35-260">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="9bf35-261">Pokud `RenderTreeBuilder` je kód zapsán, vývojář musí zaručit správnost kódu.</span><span class="sxs-lookup"><span data-stu-id="9bf35-261">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="9bf35-262">Vývojář musí například zajistit, aby:</span><span class="sxs-lookup"><span data-stu-id="9bf35-262">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="9bf35-263">Volání `OpenElement` a `CloseElement` jsou správně vyvážené.</span><span class="sxs-lookup"><span data-stu-id="9bf35-263">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="9bf35-264">Atributy jsou přidávány pouze na správných místech.</span><span class="sxs-lookup"><span data-stu-id="9bf35-264">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="9bf35-265">Nesprávné ruční vykreslení stromu tvůrce logiky může způsobit libovolné nedefinované chování, včetně selhání, server zablokuje a slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="9bf35-265">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="9bf35-266">Zvažte ruční vykreslovat strom stavitel logiku na stejné úrovni složitosti a se stejnou úrovní *nebezpečí* jako psaní kódu sestavení nebo msil pokyny ručně.</span><span class="sxs-lookup"><span data-stu-id="9bf35-266">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
