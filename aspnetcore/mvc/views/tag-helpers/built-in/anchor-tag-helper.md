---
title: Pomocný objekt příznaku kotvy v ASP.NET Core
author: pkellner
description: Seznamte se s atributy pomocníka značek ukotvení ASP.NET Core a role, kterou každý atribut hraje při rozšiřování chování značky ukotvení HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 6bfbad39115c7823b5677d3c52ca64cfb0683037
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664001"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="49340-103">Pomocný objekt příznaku kotvy v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49340-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="49340-104">Od [Petra Kellner](https://peterkellner.net) a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="49340-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="49340-105">[Pomocník značek kotvy](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) vylepšuje standardní značku HTML kotvy (`<a ... ></a>`) přidáním nových atributů.</span><span class="sxs-lookup"><span data-stu-id="49340-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="49340-106">Podle konvence jsou názvy atributů předponou `asp-`.</span><span class="sxs-lookup"><span data-stu-id="49340-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="49340-107">Hodnota atributu `href` prvku vykresleného ukotvení je určena hodnotami atributů `asp-`.</span><span class="sxs-lookup"><span data-stu-id="49340-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="49340-108">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="49340-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="49340-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49340-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="49340-110">*SpeakerController* se používá v ukázkách v tomto dokumentu:</span><span class="sxs-lookup"><span data-stu-id="49340-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="49340-111">Pomocné atributy značek ukotvení</span><span class="sxs-lookup"><span data-stu-id="49340-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="49340-112">ASP – kontroler</span><span class="sxs-lookup"><span data-stu-id="49340-112">asp-controller</span></span>

<span data-ttu-id="49340-113">Atribut [kontroleru ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) přiřadí kontroler použitý k VYGENEROVÁNÍ adresy URL.</span><span class="sxs-lookup"><span data-stu-id="49340-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="49340-114">Následující kód uvádí všechny reproduktory:</span><span class="sxs-lookup"><span data-stu-id="49340-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="49340-115">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="49340-116">Pokud je zadán atribut `asp-controller` a `asp-action` není, výchozí hodnota `asp-action` je akce kontroleru přidružená k aktuálně zpracovávanému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="49340-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="49340-117">Pokud je `asp-action` vynechána v předchozím kódu a pomocník značek Anchor je použit v zobrazení *indexu* ( */Home*) *HomeController*, vygenerovaný kód HTML je:</span><span class="sxs-lookup"><span data-stu-id="49340-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="49340-118">ASP – akce</span><span class="sxs-lookup"><span data-stu-id="49340-118">asp-action</span></span>

