---
title: BlazorSpráva stavu ASP.NET Core
author: guardrex
description: Přečtěte si, jak zachovat stav v Blazor Server aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 28ca3b5c4472dc21e709d01705dc64168107ca61
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013551"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="bc2e7-103">BlazorSpráva stavu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc2e7-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="bc2e7-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bc2e7-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="bc2e7-105">Stav uživatele vytvořený v Blazor WebAssembly aplikaci je uložený v paměti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="bc2e7-106">Mezi příklady stavu uživatele v paměti prohlížeče patří:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="bc2e7-107">Hierarchie instancí komponent a jejich poslední výstup vykreslování ve vykresleném uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="bc2e7-108">Hodnoty polí a vlastností v instancích součástí.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="bc2e7-109">Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="bc2e7-110">Hodnoty nastavené prostřednictvím volání [interoperability JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="bc2e7-111">Když uživatel zavře a znovu otevře prohlížeč nebo znovu načte stránku, stav uživatele uložený v paměti prohlížeče bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="bc2e7-112">Trvalý stav napříč relacemi prohlížeče</span><span class="sxs-lookup"><span data-stu-id="bc2e7-112">Persist state across browser sessions</span></span>

<span data-ttu-id="bc2e7-113">Obecně platí, že se udržuje stav napříč relacemi prohlížeče, kde uživatelé aktivně vytvářejí data a nečtou jenom data, která už existují.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="bc2e7-114">Aby bylo možné zachovat stav napříč relacemi prohlížeče, musí aplikace uchovávat data do jiného umístění úložiště než do paměti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="bc2e7-115">Trvalost stavu není automatické.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-115">State persistence isn't automatic.</span></span> <span data-ttu-id="bc2e7-116">Je nutné provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="bc2e7-117">Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="bc2e7-118">V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="bc2e7-119">Webové formuláře s více kroky: časově náročné pro uživatele, aby znovu zadal data pro několik dokončených kroků webového formuláře s více kroky, pokud dojde ke ztrátě jejich stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="bc2e7-120">Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře a vrátí se později.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="bc2e7-121">Nákupní košíky: všechny obchodní důležité komponenty aplikace, které představují potenciální tržby, se dají udržovat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="bc2e7-122">Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="bc2e7-123">Aplikace může zachovat jenom *stav aplikace*.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-123">An app can only persist *app state*.</span></span> <span data-ttu-id="bc2e7-124">Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="bc2e7-125">Komponenty a stromy vykreslování nejsou obecně serializovatelné.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="bc2e7-126">Aby bylo možné zachovat stav uživatelského rozhraní, jako jsou například rozbalené uzly ovládacího prvku stromového zobrazení, musí aplikace používat vlastní kód pro modelování chování uživatelského rozhraní jako serializovatelnýho stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="bc2e7-127">Kam zachovat stav</span><span class="sxs-lookup"><span data-stu-id="bc2e7-127">Where to persist state</span></span>

<span data-ttu-id="bc2e7-128">Existují tři společná umístění pro trvalý stav:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-128">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="bc2e7-129">Úložiště na straně serveru</span><span class="sxs-lookup"><span data-stu-id="bc2e7-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="bc2e7-130">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="bc2e7-130">URL</span></span>](#url)
* [<span data-ttu-id="bc2e7-131">Úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="bc2e7-131">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="bc2e7-132">Úložiště na straně serveru</span><span class="sxs-lookup"><span data-stu-id="bc2e7-132">Server-side storage</span></span>

<span data-ttu-id="bc2e7-133">V případě trvalé trvalosti dat, která zahrnuje více uživatelů a zařízení, může aplikace používat nezávislé serverové úložiště, ke kterému se přistupoval prostřednictvím webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-133">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="bc2e7-134">Vaše možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-134">Options include:</span></span>

* <span data-ttu-id="bc2e7-135">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="bc2e7-135">Blob storage</span></span>
* <span data-ttu-id="bc2e7-136">Úložiště hodnot klíčů</span><span class="sxs-lookup"><span data-stu-id="bc2e7-136">Key-value storage</span></span>
* <span data-ttu-id="bc2e7-137">Relační databáze</span><span class="sxs-lookup"><span data-stu-id="bc2e7-137">Relational database</span></span>
* <span data-ttu-id="bc2e7-138">Table Storage</span><span class="sxs-lookup"><span data-stu-id="bc2e7-138">Table storage</span></span>

<span data-ttu-id="bc2e7-139">Po uložení dat se stav uživatele zachová a bude dostupný v jakékoli nové relaci prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-139">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="bc2e7-140">Vzhledem k tomu Blazor WebAssembly , že aplikace běží zcela v prohlížeči uživatele, vyžadují další míry pro přístup k zabezpečeným externím systémům, jako jsou služby úložiště a databáze.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-140">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="bc2e7-141">Blazor WebAssemblyaplikace jsou zabezpečené stejným způsobem jako aplikace s jedním stránkou (jednostránkové).</span><span class="sxs-lookup"><span data-stu-id="bc2e7-141">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="bc2e7-142">Aplikace obvykle ověřuje uživatele prostřednictvím [OAuth](https://oauth.net) / [OpenID Connect (OIDC)](https://openid.net/connect/) a pak spolupracuje se službami úložiště a databázemi prostřednictvím volání webového rozhraní API do aplikace na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-142">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="bc2e7-143">Aplikace na straně serveru vypravuje přenos dat mezi Blazor WebAssembly aplikací a službou úložiště nebo databází.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-143">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="bc2e7-144">Blazor WebAssemblyAplikace udržuje dočasné připojení k aplikaci na straně serveru, zatímco aplikace na straně serveru má trvalé připojení k úložišti.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-144">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="bc2e7-145">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-145">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="bc2e7-146">Blazor\*Zabezpečení a Identity \* zboží</span><span class="sxs-lookup"><span data-stu-id="bc2e7-146">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="bc2e7-147">Další informace o možnostech Azure Data Storage najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-147">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="bc2e7-148">Databáze Azure</span><span class="sxs-lookup"><span data-stu-id="bc2e7-148">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="bc2e7-149">Dokumentace k Azure Storage</span><span class="sxs-lookup"><span data-stu-id="bc2e7-149">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="bc2e7-150">URL</span><span class="sxs-lookup"><span data-stu-id="bc2e7-150">URL</span></span>

<span data-ttu-id="bc2e7-151">Pro přechodná data představující stav navigace modelujte data jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-151">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="bc2e7-152">Mezi příklady stavu uživatele v adrese URL patří:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-152">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="bc2e7-153">ID zobrazené entity</span><span class="sxs-lookup"><span data-stu-id="bc2e7-153">The ID of a viewed entity.</span></span>
* <span data-ttu-id="bc2e7-154">Aktuální číslo stránky v mřížce na stránkovaném webu</span><span class="sxs-lookup"><span data-stu-id="bc2e7-154">The current page number in a paged grid.</span></span>

<span data-ttu-id="bc2e7-155">Pokud uživatel ručně znovu načte stránku, obsah panelu Adresa prohlížeče se zachová.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-155">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="bc2e7-156">Informace o definování vzorů adres URL pomocí [`@page`](xref:mvc/views/razor#page) direktivy naleznete v tématu <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-156">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="bc2e7-157">Úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="bc2e7-157">Browser storage</span></span>

<span data-ttu-id="bc2e7-158">Pro přechodná data, která uživatel aktivně vytváří, se běžně používané umístění úložiště nachází v prohlížeči [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) kolekcích:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-158">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="bc2e7-159">`localStorage`je vymezen v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-159">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="bc2e7-160">Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-160">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="bc2e7-161">Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-161">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="bc2e7-162">Data přetrvají do `localStorage` doby, než je explicitně zrušeno.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-162">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="bc2e7-163">`sessionStorage`je vymezen na kartu prohlížeče. Pokud uživatel znovu načte kartu, stav se přetrvá.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-163">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="bc2e7-164">Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-164">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="bc2e7-165">Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-165">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="bc2e7-166">`localStorage`a `sessionStorage` dá se použít v Blazor WebAssembly aplikacích, ale jenom pomocí psaní vlastního kódu nebo pomocí balíčku třetí strany.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-166">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="bc2e7-167">Obecně `sessionStorage` je bezpečnější použít.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-167">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="bc2e7-168">`sessionStorage`Vyhněte se riziku, že uživatel otevře více karet a narazí na následující:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-168">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="bc2e7-169">Chyby v úložišti stavů napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-169">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="bc2e7-170">Matoucí chování, když karta Přepisuje stav jiných karet.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-170">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="bc2e7-171">`localStorage`je lepší volbou, pokud aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-171">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="bc2e7-172">Uživatelé mohou zobrazit nebo manipulovat s daty uloženými v `localStorage` a `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-172">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bc2e7-173">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bc2e7-173">Additional resources</span></span>

* [<span data-ttu-id="bc2e7-174">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="bc2e7-174">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="bc2e7-175">Blazor Serverje stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-175">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="bc2e7-176">Ve většině případů aplikace udržuje připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-176">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="bc2e7-177">Stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-177">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="bc2e7-178">Příklady stavu uživatele uchovávaného v okruhu zahrnují:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-178">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="bc2e7-179">Hierarchie instancí komponent a jejich poslední výstup vykreslování ve vykresleném uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-179">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="bc2e7-180">Hodnoty polí a vlastností v instancích součástí.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-180">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="bc2e7-181">Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , která jsou vymezena okruhu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-181">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="bc2e7-182">Stav uživatele se může také najít v proměnných JavaScriptu v paměti prohlížeče prostřednictvím volání [interoperability JavaScriptu](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-182">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="bc2e7-183">Pokud dojde ke ztrátě dočasného připojení k síti, Blazor pokusí se uživatel znovu připojit k původnímu okruhu s původním stavem.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-183">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="bc2e7-184">Ale opětovné připojení uživatele k původnímu okruhu v paměti serveru není vždycky možné:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-184">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="bc2e7-185">Server nemůže trvale zachovávat odpojený okruh.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-185">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="bc2e7-186">Server musí uvolnit odpojený okruh po vypršení časového limitu nebo v případě, že je server pod tlakem paměti.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-186">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="bc2e7-187">V prostředích pro nasazení s více servery a vyrovnáváním zatížení můžou jednotlivé servery selhat nebo je automaticky odebrat, když už nepotřebujete pro zpracování celkového objemu požadavků.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-187">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="bc2e7-188">V případě, že se uživatel pokusí znovu připojit, může dojít k nedostupnosti původních požadavků na zpracování serveru pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-188">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="bc2e7-189">Uživatel může zavřít a znovu otevřít prohlížeč nebo znovu načíst stránku, což odstraní všechny stavy uchovávané v paměti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-189">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="bc2e7-190">Například hodnoty proměnných JavaScriptu nastavené prostřednictvím volání interoperability JavaScript se ztratí.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-190">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="bc2e7-191">Když se uživatel nedá znovu připojit k původnímu okruhu, uživatel dostane nový okruh s prázdným stavem.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-191">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="bc2e7-192">Jedná se o ekvivalent zavření a opětovného otevření desktopové aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-192">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="bc2e7-193">Zachovat stav napříč okruhy</span><span class="sxs-lookup"><span data-stu-id="bc2e7-193">Persist state across circuits</span></span>

<span data-ttu-id="bc2e7-194">Obecně platí, že se udržuje stav napříč okruhy, kde uživatelé aktivně vytvářejí data, a ne jednoduše čtou data, která již existují.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-194">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="bc2e7-195">Aby bylo možné zachovat stav napříč okruhy, musí aplikace uchovávat data do jiného umístění úložiště než z paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-195">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="bc2e7-196">Trvalost stavu není automatické.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-196">State persistence isn't automatic.</span></span> <span data-ttu-id="bc2e7-197">Je nutné provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-197">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="bc2e7-198">Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-198">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="bc2e7-199">V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-199">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="bc2e7-200">Webové formuláře s více kroky: časově náročné pro uživatele, aby znovu zadal data pro několik dokončených kroků webového formuláře s více kroky, pokud dojde ke ztrátě jejich stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-200">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="bc2e7-201">Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře a vrátí se později.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-201">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="bc2e7-202">Nákupní košíky: všechny obchodní důležité komponenty aplikace, které představují potenciální tržby, se dají udržovat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-202">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="bc2e7-203">Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-203">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="bc2e7-204">Aplikace může zachovat jenom *stav aplikace*.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-204">An app can only persist *app state*.</span></span> <span data-ttu-id="bc2e7-205">Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-205">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="bc2e7-206">Komponenty a stromy vykreslování nejsou obecně serializovatelné.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-206">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="bc2e7-207">Aby bylo možné zachovat stav uživatelského rozhraní, jako jsou například rozbalené uzly ovládacího prvku stromového zobrazení, musí aplikace používat vlastní kód pro modelování chování uživatelského rozhraní jako serializovatelnýho stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-207">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="bc2e7-208">Kam zachovat stav</span><span class="sxs-lookup"><span data-stu-id="bc2e7-208">Where to persist state</span></span>

<span data-ttu-id="bc2e7-209">Existují tři společná umístění pro trvalý stav:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-209">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="bc2e7-210">Úložiště na straně serveru</span><span class="sxs-lookup"><span data-stu-id="bc2e7-210">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="bc2e7-211">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="bc2e7-211">URL</span></span>](#url)
* [<span data-ttu-id="bc2e7-212">Úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="bc2e7-212">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="bc2e7-213">Úložiště na straně serveru</span><span class="sxs-lookup"><span data-stu-id="bc2e7-213">Server-side storage</span></span>

<span data-ttu-id="bc2e7-214">V případě trvalé trvalosti dat, která zahrnuje více uživatelů a zařízení, může aplikace používat úložiště na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-214">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="bc2e7-215">Vaše možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-215">Options include:</span></span>

* <span data-ttu-id="bc2e7-216">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="bc2e7-216">Blob storage</span></span>
* <span data-ttu-id="bc2e7-217">Úložiště hodnot klíčů</span><span class="sxs-lookup"><span data-stu-id="bc2e7-217">Key-value storage</span></span>
* <span data-ttu-id="bc2e7-218">Relační databáze</span><span class="sxs-lookup"><span data-stu-id="bc2e7-218">Relational database</span></span>
* <span data-ttu-id="bc2e7-219">Table Storage</span><span class="sxs-lookup"><span data-stu-id="bc2e7-219">Table storage</span></span>

<span data-ttu-id="bc2e7-220">Po uložení dat se stav uživatele zachová a bude dostupný v jakémkoli novém okruhu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-220">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="bc2e7-221">Další informace o možnostech Azure Data Storage najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-221">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="bc2e7-222">Databáze Azure</span><span class="sxs-lookup"><span data-stu-id="bc2e7-222">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="bc2e7-223">Dokumentace k Azure Storage</span><span class="sxs-lookup"><span data-stu-id="bc2e7-223">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="bc2e7-224">URL</span><span class="sxs-lookup"><span data-stu-id="bc2e7-224">URL</span></span>

<span data-ttu-id="bc2e7-225">Pro přechodná data představující stav navigace modelujte data jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-225">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="bc2e7-226">Mezi příklady stavu uživatele v adrese URL patří:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-226">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="bc2e7-227">ID zobrazené entity</span><span class="sxs-lookup"><span data-stu-id="bc2e7-227">The ID of a viewed entity.</span></span>
* <span data-ttu-id="bc2e7-228">Aktuální číslo stránky v mřížce na stránkovaném webu</span><span class="sxs-lookup"><span data-stu-id="bc2e7-228">The current page number in a paged grid.</span></span>

<span data-ttu-id="bc2e7-229">Obsah panelu Adresa prohlížeče se zachová:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-229">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="bc2e7-230">Pokud uživatel ručně znovu načte stránku.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-230">If the user manually reloads the page.</span></span>
* <span data-ttu-id="bc2e7-231">Pokud dojde k nedostupnosti webového serveru a uživatel je nucen znovu načíst stránku, aby se mohl připojit k jinému serveru.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-231">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="bc2e7-232">Informace o definování vzorů adres URL pomocí [`@page`](xref:mvc/views/razor#page) direktivy naleznete v tématu <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-232">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="bc2e7-233">Úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="bc2e7-233">Browser storage</span></span>

<span data-ttu-id="bc2e7-234">Pro přechodná data, která uživatel aktivně vytváří, se běžně používané umístění úložiště nachází v prohlížeči [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) kolekcích:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-234">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="bc2e7-235">`localStorage`je vymezen v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-235">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="bc2e7-236">Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-236">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="bc2e7-237">Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-237">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="bc2e7-238">Data přetrvají do `localStorage` doby, než je explicitně zrušeno.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-238">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="bc2e7-239">`sessionStorage`je vymezen na kartu prohlížeče. Pokud uživatel znovu načte kartu, stav se přetrvá.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-239">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="bc2e7-240">Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-240">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="bc2e7-241">Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-241">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="bc2e7-242">Obecně `sessionStorage` je bezpečnější použít.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-242">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="bc2e7-243">`sessionStorage`Vyhněte se riziku, že uživatel otevře více karet a narazí na následující:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-243">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="bc2e7-244">Chyby v úložišti stavů napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-244">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="bc2e7-245">Matoucí chování, když karta Přepisuje stav jiných karet.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-245">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="bc2e7-246">`localStorage`je lepší volbou, pokud aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-246">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="bc2e7-247">Upozornění pro použití úložiště prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-247">Caveats for using browser storage:</span></span>

* <span data-ttu-id="bc2e7-248">Podobně jako při použití databáze na straně serveru je načítání a ukládání dat asynchronní.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-248">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="bc2e7-249">Na rozdíl od databáze na straně serveru není úložiště během předgenerování k dispozici, protože požadovaná stránka v prohlížeči neexistuje během fáze předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-249">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="bc2e7-250">Ukládání několika kilobajtů dat je pro Blazor Server aplikace přijatelné.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-250">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="bc2e7-251">Po několika kilobajtech je potřeba vzít v úvahu dopad na výkon, protože data se načítají a ukládají v síti.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-251">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="bc2e7-252">Uživatelé můžou data zobrazit nebo s nimi manipulovat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-252">Users may view or tamper with the data.</span></span> <span data-ttu-id="bc2e7-253">[Ochrana dat ASP.NET Core](xref:security/data-protection/introduction) může riziko zmírnit.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-253">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="bc2e7-254">Například [ASP.NET Core chráněné úložiště prohlížeče](#aspnet-core-protected-browser-storage) používá ASP.NET Core ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-254">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="bc2e7-255">Balíčky NuGet třetích stran poskytují rozhraní API pro práci s `localStorage` a `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-255">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="bc2e7-256">Je vhodné zvážit výběr balíčku, který transparentně používá [ASP.NET Core ochranu dat](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="bc2e7-256">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="bc2e7-257">Ochrana dat šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-257">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="bc2e7-258">Pokud jsou data serializovaná do formátu JSON uložená v prostém textu, uživatelé můžou data zobrazit pomocí vývojářských nástrojů pro prohlížeč a také upravovat uložená data.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-258">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="bc2e7-259">Zabezpečení dat se vždy nejedná o problém, protože data můžou být triviální v podstatě.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-259">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="bc2e7-260">Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významným bezpečnostním rizikem pro uživatele nebo organizaci.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-260">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="bc2e7-261">Neumožňuje uživatelům kontrolovat *citlivá data*a manipulovat s nimi.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-261">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="bc2e7-262">ASP.NET Core chráněné úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="bc2e7-262">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="bc2e7-263">ASP.NET Core chráněné úložiště prohlížeče využívá [ASP.NET Core ochrany dat](xref:security/data-protection/introduction) pro [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-263">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="bc2e7-264">Chráněné úložiště prohlížeče spoléhá na ASP.NET Core ochrany dat a podporuje se jenom pro Blazor Server aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-264">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="bc2e7-265">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="bc2e7-265">Configuration</span></span>

1. <span data-ttu-id="bc2e7-266">Přidejte odkaz na balíček do [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-266">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="bc2e7-267">V `Startup.ConfigureServices` volejte volání metody `AddProtectedBrowserStorage` Add `localStorage` a `sessionStorage` Services do kolekce služeb:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-267">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="bc2e7-268">Ukládání a načítání dat v rámci součásti</span><span class="sxs-lookup"><span data-stu-id="bc2e7-268">Save and load data within a component</span></span>

<span data-ttu-id="bc2e7-269">V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:mvc/views/razor#inject) direktivu pro vložení instance některého z následujících prvků:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="bc2e7-270">Volba závisí na tom, jaké umístění úložiště prohlížeče chcete použít.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="bc2e7-271">V následujícím příkladu `sessionStorage` se používá:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="bc2e7-272">`@using`Direktiva může být umístěna do `_Imports.razor` souboru aplikace namísto do součásti.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="bc2e7-273">Použití `_Imports.razor` souboru zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="bc2e7-274">Chcete-li zachovat `currentCount` hodnotu v `Counter` součásti aplikace na základě Blazor Server šablony projektu, upravte `IncrementCount` metodu, kterou chcete použít `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-274">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="bc2e7-275">Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="bc2e7-276">Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="bc2e7-277">`ProtectedSessionStore`automaticky serializace a deserializace dat JSON pro ukládání komplexních objektů stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="bc2e7-278">V předchozím příkladu kódu `currentCount` se data ukládají jako `sessionStorage['count']` v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="bc2e7-279">Data nejsou uložená v prostém textu, ale místo toho jsou chráněná pomocí ASP.NET Core ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="bc2e7-280">Zašifrovaná data lze zkontrolovat, pokud `sessionStorage['count']` je vyhodnocena v konzole pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="bc2e7-281">Chcete-li obnovit `currentCount` data, pokud se uživatel vrátí do `Counter` komponenty později, včetně toho, jestli je uživatel na novém okruhu, použijte `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="bc2e7-282">Pokud parametry komponenty obsahují stav navigace, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte `null` nevýsledek v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nikoli <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="bc2e7-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>se volá pouze jednou při prvním vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="bc2e7-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>není voláno později, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="bc2e7-285">Další informace naleznete v tématu <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="bc2e7-286">Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="bc2e7-287">Pokud je povoleno předvykreslování, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="bc2e7-288">Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="bc2e7-289">Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="bc2e7-290">Zpracování stavu načítání</span><span class="sxs-lookup"><span data-stu-id="bc2e7-290">Handle the loading state</span></span>

<span data-ttu-id="bc2e7-291">Vzhledem k tomu, že je úložiště prohlížeče přístupné asynchronně přes síťové připojení, je vždy časový interval před načtením dat a k dispozici pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="bc2e7-292">Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="bc2e7-293">Jedním z těchto způsobů je sledovat, jestli data jsou `null` , což znamená, že se data pořád načítají.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="bc2e7-294">Ve výchozí `Counter` součásti je počet uchováván v `int` .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="bc2e7-295">Převést na typ s [ `currentCount` možnou hodnotou null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) přidáním otazníku ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="bc2e7-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="bc2e7-296">Namísto nepodmíněného zobrazení čítače a **`Increment`** tlačítka zobrazte tyto prvky pouze v případě, že jsou data načtena kontrolou <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="bc2e7-297">Zpracovat předvykreslování</span><span class="sxs-lookup"><span data-stu-id="bc2e7-297">Handle prerendering</span></span>

<span data-ttu-id="bc2e7-298">Během předvykreslování:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-298">During prerendering:</span></span>

* <span data-ttu-id="bc2e7-299">Interaktivní připojení k prohlížeči uživatele neexistuje.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="bc2e7-300">Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="bc2e7-301">`localStorage`nebo `sessionStorage` nejsou během předvykreslování k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="bc2e7-302">Pokud se komponenta pokusí pracovat s úložištěm, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="bc2e7-303">Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="bc2e7-304">To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="bc2e7-305">Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah do té doby, než `localStorage` `sessionStorage` je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="bc2e7-306">Chcete-li zakázat předvykreslování, otevřete `Pages/_Host.cshtml` soubor a změňte `render-mode` atribut [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="bc2e7-307">Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` nebo `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="bc2e7-308">Chcete-li zachovat předvykreslování, odložte operaci načítání, dokud není prohlížeč připojen k okruhu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="bc2e7-309">Následuje příklad uložení hodnoty čítače:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="bc2e7-310">Rozložte zachování stavu na společné místo.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="bc2e7-311">Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="bc2e7-312">Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="bc2e7-313">Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="bc2e7-314">V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="bc2e7-315">`CounterStateProvider`Komponenta zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="bc2e7-316">Chcete-li použít `CounterStateProvider` komponentu, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="bc2e7-317">Chcete-li zpřístupnit stav pro všechny komponenty v aplikaci, zabalte `CounterStateProvider` komponentu kolem komponenty <xref:Microsoft.AspNetCore.Components.Routing.Router> v `App` součásti ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="bc2e7-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="bc2e7-318">Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="bc2e7-319">`Counter`Vzor implementuje následující součást:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-319">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="bc2e7-320">Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage` , ani nefunguje se fází "načítání".</span><span class="sxs-lookup"><span data-stu-id="bc2e7-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="bc2e7-321">Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze upravit tak, aby všechny součásti, které spotřebovávají data čítače, pracovaly automaticky s předem vykreslením.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="bc2e7-322">Další informace najdete v části [zpracování předvykreslování](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="bc2e7-323">Obecně se doporučuje vzor *nadřazené komponenty zprostředkovatele stavu* :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="bc2e7-324">Pro využívání stavu napříč mnoha komponentami.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-324">To consume state across many components.</span></span>
* <span data-ttu-id="bc2e7-325">Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="bc2e7-326">Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší vyhnout se zachování stavu globálně.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="bc2e7-327">Experimentální balíček NuGet pro úložiště chráněný prohlížečem</span><span class="sxs-lookup"><span data-stu-id="bc2e7-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="bc2e7-328">ASP.NET Core chráněné úložiště prohlížeče využívá [ASP.NET Core ochrany dat](xref:security/data-protection/introduction) pro [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) a [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="bc2e7-329">`Microsoft.AspNetCore.ProtectedBrowserStorage`je nepodporovaný, experimentální balíček nevhodný pro produkční použití.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="bc2e7-330">Balíček je dostupný jenom v aplikacích ASP.NET Core 3,1 Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-330">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="bc2e7-331">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="bc2e7-331">Configuration</span></span>

1. <span data-ttu-id="bc2e7-332">Přidejte odkaz na balíček do [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="bc2e7-333">Do tohoto `Pages/_Host.cshtml` souboru přidejte do uzavírací značky tento skript `</body>` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="bc2e7-334">V `Startup.ConfigureServices` volejte volání metody `AddProtectedBrowserStorage` Add `localStorage` a `sessionStorage` Services do kolekce služeb:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="bc2e7-335">Ukládání a načítání dat v rámci součásti</span><span class="sxs-lookup"><span data-stu-id="bc2e7-335">Save and load data within a component</span></span>

<span data-ttu-id="bc2e7-336">V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:mvc/views/razor#inject) direktivu pro vložení instance některého z následujících prvků:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="bc2e7-337">Volba závisí na tom, jaké umístění úložiště prohlížeče chcete použít.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="bc2e7-338">V následujícím příkladu `sessionStorage` se používá:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="bc2e7-339">`@using`Příkaz lze umístit do `_Imports.razor` souboru místo v součásti.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="bc2e7-340">Použití `_Imports.razor` souboru zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="bc2e7-341">Chcete-li zachovat `currentCount` hodnotu v `Counter` součásti aplikace na základě Blazor Server šablony projektu, upravte `IncrementCount` metodu, kterou chcete použít `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-341">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="bc2e7-342">Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="bc2e7-343">Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="bc2e7-344">`ProtectedSessionStore`automaticky serializace a deserializace dat JSON.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="bc2e7-345">V předchozím příkladu kódu `currentCount` se data ukládají jako `sessionStorage['count']` v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="bc2e7-346">Data nejsou uložená v prostém textu, ale místo toho jsou chráněná pomocí ASP.NET Core ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="bc2e7-347">Zašifrovaná data lze zkontrolovat, pokud `sessionStorage['count']` je vyhodnocena v konzole pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="bc2e7-348">Chcete-li obnovit `currentCount` data, pokud se uživatel vrátí do `Counter` komponenty později, včetně toho, jestli se nachází na zcela novém okruhu, použijte `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="bc2e7-349">Pokud parametry komponenty obsahují stav navigace, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte výsledek v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nikoli <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="bc2e7-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>se volá pouze jednou při prvním vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="bc2e7-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>není voláno později, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="bc2e7-352">Další informace naleznete v tématu <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="bc2e7-353">Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="bc2e7-354">Pokud je povoleno předvykreslování, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="bc2e7-355">Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="bc2e7-356">Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="bc2e7-357">Zpracování stavu načítání</span><span class="sxs-lookup"><span data-stu-id="bc2e7-357">Handle the loading state</span></span>

<span data-ttu-id="bc2e7-358">Vzhledem k tomu, že je úložiště prohlížeče přístupné asynchronně přes síťové připojení, je vždy časový interval před načtením dat a k dispozici pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="bc2e7-359">Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="bc2e7-360">Jedním z těchto způsobů je sledovat, jestli data jsou `null` , což znamená, že se data pořád načítají.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="bc2e7-361">Ve výchozí `Counter` součásti je počet uchováván v `int` .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="bc2e7-362">Převést na typ s [ `currentCount` možnou hodnotou null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) přidáním otazníku ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="bc2e7-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="bc2e7-363">Namísto nepodmíněného zobrazení **`Increment`** hodnoty a tlačítka vyberte možnost zobrazit tyto prvky pouze v případě, že jsou data načtena:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="bc2e7-364">Zpracovat předvykreslování</span><span class="sxs-lookup"><span data-stu-id="bc2e7-364">Handle prerendering</span></span>

<span data-ttu-id="bc2e7-365">Během předvykreslování:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-365">During prerendering:</span></span>

* <span data-ttu-id="bc2e7-366">Interaktivní připojení k prohlížeči uživatele neexistuje.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="bc2e7-367">Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="bc2e7-368">`localStorage`nebo `sessionStorage` nejsou během předvykreslování k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="bc2e7-369">Pokud se komponenta pokusí pracovat s úložištěm, je vygenerována chyba s vysvětlením, že volání interoperability JavaScriptu nelze vydat, protože součást je předem vykreslena.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="bc2e7-370">Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="bc2e7-371">To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="bc2e7-372">Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah do té doby, než `localStorage` `sessionStorage` je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="bc2e7-373">Chcete-li zakázat předvykreslování, otevřete `Pages/_Host.cshtml` soubor a změňte `render-mode` atribut [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="bc2e7-374">Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` nebo `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="bc2e7-375">Chcete-li zachovat předvykreslování, odložte operaci načítání, dokud není prohlížeč připojen k okruhu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="bc2e7-376">Následuje příklad uložení hodnoty čítače:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-376">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="bc2e7-377">Rozložte zachování stavu na společné místo.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="bc2e7-378">Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="bc2e7-379">Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="bc2e7-380">Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="bc2e7-381">V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="bc2e7-382">`CounterStateProvider`Komponenta zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="bc2e7-383">Chcete-li použít `CounterStateProvider` komponentu, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="bc2e7-384">Chcete-li zpřístupnit stav pro všechny komponenty v aplikaci, zabalte `CounterStateProvider` komponentu kolem komponenty <xref:Microsoft.AspNetCore.Components.Routing.Router> v `App` součásti ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="bc2e7-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="bc2e7-385">Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="bc2e7-386">`Counter`Vzor implementuje následující součást:</span><span class="sxs-lookup"><span data-stu-id="bc2e7-386">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="bc2e7-387">Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage` , ani nefunguje se fází "načítání".</span><span class="sxs-lookup"><span data-stu-id="bc2e7-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="bc2e7-388">Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze upravit tak, aby všechny součásti, které spotřebovávají data čítače, pracovaly automaticky s předem vykreslením.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="bc2e7-389">Další informace najdete v části [zpracování předvykreslování](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="bc2e7-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="bc2e7-390">Obecně se doporučuje model *nadřazené komponenty zprostředkovatele stavu* :</span><span class="sxs-lookup"><span data-stu-id="bc2e7-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="bc2e7-391">Pro využívání stavu napříč mnoha komponentami.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-391">To consume state across many components.</span></span>
* <span data-ttu-id="bc2e7-392">Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="bc2e7-393">Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší vyhnout se zachování stavu globálně.</span><span class="sxs-lookup"><span data-stu-id="bc2e7-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: zone-end
