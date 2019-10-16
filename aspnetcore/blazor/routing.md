---
title: ASP.NET Core směrování Blazor
author: guardrex
description: Naučte se směrovat požadavky v aplikacích a o komponentě NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/routing
ms.openlocfilehash: a71709d6b87d8182e90f827d952090aa1e38d701
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391200"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="50480-103">ASP.NET Core směrování Blazor</span><span class="sxs-lookup"><span data-stu-id="50480-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="50480-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50480-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="50480-105">Naučte se směrovat požadavky a používat komponentu `NavLink` k vytváření navigačních odkazů v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="50480-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="50480-106">Integrace směrování ASP.NET Core Endpoint</span><span class="sxs-lookup"><span data-stu-id="50480-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="50480-107">Server Blazor je integrovaný do [Směrování koncového bodu ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="50480-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="50480-108">ASP.NET Core aplikace je nakonfigurovaná tak, aby přijímala příchozí připojení pro interaktivní součásti s `MapBlazorHub` v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="50480-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="50480-109">Nejtypickou konfigurací je směrování všech požadavků na stránku Razor, která funguje jako hostitel pro součást aplikace Blazor Server na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="50480-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="50480-110">Podle konvence se stránka *hostitele* obvykle nazývá *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="50480-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="50480-111">Trasa zadaná v hostitelském souboru se nazývá *záložní trasa* , protože v porovnání s trasou funguje s nízkou prioritou.</span><span class="sxs-lookup"><span data-stu-id="50480-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="50480-112">Záložní trasa se bere v úvahu v případě, že se jiné trasy neshodují.</span><span class="sxs-lookup"><span data-stu-id="50480-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="50480-113">Díky tomu může aplikace používat jiné řadiče a stránky bez narušování aplikace Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="50480-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="50480-114">Šablony směrování</span><span class="sxs-lookup"><span data-stu-id="50480-114">Route templates</span></span>

<span data-ttu-id="50480-115">Komponenta `Router` umožňuje směrování do každé součásti se zadanou trasou.</span><span class="sxs-lookup"><span data-stu-id="50480-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="50480-116">Komponenta `Router` se zobrazí v souboru *App. Razor* :</span><span class="sxs-lookup"><span data-stu-id="50480-116">The `Router` component appears in the *App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="50480-117">Když je zkompilován soubor *. Razor* s direktivou `@page`, vygenerovaná třída poskytuje <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="50480-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="50480-118">Za běhu součást `RouteView`:</span><span class="sxs-lookup"><span data-stu-id="50480-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="50480-119">Přijímá `RouteData` od `Router` spolu s požadovanými parametry.</span><span class="sxs-lookup"><span data-stu-id="50480-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="50480-120">Vykreslí určenou komponentu pomocí jejího rozložení (nebo volitelného výchozího rozložení) pomocí zadaných parametrů.</span><span class="sxs-lookup"><span data-stu-id="50480-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="50480-121">Volitelně můžete zadat parametr `DefaultLayout` s třídou rozložení, která se má použít pro součásti, které neurčují rozložení.</span><span class="sxs-lookup"><span data-stu-id="50480-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="50480-122">Výchozí šablony Blazor určují součást `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="50480-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="50480-123">*MainLayout. Razor* se nachází ve *sdílené* složce projektu šablony.</span><span class="sxs-lookup"><span data-stu-id="50480-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="50480-124">Další informace o rozloženích naleznete v tématu <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="50480-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="50480-125">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="50480-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="50480-126">Následující komponenta reaguje na požadavky na `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="50480-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="50480-127">Aby adresy URL byly správně přeloženy, musí aplikace zahrnovat značku `<base>` v souboru *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor) se základní cestou aplikace zadanou v atributu `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="50480-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="50480-128">Další informace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="50480-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="50480-129">Poskytnutí vlastního obsahu, když se nenalezne obsah</span><span class="sxs-lookup"><span data-stu-id="50480-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="50480-130">Komponenta `Router` umožňuje aplikaci zadat vlastní obsah, pokud se pro požadovanou trasu nenajde obsah.</span><span class="sxs-lookup"><span data-stu-id="50480-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="50480-131">V souboru *App. Razor* nastavte vlastní obsah v parametru šablony `NotFound` komponenty `Router`:</span><span class="sxs-lookup"><span data-stu-id="50480-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="50480-132">Obsah značek `<NotFound>` může obsahovat libovolné položky, jako jsou například jiné interaktivní součásti.</span><span class="sxs-lookup"><span data-stu-id="50480-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="50480-133">Pokud chcete použít výchozí rozložení pro @no__t obsahu s hodnotou 0, přečtěte si téma <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="50480-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="50480-134">Směrování na součásti z více sestavení</span><span class="sxs-lookup"><span data-stu-id="50480-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="50480-135">Použijte parametr `AdditionalAssemblies` k určení dalších sestavení pro komponentu `Router`, která se má při hledání směrovatelných komponent vzít v úvahu.</span><span class="sxs-lookup"><span data-stu-id="50480-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="50480-136">Zadaná sestavení jsou kromě @no__tho -0-zadaného sestavení považována za.</span><span class="sxs-lookup"><span data-stu-id="50480-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="50480-137">V následujícím příkladu je `Component1` směrovatelné komponenty definované v odkazované knihovně tříd.</span><span class="sxs-lookup"><span data-stu-id="50480-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="50480-138">Následující příklad `AdditionalAssemblies` vede k podpoře směrování pro `Component1`:</span><span class="sxs-lookup"><span data-stu-id="50480-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="50480-139">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="50480-139">Route parameters</span></span>

