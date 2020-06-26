---
title: Vkládání závislostí do zobrazení v ASP.NET Core
author: ardalis
description: Přečtěte si, jak ASP.NET Core podporuje vkládání závislostí do zobrazení MVC.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/dependency-injection
ms.openlocfilehash: 0cc452b25392fe92c87ef346b2e16350fb3ec19a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405884"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="8d93d-103">Vkládání závislostí do zobrazení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d93d-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="8d93d-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8d93d-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8d93d-105">ASP.NET Core podporuje [vkládání závislostí](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8d93d-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="8d93d-106">To může být užitečné pro služby specifické pro zobrazení, jako je například lokalizace nebo data požadovaná pouze pro naplnění prvků zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8d93d-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="8d93d-107">Měli byste se pokusit udržet [oddělení otázek](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) mezi řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="8d93d-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="8d93d-108">Většina dat, která jsou zobrazena v zobrazení, by měla být předána z kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8d93d-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="8d93d-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8d93d-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="8d93d-110">Vkládání konfigurace</span><span class="sxs-lookup"><span data-stu-id="8d93d-110">Configuration injection</span></span>

<span data-ttu-id="8d93d-111">*appsettings.jsna* hodnoty lze vložit přímo do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8d93d-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="8d93d-112">Příklad *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="8d93d-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="8d93d-113">Syntaxe pro `@inject` :`@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="8d93d-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="8d93d-114">Příklad použití `@inject` :</span><span class="sxs-lookup"><span data-stu-id="8d93d-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="8d93d-115">Vložení služby</span><span class="sxs-lookup"><span data-stu-id="8d93d-115">Service injection</span></span>

<span data-ttu-id="8d93d-116">Službu lze vložit do zobrazení pomocí `@inject` direktivy.</span><span class="sxs-lookup"><span data-stu-id="8d93d-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="8d93d-117">Můžete si představit `@inject` jako přidání vlastnosti do zobrazení a naplnění vlastnosti pomocí di.</span><span class="sxs-lookup"><span data-stu-id="8d93d-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="8d93d-118">Toto zobrazení obsahuje seznam `ToDoItem` instancí spolu se souhrnem znázorňujícím celkovou statistiku.</span><span class="sxs-lookup"><span data-stu-id="8d93d-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="8d93d-119">Shrnutí se vyplní ze vložených `StatisticsService` .</span><span class="sxs-lookup"><span data-stu-id="8d93d-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="8d93d-120">Tato služba je zaregistrovaná pro vkládání závislostí v `ConfigureServices` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d93d-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="8d93d-121">`StatisticsService`Provede některé výpočty se sadou `ToDoItem` instancí, ke kterým přistupuje prostřednictvím úložiště:</span><span class="sxs-lookup"><span data-stu-id="8d93d-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="8d93d-122">Ukázkové úložiště používá kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="8d93d-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="8d93d-123">Výše uvedená implementace (která funguje na všech datech v paměti) se nedoporučuje pro velké, vzdáleně dostupné datové sady.</span><span class="sxs-lookup"><span data-stu-id="8d93d-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="8d93d-124">Ukázka zobrazuje data z modelu svázaného se zobrazením a služba je vložena do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="8d93d-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Chcete-li zobrazit seznam položek celkem, dokončené položky, průměrná priorita a seznam úkolů s jejich úrovněmi priority a logickými hodnotami, které označují dokončení.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="8d93d-126">Naplnění dat vyhledávání</span><span class="sxs-lookup"><span data-stu-id="8d93d-126">Populating Lookup Data</span></span>

<span data-ttu-id="8d93d-127">Injektáže zobrazení může být užitečné k naplnění možností v prvcích uživatelského rozhraní, jako jsou rozevírací seznamy.</span><span class="sxs-lookup"><span data-stu-id="8d93d-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="8d93d-128">Vezměte v úvahu formulář profilu uživatele, který obsahuje možnosti pro určení rovnosti, stavu a dalších předvoleb.</span><span class="sxs-lookup"><span data-stu-id="8d93d-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="8d93d-129">Vykreslení takového formuláře pomocí přístupu ke standardní MVC by vyžadovalo, aby kontroler požadoval služby Data Access pro každou z těchto sad možností a pak naplnil model nebo `ViewBag` každou sadu možností, které se mají svázat.</span><span class="sxs-lookup"><span data-stu-id="8d93d-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="8d93d-130">Alternativní metoda přímo vloží služby do zobrazení a získá možnosti.</span><span class="sxs-lookup"><span data-stu-id="8d93d-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="8d93d-131">Tím se minimalizuje množství kódu, který vyžaduje kontroler, přesunutím této logiky konstrukce zobrazení do samotného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8d93d-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="8d93d-132">Akce kontroleru pro zobrazení formuláře pro úpravy profilu musí předat pouze formulář instance profilu:</span><span class="sxs-lookup"><span data-stu-id="8d93d-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="8d93d-133">Formulář HTML použitý k aktualizaci těchto předvoleb obsahuje rozevírací seznamy pro tři vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="8d93d-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Aktualizujte zobrazení profilu s použitím formuláře, který umožňuje zadání názvu, pohlaví, stavu a oblíbené barvy.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="8d93d-135">Tyto seznamy jsou vyplněny službou, která byla vložena do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="8d93d-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="8d93d-136">`ProfileOptionsService`Je služba na úrovni uživatelského rozhraní navržená tak, aby poskytovala pouze data potřebná pro tento formulář:</span><span class="sxs-lookup"><span data-stu-id="8d93d-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="8d93d-137">Nezapomeňte zaregistrovat typy, které požadujete, prostřednictvím injektáže závislosti v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d93d-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8d93d-138">Neregistrovaný typ vyvolá výjimku za běhu, protože poskytovatel služby je interně dotazován prostřednictvím [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="8d93d-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="8d93d-139">Přepsání služeb</span><span class="sxs-lookup"><span data-stu-id="8d93d-139">Overriding Services</span></span>

<span data-ttu-id="8d93d-140">Kromě vkládání nových služeb se tato technika dá také použít k přepsání dříve vložených služeb na stránce.</span><span class="sxs-lookup"><span data-stu-id="8d93d-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="8d93d-141">Následující obrázek ukazuje všechna pole, která jsou k dispozici na stránce použitá v prvním příkladu:</span><span class="sxs-lookup"><span data-stu-id="8d93d-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Kontextová nabídka technologie IntelliSense na typovaném typu @ symbol seznam polí HTML, Component, StatsService a URL](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="8d93d-143">Jak vidíte, výchozí pole zahrnují `Html` , `Component` a `Url` (a také `StatsService` vložené).</span><span class="sxs-lookup"><span data-stu-id="8d93d-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="8d93d-144">Pokud byste chtěli například nahradit výchozí pomocníky HTML vlastními, můžete to snadno použít `@inject` :</span><span class="sxs-lookup"><span data-stu-id="8d93d-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="8d93d-145">Pokud chcete stávající služby zvětšit, můžete jednoduše použít tuto techniku a zároveň dědit z nebo zabalení stávající implementace s vlastními.</span><span class="sxs-lookup"><span data-stu-id="8d93d-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="8d93d-146">Viz také</span><span class="sxs-lookup"><span data-stu-id="8d93d-146">See Also</span></span>

* <span data-ttu-id="8d93d-147">Blog Simon Timms: [získávání dat vyhledávání do zobrazení](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="8d93d-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