<span data-ttu-id="49340-119">Hodnota atributu [ASP-Action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) reprezentuje název akce kontroleru obsažený v atributu vygenerovaného `href`.</span><span class="sxs-lookup"><span data-stu-id="49340-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="49340-120">Následující kód nastaví vygenerovanou hodnotu atributu `href` na stránku hodnocení mluvčího:</span><span class="sxs-lookup"><span data-stu-id="49340-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="49340-121">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="49340-122">Pokud není zadán žádný atribut `asp-controller`, je použit výchozí řadič volající zobrazení, které spouští aktuální zobrazení.</span><span class="sxs-lookup"><span data-stu-id="49340-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="49340-123">Pokud je hodnota atributu `asp-action` `Index`, není k adrese URL připojena žádná akce, což vede k vyvolání výchozí `Index` akci.</span><span class="sxs-lookup"><span data-stu-id="49340-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="49340-124">Zadaná akce (nebo výchozí) musí existovat v řadiči, na který odkazuje `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="49340-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="49340-125">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="49340-125">asp-route-{value}</span></span>

<span data-ttu-id="49340-126">Atribut [ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) povoluje předponu trasy se zástupnými znaky.</span><span class="sxs-lookup"><span data-stu-id="49340-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="49340-127">Jakákoli hodnota, která je zabírat `{value}` zástupný symbol, je interpretována jako potenciální parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="49340-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="49340-128">Pokud se výchozí trasa nenajde, připojí se tato předpona trasy k vygenerovanému `href` atributu jako parametr požadavku a hodnota.</span><span class="sxs-lookup"><span data-stu-id="49340-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="49340-129">V opačném případě je nahrazena šablonou směrování.</span><span class="sxs-lookup"><span data-stu-id="49340-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="49340-130">Vezměte v úvahu následující akci kontroleru:</span><span class="sxs-lookup"><span data-stu-id="49340-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="49340-131">S výchozí šablonou směrování definovanou při *spuštění. Nakonfigurujte*:</span><span class="sxs-lookup"><span data-stu-id="49340-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="49340-132">Zobrazení MVC používá model, který je poskytován akcí, následovně:</span><span class="sxs-lookup"><span data-stu-id="49340-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="49340-133">Byl spárován odpovídající zástupný symbol `{id?}` výchozí trasy.</span><span class="sxs-lookup"><span data-stu-id="49340-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="49340-134">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="49340-135">Předpokládat, že předpona trasy není součástí vyhovující šablony směrování, jako u následujícího zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="49340-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="49340-136">Následující kód HTML je vygenerován, protože ve shodě trasy nebyl nalezen `speakerid`:</span><span class="sxs-lookup"><span data-stu-id="49340-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="49340-137">Pokud nejsou zadány `asp-controller` nebo `asp-action`, bude stejné výchozí zpracování následováno v atributu `asp-route`.</span><span class="sxs-lookup"><span data-stu-id="49340-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="49340-138">ASP – trasa</span><span class="sxs-lookup"><span data-stu-id="49340-138">asp-route</span></span>

<span data-ttu-id="49340-139">Atribut [ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) se používá k vytvoření adresy URL odkazující přímo na pojmenovanou trasu.</span><span class="sxs-lookup"><span data-stu-id="49340-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="49340-140">Pomocí [atributů směrování](xref:mvc/controllers/routing#attribute-routing)může být trasa pojmenována, jak je znázorněno v `SpeakerController` a která se používá ve své `Evaluations` akci:</span><span class="sxs-lookup"><span data-stu-id="49340-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="49340-141">V následujícím kódu atribut `asp-route` odkazuje na pojmenovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="49340-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="49340-142">Pomocník značek ukotvení generuje trasu přímo k této akci kontroleru pomocí adresy URL */Speaker/evaluations*.</span><span class="sxs-lookup"><span data-stu-id="49340-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="49340-143">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="49340-144">Pokud je kromě `asp-route`zadaná `asp-controller` nebo `asp-action`, vygenerovaná trasa nemusí být to, co očekáváte.</span><span class="sxs-lookup"><span data-stu-id="49340-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="49340-145">Chcete-li se vyhnout konfliktu trasy, `asp-route` by neměl být použit s atributy `asp-controller` a `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="49340-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="49340-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="49340-146">asp-all-route-data</span></span>

<span data-ttu-id="49340-147">Atribut [ASP-All-Route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) podporuje vytvoření slovníku párů klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="49340-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="49340-148">Klíč je název parametru a hodnota je hodnota parametru.</span><span class="sxs-lookup"><span data-stu-id="49340-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="49340-149">V následujícím příkladu je slovník inicializován a předán zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="49340-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="49340-150">Případně můžete data předávat pomocí modelu.</span><span class="sxs-lookup"><span data-stu-id="49340-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="49340-151">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="49340-152">`asp-all-route-data` slovník je sloučený tak, aby vytvořil QueryString splnění požadavků na přetíženou akci `Evaluations`:</span><span class="sxs-lookup"><span data-stu-id="49340-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="49340-153">Pokud všechny klíče ve slovníku odpovídají parametrům směrování, tyto hodnoty se podle potřeby nahradí v trase.</span><span class="sxs-lookup"><span data-stu-id="49340-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="49340-154">Jiné neshodné hodnoty jsou vygenerovány jako parametry žádosti.</span><span class="sxs-lookup"><span data-stu-id="49340-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="49340-155">ASP – fragment</span><span class="sxs-lookup"><span data-stu-id="49340-155">asp-fragment</span></span>

