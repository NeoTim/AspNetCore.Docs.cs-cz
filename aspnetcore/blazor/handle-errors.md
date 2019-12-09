---
title: Zpracování chyb v aplikacích ASP.NET Core Blazor
author: guardrex
description: Zjistěte, jak ASP.NET Core Blazor jak Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: d73eb9a0dd0ec7a4bec4b7b9aeaaa4a9ee888bce
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943703"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="47d90-103">Zpracování chyb v aplikacích ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="47d90-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="47d90-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="47d90-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="47d90-105">Tento článek popisuje, jak Blazor spravuje neošetřené výjimky a jak vyvíjet aplikace, které zjišťují a zpracovávají chyby.</span><span class="sxs-lookup"><span data-stu-id="47d90-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="47d90-106">Podrobné chyby při vývoji</span><span class="sxs-lookup"><span data-stu-id="47d90-106">Detailed errors during development</span></span>

<span data-ttu-id="47d90-107">Když aplikace Blazor během vývoje nefunguje správně, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace.</span><span class="sxs-lookup"><span data-stu-id="47d90-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="47d90-108">Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky zlatý pruh:</span><span class="sxs-lookup"><span data-stu-id="47d90-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="47d90-109">Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="47d90-110">V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="47d90-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="47d90-111">Uživatelské rozhraní tohoto prostředí pro zpracování chyb je součástí Blazor šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="47d90-111">The UI for this error handling experience is part of the Blazor project templates.</span></span> <span data-ttu-id="47d90-112">V Blazor aplikaci WebAssembly, přizpůsobte si prostředí v souboru *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="47d90-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="47d90-113">V aplikaci Blazor serveru upravte prostředí v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="47d90-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="47d90-114">Element `blazor-error-ui` je skrytý styly zahrnutými v šablonách Blazor a pak se zobrazí, když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="47d90-114">The `blazor-error-ui` element is hidden by the styles included with the Blazor templates and then shown when an error occurs.</span></span>

::: moniker-end

