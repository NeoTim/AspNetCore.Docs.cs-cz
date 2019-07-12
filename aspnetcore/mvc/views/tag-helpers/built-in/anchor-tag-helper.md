---
title: Ukotvení pomocné rutiny značky v ASP.NET Core
author: pkellner
description: Objevte atributy ASP.NET Core ukotvení pomocné rutiny značky a roli, kterou každý atribut hraje v rozšíření chování značky jazyka HTML anchor.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/18/2019
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 44fc4ff6334e33630974181021aa1a6880a89917
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856228"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="92cc8-103">Ukotvení pomocné rutiny značky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92cc8-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="92cc8-104">Podle [Peter Kellner](https://peterkellner.net) a [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="92cc8-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="92cc8-105">[Ukotvení pomocné rutiny značky](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) zvyšuje standard HTML anchor (`<a ... ></a>`) tak, že přidáte nové atributy značky.</span><span class="sxs-lookup"><span data-stu-id="92cc8-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="92cc8-106">Podle konvence mají předponu názvů atributů `asp-`.</span><span class="sxs-lookup"><span data-stu-id="92cc8-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="92cc8-107">Prvek vykreslené ukotvení `href` hodnota atributu je určen podle hodnot `asp-` atributy.</span><span class="sxs-lookup"><span data-stu-id="92cc8-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="92cc8-108">Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="92cc8-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="92cc8-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="92cc8-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="92cc8-110">*SpeakerController* se používá v ukázky v tomto dokumentu:</span><span class="sxs-lookup"><span data-stu-id="92cc8-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="92cc8-111">Ukotvení pomocné rutiny značky atributy</span><span class="sxs-lookup"><span data-stu-id="92cc8-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="92cc8-112">kontroler ASP</span><span class="sxs-lookup"><span data-stu-id="92cc8-112">asp-controller</span></span>

<span data-ttu-id="92cc8-113">[Asp kontroleru](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) atribut přiřadí sloužit ke generování adresy URL kontroleru.</span><span class="sxs-lookup"><span data-stu-id="92cc8-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="92cc8-114">Následující kód uvádí všechny přednášející:</span><span class="sxs-lookup"><span data-stu-id="92cc8-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="92cc8-115">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="92cc8-116">Pokud `asp-controller` je zadán atribut a `asp-action` není výchozí `asp-action` hodnotu akce kontroleru, který je přidružený k aktuálně prováděné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92cc8-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="92cc8-117">Pokud `asp-action` je vynecháno z předchozí kód a ukotvení pomocné rutiny značky se používá v *HomeController*společnosti *Index* zobrazení ( */Home*), je generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

## <a name="asp-action"></a><span data-ttu-id="92cc8-118">Akce ASP</span><span class="sxs-lookup"><span data-stu-id="92cc8-118">asp-action</span></span>

<span data-ttu-id="92cc8-119">[Asp akce](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) hodnota atributu představuje název akce kontroleru součástí generované `href` atribut.</span><span class="sxs-lookup"><span data-stu-id="92cc8-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="92cc8-120">Následující kód nastaví generované `href` hodnotu atributu na stránce hodnocení mluvčího:</span><span class="sxs-lookup"><span data-stu-id="92cc8-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="92cc8-121">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="92cc8-122">Pokud ne `asp-controller` atribut zadán, je použit výchozí řadič volání zobrazení provádění aktuálního zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92cc8-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="92cc8-123">Pokud `asp-action` hodnota atributu je `Index`, pak žádná akce je připojena k adrese URL, což vede k vyvolání výchozí `Index` akce.</span><span class="sxs-lookup"><span data-stu-id="92cc8-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="92cc8-124">Akce zadané (nebo nastavit na výchozí hodnotu), musí existovat v kontroleru odkazuje `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="92cc8-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="92cc8-125">ASP - route-{value}</span><span class="sxs-lookup"><span data-stu-id="92cc8-125">asp-route-{value}</span></span>

