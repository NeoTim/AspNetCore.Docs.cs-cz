---
title: Správa stavu ASP.NET Core Blazor
author: guardrex
description: Přečtěte si, jak zachovat stav v aplikacích Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218866"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="70ac6-103">Správa stavu ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="70ac6-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="70ac6-104">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="70ac6-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="70ac6-105">Server Blazor je stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="70ac6-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="70ac6-106">Ve většině případů aplikace udržuje průběžné připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="70ac6-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="70ac6-107">Stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="70ac6-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="70ac6-108">Příkladem stavu drženého pro okruh uživatele jsou:</span><span class="sxs-lookup"><span data-stu-id="70ac6-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="70ac6-109">Vykreslené uživatelské rozhraní&mdash;hierarchii instancí komponent a jejich poslední výstup vykreslování.</span><span class="sxs-lookup"><span data-stu-id="70ac6-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="70ac6-110">Hodnoty všech polí a vlastností v instancích součástí.</span><span class="sxs-lookup"><span data-stu-id="70ac6-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="70ac6-111">Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , která jsou vymezena okruhu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="70ac6-112">Tento článek řeší trvalost stavu v Blazor serverových aplikacích.</span><span class="sxs-lookup"><span data-stu-id="70ac6-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="70ac6-113"> aplikace WebAssembly můžou využít [trvalost stavu na straně klienta v prohlížeči](#client-side-in-the-browser) , ale kromě rozsahu tohoto článku vyžadují vlastní řešení nebo balíčky třetích stran.</span><span class="sxs-lookup"><span data-stu-id="70ac6-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a><span data-ttu-id="70ac6-114">okruhy Blazor</span><span class="sxs-lookup"><span data-stu-id="70ac6-114">Blazor circuits</span></span>

<span data-ttu-id="70ac6-115">Pokud dojde ke ztrátě dočasného připojení k síti, Blazor se pokusí uživatele znovu připojit k původnímu okruhu, aby mohli i nadále používat aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70ac6-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="70ac6-116">Ale opětovné připojení uživatele k původnímu okruhu v paměti serveru není vždycky možné:</span><span class="sxs-lookup"><span data-stu-id="70ac6-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="70ac6-117">Server nemůže trvale zachovávat odpojený okruh.</span><span class="sxs-lookup"><span data-stu-id="70ac6-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="70ac6-118">Server musí uvolnit odpojený okruh po vypršení časového limitu nebo v případě, že je server pod tlakem paměti.</span><span class="sxs-lookup"><span data-stu-id="70ac6-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="70ac6-119">V prostředích s více servery nasazení s vyrovnáváním zatížení můžou všechny požadavky na zpracování serveru být v daném okamžiku nedostupné.</span><span class="sxs-lookup"><span data-stu-id="70ac6-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="70ac6-120">Jednotlivé servery můžou selhat nebo automaticky odebrat, pokud už nepotřebujete pro zpracování celkového objemu požadavků.</span><span class="sxs-lookup"><span data-stu-id="70ac6-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="70ac6-121">Původní server nemusí být k dispozici, když se uživatel pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="70ac6-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="70ac6-122">Uživatel může zavřít a znovu otevřít prohlížeč nebo znovu načíst stránku, což odstraní všechny stavy uchovávané v paměti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="70ac6-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="70ac6-123">Například hodnoty nastavené prostřednictvím volání interoperability JavaScriptu jsou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="70ac6-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="70ac6-124">Když se uživatel nedá znovu připojit k původnímu okruhu, uživatel dostane nový okruh s prázdným stavem.</span><span class="sxs-lookup"><span data-stu-id="70ac6-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="70ac6-125">Jedná se o ekvivalent zavření a opětovného otevření desktopové aplikace.</span><span class="sxs-lookup"><span data-stu-id="70ac6-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="70ac6-126">Zachovat stav napříč okruhy</span><span class="sxs-lookup"><span data-stu-id="70ac6-126">Preserve state across circuits</span></span>

<span data-ttu-id="70ac6-127">V některých scénářích je žádoucí zachovat stav napříč okruhy.</span><span class="sxs-lookup"><span data-stu-id="70ac6-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="70ac6-128">Aplikace může uchovávat důležitá data pro uživatele, pokud:</span><span class="sxs-lookup"><span data-stu-id="70ac6-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="70ac6-129">Webový server bude nedostupný.</span><span class="sxs-lookup"><span data-stu-id="70ac6-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="70ac6-130">V prohlížeči uživatele je nucen spustit nový okruh s novým webovým serverem.</span><span class="sxs-lookup"><span data-stu-id="70ac6-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="70ac6-131">Obecně platí, že udržování stavu napříč okruhy se vztahuje na scénáře, kdy uživatelé aktivně vytvářejí data, nikoli jenom čtení dat, která už existují.</span><span class="sxs-lookup"><span data-stu-id="70ac6-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="70ac6-132">Chcete-li zachovat stav nad rámec jednoho okruhu, *neukládejte pouze data v paměti serveru*.</span><span class="sxs-lookup"><span data-stu-id="70ac6-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="70ac6-133">Aplikace musí uchovávat data do jiného umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="70ac6-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="70ac6-134">Trvalost stavu není automaticky&mdash;musíte provést kroky při vývoji aplikace pro implementaci stavového uchovávání dat.</span><span class="sxs-lookup"><span data-stu-id="70ac6-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="70ac6-135">Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit.</span><span class="sxs-lookup"><span data-stu-id="70ac6-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="70ac6-136">V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:</span><span class="sxs-lookup"><span data-stu-id="70ac6-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="70ac6-137">Webformu ve více krocích &ndash; pro uživatele časově náročné, aby znovu zadal data pro několik dokončených kroků procesu s více kroky, pokud dojde ke ztrátě jejich stavu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="70ac6-138">Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře s více kroky a později se vrátí do formuláře.</span><span class="sxs-lookup"><span data-stu-id="70ac6-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="70ac6-139">Nákupní košík &ndash; všech komerčních důležitých komponent aplikace, které představují potenciální výnosy, se dají udržovat.</span><span class="sxs-lookup"><span data-stu-id="70ac6-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="70ac6-140">Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.</span><span class="sxs-lookup"><span data-stu-id="70ac6-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="70ac6-141">Obvykle není nutné zachovávat snadno-znovu vytvořený stav, jako je například uživatelské jméno zadané do přihlašovacího dialogu, který nebyl odeslán.</span><span class="sxs-lookup"><span data-stu-id="70ac6-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="70ac6-142">Aplikace může zachovat jenom *stav aplikace*.</span><span class="sxs-lookup"><span data-stu-id="70ac6-142">An app can only persist *app state*.</span></span> <span data-ttu-id="70ac6-143">Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování.</span><span class="sxs-lookup"><span data-stu-id="70ac6-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="70ac6-144">Komponenty a stromy vykreslování nejsou obecně serializovatelné.</span><span class="sxs-lookup"><span data-stu-id="70ac6-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="70ac6-145">Aby bylo možné zachovat podobný text jako stav uživatelského rozhraní, jako jsou například rozbalené uzly prvku TreeView, aplikace musí mít vlastní kód pro modelování chování jako serializovatelný stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="70ac6-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="70ac6-146">Kam zachovat stav</span><span class="sxs-lookup"><span data-stu-id="70ac6-146">Where to persist state</span></span>

<span data-ttu-id="70ac6-147">Existují tři společná umístění pro trvalý stav v aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="70ac6-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="70ac6-148">Každý přístup nejlépe vyhovuje různým scénářům a má odlišná omezení:</span><span class="sxs-lookup"><span data-stu-id="70ac6-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="70ac6-149">Na straně serveru v databázi</span><span class="sxs-lookup"><span data-stu-id="70ac6-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="70ac6-150">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="70ac6-150">URL</span></span>](#url)
* [<span data-ttu-id="70ac6-151">Klientská strana v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="70ac6-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="70ac6-152">Na straně serveru v databázi</span><span class="sxs-lookup"><span data-stu-id="70ac6-152">Server-side in a database</span></span>

<span data-ttu-id="70ac6-153">Pro trvalá trvalá data nebo pro všechna data, která musí být rozložená na více uživatelů nebo zařízení, je k nejlepší možnost databáze na straně serveru téměř určitě.</span><span class="sxs-lookup"><span data-stu-id="70ac6-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="70ac6-154">Mezi možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="70ac6-154">Options include:</span></span>

* <span data-ttu-id="70ac6-155">Relační databáze SQL</span><span class="sxs-lookup"><span data-stu-id="70ac6-155">Relational SQL database</span></span>
* <span data-ttu-id="70ac6-156">Ukládání hodnot klíče</span><span class="sxs-lookup"><span data-stu-id="70ac6-156">Key-value store</span></span>
* <span data-ttu-id="70ac6-157">Úložiště objektů BLOB</span><span class="sxs-lookup"><span data-stu-id="70ac6-157">Blob store</span></span>
* <span data-ttu-id="70ac6-158">Úložiště tabulek</span><span class="sxs-lookup"><span data-stu-id="70ac6-158">Table store</span></span>

<span data-ttu-id="70ac6-159">Po uložení dat v databázi může nový okruh spustit uživatel kdykoli.</span><span class="sxs-lookup"><span data-stu-id="70ac6-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="70ac6-160">Data uživatele jsou zachována a k dispozici v jakémkoli novém okruhu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="70ac6-161">Další informace o možnostech Azure Data Storage najdete v [dokumentaci Azure Storage](/azure/storage/) a v [databázích Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="70ac6-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="70ac6-162">zprostředkovatele identity</span><span class="sxs-lookup"><span data-stu-id="70ac6-162">URL</span></span>

<span data-ttu-id="70ac6-163">Pro přechodná data představující stav navigace modelujte data jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="70ac6-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="70ac6-164">Mezi příklady stavu zahrnutých v adrese URL patří:</span><span class="sxs-lookup"><span data-stu-id="70ac6-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="70ac6-165">ID zobrazené entity</span><span class="sxs-lookup"><span data-stu-id="70ac6-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="70ac6-166">Aktuální číslo stránky v mřížce na stránkovaném webu</span><span class="sxs-lookup"><span data-stu-id="70ac6-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="70ac6-167">Obsah panelu Adresa prohlížeče se zachová:</span><span class="sxs-lookup"><span data-stu-id="70ac6-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="70ac6-168">Pokud uživatel ručně znovu načte stránku.</span><span class="sxs-lookup"><span data-stu-id="70ac6-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="70ac6-169">Pokud dojde k nedostupnosti webového serveru&mdash;bude uživatel muset stránku znovu načíst, aby se mohl připojit k jinému serveru.</span><span class="sxs-lookup"><span data-stu-id="70ac6-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="70ac6-170">Informace o definování vzorů adres URL pomocí direktivy `@page` naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="70ac6-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="70ac6-171">Klientská strana v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="70ac6-171">Client-side in the browser</span></span>

<span data-ttu-id="70ac6-172">Pro přechodná data, která uživatel aktivně vytváří, je běžným záložním úložištěm `localStorage` a `sessionStorage` kolekce v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="70ac6-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="70ac6-173">Pokud dojde k opuštění okruhu, nepotřebuje aplikace spravovat nebo vymazat uložený stav, což je výhodou pro úložiště na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="70ac6-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="70ac6-174">"Klientská strana" v této části odkazuje na scénáře na straně klienta v prohlížeči, nikoli na [model hostováníBlazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="70ac6-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="70ac6-175">`localStorage` a `sessionStorage` lze použít v aplikacích Blazor WebAssembly, ale pouze psaním vlastního kódu nebo pomocí balíčku třetí strany.</span><span class="sxs-lookup"><span data-stu-id="70ac6-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="70ac6-176">`localStorage` a `sessionStorage` se liší následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="70ac6-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="70ac6-177">`localStorage` je vymezen v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="70ac6-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="70ac6-178">Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává.</span><span class="sxs-lookup"><span data-stu-id="70ac6-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="70ac6-179">Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="70ac6-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="70ac6-180">Data v `localStorage` přetrvají, dokud je explicitně neodstraníte.</span><span class="sxs-lookup"><span data-stu-id="70ac6-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="70ac6-181">`sessionStorage` je vymezen na kartu prohlížeče uživatele. Pokud uživatel znovu načte kartu, stav se přetrvá.</span><span class="sxs-lookup"><span data-stu-id="70ac6-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="70ac6-182">Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="70ac6-183">Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.</span><span class="sxs-lookup"><span data-stu-id="70ac6-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="70ac6-184">Obecně je `sessionStorage` bezpečnější použít.</span><span class="sxs-lookup"><span data-stu-id="70ac6-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="70ac6-185">`sessionStorage` se vyhnete riziku, že uživatel otevře více karet a narazí na následující:</span><span class="sxs-lookup"><span data-stu-id="70ac6-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="70ac6-186">Chyby v úložišti stavů napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="70ac6-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="70ac6-187">Matoucí chování, když karta Přepisuje stav jiných karet.</span><span class="sxs-lookup"><span data-stu-id="70ac6-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="70ac6-188">`localStorage` je lepší volbou v případě, že aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="70ac6-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="70ac6-189">Upozornění pro použití úložiště prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="70ac6-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="70ac6-190">Podobně jako při použití databáze na straně serveru je načítání a ukládání dat asynchronní.</span><span class="sxs-lookup"><span data-stu-id="70ac6-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="70ac6-191">Na rozdíl od databáze na straně serveru není úložiště během předgenerování k dispozici, protože požadovaná stránka v prohlížeči neexistuje během fáze předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="70ac6-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="70ac6-192">Úložiště několika kilobajtů dat je pro Blazor serverových aplikací přijatelné.</span><span class="sxs-lookup"><span data-stu-id="70ac6-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="70ac6-193">Po několika kilobajtech je potřeba vzít v úvahu dopad na výkon, protože data se načítají a ukládají v síti.</span><span class="sxs-lookup"><span data-stu-id="70ac6-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="70ac6-194">Uživatelé můžou data zobrazit nebo s nimi manipulovat.</span><span class="sxs-lookup"><span data-stu-id="70ac6-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="70ac6-195">[Ochrana dat](xref:security/data-protection/introduction) ASP.NET Core může riziko zmírnit.</span><span class="sxs-lookup"><span data-stu-id="70ac6-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="70ac6-196">Řešení úložiště v prohlížeči třetích stran</span><span class="sxs-lookup"><span data-stu-id="70ac6-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="70ac6-197">Balíčky NuGet třetích stran poskytují rozhraní API pro práci s `localStorage` a `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="70ac6-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="70ac6-198">Je vhodné zvážit výběr balíčku, který transparentně používá [ochranu dat](xref:security/data-protection/introduction)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70ac6-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="70ac6-199">ASP.NET Core ochrana dat šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty.</span><span class="sxs-lookup"><span data-stu-id="70ac6-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="70ac6-200">Pokud jsou data serializovaná ve formátu JSON uložená ve formě prostého textu, uživatelé můžou data zobrazit pomocí vývojářských nástrojů pro prohlížeč a také upravovat uložená data.</span><span class="sxs-lookup"><span data-stu-id="70ac6-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="70ac6-201">Zabezpečení dat se vždy nejedná o problém, protože data můžou být triviální v podstatě.</span><span class="sxs-lookup"><span data-stu-id="70ac6-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="70ac6-202">Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významným bezpečnostním rizikem pro uživatele nebo organizaci.</span><span class="sxs-lookup"><span data-stu-id="70ac6-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="70ac6-203">Neumožňuje uživatelům kontrolovat *citlivá data*a manipulovat s nimi.</span><span class="sxs-lookup"><span data-stu-id="70ac6-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="70ac6-204">Experimentální balíček chráněného úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="70ac6-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="70ac6-205">Příklad balíčku NuGet, který poskytuje [ochranu dat](xref:security/data-protection/introduction) pro `localStorage` a `sessionStorage` je [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="70ac6-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="70ac6-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` je nepodporovaný experimentální balíček nevhodný pro použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="70ac6-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="70ac6-207">Instalace</span><span class="sxs-lookup"><span data-stu-id="70ac6-207">Installation</span></span>

<span data-ttu-id="70ac6-208">Instalace balíčku `Microsoft.AspNetCore.ProtectedBrowserStorage`:</span><span class="sxs-lookup"><span data-stu-id="70ac6-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="70ac6-209">V projektu Blazor server aplikace přidejte odkaz na balíček do [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="70ac6-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="70ac6-210">V HTML nejvyšší úrovně (například v souboru *Pages/_Host. cshtml* ve výchozí šabloně projektu) přidejte následující značku `<script>`:</span><span class="sxs-lookup"><span data-stu-id="70ac6-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="70ac6-211">V metodě `Startup.ConfigureServices` volejte `AddProtectedBrowserStorage` a přidejte `localStorage` a `sessionStorage` služby do kolekce služeb:</span><span class="sxs-lookup"><span data-stu-id="70ac6-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="70ac6-212">Ukládání a načítání dat v rámci součásti</span><span class="sxs-lookup"><span data-stu-id="70ac6-212">Save and load data within a component</span></span>

<span data-ttu-id="70ac6-213">V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) pro vložení instance některého z následujících prvků:</span><span class="sxs-lookup"><span data-stu-id="70ac6-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="70ac6-214">Volba závisí na tom, které záložní úložiště chcete použít.</span><span class="sxs-lookup"><span data-stu-id="70ac6-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="70ac6-215">V následujícím příkladu se používá `sessionStorage`:</span><span class="sxs-lookup"><span data-stu-id="70ac6-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="70ac6-216">Příkaz `@using` lze umístit do souboru *_Imports. Razor* místo do součásti.</span><span class="sxs-lookup"><span data-stu-id="70ac6-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="70ac6-217">Použití souboru *_Imports. Razor* zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="70ac6-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="70ac6-218">Chcete-li zachovat hodnotu `_currentCount` v součásti `Counter` šablony projektu, upravte metodu `IncrementCount` na použití `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="70ac6-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="70ac6-219">Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné.</span><span class="sxs-lookup"><span data-stu-id="70ac6-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="70ac6-220">Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav.</span><span class="sxs-lookup"><span data-stu-id="70ac6-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="70ac6-221">`ProtectedSessionStore` automaticky serializovat a deserializovat data JSON.</span><span class="sxs-lookup"><span data-stu-id="70ac6-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="70ac6-222">V předchozím příkladu kódu se `_currentCount` data ukládají jako `sessionStorage['count']` v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="70ac6-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="70ac6-223">Data nejsou uložená ve formátu prostého textu, ale jsou chráněná pomocí [ochrany dat](xref:security/data-protection/introduction)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="70ac6-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="70ac6-224">Zašifrovaná data lze zobrazit, pokud je `sessionStorage['count']` vyhodnocována v konzole pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="70ac6-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="70ac6-225">Chcete-li obnovit `_currentCount` data v případě, že se uživatel vrátí do `Counter` komponenty později (včetně, pokud jsou na zcela novém okruhu), použijte `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="70ac6-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="70ac6-226">Pokud parametry komponenty obsahují navigační stav, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte výsledek v `OnParametersSetAsync`, nikoli `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="70ac6-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="70ac6-227">`OnInitializedAsync` se volá pouze jednou při prvním vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="70ac6-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="70ac6-228">`OnInitializedAsync` se později nevolá, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce.</span><span class="sxs-lookup"><span data-stu-id="70ac6-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="70ac6-229">Další informace naleznete v tématu <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="70ac6-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="70ac6-230">Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="70ac6-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="70ac6-231">Pokud je povoleno předvykreslování, je vygenerována chyba podobná této:</span><span class="sxs-lookup"><span data-stu-id="70ac6-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="70ac6-232">V tuto chvíli nelze vydat volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="70ac6-233">Důvodem je to, že se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="70ac6-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="70ac6-234">Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="70ac6-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="70ac6-235">Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="70ac6-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="70ac6-236">Zpracování stavu načítání</span><span class="sxs-lookup"><span data-stu-id="70ac6-236">Handle the loading state</span></span>

<span data-ttu-id="70ac6-237">Vzhledem k tomu, že je úložiště prohlížeče asynchronní (přístup prostřednictvím připojení k síti), je vždy časový interval, než se data načtou a budou k dispozici pro použití komponentou.</span><span class="sxs-lookup"><span data-stu-id="70ac6-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="70ac6-238">Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.</span><span class="sxs-lookup"><span data-stu-id="70ac6-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="70ac6-239">Jedním z těchto způsobů je sledovat, jestli se data `null` (pořád se načítají), nebo ne.</span><span class="sxs-lookup"><span data-stu-id="70ac6-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="70ac6-240">Ve výchozí součásti `Counter` je počet uchováván v `int`.</span><span class="sxs-lookup"><span data-stu-id="70ac6-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="70ac6-241">Nastavit `_currentCount` s možnou hodnotou null přidáním otazníku (`?`) do typu (`int`):</span><span class="sxs-lookup"><span data-stu-id="70ac6-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="70ac6-242">Namísto nepodmíněného zobrazení tlačítka počet a **přírůstek** vyberte, zda chcete tyto prvky zobrazit pouze v případě, že jsou data načtena:</span><span class="sxs-lookup"><span data-stu-id="70ac6-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="70ac6-243">Zpracovat předvykreslování</span><span class="sxs-lookup"><span data-stu-id="70ac6-243">Handle prerendering</span></span>

<span data-ttu-id="70ac6-244">Během předvykreslování:</span><span class="sxs-lookup"><span data-stu-id="70ac6-244">During prerendering:</span></span>

* <span data-ttu-id="70ac6-245">Interaktivní připojení k prohlížeči uživatele neexistuje.</span><span class="sxs-lookup"><span data-stu-id="70ac6-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="70ac6-246">Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="70ac6-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="70ac6-247">`localStorage` nebo `sessionStorage` nejsou během předvykreslování k dispozici.</span><span class="sxs-lookup"><span data-stu-id="70ac6-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="70ac6-248">Pokud se komponenta pokusí o interakci s úložištěm, je vygenerována chyba podobná této:</span><span class="sxs-lookup"><span data-stu-id="70ac6-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="70ac6-249">V tuto chvíli nelze vydat volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="70ac6-250">Důvodem je to, že se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="70ac6-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="70ac6-251">Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="70ac6-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="70ac6-252">To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="70ac6-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="70ac6-253">Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah, dokud nebudou k dispozici `localStorage` nebo `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="70ac6-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="70ac6-254">Chcete-li zakázat předvykreslování, otevřete soubor *Pages/_Host. cshtml* a změňte `render-mode` [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="70ac6-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="70ac6-255">Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` ani `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="70ac6-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="70ac6-256">Aby bylo možné předvykreslování povolit, odložte operaci načtení, dokud se prohlížeč nepřipojí k okruhu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="70ac6-257">Následuje příklad uložení hodnoty čítače:</span><span class="sxs-lookup"><span data-stu-id="70ac6-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="70ac6-258">Rozložte zachování stavu na společné místo.</span><span class="sxs-lookup"><span data-stu-id="70ac6-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="70ac6-259">Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="70ac6-260">Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="70ac6-261">Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.</span><span class="sxs-lookup"><span data-stu-id="70ac6-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="70ac6-262">V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá:</span><span class="sxs-lookup"><span data-stu-id="70ac6-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
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
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="70ac6-263">Komponenta `CounterStateProvider` zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.</span><span class="sxs-lookup"><span data-stu-id="70ac6-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="70ac6-264">Chcete-li použít komponentu `CounterStateProvider`, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače.</span><span class="sxs-lookup"><span data-stu-id="70ac6-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="70ac6-265">Chcete-li zpřístupnit stav pro všechny součásti aplikace, zabalte `CounterStateProvider` komponentu kolem `Router` v součásti `App` (*App. Razor*):</span><span class="sxs-lookup"><span data-stu-id="70ac6-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="70ac6-266">Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače.</span><span class="sxs-lookup"><span data-stu-id="70ac6-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="70ac6-267">Následující součást `Counter` implementuje vzor:</span><span class="sxs-lookup"><span data-stu-id="70ac6-267">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="70ac6-268">Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage`, ani nefunguje se fází "načítání".</span><span class="sxs-lookup"><span data-stu-id="70ac6-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="70ac6-269">Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze změnit tak, aby všechny komponenty, které data čítače spotřebují automaticky, pracovaly s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="70ac6-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="70ac6-270">Podrobnosti najdete v části o [předvykreslování popisovače](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="70ac6-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="70ac6-271">Obecně se doporučuje model *nadřazené komponenty zprostředkovatele stavu* :</span><span class="sxs-lookup"><span data-stu-id="70ac6-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="70ac6-272">Pro využívání stavu v mnoha dalších součástech.</span><span class="sxs-lookup"><span data-stu-id="70ac6-272">To consume state in many other components.</span></span>
* <span data-ttu-id="70ac6-273">Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.</span><span class="sxs-lookup"><span data-stu-id="70ac6-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="70ac6-274">Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší se vyhnout zpracování načítání a ukládání stavu globálně.</span><span class="sxs-lookup"><span data-stu-id="70ac6-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
