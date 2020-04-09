---
title: ASP.NET Blazor Správa hlavního státu
author: guardrex
description: Přečtěte si, Blazor jak zachovat stav v serverových aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218866"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="97856-103">ASP.NET Blazor Správa hlavního státu</span><span class="sxs-lookup"><span data-stu-id="97856-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="97856-104">Podle [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="97856-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="97856-105">Server je stavové rozhraní aplikace.</span><span class="sxs-lookup"><span data-stu-id="97856-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="97856-106">Ve většině času aplikace udržuje trvalé připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="97856-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="97856-107">Stav uživatele je uložen v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="97856-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="97856-108">Příklady stavu drženého pro okruh uživatele:</span><span class="sxs-lookup"><span data-stu-id="97856-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="97856-109">Rendrované uI&mdash;hierarchie instancí komponent a jejich nejnovější výstup vykreslení.</span><span class="sxs-lookup"><span data-stu-id="97856-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="97856-110">Hodnoty všech polí a vlastností v instancích komponent.</span><span class="sxs-lookup"><span data-stu-id="97856-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="97856-111">Data uchovávaná v instancích [služby vkládání závislostí (DI),](xref:fundamentals/dependency-injection) které jsou vymezeny na okruh.</span><span class="sxs-lookup"><span data-stu-id="97856-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="97856-112">Tento článek se zabývá Blazor trvaloststavu v serverových aplikacích.</span><span class="sxs-lookup"><span data-stu-id="97856-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="97856-113">Aplikace WebAssembly můžete využít [trvalost stavu na straně klienta v prohlížeči,](#client-side-in-the-browser) ale vyžadují vlastní řešení nebo balíčky třetích stran nad rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="97856-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="97856-114">Obvody</span><span class="sxs-lookup"><span data-stu-id="97856-114"> circuits</span></span>

<span data-ttu-id="97856-115">Pokud uživatel dojde ke ztrátě Blazor dočasného připojení k síti, pokusí se znovu připojit uživatele k původnímu okruhu, aby mohl pokračovat v používání aplikace.</span><span class="sxs-lookup"><span data-stu-id="97856-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="97856-116">Opětovné připojení uživatele k původnímu okruhu v paměti serveru však není vždy možné:</span><span class="sxs-lookup"><span data-stu-id="97856-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="97856-117">Server nemůže udržet odpojený obvod navždy.</span><span class="sxs-lookup"><span data-stu-id="97856-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="97856-118">Server musí uvolnit odpojený obvod po uplynutí časového omezení nebo po nedostatku paměti.</span><span class="sxs-lookup"><span data-stu-id="97856-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="97856-119">V prostředích nasazení s vyrovnáváním zatížení s vyrovnáváním zatížení mohou být všechny požadavky na zpracování serveru v daném okamžiku nedostupné.</span><span class="sxs-lookup"><span data-stu-id="97856-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="97856-120">Jednotlivé servery mohou selhat nebo být automaticky odebrány, pokud již není nutné zpracovávat celkový objem požadavků.</span><span class="sxs-lookup"><span data-stu-id="97856-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="97856-121">Původní server nemusí být k dispozici, pokud se uživatel pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="97856-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="97856-122">Uživatel může zavřít a znovu otevřít svůj prohlížeč nebo znovu načíst stránku, která odebere jakýkoli stav, který je v paměti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="97856-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="97856-123">Například hodnoty nastavené prostřednictvím interop volání JavaScriptu jsou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="97856-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="97856-124">Když uživatel nemůže být znovu připojen k původnímu okruhu, obdrží nový okruh s prázdným stavem.</span><span class="sxs-lookup"><span data-stu-id="97856-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="97856-125">To je ekvivalentní k zavření a opětovnému otevření aplikace klasické pracovní plochy.</span><span class="sxs-lookup"><span data-stu-id="97856-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="97856-126">Zachovat stav napříč obvody</span><span class="sxs-lookup"><span data-stu-id="97856-126">Preserve state across circuits</span></span>

<span data-ttu-id="97856-127">V některých scénářích zachování stavu napříč obvody je žádoucí.</span><span class="sxs-lookup"><span data-stu-id="97856-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="97856-128">Aplikace může uchovávat důležitá data pro uživatele, pokud:</span><span class="sxs-lookup"><span data-stu-id="97856-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="97856-129">Webový server přestane být k dispozici.</span><span class="sxs-lookup"><span data-stu-id="97856-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="97856-130">Prohlížeč uživatele je nucen spustit nový okruh s novým webovým serverem.</span><span class="sxs-lookup"><span data-stu-id="97856-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="97856-131">Obecně platí, že udržování stavu napříč okruhy platí pro scénáře, kde uživatelé aktivně vytvářejí data, nikoli pouze čtení dat, která již existuje.</span><span class="sxs-lookup"><span data-stu-id="97856-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="97856-132">Chcete-li zachovat stav mimo jeden okruh, *neuklápěte pouze data do paměti serveru*.</span><span class="sxs-lookup"><span data-stu-id="97856-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="97856-133">Aplikace musí zachovat data do jiného umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="97856-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="97856-134">Trvalosti stavu není&mdash;automatické, musíte provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.</span><span class="sxs-lookup"><span data-stu-id="97856-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="97856-135">Trvalost dat je obvykle vyžadována pouze pro stav s vysokou hodnotou, který uživatelé vynaložili úsilí k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="97856-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="97856-136">V následujících příkladech zachování stavu buď šetří čas nebo pomáhá v komerčních aktivitách:</span><span class="sxs-lookup"><span data-stu-id="97856-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="97856-137">Webform &ndash; vícekroků Je časově náročné, aby uživatel znovu zadá data pro několik dokončených kroků vícekrokového procesu, pokud dojde ke ztrátě jejich stavu.</span><span class="sxs-lookup"><span data-stu-id="97856-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="97856-138">Uživatel ztratí stav v tomto scénáři, pokud přejít od formuláře více kroků a vrátit se do formuláře později.</span><span class="sxs-lookup"><span data-stu-id="97856-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="97856-139">Nákupní &ndash; košík Lze udržovat jakoukoli komerčně důležitou součást aplikace, která představuje potenciální výnosy.</span><span class="sxs-lookup"><span data-stu-id="97856-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="97856-140">Uživatel, který ztratí svůj stav, a tedy i nákupní košík, může po návratu na web později zakoupit méně produktů nebo služeb.</span><span class="sxs-lookup"><span data-stu-id="97856-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="97856-141">Obvykle není nutné zachovat snadno znovu vytvořený stav, například uživatelské jméno zadané do přihlašovacího dialogového okna, které nebylo odesláno.</span><span class="sxs-lookup"><span data-stu-id="97856-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="97856-142">Aplikace může zachovat pouze *stav aplikace*.</span><span class="sxs-lookup"><span data-stu-id="97856-142">An app can only persist *app state*.</span></span> <span data-ttu-id="97856-143">Ui nelze trvalé, jako jsou instance komponent a jejich vykreslení stromy.</span><span class="sxs-lookup"><span data-stu-id="97856-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="97856-144">Součásti a vykreslovací stromy nejsou obecně serializovatelné.</span><span class="sxs-lookup"><span data-stu-id="97856-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="97856-145">Chcete-li zachovat něco podobného stavu ui, jako jsou rozšířené uzly TreeView, aplikace musí mít vlastní kód pro modelování chování jako serializovatelný stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="97856-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="97856-146">Kde přetrvávat stav</span><span class="sxs-lookup"><span data-stu-id="97856-146">Where to persist state</span></span>

<span data-ttu-id="97856-147">Existují tři běžná umístění pro Blazor trvalý stav v aplikaci Server.</span><span class="sxs-lookup"><span data-stu-id="97856-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="97856-148">Každý přístup je nejvhodnější pro různé scénáře a má různé upozornění:</span><span class="sxs-lookup"><span data-stu-id="97856-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="97856-149">Na straně serveru v databázi</span><span class="sxs-lookup"><span data-stu-id="97856-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="97856-150">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="97856-150">URL</span></span>](#url)
* [<span data-ttu-id="97856-151">Na straně klienta v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="97856-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="97856-152">Na straně serveru v databázi</span><span class="sxs-lookup"><span data-stu-id="97856-152">Server-side in a database</span></span>

<span data-ttu-id="97856-153">Pro trvalost trvalých dat nebo pro všechna data, která musí span více uživatelů nebo zařízení, nezávislé databáze na straně serveru je téměř jistě nejlepší volbou.</span><span class="sxs-lookup"><span data-stu-id="97856-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="97856-154">Mezi možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="97856-154">Options include:</span></span>

* <span data-ttu-id="97856-155">Relační databáze SQL</span><span class="sxs-lookup"><span data-stu-id="97856-155">Relational SQL database</span></span>
* <span data-ttu-id="97856-156">Ukládání hodnot klíče</span><span class="sxs-lookup"><span data-stu-id="97856-156">Key-value store</span></span>
* <span data-ttu-id="97856-157">Úložiště objektů blob</span><span class="sxs-lookup"><span data-stu-id="97856-157">Blob store</span></span>
* <span data-ttu-id="97856-158">Úložiště tabulek</span><span class="sxs-lookup"><span data-stu-id="97856-158">Table store</span></span>

<span data-ttu-id="97856-159">Po uložení dat do databáze může uživatel kdykoli spustit nový okruh.</span><span class="sxs-lookup"><span data-stu-id="97856-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="97856-160">Data uživatele jsou uchována a jsou k dispozici v každém novém okruhu.</span><span class="sxs-lookup"><span data-stu-id="97856-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="97856-161">Další informace o možnostech úložiště dat Azure najdete v tématu [Dokumentace k úložišti Azure](/azure/storage/) a Databáze [Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="97856-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="97856-162">zprostředkovatele identity</span><span class="sxs-lookup"><span data-stu-id="97856-162">URL</span></span>

<span data-ttu-id="97856-163">Pro přechodná data představující stav navigace modelujte data jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="97856-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="97856-164">Příklady stavu modelovaného v adrese URL:</span><span class="sxs-lookup"><span data-stu-id="97856-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="97856-165">ID zobrazené entity.</span><span class="sxs-lookup"><span data-stu-id="97856-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="97856-166">Aktuální číslo stránky ve stránkované mřížce.</span><span class="sxs-lookup"><span data-stu-id="97856-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="97856-167">Obsah adresního řádku prohlížeče zůstane zachován:</span><span class="sxs-lookup"><span data-stu-id="97856-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="97856-168">Pokud uživatel ručně znovu načte stránku.</span><span class="sxs-lookup"><span data-stu-id="97856-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="97856-169">Pokud webový server&mdash;přestane být k dispozici, je uživatel nucen znovu načíst stránku, aby se mohl připojit k jinému serveru.</span><span class="sxs-lookup"><span data-stu-id="97856-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="97856-170">Informace o definování vzorů adres `@page` URL pomocí <xref:blazor/routing>direktivy naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="97856-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="97856-171">Na straně klienta v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="97856-171">Client-side in the browser</span></span>

<span data-ttu-id="97856-172">Pro přechodná data, která uživatel aktivně vytváří, společné úložiště zálohování `localStorage` `sessionStorage` je prohlížeče a kolekce.</span><span class="sxs-lookup"><span data-stu-id="97856-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="97856-173">Aplikace není nutné spravovat nebo vymazat uložený stav, pokud je okruh opuštěný, což je výhoda oproti úložišti na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="97856-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="97856-174">"Na straně klienta" v této části odkazuje na scénáře na straně klienta v prohlížeči, nikoli [ Blazor webassembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="97856-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="97856-175">`localStorage`a `sessionStorage` lze jej Blazor použít v aplikacích WebAssembly, ale pouze napsáním vlastního kódu nebo použitím balíčku třetí strany.</span><span class="sxs-lookup"><span data-stu-id="97856-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="97856-176">`localStorage`a `sessionStorage` liší se takto:</span><span class="sxs-lookup"><span data-stu-id="97856-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="97856-177">`localStorage`je vymezen a to do prohlížeče uživatele.</span><span class="sxs-lookup"><span data-stu-id="97856-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="97856-178">Pokud uživatel znovu načte stránku nebo zavře a znovu otevře prohlížeč, stav přetrvává.</span><span class="sxs-lookup"><span data-stu-id="97856-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="97856-179">Pokud uživatel otevře více karet prohlížeče, stav je sdílen mezi kartami.</span><span class="sxs-lookup"><span data-stu-id="97856-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="97856-180">Data přetrvávají, `localStorage` dokud není explicitně vymazána.</span><span class="sxs-lookup"><span data-stu-id="97856-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="97856-181">`sessionStorage`je vymezen a to na kartu prohlížeče uživatele. Pokud uživatel znovu načte kartu, stav přetrvává.</span><span class="sxs-lookup"><span data-stu-id="97856-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="97856-182">Pokud uživatel zavře kartu nebo prohlížeč, stav se ztratí.</span><span class="sxs-lookup"><span data-stu-id="97856-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="97856-183">Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.</span><span class="sxs-lookup"><span data-stu-id="97856-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="97856-184">Obecně `sessionStorage` platí, že je bezpečnější používat.</span><span class="sxs-lookup"><span data-stu-id="97856-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="97856-185">`sessionStorage`zabraňuje riziku, že uživatel otevře více karet a narazí na následující:</span><span class="sxs-lookup"><span data-stu-id="97856-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="97856-186">Chyby ve stavu úložiště na kartách.</span><span class="sxs-lookup"><span data-stu-id="97856-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="97856-187">Matoucí chování, když karta přepíše stav ostatních karet.</span><span class="sxs-lookup"><span data-stu-id="97856-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="97856-188">`localStorage`je lepší volbou, pokud aplikace musí zachovat stav při zavírání a opětovném otevření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="97856-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="97856-189">Upozornění pro použití úložiště prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="97856-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="97856-190">Podobně jako použití databáze na straně serveru, načítání a ukládání dat jsou asynchronní.</span><span class="sxs-lookup"><span data-stu-id="97856-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="97856-191">Na rozdíl od databáze na straně serveru není úložiště během předběžného vykreslování k dispozici, protože požadovaná stránka neexistuje v prohlížeči během fáze předběžného vykreslování.</span><span class="sxs-lookup"><span data-stu-id="97856-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="97856-192">Úložiště několika kilobajtů dat je rozumné Blazor zachovat pro serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="97856-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="97856-193">Kromě několika kilobajtů je třeba zvážit důsledky pro výkon, protože data jsou načtena a uložena v síti.</span><span class="sxs-lookup"><span data-stu-id="97856-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="97856-194">Uživatelé mohou zobrazit data nebo s nimi manipulovat.</span><span class="sxs-lookup"><span data-stu-id="97856-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="97856-195">ASP.NET základní [ochrana dat](xref:security/data-protection/introduction) může zmírnit riziko.</span><span class="sxs-lookup"><span data-stu-id="97856-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="97856-196">Řešení pro ukládání prohlížečů jiných výrobců</span><span class="sxs-lookup"><span data-stu-id="97856-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="97856-197">Balíčky NuGet jiných výrobců poskytují `localStorage` api `sessionStorage`pro práci s a .</span><span class="sxs-lookup"><span data-stu-id="97856-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="97856-198">Stojí za to zvážit výběr balíčku, který transparentně používá ASP.NET [ochrany dat](xref:security/data-protection/introduction)core .</span><span class="sxs-lookup"><span data-stu-id="97856-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="97856-199">ASP.NET Core Data Protection šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty.</span><span class="sxs-lookup"><span data-stu-id="97856-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="97856-200">Pokud jsou serializovaná data JSON uložena ve formátu prostého textu, mohou uživatelé data zobrazit pomocí nástrojů pro vývojáře prohlížeče a také upravit uložená data.</span><span class="sxs-lookup"><span data-stu-id="97856-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="97856-201">Zabezpečení dat není vždy problém, protože data mohou být triviální povahy.</span><span class="sxs-lookup"><span data-stu-id="97856-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="97856-202">Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významné bezpečnostní riziko pro uživatele nebo organizaci.</span><span class="sxs-lookup"><span data-stu-id="97856-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="97856-203">Neumožňuje uživatelům kontrolovat nebo manipulovat s *citlivými daty*.</span><span class="sxs-lookup"><span data-stu-id="97856-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="97856-204">Experimentální balíček chráněného úložiště prohlížečů</span><span class="sxs-lookup"><span data-stu-id="97856-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="97856-205">Příklad balíčku NuGet, který poskytuje `localStorage` [ochranu dat](xref:security/data-protection/introduction) pro a `sessionStorage` je [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="97856-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="97856-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`je nepodporovaný experimentální balíček, který není v současné době vhodný pro produkční použití.</span><span class="sxs-lookup"><span data-stu-id="97856-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="97856-207">Instalace</span><span class="sxs-lookup"><span data-stu-id="97856-207">Installation</span></span>

<span data-ttu-id="97856-208">Instalace `Microsoft.AspNetCore.ProtectedBrowserStorage` balíčku:</span><span class="sxs-lookup"><span data-stu-id="97856-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="97856-209">V Blazor projektu aplikace Server přidejte odkaz na balíček [microsoft.aspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="97856-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="97856-210">V html nejvyšší úrovně (například v souboru *Pages/_Host.cshtml* ve výchozí `<script>` šabloně projektu) přidejte následující značku:</span><span class="sxs-lookup"><span data-stu-id="97856-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="97856-211">V `Startup.ConfigureServices` metodě `AddProtectedBrowserStorage` volání `localStorage` přidat `sessionStorage` a služby kolekce služeb:</span><span class="sxs-lookup"><span data-stu-id="97856-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="97856-212">Uložení a načtení dat v rámci komponenty</span><span class="sxs-lookup"><span data-stu-id="97856-212">Save and load data within a component</span></span>

<span data-ttu-id="97856-213">V každé součásti, která vyžaduje načítání [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) nebo ukládání dat do úložiště prohlížeče, použijte k vložení instance jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="97856-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="97856-214">Volba závisí na tom, které záložní úložiště chcete použít.</span><span class="sxs-lookup"><span data-stu-id="97856-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="97856-215">V následujícím příkladu se `sessionStorage` používá:</span><span class="sxs-lookup"><span data-stu-id="97856-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="97856-216">Příkaz `@using` lze umístit do souboru *_Imports.razor* namísto v součásti.</span><span class="sxs-lookup"><span data-stu-id="97856-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="97856-217">Použití souboru *_Imports.razor* zpřístupní obor názvů větším segmentům aplikace nebo celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="97856-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="97856-218">Chcete-li `_currentCount` zachovat `Counter` hodnotu v součásti `IncrementCount` šablony projektu, upravte metodu, která má být používána `ProtectedSessionStore.SetAsync`:</span><span class="sxs-lookup"><span data-stu-id="97856-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="97856-219">Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobným scénářem.</span><span class="sxs-lookup"><span data-stu-id="97856-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="97856-220">Aplikace s větší pravděpodobností ukládají celé objekty modelu, které obsahují složitý stav.</span><span class="sxs-lookup"><span data-stu-id="97856-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="97856-221">`ProtectedSessionStore`automaticky serializuje a reserializuje data JSON.</span><span class="sxs-lookup"><span data-stu-id="97856-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="97856-222">V předchozím příkladu kódu `_currentCount` jsou data `sessionStorage['count']` uložena jako v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="97856-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="97856-223">Data nejsou uložena ve formátu prostého textu, ale jsou chráněna pomocí ASP.NET [ochrany dat](xref:security/data-protection/introduction)společnosti Core .</span><span class="sxs-lookup"><span data-stu-id="97856-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="97856-224">Šifrovaná data lze `sessionStorage['count']` vidět, pokud je vyhodnocena v konzole pro vývojáře prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="97856-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="97856-225">Chcete-li `_currentCount` obnovit data, pokud `Counter` se uživatel vrátí k součásti později (včetně toho, zda jsou na zcela novém okruhu), použijte `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="97856-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="97856-226">Pokud parametry komponenty zahrnují stav `ProtectedSessionStore.GetAsync` navigace, zavolejte `OnParametersSetAsync`a `OnInitializedAsync`přiřaďte výsledek v , nikoli .</span><span class="sxs-lookup"><span data-stu-id="97856-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="97856-227">`OnInitializedAsync`je volána pouze jednou při první instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="97856-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="97856-228">`OnInitializedAsync`není volána znovu později, pokud uživatel přejde na jinou adresu URL, zatímco zůstane na stejné stránce.</span><span class="sxs-lookup"><span data-stu-id="97856-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="97856-229">Další informace naleznete v tématu <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="97856-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="97856-230">Příklady v této části fungují pouze v případě, že server nemá povoleno předběžné vykreslování.</span><span class="sxs-lookup"><span data-stu-id="97856-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="97856-231">Při zapnutém předběžném vykreslování je generována chyba podobná:</span><span class="sxs-lookup"><span data-stu-id="97856-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="97856-232">V tuto chvíli nelze vydat interop volání jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="97856-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="97856-233">Důvodem je, že součást je právě vykreslována.</span><span class="sxs-lookup"><span data-stu-id="97856-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="97856-234">Buď zakázat předběžné vykreslování nebo přidat další kód pro práci s prerendering.</span><span class="sxs-lookup"><span data-stu-id="97856-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="97856-235">Další informace o psaní kódu, který funguje s předběžným vykreslováním, najdete v části [Zpracování předběžného vykreslování.](#handle-prerendering)</span><span class="sxs-lookup"><span data-stu-id="97856-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="97856-236">Zpracování stavu načítání</span><span class="sxs-lookup"><span data-stu-id="97856-236">Handle the loading state</span></span>

<span data-ttu-id="97856-237">Vzhledem k tomu, že úložiště prohlížeče je asynchronní (přístupné prostřednictvím síťového připojení), je vždy doba, než jsou data načtena a k dispozici pro použití komponentou.</span><span class="sxs-lookup"><span data-stu-id="97856-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="97856-238">Pro dosažení nejlepších výsledků vykreslování zprávy stavu načítání při načítání probíhá namísto zobrazení prázdných nebo výchozích dat.</span><span class="sxs-lookup"><span data-stu-id="97856-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="97856-239">Jedním z přístupů je `null` sledovat, zda jsou data (stále načítání) nebo ne.</span><span class="sxs-lookup"><span data-stu-id="97856-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="97856-240">Ve výchozí `Counter` součásti je počet `int`držen v .</span><span class="sxs-lookup"><span data-stu-id="97856-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="97856-241">Přidejte `_currentCount` k typu`?``int`():</span><span class="sxs-lookup"><span data-stu-id="97856-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="97856-242">Místo bezpodmínečného zobrazení tlačítka počet a **přírůstek** zvolte zobrazení těchto prvků pouze v případě, že jsou načtena data:</span><span class="sxs-lookup"><span data-stu-id="97856-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="97856-243">Předběžné vykreslování popisovače</span><span class="sxs-lookup"><span data-stu-id="97856-243">Handle prerendering</span></span>

<span data-ttu-id="97856-244">Během předběžného vykreslování:</span><span class="sxs-lookup"><span data-stu-id="97856-244">During prerendering:</span></span>

* <span data-ttu-id="97856-245">Interaktivní připojení k prohlížeči uživatele neexistuje.</span><span class="sxs-lookup"><span data-stu-id="97856-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="97856-246">Prohlížeč ještě nemá stránku, na které by mohl spustit kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="97856-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="97856-247">`localStorage`nebo `sessionStorage` nejsou k dispozici během předběžného vykreslování.</span><span class="sxs-lookup"><span data-stu-id="97856-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="97856-248">Pokud se komponenta pokusí o interakci s úložištěm, je generována chyba podobná:</span><span class="sxs-lookup"><span data-stu-id="97856-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="97856-249">V tuto chvíli nelze vydat interop volání jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="97856-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="97856-250">Důvodem je, že součást je právě vykreslována.</span><span class="sxs-lookup"><span data-stu-id="97856-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="97856-251">Jedním ze způsobů, jak chybu vyřešit, je zakázat předběžné vykreslování.</span><span class="sxs-lookup"><span data-stu-id="97856-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="97856-252">To je obvykle nejlepší volbou, pokud aplikace využívá úložiště založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="97856-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="97856-253">Předběžné vykreslování zvyšuje složitost a neprospívá aplikaci, `sessionStorage` protože aplikace nemůže předem vykreslit žádný užitečný obsah, dokud není `localStorage` k dispozici.</span><span class="sxs-lookup"><span data-stu-id="97856-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="97856-254">Chcete-li zakázat předběžné vykreslování, `render-mode` otevřete soubor *Pages/_Host.cshtml* a změňte [pomocníka pro značku komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="97856-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="97856-255">Předběžné vykreslování může být `localStorage` `sessionStorage`užitečné pro jiné stránky, které nepoužívají nebo .</span><span class="sxs-lookup"><span data-stu-id="97856-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="97856-256">Chcete-li zachovat povolenou předběžnou interpretaci, odložte operaci načítání, dokud není prohlížeč připojen k okruhu.</span><span class="sxs-lookup"><span data-stu-id="97856-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="97856-257">Následuje příklad pro ukládání hodnoty čítače:</span><span class="sxs-lookup"><span data-stu-id="97856-257">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="97856-258">Faktor z zachování stavu na společné místo</span><span class="sxs-lookup"><span data-stu-id="97856-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="97856-259">Pokud mnoho součástí spoléhá na úložiště založené na prohlížeči, reimplementing kód zprostředkovatele stavu mnohokrát vytvoří duplikaci kódu.</span><span class="sxs-lookup"><span data-stu-id="97856-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="97856-260">Jednou z možností, jak se vyhnout duplikaci kódu, je vytvoření *nadřazené součásti zprostředkovatele stavu,* která zapouzdřuje logiku zprostředkovatele stavu.</span><span class="sxs-lookup"><span data-stu-id="97856-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="97856-261">Podřízené součásti mohou pracovat s trvalá data bez ohledu na mechanismus trvalosti stavu.</span><span class="sxs-lookup"><span data-stu-id="97856-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="97856-262">V následujícím příkladu `CounterStateProvider` komponenty jsou data čítačů zachována:</span><span class="sxs-lookup"><span data-stu-id="97856-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

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

<span data-ttu-id="97856-263">Komponenta `CounterStateProvider` zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.</span><span class="sxs-lookup"><span data-stu-id="97856-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="97856-264">Chcete-li `CounterStateProvider` použít komponentu, obtékejte instanci komponenty kolem jakékoli jiné součásti, která vyžaduje přístup ke stavu čítače.</span><span class="sxs-lookup"><span data-stu-id="97856-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="97856-265">Chcete-li, aby byl stav přístupný všem `CounterStateProvider` součástem `Router` v `App` aplikaci, obtékejte komponentu kolem komponenty (*App.razor*):</span><span class="sxs-lookup"><span data-stu-id="97856-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="97856-266">Zabalené součásti přijímat a můžete upravit trvalý stav čítače.</span><span class="sxs-lookup"><span data-stu-id="97856-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="97856-267">Následující `Counter` součást implementuje vzor:</span><span class="sxs-lookup"><span data-stu-id="97856-267">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="97856-268">Předchozí součást není nutné pro interakci `ProtectedBrowserStorage`s , ani se nezabývá "načítání" fáze.</span><span class="sxs-lookup"><span data-stu-id="97856-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="97856-269">Chcete-li pracovat s prerendering, jak je popsáno výše, lze změnit tak, `CounterStateProvider` aby všechny součásti, které spotřebovávají data čítače automaticky pracovat s prerendering.</span><span class="sxs-lookup"><span data-stu-id="97856-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="97856-270">Podrobnosti najdete v části [Zpracování předběžného vykreslování.](#handle-prerendering)</span><span class="sxs-lookup"><span data-stu-id="97856-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="97856-271">Obecně se doporučuje vzor *nadřazené součásti zprostředkovatele stavu:*</span><span class="sxs-lookup"><span data-stu-id="97856-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="97856-272">Chcete-li spotřebovat stav v mnoha dalších součástech.</span><span class="sxs-lookup"><span data-stu-id="97856-272">To consume state in many other components.</span></span>
* <span data-ttu-id="97856-273">Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má přetrvávat.</span><span class="sxs-lookup"><span data-stu-id="97856-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="97856-274">Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší se vyhnout zpracování načítání a ukládání stavu globálně.</span><span class="sxs-lookup"><span data-stu-id="97856-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
