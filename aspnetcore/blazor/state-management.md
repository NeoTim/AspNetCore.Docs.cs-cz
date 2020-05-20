---
<span data-ttu-id="dd610-101">title: ' ASP.NET Core Blazor Správa stavu ' Autor: Popis: ' Zjistěte, jak zachovat stav v Blazor serverových aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="dd610-101">title: 'ASP.NET Core Blazor state management' author: description: 'Learn how to persist state in Blazor Server apps.'</span></span>
<span data-ttu-id="dd610-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dd610-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dd610-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dd610-103">'Blazor'</span></span>
- <span data-ttu-id="dd610-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dd610-104">'Identity'</span></span>
- <span data-ttu-id="dd610-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dd610-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="dd610-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dd610-106">'Razor'</span></span>
- <span data-ttu-id="dd610-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="dd610-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-state-management"></a><span data-ttu-id="dd610-108">BlazorSpráva stavu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd610-108">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="dd610-109">Pomocí [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="dd610-109">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

Blazor<span data-ttu-id="dd610-110">Server je stavová architektura aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd610-110"> Server is a stateful app framework.</span></span> <span data-ttu-id="dd610-111">Ve většině případů aplikace udržuje průběžné připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="dd610-111">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="dd610-112">Stav uživatele je uložený v paměti serveru v *okruhu*.</span><span class="sxs-lookup"><span data-stu-id="dd610-112">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="dd610-113">Příkladem stavu drženého pro okruh uživatele jsou:</span><span class="sxs-lookup"><span data-stu-id="dd610-113">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="dd610-114">Vykreslené uživatelské rozhraní: hierarchie instancí komponent a jejich poslední výstup vykreslování.</span><span class="sxs-lookup"><span data-stu-id="dd610-114">The rendered UI: The hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="dd610-115">Hodnoty všech polí a vlastností v instancích součástí.</span><span class="sxs-lookup"><span data-stu-id="dd610-115">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="dd610-116">Data uchovávaná v instancích služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) , která jsou vymezena okruhu.</span><span class="sxs-lookup"><span data-stu-id="dd610-116">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="dd610-117">Tento článek řeší trvalost stavu v Blazor serverových aplikacích.</span><span class="sxs-lookup"><span data-stu-id="dd610-117">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="dd610-118">Aplikace WebAssembly můžou využít [trvalost stavu na straně klienta v prohlížeči](#client-side-in-the-browser) , ale vyžadují vlastní řešení nebo balíčky třetích stran nad rámec tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="dd610-118"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="blazor-circuits"></a>Blazor<span data-ttu-id="dd610-119">spoj</span><span class="sxs-lookup"><span data-stu-id="dd610-119"> circuits</span></span>

<span data-ttu-id="dd610-120">Pokud dojde ke ztrátě dočasného připojení k síti, Blazor pokusí se uživatel znovu připojit k původnímu okruhu, aby mohli dál používat aplikaci.</span><span class="sxs-lookup"><span data-stu-id="dd610-120">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="dd610-121">Ale opětovné připojení uživatele k původnímu okruhu v paměti serveru není vždycky možné:</span><span class="sxs-lookup"><span data-stu-id="dd610-121">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="dd610-122">Server nemůže trvale zachovávat odpojený okruh.</span><span class="sxs-lookup"><span data-stu-id="dd610-122">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="dd610-123">Server musí uvolnit odpojený okruh po vypršení časového limitu nebo v případě, že je server pod tlakem paměti.</span><span class="sxs-lookup"><span data-stu-id="dd610-123">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="dd610-124">V prostředích s více servery nasazení s vyrovnáváním zatížení můžou všechny požadavky na zpracování serveru být v daném okamžiku nedostupné.</span><span class="sxs-lookup"><span data-stu-id="dd610-124">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="dd610-125">Jednotlivé servery můžou selhat nebo automaticky odebrat, pokud už nepotřebujete pro zpracování celkového objemu požadavků.</span><span class="sxs-lookup"><span data-stu-id="dd610-125">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="dd610-126">Původní server nemusí být k dispozici, když se uživatel pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="dd610-126">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="dd610-127">Uživatel může zavřít a znovu otevřít prohlížeč nebo znovu načíst stránku, což odstraní všechny stavy uchovávané v paměti prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="dd610-127">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="dd610-128">Například hodnoty nastavené prostřednictvím volání interoperability JavaScriptu jsou ztraceny.</span><span class="sxs-lookup"><span data-stu-id="dd610-128">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="dd610-129">Když se uživatel nedá znovu připojit k původnímu okruhu, uživatel dostane nový okruh s prázdným stavem.</span><span class="sxs-lookup"><span data-stu-id="dd610-129">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="dd610-130">Jedná se o ekvivalent zavření a opětovného otevření desktopové aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd610-130">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="dd610-131">Zachovat stav napříč okruhy</span><span class="sxs-lookup"><span data-stu-id="dd610-131">Preserve state across circuits</span></span>

<span data-ttu-id="dd610-132">V některých scénářích je žádoucí zachovat stav napříč okruhy.</span><span class="sxs-lookup"><span data-stu-id="dd610-132">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="dd610-133">Aplikace může uchovávat důležitá data pro uživatele, pokud:</span><span class="sxs-lookup"><span data-stu-id="dd610-133">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="dd610-134">Webový server bude nedostupný.</span><span class="sxs-lookup"><span data-stu-id="dd610-134">The web server becomes unavailable.</span></span>
* <span data-ttu-id="dd610-135">V prohlížeči uživatele je nucen spustit nový okruh s novým webovým serverem.</span><span class="sxs-lookup"><span data-stu-id="dd610-135">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="dd610-136">Obecně platí, že udržování stavu napříč okruhy se vztahuje na scénáře, kdy uživatelé aktivně vytvářejí data, nikoli jenom čtení dat, která už existují.</span><span class="sxs-lookup"><span data-stu-id="dd610-136">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="dd610-137">Chcete-li zachovat stav nad rámec jednoho okruhu, *neukládejte pouze data v paměti serveru*.</span><span class="sxs-lookup"><span data-stu-id="dd610-137">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="dd610-138">Aplikace musí uchovávat data do jiného umístění úložiště.</span><span class="sxs-lookup"><span data-stu-id="dd610-138">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="dd610-139">Trvalost stavu není automatické.</span><span class="sxs-lookup"><span data-stu-id="dd610-139">State persistence isn't automatic.</span></span> <span data-ttu-id="dd610-140">Je nutné provést kroky při vývoji aplikace k implementaci stavové trvalosti dat.</span><span class="sxs-lookup"><span data-stu-id="dd610-140">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="dd610-141">Trvalost dat se obvykle vyžaduje jenom pro stav vysoké hodnoty, který uživatelé vynaložené úsilím vytvořit.</span><span class="sxs-lookup"><span data-stu-id="dd610-141">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="dd610-142">V následujících příkladech trvalého stavu ušetříte čas nebo pomůcky při komerčních činnostech:</span><span class="sxs-lookup"><span data-stu-id="dd610-142">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="dd610-143">U více kroků WebForm je &ndash; časově náročné, aby uživatel znovu zadal data pro několik dokončených kroků procesu s více kroky, pokud dojde ke ztrátě jejich stavu.</span><span class="sxs-lookup"><span data-stu-id="dd610-143">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="dd610-144">Uživatel ztratí stav v tomto scénáři, pokud přechází z formuláře s více kroky a později se vrátí do formuláře.</span><span class="sxs-lookup"><span data-stu-id="dd610-144">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="dd610-145">Nákupní košík &ndash; : všechny obchodní důležité komponenty aplikace, které představují potenciální tržby, se dají udržovat.</span><span class="sxs-lookup"><span data-stu-id="dd610-145">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="dd610-146">Uživatel, který ztratí svůj stav, a tedy i jeho nákupní košík, může koupit méně produktů nebo služeb při návratu do webu později.</span><span class="sxs-lookup"><span data-stu-id="dd610-146">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="dd610-147">Obvykle není nutné zachovávat snadno-znovu vytvořený stav, jako je například uživatelské jméno zadané do přihlašovacího dialogu, který nebyl odeslán.</span><span class="sxs-lookup"><span data-stu-id="dd610-147">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dd610-148">Aplikace může zachovat jenom *stav aplikace*.</span><span class="sxs-lookup"><span data-stu-id="dd610-148">An app can only persist *app state*.</span></span> <span data-ttu-id="dd610-149">Uživatelská rozhraní nelze zachovat, například instance komponent a jejich stromy vykreslování.</span><span class="sxs-lookup"><span data-stu-id="dd610-149">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="dd610-150">Komponenty a stromy vykreslování nejsou obecně serializovatelné.</span><span class="sxs-lookup"><span data-stu-id="dd610-150">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="dd610-151">Aby bylo možné zachovat podobný text jako stav uživatelského rozhraní, jako jsou například rozbalené uzly prvku TreeView, aplikace musí mít vlastní kód pro modelování chování jako serializovatelný stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd610-151">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="dd610-152">Kam zachovat stav</span><span class="sxs-lookup"><span data-stu-id="dd610-152">Where to persist state</span></span>

<span data-ttu-id="dd610-153">Pro trvalý stav v serverové aplikaci existují tři společná umístění Blazor .</span><span class="sxs-lookup"><span data-stu-id="dd610-153">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="dd610-154">Každý přístup nejlépe vyhovuje různým scénářům a má odlišná omezení:</span><span class="sxs-lookup"><span data-stu-id="dd610-154">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="dd610-155">Na straně serveru v databázi</span><span class="sxs-lookup"><span data-stu-id="dd610-155">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="dd610-156">URL</span><span class="sxs-lookup"><span data-stu-id="dd610-156">URL</span></span>](#url)
* [<span data-ttu-id="dd610-157">Klientská strana v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="dd610-157">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="dd610-158">Na straně serveru v databázi</span><span class="sxs-lookup"><span data-stu-id="dd610-158">Server-side in a database</span></span>

<span data-ttu-id="dd610-159">Pro trvalá trvalá data nebo pro všechna data, která musí být rozložená na více uživatelů nebo zařízení, je k nejlepší možnost databáze na straně serveru téměř určitě.</span><span class="sxs-lookup"><span data-stu-id="dd610-159">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="dd610-160">Mezi možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="dd610-160">Options include:</span></span>

* <span data-ttu-id="dd610-161">Relační databáze SQL</span><span class="sxs-lookup"><span data-stu-id="dd610-161">Relational SQL database</span></span>
* <span data-ttu-id="dd610-162">Ukládání hodnot klíče</span><span class="sxs-lookup"><span data-stu-id="dd610-162">Key-value store</span></span>
* <span data-ttu-id="dd610-163">Úložiště objektů BLOB</span><span class="sxs-lookup"><span data-stu-id="dd610-163">Blob store</span></span>
* <span data-ttu-id="dd610-164">Úložiště tabulek</span><span class="sxs-lookup"><span data-stu-id="dd610-164">Table store</span></span>

<span data-ttu-id="dd610-165">Po uložení dat v databázi může nový okruh spustit uživatel kdykoli.</span><span class="sxs-lookup"><span data-stu-id="dd610-165">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="dd610-166">Data uživatele jsou zachována a k dispozici v jakémkoli novém okruhu.</span><span class="sxs-lookup"><span data-stu-id="dd610-166">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="dd610-167">Další informace o možnostech Azure Data Storage najdete v [dokumentaci Azure Storage](/azure/storage/) a v [databázích Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="dd610-167">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="dd610-168">URL</span><span class="sxs-lookup"><span data-stu-id="dd610-168">URL</span></span>

<span data-ttu-id="dd610-169">Pro přechodná data představující stav navigace modelujte data jako součást adresy URL.</span><span class="sxs-lookup"><span data-stu-id="dd610-169">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="dd610-170">Mezi příklady stavu zahrnutých v adrese URL patří:</span><span class="sxs-lookup"><span data-stu-id="dd610-170">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="dd610-171">ID zobrazené entity</span><span class="sxs-lookup"><span data-stu-id="dd610-171">The ID of a viewed entity.</span></span>
* <span data-ttu-id="dd610-172">Aktuální číslo stránky v mřížce na stránkovaném webu</span><span class="sxs-lookup"><span data-stu-id="dd610-172">The current page number in a paged grid.</span></span>

<span data-ttu-id="dd610-173">Obsah panelu Adresa prohlížeče se zachová:</span><span class="sxs-lookup"><span data-stu-id="dd610-173">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="dd610-174">Pokud uživatel ručně znovu načte stránku.</span><span class="sxs-lookup"><span data-stu-id="dd610-174">If the user manually reloads the page.</span></span>
* <span data-ttu-id="dd610-175">Pokud dojde k nedostupnosti webového serveru a uživatel je nucen znovu načíst stránku, aby se mohl připojit k jinému serveru.</span><span class="sxs-lookup"><span data-stu-id="dd610-175">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="dd610-176">Informace o definování vzorů adres URL pomocí `@page` direktivy naleznete v tématu <xref:blazor/routing> .</span><span class="sxs-lookup"><span data-stu-id="dd610-176">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="dd610-177">Klientská strana v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="dd610-177">Client-side in the browser</span></span>

<span data-ttu-id="dd610-178">Pro přechodná data, která uživatel aktivně vytváří, je běžným záložním úložištěm prohlížeč `localStorage` a `sessionStorage` kolekce.</span><span class="sxs-lookup"><span data-stu-id="dd610-178">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="dd610-179">Pokud dojde k opuštění okruhu, nepotřebuje aplikace spravovat nebo vymazat uložený stav, což je výhodou pro úložiště na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="dd610-179">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="dd610-180">"Klientská strana" v této části odkazuje na scénáře na straně klienta v prohlížeči, nikoli na [ Blazor model hostování webového sestavení](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="dd610-180">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="dd610-181">`localStorage`a `sessionStorage` lze jej použít v Blazor aplikacích pro WebAssembly, ale pouze psaním vlastního kódu nebo pomocí balíčku třetí strany.</span><span class="sxs-lookup"><span data-stu-id="dd610-181">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="dd610-182">`localStorage`a `sessionStorage` liší se následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="dd610-182">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="dd610-183">`localStorage`je vymezen v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="dd610-183">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="dd610-184">Pokud uživatel stránku znovu načte nebo zavře a znovu otevře prohlížeč, stav přetrvává.</span><span class="sxs-lookup"><span data-stu-id="dd610-184">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="dd610-185">Pokud uživatel otevře více karet prohlížeče, stav se sdílí napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="dd610-185">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="dd610-186">Data přetrvají do `localStorage` doby, než je explicitně zrušeno.</span><span class="sxs-lookup"><span data-stu-id="dd610-186">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="dd610-187">`sessionStorage`je vymezen na kartu prohlížeče uživatele. Pokud uživatel znovu načte kartu, stav se přetrvá.</span><span class="sxs-lookup"><span data-stu-id="dd610-187">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="dd610-188">Pokud uživatel zavře kartu nebo prohlížeč, dojde ke ztrátě stavu.</span><span class="sxs-lookup"><span data-stu-id="dd610-188">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="dd610-189">Pokud uživatel otevře více karet prohlížeče, každá karta má svou vlastní nezávislou verzi dat.</span><span class="sxs-lookup"><span data-stu-id="dd610-189">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="dd610-190">Obecně `sessionStorage` je bezpečnější použít.</span><span class="sxs-lookup"><span data-stu-id="dd610-190">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="dd610-191">`sessionStorage`Vyhněte se riziku, že uživatel otevře více karet a narazí na následující:</span><span class="sxs-lookup"><span data-stu-id="dd610-191">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="dd610-192">Chyby v úložišti stavů napříč kartami.</span><span class="sxs-lookup"><span data-stu-id="dd610-192">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="dd610-193">Matoucí chování, když karta Přepisuje stav jiných karet.</span><span class="sxs-lookup"><span data-stu-id="dd610-193">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="dd610-194">`localStorage`je lepší volbou, pokud aplikace musí zachovat stav v rámci zavírání a znovu otevřít prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="dd610-194">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="dd610-195">Upozornění pro použití úložiště prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="dd610-195">Caveats for using browser storage:</span></span>

* <span data-ttu-id="dd610-196">Podobně jako při použití databáze na straně serveru je načítání a ukládání dat asynchronní.</span><span class="sxs-lookup"><span data-stu-id="dd610-196">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="dd610-197">Na rozdíl od databáze na straně serveru není úložiště během předgenerování k dispozici, protože požadovaná stránka v prohlížeči neexistuje během fáze předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="dd610-197">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="dd610-198">Úložiště několika kilobajtů dat je pro Blazor serverové aplikace přijatelné.</span><span class="sxs-lookup"><span data-stu-id="dd610-198">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="dd610-199">Po několika kilobajtech je potřeba vzít v úvahu dopad na výkon, protože data se načítají a ukládají v síti.</span><span class="sxs-lookup"><span data-stu-id="dd610-199">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="dd610-200">Uživatelé můžou data zobrazit nebo s nimi manipulovat.</span><span class="sxs-lookup"><span data-stu-id="dd610-200">Users may view or tamper with the data.</span></span> <span data-ttu-id="dd610-201">[Ochrana dat](xref:security/data-protection/introduction) ASP.NET Core může riziko zmírnit.</span><span class="sxs-lookup"><span data-stu-id="dd610-201">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="dd610-202">Řešení úložiště v prohlížeči třetích stran</span><span class="sxs-lookup"><span data-stu-id="dd610-202">Third-party browser storage solutions</span></span>

<span data-ttu-id="dd610-203">Balíčky NuGet třetích stran poskytují rozhraní API pro práci s `localStorage` a `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="dd610-203">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="dd610-204">Je vhodné zvážit výběr balíčku, který transparentně používá [ochranu dat](xref:security/data-protection/introduction)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd610-204">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="dd610-205">ASP.NET Core ochrana dat šifruje uložená data a snižuje potenciální riziko manipulace s uloženými daty.</span><span class="sxs-lookup"><span data-stu-id="dd610-205">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="dd610-206">Pokud jsou data serializovaná ve formátu JSON uložená ve formě prostého textu, uživatelé můžou data zobrazit pomocí vývojářských nástrojů pro prohlížeč a také upravovat uložená data.</span><span class="sxs-lookup"><span data-stu-id="dd610-206">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="dd610-207">Zabezpečení dat se vždy nejedná o problém, protože data můžou být triviální v podstatě.</span><span class="sxs-lookup"><span data-stu-id="dd610-207">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="dd610-208">Například čtení nebo úprava uložené barvy prvku uživatelského rozhraní není významným bezpečnostním rizikem pro uživatele nebo organizaci.</span><span class="sxs-lookup"><span data-stu-id="dd610-208">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="dd610-209">Neumožňuje uživatelům kontrolovat *citlivá data*a manipulovat s nimi.</span><span class="sxs-lookup"><span data-stu-id="dd610-209">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="dd610-210">Experimentální balíček chráněného úložiště prohlížeče</span><span class="sxs-lookup"><span data-stu-id="dd610-210">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="dd610-211">Příklad balíčku NuGet, který poskytuje [ochranu dat](xref:security/data-protection/introduction) pro `localStorage` a `sessionStorage` je [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="dd610-211">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="dd610-212">`Microsoft.AspNetCore.ProtectedBrowserStorage`je nepodporovaný experimentální balíček nevhodný pro produkční použití v tuto chvíli.</span><span class="sxs-lookup"><span data-stu-id="dd610-212">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="dd610-213">Instalace</span><span class="sxs-lookup"><span data-stu-id="dd610-213">Installation</span></span>

<span data-ttu-id="dd610-214">Postup instalace `Microsoft.AspNetCore.ProtectedBrowserStorage` balíčku:</span><span class="sxs-lookup"><span data-stu-id="dd610-214">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="dd610-215">V Blazor projektu Server App přidejte odkaz na balíček [Microsoft. AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="dd610-215">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="dd610-216">V HTML nejvyšší úrovně (například v souboru *Pages/_Host. cshtml* ve výchozí šabloně projektu) přidejte následující `<script>` značku:</span><span class="sxs-lookup"><span data-stu-id="dd610-216">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="dd610-217">V `Startup.ConfigureServices` metodě zavolejte `AddProtectedBrowserStorage` na Přidat `localStorage` a `sessionStorage` služby do kolekce služeb:</span><span class="sxs-lookup"><span data-stu-id="dd610-217">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="dd610-218">Ukládání a načítání dat v rámci součásti</span><span class="sxs-lookup"><span data-stu-id="dd610-218">Save and load data within a component</span></span>

<span data-ttu-id="dd610-219">V každé součásti, která vyžaduje načtení nebo uložení dat do úložiště prohlížeče, použijte [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) k vložení instance některého z následujících prvků:</span><span class="sxs-lookup"><span data-stu-id="dd610-219">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="dd610-220">Volba závisí na tom, které záložní úložiště chcete použít.</span><span class="sxs-lookup"><span data-stu-id="dd610-220">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="dd610-221">V následujícím příkladu `sessionStorage` se používá:</span><span class="sxs-lookup"><span data-stu-id="dd610-221">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="dd610-222">`@using`Příkaz lze umístit do souboru *_Imports. Razor* místo do součásti.</span><span class="sxs-lookup"><span data-stu-id="dd610-222">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="dd610-223">Použití souboru *_Imports. Razor* zpřístupňuje obor názvů pro větší segmenty aplikace nebo celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="dd610-223">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="dd610-224">Chcete-li zachovat `currentCount` hodnotu v `Counter` součásti šablony projektu, upravte `IncrementCount` metodu, kterou chcete použít `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="dd610-224">To persist the `currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="dd610-225">Ve větších a realističtějších aplikacích je ukládání jednotlivých polí nepravděpodobné.</span><span class="sxs-lookup"><span data-stu-id="dd610-225">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="dd610-226">Pro aplikace je pravděpodobnější ukládání celých objektů modelu, které zahrnují komplexní stav.</span><span class="sxs-lookup"><span data-stu-id="dd610-226">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="dd610-227">`ProtectedSessionStore`automaticky serializace a deserializace dat JSON.</span><span class="sxs-lookup"><span data-stu-id="dd610-227">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="dd610-228">V předchozím příkladu kódu `currentCount` se data ukládají jako `sessionStorage['count']` v prohlížeči uživatele.</span><span class="sxs-lookup"><span data-stu-id="dd610-228">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="dd610-229">Data nejsou uložená ve formátu prostého textu, ale jsou chráněná pomocí [ochrany dat](xref:security/data-protection/introduction)ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd610-229">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="dd610-230">Šifrovaná data se můžou zobrazit, pokud `sessionStorage['count']` se vyhodnotí v konzole pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="dd610-230">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="dd610-231">Chcete-li obnovit `currentCount` data, pokud se uživatel vrátí do `Counter` komponenty později (včetně toho, jestli jsou na zcela novém okruhu), použijte `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="dd610-231">To recover the `currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="dd610-232">Pokud parametry komponenty obsahují stav navigace, zavolejte `ProtectedSessionStore.GetAsync` a přiřaďte výsledek v `OnParametersSetAsync` , nikoli `OnInitializedAsync` .</span><span class="sxs-lookup"><span data-stu-id="dd610-232">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="dd610-233">`OnInitializedAsync`je volána pouze jednou při prvním vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="dd610-233">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="dd610-234">`OnInitializedAsync`není voláno později, pokud uživatel přejde na jinou adresu URL a zůstane na stejné stránce.</span><span class="sxs-lookup"><span data-stu-id="dd610-234">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="dd610-235">Další informace naleznete v tématu <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="dd610-235">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="dd610-236">Příklady v této části fungují pouze v případě, že server nemá povolené předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="dd610-236">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="dd610-237">Pokud je povoleno předvykreslování, je vygenerována chyba podobná této:</span><span class="sxs-lookup"><span data-stu-id="dd610-237">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="dd610-238">V tuto chvíli nelze vydat volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="dd610-238">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="dd610-239">Důvodem je to, že se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="dd610-239">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="dd610-240">Buď zakažte předvykreslování nebo přidejte další kód pro práci s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="dd610-240">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="dd610-241">Další informace o psaní kódu, který funguje s předvykreslováním, najdete v části [popisovač předvykreslování](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="dd610-241">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="dd610-242">Zpracování stavu načítání</span><span class="sxs-lookup"><span data-stu-id="dd610-242">Handle the loading state</span></span>

<span data-ttu-id="dd610-243">Vzhledem k tomu, že je úložiště prohlížeče asynchronní (přístup prostřednictvím připojení k síti), je vždy časový interval, než se data načtou a budou k dispozici pro použití komponentou.</span><span class="sxs-lookup"><span data-stu-id="dd610-243">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="dd610-244">Nejlepších výsledků dosáhnete, když při načítání vykreslíte zprávu o stavu načítání, místo aby se zobrazila prázdná nebo výchozí data.</span><span class="sxs-lookup"><span data-stu-id="dd610-244">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="dd610-245">Jedním z těchto způsobů je sledovat, jestli jsou data `null` (pořád se načítají), nebo ne.</span><span class="sxs-lookup"><span data-stu-id="dd610-245">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="dd610-246">Ve výchozí `Counter` součásti je počet uchováván v `int` .</span><span class="sxs-lookup"><span data-stu-id="dd610-246">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="dd610-247">Převést `currentCount` na typ s možnou hodnotou null přidáním otazníku ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="dd610-247">Make `currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="dd610-248">Namísto nepodmíněného zobrazení tlačítka počet a **přírůstek** vyberte, zda chcete tyto prvky zobrazit pouze v případě, že jsou data načtena:</span><span class="sxs-lookup"><span data-stu-id="dd610-248">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="dd610-249">Zpracovat předvykreslování</span><span class="sxs-lookup"><span data-stu-id="dd610-249">Handle prerendering</span></span>

<span data-ttu-id="dd610-250">Během předvykreslování:</span><span class="sxs-lookup"><span data-stu-id="dd610-250">During prerendering:</span></span>

* <span data-ttu-id="dd610-251">Interaktivní připojení k prohlížeči uživatele neexistuje.</span><span class="sxs-lookup"><span data-stu-id="dd610-251">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="dd610-252">Prohlížeč ještě nemá stránku, ve které může spustit kód jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dd610-252">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="dd610-253">`localStorage`nebo `sessionStorage` nejsou během předvykreslování k dispozici.</span><span class="sxs-lookup"><span data-stu-id="dd610-253">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="dd610-254">Pokud se komponenta pokusí o interakci s úložištěm, je vygenerována chyba podobná této:</span><span class="sxs-lookup"><span data-stu-id="dd610-254">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="dd610-255">V tuto chvíli nelze vydat volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="dd610-255">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="dd610-256">Důvodem je to, že se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="dd610-256">This is because the component is being prerendered.</span></span>

<span data-ttu-id="dd610-257">Jedním ze způsobů, jak chybu vyřešit, je zakázat předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="dd610-257">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="dd610-258">To je obvykle nejlepší volba, pokud aplikace využívá úložiště založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="dd610-258">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="dd610-259">Předvykreslování přináší složitost a nevýhoduje aplikaci, protože aplikace nemůže využít žádný užitečný obsah do té doby, než `localStorage` `sessionStorage` je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="dd610-259">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="dd610-260">Chcete-li zakázat předvykreslování, otevřete soubor *Pages/_Host. cshtml* a změňte `render-mode` [pomocníka značek komponenty](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> .</span><span class="sxs-lookup"><span data-stu-id="dd610-260">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="dd610-261">Předvykreslování může být užitečné pro jiné stránky, které nepoužívají `localStorage` nebo `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="dd610-261">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="dd610-262">Aby bylo možné předvykreslování povolit, odložte operaci načtení, dokud se prohlížeč nepřipojí k okruhu.</span><span class="sxs-lookup"><span data-stu-id="dd610-262">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="dd610-263">Následuje příklad uložení hodnoty čítače:</span><span class="sxs-lookup"><span data-stu-id="dd610-263">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="dd610-264">Rozložte zachování stavu na společné místo.</span><span class="sxs-lookup"><span data-stu-id="dd610-264">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="dd610-265">Pokud mnoho komponent spoléhá na úložiště založené na prohlížeči, často se kód zprostředkovatele stavu znovu implementuje vytvořením duplicit kódu.</span><span class="sxs-lookup"><span data-stu-id="dd610-265">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="dd610-266">Jednou z možností, jak zabránit duplikaci kódu, je vytvoření *nadřazené komponenty poskytovatele stavu* , která zapouzdřuje logiku poskytovatele stavu.</span><span class="sxs-lookup"><span data-stu-id="dd610-266">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="dd610-267">Podřízené komponenty mohou pracovat s trvalými daty bez ohledu na mechanismus trvalosti stavu.</span><span class="sxs-lookup"><span data-stu-id="dd610-267">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="dd610-268">V následujícím příkladu `CounterStateProvider` komponenty jsou data čítače trvalá:</span><span class="sxs-lookup"><span data-stu-id="dd610-268">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="dd610-269">`CounterStateProvider`Komponenta zpracovává fázi načítání tím, že nevykresluje svůj podřízený obsah, dokud není načítání dokončeno.</span><span class="sxs-lookup"><span data-stu-id="dd610-269">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="dd610-270">Chcete-li použít `CounterStateProvider` komponentu, zabalte instanci součásti kolem jakékoli jiné komponenty, která vyžaduje přístup ke stavu čítače.</span><span class="sxs-lookup"><span data-stu-id="dd610-270">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="dd610-271">Chcete-li zpřístupnit stav pro všechny součásti aplikace, zabalte `CounterStateProvider` komponentu kolem komponenty `Router` v `App` součásti (*App. Razor*):</span><span class="sxs-lookup"><span data-stu-id="dd610-271">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="dd610-272">Zabalené komponenty obdrží a můžou upravovat trvalý stav čítače.</span><span class="sxs-lookup"><span data-stu-id="dd610-272">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="dd610-273">`Counter`Vzor implementuje následující součást:</span><span class="sxs-lookup"><span data-stu-id="dd610-273">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="dd610-274">Předchozí komponenta není nutná k interakci s `ProtectedBrowserStorage` , ani nefunguje se fází "načítání".</span><span class="sxs-lookup"><span data-stu-id="dd610-274">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="dd610-275">Chcete-li se vypořádat s předem popsaným způsobem, `CounterStateProvider` lze upravit tak, aby všechny součásti, které spotřebovávají data čítače, pracovaly automaticky s předem vykreslením.</span><span class="sxs-lookup"><span data-stu-id="dd610-275">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="dd610-276">Podrobnosti najdete v části o [předvykreslování popisovače](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="dd610-276">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="dd610-277">Obecně se doporučuje model *nadřazené komponenty zprostředkovatele stavu* :</span><span class="sxs-lookup"><span data-stu-id="dd610-277">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="dd610-278">Pro využívání stavu v mnoha dalších součástech.</span><span class="sxs-lookup"><span data-stu-id="dd610-278">To consume state in many other components.</span></span>
* <span data-ttu-id="dd610-279">Pokud existuje pouze jeden objekt stavu nejvyšší úrovně, který má být zachován.</span><span class="sxs-lookup"><span data-stu-id="dd610-279">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="dd610-280">Chcete-li zachovat mnoho různých stavových objektů a využívat různé podmnožiny objektů na různých místech, je lepší se vyhnout zpracování načítání a ukládání stavu globálně.</span><span class="sxs-lookup"><span data-stu-id="dd610-280">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