<span data-ttu-id="92cc8-126">[Asp - route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) atribut umožňuje předponu trasy zástupný znak.</span><span class="sxs-lookup"><span data-stu-id="92cc8-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="92cc8-127">Všechny hodnoty zabírá `{value}` zástupný symbol je interpretován jako potenciální parametr trasa.</span><span class="sxs-lookup"><span data-stu-id="92cc8-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="92cc8-128">Pokud výchozí trasa není nalezen, tuto předponu trasy, se připojí k generované `href` atribut jako parametr žádosti a hodnotu.</span><span class="sxs-lookup"><span data-stu-id="92cc8-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="92cc8-129">V opačném případě je nahrazena v šabloně trasy.</span><span class="sxs-lookup"><span data-stu-id="92cc8-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="92cc8-130">Vezměte v úvahu následující akce kontroleru:</span><span class="sxs-lookup"><span data-stu-id="92cc8-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="92cc8-131">Pomocí výchozí šablony trasy definované v *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="92cc8-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="92cc8-132">Zobrazení MVC používá model poskytované akci, následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="92cc8-132">The MVC view uses the model, provided by the action, as follows:</span></span>

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

<span data-ttu-id="92cc8-133">Výchozí trasa `{id?}` odpovídal zástupný symbol.</span><span class="sxs-lookup"><span data-stu-id="92cc8-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="92cc8-134">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="92cc8-135">Předpokládejme, že předponu trasy, které nejsou součástí odpovídající směrování šablony, stejně jako u následující zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="92cc8-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

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

<span data-ttu-id="92cc8-136">Následující kód HTML je generována, protože `speakerid` nebyl nalezen v odpovídající trasy:</span><span class="sxs-lookup"><span data-stu-id="92cc8-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="92cc8-137">Pokud `asp-controller` nebo `asp-action` nejsou zadané, pak stejnou výchozí zpracování je a potom je `asp-route` atribut.</span><span class="sxs-lookup"><span data-stu-id="92cc8-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="92cc8-138">ASP trasy</span><span class="sxs-lookup"><span data-stu-id="92cc8-138">asp-route</span></span>

<span data-ttu-id="92cc8-139">[Trasy asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) atribut se používá pro vytvoření adresy URL propojení přímo s pojmenovanou trasu.</span><span class="sxs-lookup"><span data-stu-id="92cc8-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="92cc8-140">Pomocí [směrování atributů](xref:mvc/controllers/routing#attribute-routing), může mít název trasy, jak je znázorněno `SpeakerController` a používat v jeho `Evaluations` akce:</span><span class="sxs-lookup"><span data-stu-id="92cc8-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="92cc8-141">V následujícím kódu `asp-route` atribut odkazuje na pojmenovanou trasu:</span><span class="sxs-lookup"><span data-stu-id="92cc8-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="92cc8-142">Ukotvení pomocné rutiny značky vytváří trasu přímo k této akci kontroleru pomocí adresy URL */mluvčího/hodnocení*.</span><span class="sxs-lookup"><span data-stu-id="92cc8-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="92cc8-143">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="92cc8-144">Pokud `asp-controller` nebo `asp-action` určena kromě `asp-route`, postupu generované nemusí být co očekáváte.</span><span class="sxs-lookup"><span data-stu-id="92cc8-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="92cc8-145">Aby se zabránilo konfliktu trasy `asp-route` by neměl být použit s `asp-controller` a `asp-action` atributy.</span><span class="sxs-lookup"><span data-stu-id="92cc8-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

## <a name="asp-all-route-data"></a><span data-ttu-id="92cc8-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="92cc8-146">asp-all-route-data</span></span>

<span data-ttu-id="92cc8-147">[Asp všechny trasy dat](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) atribut podporuje vytváření slovník párů klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="92cc8-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="92cc8-148">Klíč je název parametru a hodnota je hodnota tohoto parametru.</span><span class="sxs-lookup"><span data-stu-id="92cc8-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="92cc8-149">V následujícím příkladu je slovník inicializován a předána do zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="92cc8-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="92cc8-150">Alternativně může předávat data pomocí modelu.</span><span class="sxs-lookup"><span data-stu-id="92cc8-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="92cc8-151">Předcházející kód vygeneruje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="92cc8-152">`asp-all-route-data` Slovníku se sloučí k vytvoření dotazu splnění požadavků přetížené `Evaluations` akce:</span><span class="sxs-lookup"><span data-stu-id="92cc8-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="92cc8-153">Pokud se ve slovníku všechny klíče shodují parametry trasy, tyto hodnoty jsou nahrazeny v postupu podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="92cc8-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="92cc8-154">Neodpovídající hodnoty jsou generovány jako parametry požadavku.</span><span class="sxs-lookup"><span data-stu-id="92cc8-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="92cc8-155">ASP fragment</span><span class="sxs-lookup"><span data-stu-id="92cc8-155">asp-fragment</span></span>