<span data-ttu-id="50480-140">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponenty se stejným názvem (bez rozlišení velkých a malých písmen):</span><span class="sxs-lookup"><span data-stu-id="50480-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="50480-141">Volitelné parametry nejsou podporované pro aplikace Blazor v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="50480-141">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0.</span></span> <span data-ttu-id="50480-142">V předchozím příkladu jsou aplikovány dvě direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="50480-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="50480-143">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="50480-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="50480-144">Druhá direktiva `@page` přijímá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.</span><span class="sxs-lookup"><span data-stu-id="50480-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="50480-145">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="50480-145">Route constraints</span></span>

<span data-ttu-id="50480-146">Omezení trasy vynutilo typ shodný s typem pro segment směrování do součásti.</span><span class="sxs-lookup"><span data-stu-id="50480-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="50480-147">V následujícím příkladu trasa k součásti `Users` odpovídá pouze v případě, že:</span><span class="sxs-lookup"><span data-stu-id="50480-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="50480-148">V adrese URL požadavku je přítomen segment směrování `Id`.</span><span class="sxs-lookup"><span data-stu-id="50480-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="50480-149">Segment `Id` je celé číslo (`int`).</span><span class="sxs-lookup"><span data-stu-id="50480-149">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="50480-150">K dispozici jsou omezení tras uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="50480-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="50480-151">Pro omezení trasy, která se shodují s invariantní jazykovou verzí, se podívejte na upozornění pod tabulkou, kde najdete další informace.</span><span class="sxs-lookup"><span data-stu-id="50480-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="50480-152">Jedinečn</span><span class="sxs-lookup"><span data-stu-id="50480-152">Constraint</span></span> | <span data-ttu-id="50480-153">Příklad</span><span class="sxs-lookup"><span data-stu-id="50480-153">Example</span></span>           | <span data-ttu-id="50480-154">Příklady shody</span><span class="sxs-lookup"><span data-stu-id="50480-154">Example Matches</span></span>                                                                  | <span data-ttu-id="50480-155">Invariantní</span><span class="sxs-lookup"><span data-stu-id="50480-155">Invariant</span></span><br><span data-ttu-id="50480-156">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="50480-156">culture</span></span><br><span data-ttu-id="50480-157">shoda</span><span class="sxs-lookup"><span data-stu-id="50480-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="50480-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="50480-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="50480-159">Ne</span><span class="sxs-lookup"><span data-stu-id="50480-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="50480-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="50480-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="50480-161">Ano</span><span class="sxs-lookup"><span data-stu-id="50480-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="50480-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="50480-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="50480-163">Ano</span><span class="sxs-lookup"><span data-stu-id="50480-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="50480-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="50480-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="50480-165">Ano</span><span class="sxs-lookup"><span data-stu-id="50480-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="50480-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="50480-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="50480-167">Ano</span><span class="sxs-lookup"><span data-stu-id="50480-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="50480-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="50480-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="50480-169">Ne</span><span class="sxs-lookup"><span data-stu-id="50480-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="50480-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="50480-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="50480-171">Ano</span><span class="sxs-lookup"><span data-stu-id="50480-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="50480-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="50480-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="50480-173">Ano</span><span class="sxs-lookup"><span data-stu-id="50480-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="50480-174">Omezení směrování, která ověřují adresu URL a jsou převedena na typ CLR (například `int` nebo `DateTime`) vždy používají invariantní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="50480-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="50480-175">Tato omezení předpokládají, že adresa URL nelze lokalizovat.</span><span class="sxs-lookup"><span data-stu-id="50480-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="50480-176">Směrování s adresami URL, které obsahují tečky</span><span class="sxs-lookup"><span data-stu-id="50480-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="50480-177">V aplikacích Blazor Server jsou výchozí trasy v *_Host. cshtml* `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="50480-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="50480-178">Adresa URL požadavku, která obsahuje tečku (`.`), není shodná s výchozí cestou, protože adresa URL se zobrazí pro vyžádání souboru.</span><span class="sxs-lookup"><span data-stu-id="50480-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="50480-179">Aplikace Blazor vrací odpověď na *404, která nebyla nalezena* pro statický soubor, který neexistuje.</span><span class="sxs-lookup"><span data-stu-id="50480-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="50480-180">Chcete-li použít trasy, které obsahují tečku, nakonfigurujte *_Host. cshtml* s následující šablonou směrování:</span><span class="sxs-lookup"><span data-stu-id="50480-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="50480-181">Šablona `"/{**path}"` zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="50480-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="50480-182">Dvojitá hvězdička *– veškerá* syntaxe (`**`) pro zachycení cesty mezi více hranicemi složek bez kódování lomítka (`/`).</span><span class="sxs-lookup"><span data-stu-id="50480-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="50480-183">Název parametru trasy `path`.</span><span class="sxs-lookup"><span data-stu-id="50480-183">A `path` route parameter name.</span></span>

