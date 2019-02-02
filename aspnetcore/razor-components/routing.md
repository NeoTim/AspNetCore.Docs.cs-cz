---
title: Směrování součásti syntaxe Razor
author: guardrex
description: Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/01/2019
uid: razor-components/routing
ms.openlocfilehash: 7ea7be33cf271ba1e58e74f1cf58dae998370575
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668098"
---
# <a name="razor-components-routing"></a><span data-ttu-id="07153-103">Směrování součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="07153-103">Razor Components routing</span></span>

<span data-ttu-id="07153-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="07153-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="07153-105">Zjistěte, jak směrovat požadavky v aplikacích a o NavLink komponentě.</span><span class="sxs-lookup"><span data-stu-id="07153-105">Learn how to route requests in apps and about the NavLink component.</span></span>

<span data-ttu-id="07153-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="07153-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="07153-107">Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="07153-107">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

## <a name="route-templates"></a><span data-ttu-id="07153-108">Šablony trasy</span><span class="sxs-lookup"><span data-stu-id="07153-108">Route templates</span></span>

<span data-ttu-id="07153-109">`<Router>` Součást umožňuje směrování a je k dispozici šablona trasy pro jednotlivé dostupné komponenty.</span><span class="sxs-lookup"><span data-stu-id="07153-109">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="07153-110">`<Router>` Součást se zobrazí v *App.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="07153-110">The `<Router>` component appears in the *App.cshtml* file:</span></span>

```cshtml
<Router AppAssembly=typeof(Program).Assembly />
```

<span data-ttu-id="07153-111">Při  *\*.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy [RouteAttribute](/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="07153-111">When a *\*.cshtml* file with an `@page` directive is compiled, the generated class is given a [RouteAttribute](/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) specifying the route template.</span></span> <span data-ttu-id="07153-112">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="07153-112">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="07153-113">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="07153-113">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="07153-114">V [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/), následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="07153-114">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/), the following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="07153-115">*Pages/BlazorRoute.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="07153-115">*Pages/BlazorRoute.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?start=1&end=4)]

<span data-ttu-id="07153-116">`<Router>` podporuje nastavení záložního součásti pro vykreslení při požadované trase není vyřešený.</span><span class="sxs-lookup"><span data-stu-id="07153-116">`<Router>` supports setting a fallback component for rendering when a requested route isn't resolved.</span></span> <span data-ttu-id="07153-117">Povolit tento scénář vyjádřit výslovný souhlas tím, že nastavíte `FallbackComponent` parametru na typ třídy záložní komponenty.</span><span class="sxs-lookup"><span data-stu-id="07153-117">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="07153-118">Následující příklad nastaví komponentu podle *Pages/MyFallbackRazorComponent.cshtml* záložní součástí `<Router>`:</span><span class="sxs-lookup"><span data-stu-id="07153-118">The following example sets a component defined in *Pages/MyFallbackRazorComponent.cshtml* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="07153-119">Ke generování tras správně, musí aplikace obsahovat `<base>` označení na jeho *wwwroot/index.html* soubor s základní cesty aplikace určené `href` atribut (`<base href="/" />`).</span><span class="sxs-lookup"><span data-stu-id="07153-119">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/" />`).</span></span> <span data-ttu-id="07153-120">Další informace najdete v tématu [hostitele a nasazení: Základní cesta aplikace](xref:host-and-deploy/razor-components/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="07153-120">For more information, see [Host and deploy: App base path](xref:host-and-deploy/razor-components/index#app-base-path).</span></span>

## <a name="route-parameters"></a><span data-ttu-id="07153-121">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="07153-121">Route parameters</span></span>

<span data-ttu-id="07153-122">Směrovač používá parametry trasy k naplnění odpovídajících parametrů součásti se stejným názvem (malých písmen).</span><span class="sxs-lookup"><span data-stu-id="07153-122">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

<span data-ttu-id="07153-123">*Pages/RouteParameter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="07153-123">*Pages/RouteParameter.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?start=1&end=8)]

<span data-ttu-id="07153-124">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="07153-124">Optional parameters aren't supported yet, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="07153-125">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="07153-125">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="07153-126">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="07153-126">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="07153-127">Omezení trasy</span><span class="sxs-lookup"><span data-stu-id="07153-127">Route constraints</span></span>

<span data-ttu-id="07153-128">Omezení trasy vynucuje typ odpovídající v segmentu směrování do komponenty.</span><span class="sxs-lookup"><span data-stu-id="07153-128">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="07153-129">V následujícím příkladu trasy, která má součásti uživatelé pouze odpovídá, pokud:</span><span class="sxs-lookup"><span data-stu-id="07153-129">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="07153-130">`Id` Segment směrování je k dispozici na adrese URL požadavku.</span><span class="sxs-lookup"><span data-stu-id="07153-130">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="07153-131">`Id` Segmentu je celé číslo (`int`).</span><span class="sxs-lookup"><span data-stu-id="07153-131">The `Id` segment is an integer (`int`).</span></span>

```cshtml
@page "/Users/{Id:int}"

<h1>The user Id is @Id!</h1>

@functions {
    [Parameter]
    private int Id { get; set; }
}
```