<span data-ttu-id="92cc8-156">[Asp fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) atribut definuje fragment adresy URL pro připojení k adrese URL.</span><span class="sxs-lookup"><span data-stu-id="92cc8-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="92cc8-157">Ukotvení pomocné rutiny značky přidá znak hash (#).</span><span class="sxs-lookup"><span data-stu-id="92cc8-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="92cc8-158">Vezměte v úvahu následující kód:</span><span class="sxs-lookup"><span data-stu-id="92cc8-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="92cc8-159">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="92cc8-160">Hodnota hash značky jsou užitečné při vytváření aplikací na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="92cc8-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="92cc8-161">Jejich lze snadno označení a vyhledávání v jazyce JavaScript, třeba.</span><span class="sxs-lookup"><span data-stu-id="92cc8-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="92cc8-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="92cc8-162">asp-area</span></span>

<span data-ttu-id="92cc8-163">[Asp oblasti](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) atribut nastaví název oblasti, která slouží k nastavení odpovídající trasy.</span><span class="sxs-lookup"><span data-stu-id="92cc8-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="92cc8-164">Následující příklady znázornění jak `asp-area` atribut způsobí, že přemapování trasy.</span><span class="sxs-lookup"><span data-stu-id="92cc8-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

### <a name="usage-in-razor-pages"></a><span data-ttu-id="92cc8-165">Použití stránek Razor</span><span class="sxs-lookup"><span data-stu-id="92cc8-165">Usage in Razor Pages</span></span>

<span data-ttu-id="92cc8-166">Oblasti stránek Razor se podporují v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="92cc8-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="92cc8-167">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="92cc8-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="92cc8-168">**{Název projektu}**</span><span class="sxs-lookup"><span data-stu-id="92cc8-168">**{Project name}**</span></span>
  * <span data-ttu-id="92cc8-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="92cc8-169">**wwwroot**</span></span>
  * <span data-ttu-id="92cc8-170">**Oblasti**</span><span class="sxs-lookup"><span data-stu-id="92cc8-170">**Areas**</span></span>
    * <span data-ttu-id="92cc8-171">**Relace**</span><span class="sxs-lookup"><span data-stu-id="92cc8-171">**Sessions**</span></span>
      * <span data-ttu-id="92cc8-172">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="92cc8-172">**Pages**</span></span>
        * <span data-ttu-id="92cc8-173">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92cc8-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="92cc8-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92cc8-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="92cc8-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="92cc8-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="92cc8-176">**Stránky**</span><span class="sxs-lookup"><span data-stu-id="92cc8-176">**Pages**</span></span>

<span data-ttu-id="92cc8-177">Kód tak, aby odkazovaly *relace* oblasti *Index* je stránka Razor:</span><span class="sxs-lookup"><span data-stu-id="92cc8-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="92cc8-178">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="92cc8-179">Pro podporu oblasti v aplikaci pro stránky Razor, proveďte jednu z následujících postupů v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="92cc8-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="92cc8-180">Nastavte [verze kompatibility](xref:mvc/compatibility-version) 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="92cc8-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="92cc8-181">Nastavte [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) vlastnost `true`:</span><span class="sxs-lookup"><span data-stu-id="92cc8-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

### <a name="usage-in-mvc"></a><span data-ttu-id="92cc8-182">Použití v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="92cc8-182">Usage in MVC</span></span>

<span data-ttu-id="92cc8-183">Vezměte v úvahu následující hierarchii adresářů:</span><span class="sxs-lookup"><span data-stu-id="92cc8-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="92cc8-184">**{Název projektu}**</span><span class="sxs-lookup"><span data-stu-id="92cc8-184">**{Project name}**</span></span>
  * <span data-ttu-id="92cc8-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="92cc8-185">**wwwroot**</span></span>
  * <span data-ttu-id="92cc8-186">**Oblasti**</span><span class="sxs-lookup"><span data-stu-id="92cc8-186">**Areas**</span></span>
    * <span data-ttu-id="92cc8-187">**Blogy**</span><span class="sxs-lookup"><span data-stu-id="92cc8-187">**Blogs**</span></span>
      * <span data-ttu-id="92cc8-188">**Kontrolery**</span><span class="sxs-lookup"><span data-stu-id="92cc8-188">**Controllers**</span></span>
        * <span data-ttu-id="92cc8-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="92cc8-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="92cc8-190">**Zobrazení**</span><span class="sxs-lookup"><span data-stu-id="92cc8-190">**Views**</span></span>
        * <span data-ttu-id="92cc8-191">**Domovská stránka**</span><span class="sxs-lookup"><span data-stu-id="92cc8-191">**Home**</span></span>
          * <span data-ttu-id="92cc8-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92cc8-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="92cc8-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92cc8-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="92cc8-194">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="92cc8-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="92cc8-195">**Kontrolery**</span><span class="sxs-lookup"><span data-stu-id="92cc8-195">**Controllers**</span></span>