<span data-ttu-id="50480-184">Další informace najdete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="50480-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="50480-185">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="50480-185">NavLink component</span></span>

<span data-ttu-id="50480-186">Při vytváření navigačních odkazů použijte komponentu `NavLink` místo prvků hypertextového odkazu HTML (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="50480-186">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="50480-187">Komponenta `NavLink` se chová jako prvek `<a>` s tím rozdílem, že přepíná třídu CSS `active` na základě toho, zda `href` odpovídá aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="50480-187">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="50480-188">Třída `active` pomáhá uživateli pochopit, kterou stránku tvoří aktivní stránka mezi zobrazenými navigačními odkazy.</span><span class="sxs-lookup"><span data-stu-id="50480-188">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="50480-189">Následující součást `NavMenu` vytvoří navigační panel [bootstrap](https://getbootstrap.com/docs/) , který ukazuje, jak používat komponenty `NavLink`:</span><span class="sxs-lookup"><span data-stu-id="50480-189">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="50480-190">Existují dvě možnosti @no__t 0, které lze přiřadit k atributu `Match` prvku `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="50480-190">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="50480-191">`NavLinkMatch.All` &ndash; `NavLink` je aktivní, pokud odpovídá celé aktuální adrese URL.</span><span class="sxs-lookup"><span data-stu-id="50480-191">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="50480-192">`NavLinkMatch.Prefix` (*výchozí*) &ndash; `NavLink` je aktivní, pokud odpovídá libovolné předponě aktuální adresy URL.</span><span class="sxs-lookup"><span data-stu-id="50480-192">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="50480-193">V předchozím příkladu Home `NavLink` `href=""` odpovídá domovské adrese URL a přijímá pouze třídu CSS `active` na výchozí základní cestě URL aplikace (například `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="50480-193">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="50480-194">Druhá `NavLink` přijímá třídu `active`, pokud uživatel navštíví libovolnou adresu URL s předponou `MyComponent` (například `https://localhost:5001/MyComponent` a `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="50480-194">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="50480-195">Další atributy komponenty `NavLink` jsou předány do vykreslené značky ukotvení.</span><span class="sxs-lookup"><span data-stu-id="50480-195">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="50480-196">V následujícím příkladu obsahuje součást `NavLink` atribut `target`:</span><span class="sxs-lookup"><span data-stu-id="50480-196">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="50480-197">Vykresluje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="50480-197">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="50480-198">Identifikátory URI a pomocníka pro stav navigace</span><span class="sxs-lookup"><span data-stu-id="50480-198">URI and navigation state helpers</span></span>

<span data-ttu-id="50480-199">Pro práci s identifikátory URI a navigací v C# kódu použijte `Microsoft.AspNetCore.Components.NavigationManager`.</span><span class="sxs-lookup"><span data-stu-id="50480-199">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="50480-200">`NavigationManager` poskytuje událost a metody uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="50480-200">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="50480-201">Člen</span><span class="sxs-lookup"><span data-stu-id="50480-201">Member</span></span> | <span data-ttu-id="50480-202">Popis</span><span class="sxs-lookup"><span data-stu-id="50480-202">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="50480-203">Získá aktuální absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="50480-203">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="50480-204">Získá základní identifikátor URI (s koncovým lomítkem), který může být součástí relativních cest URI pro vytvoření absolutního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="50480-204">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="50480-205">@No__t-0 obvykle odpovídá atributu `href` na elementu `<base>` dokumentu v *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor).</span><span class="sxs-lookup"><span data-stu-id="50480-205">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="50480-206">Přejde k zadanému identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="50480-206">Navigates to the specified URI.</span></span> <span data-ttu-id="50480-207">Pokud je `forceLoad` `true`:</span><span class="sxs-lookup"><span data-stu-id="50480-207">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="50480-208">Směrování na straně klienta se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="50480-208">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="50480-209">Prohlížeč je nucen načíst novou stránku ze serveru, bez ohledu na to, zda je identifikátor URI obvykle zpracováván směrovačem na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="50480-209">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="50480-210">Událost, která se aktivuje, když se změní navigační umístění</span><span class="sxs-lookup"><span data-stu-id="50480-210">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="50480-211">Převede relativní identifikátor URI na absolutní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="50480-211">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="50480-212">Vzhledem k základnímu identifikátoru URI (například identifikátor URI, který dříve vrátil `GetBaseUri`), převede absolutní identifikátor URI na identifikátor URI relativní k předponě základního identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="50480-212">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="50480-213">Pokud je vybráno tlačítko, přejde následující komponenta na součást `Counter` aplikace:</span><span class="sxs-lookup"><span data-stu-id="50480-213">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```