## <a name="how-the-opno-locblazor-framework-reacts-to-unhandled-exceptions"></a><span data-ttu-id="47d90-115">Jak Blazor Framework reaguje na neošetřené výjimky</span><span class="sxs-lookup"><span data-stu-id="47d90-115">How the Blazor framework reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="47d90-116"> Server je stavový systém.</span><span class="sxs-lookup"><span data-stu-id="47d90-116"> Server is a stateful framework.</span></span> <span data-ttu-id="47d90-117">I když uživatelé pracují s aplikací, udržují připojení k serveru známému jako *okruh*.</span><span class="sxs-lookup"><span data-stu-id="47d90-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="47d90-118">Okruh obsahuje aktivní instance komponent a mnoho dalších aspektů stavu, například:</span><span class="sxs-lookup"><span data-stu-id="47d90-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="47d90-119">Poslední Vykreslený výstup komponent.</span><span class="sxs-lookup"><span data-stu-id="47d90-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="47d90-120">Aktuální sada delegátů zpracovávajících události, které mohou být aktivovány událostmi na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="47d90-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="47d90-121">Pokud uživatel otevře aplikaci na více kartách prohlížeče, má několik nezávislých okruhů.</span><span class="sxs-lookup"><span data-stu-id="47d90-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="47d90-122"> považuje většinu neošetřených výjimek za závažné v okruhu, ve kterém se vyskytují.</span><span class="sxs-lookup"><span data-stu-id="47d90-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="47d90-123">Pokud je okruh ukončen z důvodu neošetřené výjimky, uživatel může pokračovat v interakci s aplikací pouze tak, že znovu načte stránku, aby vytvořila nový okruh.</span><span class="sxs-lookup"><span data-stu-id="47d90-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="47d90-124">Nejsou ovlivněny okruhy mimo tu, která je ukončená, což jsou okruhy pro ostatní uživatele nebo jiné karty prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="47d90-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="47d90-125">Tento scénář je podobný aplikaci klasické pracovní plochy, která selže,&mdash;je nutné restartovat aplikaci, ale ostatní aplikace to neovlivnily.</span><span class="sxs-lookup"><span data-stu-id="47d90-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="47d90-126">Okruh se ukončí, když dojde k neošetřené výjimce z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="47d90-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="47d90-127">Neošetřená výjimka často opustí okruh v nedefinovaném stavu.</span><span class="sxs-lookup"><span data-stu-id="47d90-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="47d90-128">Po neošetřené výjimce nelze zaručit normální operaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="47d90-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="47d90-129">V případě, že okruh pokračuje, mohou se v aplikaci zobrazit slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="47d90-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="47d90-130">Správa neošetřených výjimek v kódu pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="47d90-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="47d90-131">Aby aplikace pokračovala i po chybě, aplikace musí mít logiku zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="47d90-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="47d90-132">Pozdější části tohoto článku popisují možné zdroje neošetřených výjimek.</span><span class="sxs-lookup"><span data-stu-id="47d90-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="47d90-133">V produkčním prostředí nevykresluje zprávy o výjimkách rozhraní nebo trasování zásobníku v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="47d90-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="47d90-134">Vykreslování zpráv výjimek nebo trasování zásobníku může:</span><span class="sxs-lookup"><span data-stu-id="47d90-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="47d90-135">Vyzradit citlivé informace koncovým uživatelům.</span><span class="sxs-lookup"><span data-stu-id="47d90-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="47d90-136">Pomáhat uživateli se zlými úmysly zjišťovat slabiny v aplikaci, která může ohrozit zabezpečení aplikace, serveru nebo sítě.</span><span class="sxs-lookup"><span data-stu-id="47d90-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="47d90-137">Protokolovat chyby trvalého poskytovatele</span><span class="sxs-lookup"><span data-stu-id="47d90-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="47d90-138">Pokud dojde k neošetřené výjimce, zaznamená se výjimka do <xref:Microsoft.Extensions.Logging.ILogger> instancí nakonfigurovaných v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="47d90-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="47d90-139">Ve výchozím nastavení se aplikace Blazor do výstupu konzoly přihlásí pomocí zprostředkovatele protokolování konzoly.</span><span class="sxs-lookup"><span data-stu-id="47d90-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="47d90-140">Zvažte možnost protokolování do trvalého umístění u poskytovatele, který spravuje velikost protokolu a rotaci protokolů.</span><span class="sxs-lookup"><span data-stu-id="47d90-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="47d90-141">Další informace najdete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="47d90-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="47d90-142">Během vývoje Blazor obvykle odesílá úplné podrobnosti o výjimkách do konzoly prohlížeče, aby bylo možné v ladění.</span><span class="sxs-lookup"><span data-stu-id="47d90-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="47d90-143">V produkčním prostředí jsou podrobné chyby v konzole prohlížeče ve výchozím nastavení zakázané, což znamená, že se do klientů neodesílají chyby, ale úplné podrobnosti o výjimce se pořád protokolují na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="47d90-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="47d90-144">Další informace najdete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="47d90-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="47d90-145">Musíte se rozhodnout, které incidenty se mají protokolovat, a úroveň závažnosti protokolovaných incidentů.</span><span class="sxs-lookup"><span data-stu-id="47d90-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="47d90-146">Nepřátelským uživatelům může být možné aktivovat chyby záměrně.</span><span class="sxs-lookup"><span data-stu-id="47d90-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="47d90-147">Například Neprotokolujte incident z chyby, kde je v adrese URL komponenty, která zobrazuje podrobnosti o produktu, zadán neznámý `ProductId`.</span><span class="sxs-lookup"><span data-stu-id="47d90-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="47d90-148">Ne všechny chyby by se měly považovat za incidenty s vysokou závažností pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="47d90-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="47d90-149">Místa, kde může dojít k chybám</span><span class="sxs-lookup"><span data-stu-id="47d90-149">Places where errors may occur</span></span>