<span data-ttu-id="92cc8-196">Nastavení `asp-area` "Blogy" předpon adresáři *oblasti/blogy* trasy z přidružených kontrolerů a zobrazení pro tuto značku ukotvení.</span><span class="sxs-lookup"><span data-stu-id="92cc8-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="92cc8-197">Kód tak, aby odkazovaly *AboutBlog* zobrazení:</span><span class="sxs-lookup"><span data-stu-id="92cc8-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="92cc8-198">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="92cc8-199">Pro podporu oblasti v aplikaci MVC, šablona trasy musí obsahovat odkaz na oblast, pokud existuje.</span><span class="sxs-lookup"><span data-stu-id="92cc8-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="92cc8-200">Tato šablona je reprezentována druhý parametr `routes.MapRoute` volání metody *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="92cc8-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="92cc8-201">protokol ASP</span><span class="sxs-lookup"><span data-stu-id="92cc8-201">asp-protocol</span></span>

<span data-ttu-id="92cc8-202">[Protokolu asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) atribut slouží k určení protokol (například `https`) v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="92cc8-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="92cc8-203">Příklad:</span><span class="sxs-lookup"><span data-stu-id="92cc8-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="92cc8-204">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="92cc8-205">Název hostitele v tomto příkladu je localhost.</span><span class="sxs-lookup"><span data-stu-id="92cc8-205">The host name in the example is localhost.</span></span> <span data-ttu-id="92cc8-206">Ukotvení pomocné rutiny značky používá veřejnou doménu na webu při generování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="92cc8-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="92cc8-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="92cc8-207">asp-host</span></span>

<span data-ttu-id="92cc8-208">[Asp hostitele](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) atribut slouží k určení názvu hostitele v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="92cc8-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="92cc8-209">Příklad:</span><span class="sxs-lookup"><span data-stu-id="92cc8-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="92cc8-210">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="92cc8-211">stránka ASP</span><span class="sxs-lookup"><span data-stu-id="92cc8-211">asp-page</span></span>

<span data-ttu-id="92cc8-212">[Stránka asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) atribut se používá se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="92cc8-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="92cc8-213">Můžete nastavit značku ukotvení `href` hodnotu atributu na určitou stránku.</span><span class="sxs-lookup"><span data-stu-id="92cc8-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="92cc8-214">Předpony názvu stránky s lomítkem ("/") se vytvoří adresa URL.</span><span class="sxs-lookup"><span data-stu-id="92cc8-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="92cc8-215">Následující příklad ukazuje na účastník stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="92cc8-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="92cc8-216">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="92cc8-217">`asp-page` Atribut je vzájemně se vylučuje s `asp-route`, `asp-controller`, a `asp-action` atributy.</span><span class="sxs-lookup"><span data-stu-id="92cc8-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="92cc8-218">Ale `asp-page` jde použít s `asp-route-{value}` řídit směrování, jak ukazuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="92cc8-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="92cc8-219">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="92cc8-220">rutina stránky ASP</span><span class="sxs-lookup"><span data-stu-id="92cc8-220">asp-page-handler</span></span>

<span data-ttu-id="92cc8-221">[Rutina stránky asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) atribut se používá se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="92cc8-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="92cc8-222">Je určena pro odkazování na určitou stránku obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="92cc8-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="92cc8-223">Vezměte v úvahu následující rutiny stránky:</span><span class="sxs-lookup"><span data-stu-id="92cc8-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="92cc8-224">Model stránky přidruženého k tomuto kódu odkazy na `OnGetProfile` obslužná rutina stránky.</span><span class="sxs-lookup"><span data-stu-id="92cc8-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="92cc8-225">Poznámka: `On<Verb>` předpona názvu metody obslužné rutiny stránky je vynecháno v `asp-page-handler` hodnotu atributu.</span><span class="sxs-lookup"><span data-stu-id="92cc8-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="92cc8-226">Pokud je metoda asynchronní, `Async` přípona je tento parametr vynechán, příliš.</span><span class="sxs-lookup"><span data-stu-id="92cc8-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="92cc8-227">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="92cc8-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="92cc8-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="92cc8-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