<span data-ttu-id="49340-156">Atribut [fragment ASP –](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) definuje fragment adresy URL pro připojení k adrese URL.</span><span class="sxs-lookup"><span data-stu-id="49340-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="49340-157">Pomocník značek ukotvení přidá znak hash (#).</span><span class="sxs-lookup"><span data-stu-id="49340-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="49340-158">Vezměte v úvahu následující značky:</span><span class="sxs-lookup"><span data-stu-id="49340-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="49340-159">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="49340-160">Značky hash jsou užitečné při sestavování aplikací na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="49340-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="49340-161">Je možné je použít k snadnému označení a hledání v JavaScriptu, například.</span><span class="sxs-lookup"><span data-stu-id="49340-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="49340-162">ASP – oblast</span><span class="sxs-lookup"><span data-stu-id="49340-162">asp-area</span></span>

<span data-ttu-id="49340-163">Atribut [Area ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) nastavuje název oblasti použitý k nastavení příslušné trasy.</span><span class="sxs-lookup"><span data-stu-id="49340-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="49340-164">V následujících příkladech je znázorněno, jak atribut `asp-area` způsobuje přemapování tras.</span><span class="sxs-lookup"><span data-stu-id="49340-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="49340-165">Využití v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="49340-165">Usage in Razor Pages</span></span>

<span data-ttu-id="49340-166">Razor Pages oblasti jsou podporovány v ASP.NET Core 2,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="49340-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="49340-167">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="49340-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="49340-168">**{Název projektu}**</span><span class="sxs-lookup"><span data-stu-id="49340-168">**{Project name}**</span></span>
  * <span data-ttu-id="49340-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="49340-169">**wwwroot**</span></span>
  * <span data-ttu-id="49340-170">**Oblasti**</span><span class="sxs-lookup"><span data-stu-id="49340-170">**Areas**</span></span>
    * <span data-ttu-id="49340-171">**Rušování**</span><span class="sxs-lookup"><span data-stu-id="49340-171">**Sessions**</span></span>
      * <span data-ttu-id="49340-172">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="49340-172">**Pages**</span></span>
        * <span data-ttu-id="49340-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="49340-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="49340-174">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="49340-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="49340-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="49340-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="49340-176">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="49340-176">**Pages**</span></span>

<span data-ttu-id="49340-177">Značka, na kterou se odkazuje na stránku *indexu* oblasti *relací* , je:</span><span class="sxs-lookup"><span data-stu-id="49340-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="49340-178">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="49340-179">Chcete-li podporovat oblasti aplikace Razor Pages, proveďte v `Startup.ConfigureServices`jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="49340-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="49340-180">Nastavte [verzi kompatibility](xref:mvc/compatibility-version) na 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="49340-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="49340-181">Nastavte vlastnost [RazorPagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) na `true`:</span><span class="sxs-lookup"><span data-stu-id="49340-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="49340-182">Využití v MVC</span><span class="sxs-lookup"><span data-stu-id="49340-182">Usage in MVC</span></span>

<span data-ttu-id="49340-183">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="49340-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="49340-184">**{Název projektu}**</span><span class="sxs-lookup"><span data-stu-id="49340-184">**{Project name}**</span></span>
  * <span data-ttu-id="49340-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="49340-185">**wwwroot**</span></span>
  * <span data-ttu-id="49340-186">**Oblasti**</span><span class="sxs-lookup"><span data-stu-id="49340-186">**Areas**</span></span>
    * <span data-ttu-id="49340-187">**Blogy**</span><span class="sxs-lookup"><span data-stu-id="49340-187">**Blogs**</span></span>
      * <span data-ttu-id="49340-188">**Kontrolery**</span><span class="sxs-lookup"><span data-stu-id="49340-188">**Controllers**</span></span>
        * <span data-ttu-id="49340-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="49340-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="49340-190">**Zobrazení**</span><span class="sxs-lookup"><span data-stu-id="49340-190">**Views**</span></span>
        * <span data-ttu-id="49340-191">**Domovské**</span><span class="sxs-lookup"><span data-stu-id="49340-191">**Home**</span></span>
          * <span data-ttu-id="49340-192">*AboutBlog. cshtml*</span><span class="sxs-lookup"><span data-stu-id="49340-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="49340-193">*Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="49340-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="49340-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="49340-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="49340-195">**Kontrolery**</span><span class="sxs-lookup"><span data-stu-id="49340-195">**Controllers**</span></span>