<span data-ttu-id="47d90-150">Rozhraní a kód aplikace mohou aktivovat neošetřené výjimky v žádném z následujících umístění:</span><span class="sxs-lookup"><span data-stu-id="47d90-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="47d90-151">Instance komponenty</span><span class="sxs-lookup"><span data-stu-id="47d90-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="47d90-152">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="47d90-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="47d90-153">Logika vykreslování</span><span class="sxs-lookup"><span data-stu-id="47d90-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="47d90-154">Obslužné rutiny událostí</span><span class="sxs-lookup"><span data-stu-id="47d90-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="47d90-155">Vyřazení součásti</span><span class="sxs-lookup"><span data-stu-id="47d90-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="47d90-156">Zprostředkovatel komunikace s JavaScriptem</span><span class="sxs-lookup"><span data-stu-id="47d90-156">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="47d90-157">Obslužné rutiny okruhu</span><span class="sxs-lookup"><span data-stu-id="47d90-157">Circuit handlers</span></span>](#circuit-handlers)
* [<span data-ttu-id="47d90-158">Vyřazení okruhů</span><span class="sxs-lookup"><span data-stu-id="47d90-158">Circuit disposal</span></span>](#circuit-disposal)
* [<span data-ttu-id="47d90-159">Předvykreslování</span><span class="sxs-lookup"><span data-stu-id="47d90-159">Prerendering</span></span>](#prerendering)

<span data-ttu-id="47d90-160">Předchozí neošetřené výjimky jsou popsány v následujících částech tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="47d90-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="47d90-161">Instance komponenty</span><span class="sxs-lookup"><span data-stu-id="47d90-161">Component instantiation</span></span>

<span data-ttu-id="47d90-162">Když Blazor vytvoří instanci komponenty:</span><span class="sxs-lookup"><span data-stu-id="47d90-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="47d90-163">Je vyvolán konstruktor součásti.</span><span class="sxs-lookup"><span data-stu-id="47d90-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="47d90-164">Konstruktory jakékoliv nejednoznačné služby DI Services dodávané konstruktoru komponenty prostřednictvím direktivy [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) nebo atributu [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) jsou vyvolány.</span><span class="sxs-lookup"><span data-stu-id="47d90-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span> 

<span data-ttu-id="47d90-165">Okruh se nezdařil, pokud kterýkoli z spouštěného konstruktoru nebo setter pro jakoukoliv `[Inject]` vlastnost vyvolá neošetřenou výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-165">A circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="47d90-166">Výjimka je závažná, protože architektura nemůže vytvořit instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="47d90-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="47d90-167">Pokud logika konstruktoru může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="47d90-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="47d90-168">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="47d90-168">Lifecycle methods</span></span>

<span data-ttu-id="47d90-169">Během životnosti komponenty Blazor vyvolá následující [metody životního cyklu](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="47d90-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="47d90-170">Pokud jakákoli metoda životního cyklu vyvolá výjimku synchronně nebo asynchronně, je výjimka pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to the circuit.</span></span> <span data-ttu-id="47d90-171">Pro součásti, které se zabývat chybami v metodách životního cyklu, přidejte logiku zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="47d90-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="47d90-172">V následujícím příkladu, kde `OnParametersSetAsync` volá metodu pro získání produktu:</span><span class="sxs-lookup"><span data-stu-id="47d90-172">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="47d90-173">Výjimka vyvolaná v metodě `ProductRepository.GetProductByIdAsync` je zpracována pomocí příkazu `try-catch`.</span><span class="sxs-lookup"><span data-stu-id="47d90-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="47d90-174">Po spuštění bloku `catch`:</span><span class="sxs-lookup"><span data-stu-id="47d90-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="47d90-175">`loadFailed` je nastavená na `true`, která se používá k zobrazení chybové zprávy uživateli.</span><span class="sxs-lookup"><span data-stu-id="47d90-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="47d90-176">Chyba je zaznamenána do protokolu.</span><span class="sxs-lookup"><span data-stu-id="47d90-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="47d90-177">Logika vykreslování</span><span class="sxs-lookup"><span data-stu-id="47d90-177">Rendering logic</span></span>

<span data-ttu-id="47d90-178">Deklarativní označení v souboru komponenty `.razor` je zkompilováno do C# metody nazvané `BuildRenderTree`.</span><span class="sxs-lookup"><span data-stu-id="47d90-178">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="47d90-179">Když se komponenta vykreslí, `BuildRenderTree` spustí a vytvoří strukturu dat popisující prvky, text a podřízené komponenty vykreslené komponenty.</span><span class="sxs-lookup"><span data-stu-id="47d90-179">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="47d90-180">Logika vykreslování může vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="47d90-181">K tomuto scénáři dochází například při vyhodnocování `@someObject.PropertyName`, ale `@someObject` je `null`.</span><span class="sxs-lookup"><span data-stu-id="47d90-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="47d90-182">Neošetřená výjimka vyvolaná logikou vykreslování je pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-182">An unhandled exception thrown by rendering logic is fatal to the circuit.</span></span>

<span data-ttu-id="47d90-183">Chcete-li zabránit výjimce odkazu s hodnotou null v logice vykreslování, vyhledejte objekt `null` před přístupem k jeho členům.</span><span class="sxs-lookup"><span data-stu-id="47d90-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="47d90-184">V následujícím příkladu nejsou k dispozici `person.Address` vlastnosti, pokud je `person.Address` `null`:</span><span class="sxs-lookup"><span data-stu-id="47d90-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="47d90-185">Předchozí kód předpokládá, že `person` není `null`.</span><span class="sxs-lookup"><span data-stu-id="47d90-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="47d90-186">Struktura kódu často zaručuje, že objekt existuje v době, kdy je komponenta vykreslena.</span><span class="sxs-lookup"><span data-stu-id="47d90-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="47d90-187">V těchto případech není nutné kontrolovat `null` logikou vykreslování.</span><span class="sxs-lookup"><span data-stu-id="47d90-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="47d90-188">V předchozím příkladu může být zaručeno, že `person` existuje, protože `person` se vytvoří při vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="47d90-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="47d90-189">Obslužné rutiny událostí</span><span class="sxs-lookup"><span data-stu-id="47d90-189">Event handlers</span></span>

<span data-ttu-id="47d90-190">Kód na straně klienta vyvolá volání C# kódu při vytváření obslužných rutin událostí pomocí:</span><span class="sxs-lookup"><span data-stu-id="47d90-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="47d90-191">Jiné atributy `@on...`</span><span class="sxs-lookup"><span data-stu-id="47d90-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="47d90-192">Kód obslužné rutiny události může v těchto scénářích vyvolat neošetřenou výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="47d90-193">Pokud obslužná rutina události vyvolá neošetřenou výjimku (například databázový dotaz neuspěje), je výjimka pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to the circuit.</span></span> <span data-ttu-id="47d90-194">Pokud aplikace volá kód, který může selhat z externích důvodů, Zachyťte výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="47d90-194">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="47d90-195">Pokud uživatelský kód neprovede soutisk a zpracuje výjimku, rozhraní zaprotokoluje výjimku a ukončí okruh.</span><span class="sxs-lookup"><span data-stu-id="47d90-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="47d90-196">Vyřazení součásti</span><span class="sxs-lookup"><span data-stu-id="47d90-196">Component disposal</span></span>

<span data-ttu-id="47d90-197">Součást může být odebrána z uživatelského rozhraní, například proto, že uživatel přešel na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="47d90-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="47d90-198">Když je komponenta, která implementuje <xref:System.IDisposable?displayProperty=fullName>, odebrána z uživatelského rozhraní, rozhraní zavolá metodu <xref:System.IDisposable.Dispose*> součásti.</span><span class="sxs-lookup"><span data-stu-id="47d90-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span> 

<span data-ttu-id="47d90-199">Pokud metoda `Dispose` komponenty vyvolá neošetřenou výjimku, je výjimka pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="47d90-200">Pokud logika vyřazení může vyvolat výjimky, aplikace by měla zachytit výjimky pomocí příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="47d90-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="47d90-201">Další informace o vyřazení součástí najdete v tématu <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="47d90-201">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="47d90-202">Interoperabilita JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="47d90-202">JavaScript interop</span></span>

<span data-ttu-id="47d90-203">`IJSRuntime.InvokeAsync<T>` umožňuje kódu .NET provádět asynchronní volání prostředí JavaScript Runtime v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="47d90-203">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="47d90-204">Při zpracování chyb pomocí `InvokeAsync<T>`platí následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="47d90-204">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="47d90-205">Pokud se volání `InvokeAsync<T>` nezdařila synchronně, dojde k výjimce .NET.</span><span class="sxs-lookup"><span data-stu-id="47d90-205">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="47d90-206">Volání `InvokeAsync<T>` může selhat, například proto, že zadané argumenty nemohou být serializovány.</span><span class="sxs-lookup"><span data-stu-id="47d90-206">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="47d90-207">Kód pro vývojáře musí zachytit výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-207">Developer code must catch the exception.</span></span> <span data-ttu-id="47d90-208">Pokud kód aplikace v metodě obslužné rutiny události nebo životní cyklus komponenty nezpracovává výjimku, je výsledná výjimka pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to the circuit.</span></span>
* <span data-ttu-id="47d90-209">Pokud se volání `InvokeAsync<T>` asynchronně nezdařilo, <xref:System.Threading.Tasks.Task> .NET dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="47d90-209">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="47d90-210">Volání `InvokeAsync<T>` může selhat, například proto, že kód na straně JavaScriptu vyvolá výjimku nebo vrátí `Promise`, která byla dokončena jako `rejected`.</span><span class="sxs-lookup"><span data-stu-id="47d90-210">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="47d90-211">Kód pro vývojáře musí zachytit výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-211">Developer code must catch the exception.</span></span> <span data-ttu-id="47d90-212">Při použití operátoru [await](/dotnet/csharp/language-reference/keywords/await) zvažte zabalení volání metody v příkazu [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="47d90-212">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="47d90-213">V opačném případě selhání kódu způsobí neošetřenou výjimku, která je pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-213">Otherwise, the failing code results in an unhandled exception that's fatal to the circuit.</span></span>
* <span data-ttu-id="47d90-214">Ve výchozím nastavení musí být volání `InvokeAsync<T>` dokončena v určitou dobu nebo jinak vyprší časový limit volání. Výchozí doba časového limitu je jedna minuta.</span><span class="sxs-lookup"><span data-stu-id="47d90-214">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="47d90-215">Časový limit chrání kód proti ztrátě v připojení k síti nebo kódu JavaScriptu, který nikdy neposílá zpět zprávu o dokončení.</span><span class="sxs-lookup"><span data-stu-id="47d90-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="47d90-216">Pokud vyprší časový limit volání, výsledné `Task` se nezdařila s <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="47d90-216">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="47d90-217">Depeše a zpracovává výjimku pomocí protokolování.</span><span class="sxs-lookup"><span data-stu-id="47d90-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="47d90-218">Podobně kód JavaScriptu může iniciovat volání metod .NET, které jsou označeny atributem [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) .</span><span class="sxs-lookup"><span data-stu-id="47d90-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions) attribute.</span></span> <span data-ttu-id="47d90-219">Pokud tyto metody rozhraní .NET vyvolají neošetřenou výjimku:</span><span class="sxs-lookup"><span data-stu-id="47d90-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="47d90-220">Výjimka není pro okruh považována za závažnou.</span><span class="sxs-lookup"><span data-stu-id="47d90-220">The exception isn't treated as fatal to the circuit.</span></span>
* <span data-ttu-id="47d90-221">`Promise` na straně JavaScriptu se zamítlo.</span><span class="sxs-lookup"><span data-stu-id="47d90-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="47d90-222">Máte možnost použít kód pro zpracování chyb na straně .NET nebo na straně JavaScriptu volání metody.</span><span class="sxs-lookup"><span data-stu-id="47d90-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="47d90-223">Další informace najdete v tématu <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="47d90-223">For more information, see <xref:blazor/javascript-interop>.</span></span>

### <a name="circuit-handlers"></a><span data-ttu-id="47d90-224">Obslužné rutiny okruhu</span><span class="sxs-lookup"><span data-stu-id="47d90-224">Circuit handlers</span></span>

Blazor<span data-ttu-id="47d90-225"> umožňuje kódu definovat *obslužnou rutinu okruhu*, která obdrží oznámení při změně stavu okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="47d90-225"> allows code to define a *circuit handler*, which receives notifications when the state of a user's circuit changes.</span></span> <span data-ttu-id="47d90-226">Používají se následující stavy:</span><span class="sxs-lookup"><span data-stu-id="47d90-226">The following states are used:</span></span>

* `initialized`
* `connected`
* `disconnected`
* `disposed`

<span data-ttu-id="47d90-227">Oznámení se spravují pomocí registrace služby DI, která dědí z `CircuitHandler` abstraktní základní třídy.</span><span class="sxs-lookup"><span data-stu-id="47d90-227">Notifications are managed by registering a DI service that inherits from the `CircuitHandler` abstract base class.</span></span>

<span data-ttu-id="47d90-228">Pokud metody obslužné rutiny vlastního okruhu vyvolávají neošetřenou výjimku, je výjimka pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-228">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the circuit.</span></span> <span data-ttu-id="47d90-229">Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód v jednom nebo více příkazech [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="47d90-229">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

### <a name="circuit-disposal"></a><span data-ttu-id="47d90-230">Vyřazení okruhů</span><span class="sxs-lookup"><span data-stu-id="47d90-230">Circuit disposal</span></span>

<span data-ttu-id="47d90-231">Když je okruh ukončený, protože uživatel je odpojený a rozhraní čistí stav okruhu, rozhraní uvolní obor DI okruhu.</span><span class="sxs-lookup"><span data-stu-id="47d90-231">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="47d90-232">Při likvidaci oboru se uvolní jakékoli služby DI Services s rozsahem okruhů, které implementují <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="47d90-232">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="47d90-233">Pokud jakákoli služba DI vyvolá neošetřenou výjimku při vyřazení, rozhraní zaprotokoluje výjimku.</span><span class="sxs-lookup"><span data-stu-id="47d90-233">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

### <a name="prerendering"></a><span data-ttu-id="47d90-234">Předvykreslování</span><span class="sxs-lookup"><span data-stu-id="47d90-234">Prerendering</span></span>

::: moniker range=">= aspnetcore-3.1"

Blazor<span data-ttu-id="47d90-235"> komponenty lze předem vykreslovat pomocí pomocné rutiny `Component` tag, aby byly vykreslené značky HTML vráceny jako součást počátečního požadavku HTTP uživatele.</span><span class="sxs-lookup"><span data-stu-id="47d90-235"> components can be prerendered using the `Component` Tag Helper so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="47d90-236">Funguje to takto:</span><span class="sxs-lookup"><span data-stu-id="47d90-236">This works by:</span></span>

* <span data-ttu-id="47d90-237">Vytváření nového okruhu pro všechny předem vykreslené komponenty, které jsou součástí stejné stránky.</span><span class="sxs-lookup"><span data-stu-id="47d90-237">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="47d90-238">Generování počátečního kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="47d90-238">Generating the initial HTML.</span></span>
* <span data-ttu-id="47d90-239">Považovat okruh za `disconnected`, dokud prohlížeč uživatele nevytvoří SignalR připojení zpátky ke stejnému serveru.</span><span class="sxs-lookup"><span data-stu-id="47d90-239">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="47d90-240">Po navázání spojení se v okruhu obnoví interakce mezi aktivitami a kód HTML značek se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="47d90-240">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="47d90-241">Pokud nějaká komponenta vyvolá neošetřenou výjimku při předvykreslování, například během metody životního cyklu nebo v logice vykreslování:</span><span class="sxs-lookup"><span data-stu-id="47d90-241">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="47d90-242">Výjimka je pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-242">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="47d90-243">Výjimka vyvolá zásobník volání z pomocníka značky `Component`.</span><span class="sxs-lookup"><span data-stu-id="47d90-243">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="47d90-244">Proto se celý požadavek HTTP nezdařil, pokud není výjimka výslovně zachycena kódem vývojáře.</span><span class="sxs-lookup"><span data-stu-id="47d90-244">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="47d90-245">Za běžných okolností, když se předvykreslování nepovede, pokračuje sestavení a vykreslení komponenty nesmysl, protože pracovní komponenta se nedá vykreslit.</span><span class="sxs-lookup"><span data-stu-id="47d90-245">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="47d90-246">Chcete-li tolerovat chyby, ke kterým může dojít při předvykreslování, musí být logika zpracování chyb umístěna v rámci součásti, která může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="47d90-246">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="47d90-247">Použijte příkazy [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s ošetřením a protokolováním chyb.</span><span class="sxs-lookup"><span data-stu-id="47d90-247">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="47d90-248">Namísto zabalení pomocníka značky `Component` v příkazu `try-catch`, umístěte logiku zpracování chyb do komponenty vygenerované pomocníkem `Component` značek.</span><span class="sxs-lookup"><span data-stu-id="47d90-248">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

Blazor<span data-ttu-id="47d90-249"> komponenty lze předem vykreslovat pomocí `Html.RenderComponentAsync`, aby byly vykreslené značky HTML vráceny jako součást počátečního požadavku HTTP uživatele.</span><span class="sxs-lookup"><span data-stu-id="47d90-249"> components can be prerendered using `Html.RenderComponentAsync` so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="47d90-250">Funguje to takto:</span><span class="sxs-lookup"><span data-stu-id="47d90-250">This works by:</span></span>

* <span data-ttu-id="47d90-251">Vytváření nového okruhu pro všechny předem vykreslené komponenty, které jsou součástí stejné stránky.</span><span class="sxs-lookup"><span data-stu-id="47d90-251">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="47d90-252">Generování počátečního kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="47d90-252">Generating the initial HTML.</span></span>
* <span data-ttu-id="47d90-253">Považovat okruh za `disconnected`, dokud prohlížeč uživatele nevytvoří SignalR připojení zpátky ke stejnému serveru.</span><span class="sxs-lookup"><span data-stu-id="47d90-253">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="47d90-254">Po navázání spojení se v okruhu obnoví interakce mezi aktivitami a kód HTML značek se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="47d90-254">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="47d90-255">Pokud nějaká komponenta vyvolá neošetřenou výjimku při předvykreslování, například během metody životního cyklu nebo v logice vykreslování:</span><span class="sxs-lookup"><span data-stu-id="47d90-255">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="47d90-256">Výjimka je pro okruh závažná.</span><span class="sxs-lookup"><span data-stu-id="47d90-256">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="47d90-257">Výjimka vyvolala zásobník volání z `Html.RenderComponentAsync` volání.</span><span class="sxs-lookup"><span data-stu-id="47d90-257">The exception is thrown up the call stack from the `Html.RenderComponentAsync` call.</span></span> <span data-ttu-id="47d90-258">Proto se celý požadavek HTTP nezdařil, pokud není výjimka výslovně zachycena kódem vývojáře.</span><span class="sxs-lookup"><span data-stu-id="47d90-258">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="47d90-259">Za běžných okolností, když se předvykreslování nepovede, pokračuje sestavení a vykreslení komponenty nesmysl, protože pracovní komponenta se nedá vykreslit.</span><span class="sxs-lookup"><span data-stu-id="47d90-259">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="47d90-260">Chcete-li tolerovat chyby, ke kterým může dojít při předvykreslování, musí být logika zpracování chyb umístěna v rámci součásti, která může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="47d90-260">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="47d90-261">Použijte příkazy [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s ošetřením a protokolováním chyb.</span><span class="sxs-lookup"><span data-stu-id="47d90-261">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="47d90-262">Místo zabalení volání `RenderComponentAsync` v příkazu `try-catch` umístěte logiku zpracování chyb do komponenty vygenerované `RenderComponentAsync`.</span><span class="sxs-lookup"><span data-stu-id="47d90-262">Instead of wrapping the call to `RenderComponentAsync` in a `try-catch` statement, place error handling logic in the component rendered by `RenderComponentAsync`.</span></span>

::: moniker-end

## <a name="advanced-scenarios"></a><span data-ttu-id="47d90-263">Složitější scénáře</span><span class="sxs-lookup"><span data-stu-id="47d90-263">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="47d90-264">Rekurzivní vykreslování</span><span class="sxs-lookup"><span data-stu-id="47d90-264">Recursive rendering</span></span>

<span data-ttu-id="47d90-265">Komponenty lze rekurzivně vnořovat.</span><span class="sxs-lookup"><span data-stu-id="47d90-265">Components can be nested recursively.</span></span> <span data-ttu-id="47d90-266">To je užitečné pro reprezentaci rekurzivních datových struktur.</span><span class="sxs-lookup"><span data-stu-id="47d90-266">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="47d90-267">Například komponenta `TreeNode` může vykreslovat více `TreeNode` komponent pro každý podřízený uzel.</span><span class="sxs-lookup"><span data-stu-id="47d90-267">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="47d90-268">Při rekurzivním vykreslování Vyhněte se vzorům kódování, které vedou k nekonečné rekurzi:</span><span class="sxs-lookup"><span data-stu-id="47d90-268">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="47d90-269">Neprovádějte rekurzivní vykreslování struktury dat, která obsahuje cyklus.</span><span class="sxs-lookup"><span data-stu-id="47d90-269">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="47d90-270">Například nevykreslují uzel stromu, jehož podřízené položky zahrnují sám sebe.</span><span class="sxs-lookup"><span data-stu-id="47d90-270">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="47d90-271">Nevytvářejte řetěz rozložení, které obsahují cyklus.</span><span class="sxs-lookup"><span data-stu-id="47d90-271">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="47d90-272">Nevytvářejte například rozložení, jehož rozložení je samotné.</span><span class="sxs-lookup"><span data-stu-id="47d90-272">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="47d90-273">Neumožněte koncovému uživateli narušovat invariantní rekurzivních dat (pravidla) prostřednictvím škodlivých vstupů dat nebo volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="47d90-273">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="47d90-274">Nekonečná smyčka během vykreslování:</span><span class="sxs-lookup"><span data-stu-id="47d90-274">Infinite loops during rendering:</span></span>

* <span data-ttu-id="47d90-275">Způsobí, že proces vykreslování bude trvale pokračovat.</span><span class="sxs-lookup"><span data-stu-id="47d90-275">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="47d90-276">Je ekvivalentní vytvořit neukončenou smyčku.</span><span class="sxs-lookup"><span data-stu-id="47d90-276">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="47d90-277">V těchto scénářích přestává ovlivněný okruh a vlákno se obvykle pokouší:</span><span class="sxs-lookup"><span data-stu-id="47d90-277">In these scenarios, the affected circuit hangs, and the thread usually attempts to:</span></span>

* <span data-ttu-id="47d90-278">Spotřebujte tolik času procesoru povolený operačním systémem, a to po dobu neurčitou.</span><span class="sxs-lookup"><span data-stu-id="47d90-278">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="47d90-279">Spotřebovává neomezený objem paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="47d90-279">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="47d90-280">Spotřebovávání neomezené paměti je ekvivalentem k situaci, kdy neukončená smyčka přidá položky do kolekce při každé iteraci.</span><span class="sxs-lookup"><span data-stu-id="47d90-280">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="47d90-281">Aby se zabránilo nekonečným vzorům rekurzování, ujistěte se, že kód rekurzivního vykreslování obsahuje vhodné podmínky</span><span class="sxs-lookup"><span data-stu-id="47d90-281">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="47d90-282">Vlastní logika stromu vykreslování</span><span class="sxs-lookup"><span data-stu-id="47d90-282">Custom render tree logic</span></span>

<span data-ttu-id="47d90-283">Většina Blazor komponenty jsou implementovány jako soubory *. Razor* a jsou kompilovány k vytvoření logiky, která pracuje na `RenderTreeBuilder` pro vykreslení výstupu.</span><span class="sxs-lookup"><span data-stu-id="47d90-283">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="47d90-284">Vývojář může ručně implementovat `RenderTreeBuilder` logiku pomocí procedurálního C# kódu.</span><span class="sxs-lookup"><span data-stu-id="47d90-284">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="47d90-285">Další informace najdete v tématu <xref:blazor/components#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="47d90-285">For more information, see <xref:blazor/components#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="47d90-286">Použití logiky tvůrce stromu ručního vykreslování je považováno za pokročilý a nebezpečný scénář, nedoporučuje se pro obecný vývoj komponent.</span><span class="sxs-lookup"><span data-stu-id="47d90-286">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="47d90-287">Pokud je zapsán `RenderTreeBuilder` kód, vývojář musí zaručit správnost kódu.</span><span class="sxs-lookup"><span data-stu-id="47d90-287">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="47d90-288">Vývojář například musí zajistit, aby:</span><span class="sxs-lookup"><span data-stu-id="47d90-288">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="47d90-289">Volání `OpenElement` a `CloseElement` jsou správně vyvážená.</span><span class="sxs-lookup"><span data-stu-id="47d90-289">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="47d90-290">Atributy se přidávají jenom na správných místech.</span><span class="sxs-lookup"><span data-stu-id="47d90-290">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="47d90-291">Nesprávná logika tvůrce stromu ručního vykreslování může způsobit libovolné nedefinované chování, včetně havárií, zablokování serveru a ohrožení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="47d90-291">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="47d90-292">Zvažte ruční vykreslování logiky tvůrce stromu na stejné úrovni složitosti a se stejnou úrovní *nebezpečí* při psaní kódu sestavení nebo instrukcí jazyka MSIL ručně.</span><span class="sxs-lookup"><span data-stu-id="47d90-292">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
