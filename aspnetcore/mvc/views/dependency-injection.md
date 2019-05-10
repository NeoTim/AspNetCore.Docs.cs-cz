---
title: Injektáž závislostí do zobrazení v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje injektáž závislostí do zobrazení MVC.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: b411b164bfea81f82c5c9fc1052e0ecfe65f0bc2
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517054"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="f6ebc-103">Injektáž závislostí do zobrazení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6ebc-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="f6ebc-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f6ebc-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f6ebc-105">Podporuje ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="f6ebc-106">To může být užitečné pro zobrazení konkrétní služby, jako je lokalizace nebo data, vyžaduje se jenom pro naplnění zobrazení elementů.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="f6ebc-107">Snažte se zachovat [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) mezi kontrolerů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="f6ebc-108">Většina dat, které vaše zobrazení by měl předávat v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="f6ebc-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f6ebc-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="f6ebc-110">Konfigurace vkládání</span><span class="sxs-lookup"><span data-stu-id="f6ebc-110">Configuration injection</span></span>

<span data-ttu-id="f6ebc-111">*appSettings.JSON* hodnoty můžete vloží přímo do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="f6ebc-112">Příklad *appsettings.json* souboru:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="f6ebc-113">Syntaxe pro `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="f6ebc-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="f6ebc-114">Příklad použití `@inject`:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="f6ebc-115">Dokáže vložit službu</span><span class="sxs-lookup"><span data-stu-id="f6ebc-115">Service injection</span></span>

<span data-ttu-id="f6ebc-116">Služby můžete být vloženy do zobrazení pomocí `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="f6ebc-117">Můžete si představit `@inject` jako přidání vlastnosti do zobrazení a naplnění danou vlastnost pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="f6ebc-118">Toto zobrazení ukazuje seznam `ToDoItem` instancí, spolu se shrnutím zobrazující celkové statistiky.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="f6ebc-119">Tento souhrn se vyplní z vložené `StatisticsService`.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="f6ebc-120">Tato služba je zaregistrovaný pro injektáž závislostí v `ConfigureServices` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="f6ebc-121">`StatisticsService` Provádí pár výpočtů na sadu `ToDoItem` instance, které přistupuje prostřednictvím úložiště:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="f6ebc-122">Ukázkové úložiště používá kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="f6ebc-123">Implementace uvedené výše (která funguje na všechna data v paměti) se nedoporučuje pro velké a vzdáleně jenom datové sady.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="f6ebc-124">Ukázka zobrazí data z model vázán k zobrazení a service vložené do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Chcete-li zobrazit výpis celkový počet položek dokončení položky, průměrná priority a seznam úkolů s jejich úrovně priority a logické hodnoty označující dokončení.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="f6ebc-126">Naplňování vyhledávání dat</span><span class="sxs-lookup"><span data-stu-id="f6ebc-126">Populating Lookup Data</span></span>

<span data-ttu-id="f6ebc-127">Vkládání zobrazení může být užitečné k naplnění možnosti v prvky uživatelského rozhraní, jako je například rozevíracích seznamů.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="f6ebc-128">Vezměte v úvahu formuláře profilu uživatele, který obsahuje možnosti pro určení rovnosti, stavu a další předvolby.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="f6ebc-129">Vykreslování formulář, pomocí standardní metoda MVC by vyžadovaly kontroleru na žádost o služby data access services pro každou z těchto sad možností a potom naplnit modelu nebo `ViewBag` s každou sadu možností vázat.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="f6ebc-130">Alternativním přístupem vloží přímo do zobrazení získat možnosti služby.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="f6ebc-131">Tím se minimalizují množství kódu vyžadovaného adaptérem přesun tuto logiku element vytváření zobrazení v samotném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="f6ebc-132">Akce kontroleru k zobrazení formuláře úprav profilu stačí předat formuláři instance profilu:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="f6ebc-133">Formuláře HTML, který používá k aktualizaci těchto předvolby zahrnuje rozevíracích seznamů pro tři vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Zobrazení profil aktualizujte formulář umožňuje položka jméno, pohlaví, stavu a oblíbené barvy.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="f6ebc-135">Tyto seznamy se vyplní podle služby, které byly vloženy do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="f6ebc-136">`ProfileOptionsService` Je navrženo pro zajištění pouze data potřebná pro tento formulář služba úrovni uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="f6ebc-137">Nezapomeňte k registraci typů, které požadujete prostřednictvím injektáž závislostí v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f6ebc-138">Neregistrovaný typ dojde k výjimce za běhu, protože poskytovatel služeb je interně dotazován prostřednictvím [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="f6ebc-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="f6ebc-139">Přepsání služby</span><span class="sxs-lookup"><span data-stu-id="f6ebc-139">Overriding Services</span></span>

<span data-ttu-id="f6ebc-140">Kromě vkládání nových služeb, můžete tento postup použít také k přepsání dříve vložený služby na stránce.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="f6ebc-141">Následující obrázek znázorňuje všechna pole, které jsou k dispozici na stránce použít v prvním příkladu:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Kontextové nabídky technologie IntelliSense na zadaný symbol výpis pole Html, komponenty, StatsService a adresa Url @](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="f6ebc-143">Jak je vidět, výchozí pole obsahují `Html`, `Component`, a `Url` (stejně jako `StatsService` , který jsme vloží).</span><span class="sxs-lookup"><span data-stu-id="f6ebc-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="f6ebc-144">Například Kdybyste chtěli nahraďte pomocných rutin HTML výchozí vlastní, můžete snadno udělat pomocí `@inject`:</span><span class="sxs-lookup"><span data-stu-id="f6ebc-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="f6ebc-145">Pokud chcete rozšířit stávající služby, můžete jednoduše použít tento postup při dědění z nebo obtékání stávající implementaci vlastní.</span><span class="sxs-lookup"><span data-stu-id="f6ebc-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="f6ebc-146">Viz také</span><span class="sxs-lookup"><span data-stu-id="f6ebc-146">See Also</span></span>

* <span data-ttu-id="f6ebc-147">Simon Timms blogu: [Načítání dat vyhledávací do zobrazení](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="f6ebc-147">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