<span data-ttu-id="49340-196">Nastavení `asp-area` na "Blogy" přidělí oblasti adresáře */Blogy* na trasy přidružených řadičů a zobrazení pro tuto značku ukotvení.</span><span class="sxs-lookup"><span data-stu-id="49340-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="49340-197">Značka pro odkaz na zobrazení *AboutBlog* je:</span><span class="sxs-lookup"><span data-stu-id="49340-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="49340-198">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="49340-199">Aby bylo možné podporovat oblasti v aplikaci MVC, šablona trasy musí zahrnovat odkaz na oblast, pokud existuje.</span><span class="sxs-lookup"><span data-stu-id="49340-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="49340-200">Tato šablona je reprezentovaná druhým parametrem volání metody `routes.MapRoute` v *Startup. Configure*:</span><span class="sxs-lookup"><span data-stu-id="49340-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="49340-201">protokol ASP</span><span class="sxs-lookup"><span data-stu-id="49340-201">asp-protocol</span></span>

<span data-ttu-id="49340-202">Atribut [ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) je určen pro určení protokolu (například `https`) ve vaší adrese URL.</span><span class="sxs-lookup"><span data-stu-id="49340-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="49340-203">Příklad:</span><span class="sxs-lookup"><span data-stu-id="49340-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="49340-204">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="49340-205">Název hostitele v příkladu je localhost.</span><span class="sxs-lookup"><span data-stu-id="49340-205">The host name in the example is localhost.</span></span> <span data-ttu-id="49340-206">Pomocník značek ukotvení používá při generování adresy URL veřejnou doménu webu.</span><span class="sxs-lookup"><span data-stu-id="49340-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="49340-207">ASP – hostitel</span><span class="sxs-lookup"><span data-stu-id="49340-207">asp-host</span></span>

<span data-ttu-id="49340-208">Atribut [Host ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) slouží k zadání názvu hostitele v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="49340-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="49340-209">Příklad:</span><span class="sxs-lookup"><span data-stu-id="49340-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="49340-210">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="49340-211">ASP – stránka</span><span class="sxs-lookup"><span data-stu-id="49340-211">asp-page</span></span>

<span data-ttu-id="49340-212">Atribut [stránky ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) se používá s Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="49340-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="49340-213">Použijte ho k nastavení hodnoty atributu `href` značky kotvy na konkrétní stránku.</span><span class="sxs-lookup"><span data-stu-id="49340-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="49340-214">Předpona názvu stránky s lomítkem ("/") vytvoří adresu URL.</span><span class="sxs-lookup"><span data-stu-id="49340-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="49340-215">Následující ukázka odkazuje na stránku Razor pro daného účastníka:</span><span class="sxs-lookup"><span data-stu-id="49340-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="49340-216">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="49340-217">Atribut `asp-page` se vzájemně vylučuje pomocí atributů `asp-route`, `asp-controller`a `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="49340-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="49340-218">`asp-page` však lze použít s `asp-route-{value}` k řízení směrování, jak ukazuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="49340-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="49340-219">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="49340-220">ASP – obslužná rutina stránky</span><span class="sxs-lookup"><span data-stu-id="49340-220">asp-page-handler</span></span>

<span data-ttu-id="49340-221">Atribut [ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) se používá s Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="49340-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="49340-222">Je určený pro propojení s konkrétními obslužnými rutinami stránky.</span><span class="sxs-lookup"><span data-stu-id="49340-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="49340-223">Vezměte v úvahu následující obslužnou rutinu stránky:</span><span class="sxs-lookup"><span data-stu-id="49340-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="49340-224">Přidružená označení značek modelu stránky k obslužné rutině stránky `OnGetProfile`.</span><span class="sxs-lookup"><span data-stu-id="49340-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="49340-225">Všimněte si, že předpona `On<Verb>` názvu metody obslužné rutiny stránky je v hodnotě atributu `asp-page-handler` vynechána.</span><span class="sxs-lookup"><span data-stu-id="49340-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="49340-226">Je-li metoda asynchronní, je `Async` přípona vynechána.</span><span class="sxs-lookup"><span data-stu-id="49340-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="49340-227">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="49340-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="49340-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="49340-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