<span data-ttu-id="07153-132">Omezení trasy, které jsou uvedeny v následující tabulce jsou k dispozici pro použití.</span><span class="sxs-lookup"><span data-stu-id="07153-132">The route constraints shown in the following table are available for use.</span></span> <span data-ttu-id="07153-133">Omezení trasy, které odpovídají neutrální jazykové verzi najdete v upozornění níže uvedená tabulka pro další informace.</span><span class="sxs-lookup"><span data-stu-id="07153-133">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="07153-134">Omezení</span><span class="sxs-lookup"><span data-stu-id="07153-134">Constraint</span></span> | <span data-ttu-id="07153-135">Příklad</span><span class="sxs-lookup"><span data-stu-id="07153-135">Example</span></span>           | <span data-ttu-id="07153-136">Příklad shody</span><span class="sxs-lookup"><span data-stu-id="07153-136">Example Matches</span></span>                                                                  | <span data-ttu-id="07153-137">Invariantní</span><span class="sxs-lookup"><span data-stu-id="07153-137">Invariant</span></span><br><span data-ttu-id="07153-138">jazyková verze</span><span class="sxs-lookup"><span data-stu-id="07153-138">culture</span></span><br><span data-ttu-id="07153-139">shoda</span><span class="sxs-lookup"><span data-stu-id="07153-139">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="07153-140">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="07153-140">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="07153-141">Ne</span><span class="sxs-lookup"><span data-stu-id="07153-141">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="07153-142">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="07153-142">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="07153-143">Ano</span><span class="sxs-lookup"><span data-stu-id="07153-143">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="07153-144">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="07153-144">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="07153-145">Ano</span><span class="sxs-lookup"><span data-stu-id="07153-145">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="07153-146">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="07153-146">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="07153-147">Ano</span><span class="sxs-lookup"><span data-stu-id="07153-147">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="07153-148">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="07153-148">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="07153-149">Ano</span><span class="sxs-lookup"><span data-stu-id="07153-149">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="07153-150">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="07153-150">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="07153-151">Ne</span><span class="sxs-lookup"><span data-stu-id="07153-151">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="07153-152">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="07153-152">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="07153-153">Ano</span><span class="sxs-lookup"><span data-stu-id="07153-153">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="07153-154">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="07153-154">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="07153-155">Ano</span><span class="sxs-lookup"><span data-stu-id="07153-155">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="07153-156">Směrovat omezení, která Ověřte adresu URL a jsou převedeny na typ CLR (například `int` nebo `DateTime`) vždy používejte neutrální jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="07153-156">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="07153-157">Tato omezení předpokládá, že adresa URL je nepřekládá.</span><span class="sxs-lookup"><span data-stu-id="07153-157">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="07153-158">Komponenta NavLink</span><span class="sxs-lookup"><span data-stu-id="07153-158">NavLink component</span></span>

<span data-ttu-id="07153-159">Použít komponentu NavLink namísto HTML  **\<>** prvky při vytváření navigačních odkazů.</span><span class="sxs-lookup"><span data-stu-id="07153-159">Use a NavLink component in place of HTML **\<a>** elements when creating navigation links.</span></span> <span data-ttu-id="07153-160">Komponentu NavLink se chová jako  **\<>** elementu, s výjimkou přepíná `active` třídu šablony stylů CSS podle toho, jestli jeho `href` odpovídá aktuální adresa URL.</span><span class="sxs-lookup"><span data-stu-id="07153-160">A NavLink component behaves like an **\<a>** element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="07153-161">`active` Třídy pomáhá uživateli vědět, na stránce, které je aktivní stránkou. mezi navigační odkazy zobrazí.</span><span class="sxs-lookup"><span data-stu-id="07153-161">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="07153-162">Součástí NavMenu [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) vytvoří [Bootstrap](https://getbootstrap.com/docs/) navigačního panelu, který ukazuje, jak používat NavLink komponenty.</span><span class="sxs-lookup"><span data-stu-id="07153-162">The NavMenu component in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) creates a [Bootstrap](https://getbootstrap.com/docs/) nav bar that demonstrates how to use NavLink components.</span></span> <span data-ttu-id="07153-163">Následující kód ukazuje prvních dvou NavLinks v *Shared/NavMenu.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="07153-163">The following markup shows the first two NavLinks in the *Shared/NavMenu.cshtml* file.</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?start=13&end=24&highlight=4-6,9-11)]

<span data-ttu-id="07153-164">Existují dva `NavLinkMatch` možnosti:</span><span class="sxs-lookup"><span data-stu-id="07153-164">There are two `NavLinkMatch` options:</span></span>

* <span data-ttu-id="07153-165">`NavLinkMatch.All` &ndash; Určuje, že NavLink musí být v případě, že odpovídá celou adresu URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="07153-165">`NavLinkMatch.All` &ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* <span data-ttu-id="07153-166">`NavLinkMatch.Prefix` &ndash; Určuje, že NavLink musí být v případě, že odpovídá jakoukoli předponu adresy URL aktuální aktivní.</span><span class="sxs-lookup"><span data-stu-id="07153-166">`NavLinkMatch.Prefix` &ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="07153-167">V předchozím příkladu Home NavLink (`href=""`) odpovídá všem adresám URL a vždy přijímá `active` třídu šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="07153-167">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="07153-168">Druhý NavLink přijímá pouze `active` třídy, když uživatel navštíví BlazorRoute součásti (`href="BlazorRoute"`).</span><span class="sxs-lookup"><span data-stu-id="07153-168">The second NavLink only receives the `active` class when the user visits the BlazorRoute component (`href="BlazorRoute"`).</span></span>
