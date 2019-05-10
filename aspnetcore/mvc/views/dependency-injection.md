---
title: Injektáž závislostí do zobrazení v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje injektáž závislostí do zobrazení MVC.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: 940e237fd73a864d9fd59f5447fe486f93648f62
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087472"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="3ef46-103">Injektáž závislostí do zobrazení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ef46-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="3ef46-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3ef46-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3ef46-105">Podporuje ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection) do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3ef46-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="3ef46-106">To může být užitečné pro zobrazení konkrétní služby, jako je lokalizace nebo data, vyžaduje se jenom pro naplnění zobrazení elementů.</span><span class="sxs-lookup"><span data-stu-id="3ef46-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="3ef46-107">Snažte se zachovat [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) mezi kontrolerů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3ef46-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="3ef46-108">Většina dat, které vaše zobrazení by měl předávat v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3ef46-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="3ef46-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3ef46-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="a-simple-example"></a><span data-ttu-id="3ef46-110">Jednoduchý příklad</span><span class="sxs-lookup"><span data-stu-id="3ef46-110">A Simple Example</span></span>

<span data-ttu-id="3ef46-111">Službu můžete vložit do zobrazení pomocí `@inject` směrnice.</span><span class="sxs-lookup"><span data-stu-id="3ef46-111">You can inject a service into a view using the `@inject` directive.</span></span> <span data-ttu-id="3ef46-112">Můžete si představit `@inject` jako přidání vlastnosti do zobrazení a naplnění danou vlastnost pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="3ef46-112">You can think of `@inject` as adding a property to your view, and populating the property using DI.</span></span>

<span data-ttu-id="3ef46-113">Syntaxe pro `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="3ef46-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="3ef46-114">Příklad `@inject` v akci:</span><span class="sxs-lookup"><span data-stu-id="3ef46-114">An example of `@inject` in action:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="3ef46-115">Toto zobrazení ukazuje seznam `ToDoItem` instancí, spolu se shrnutím zobrazující celkové statistiky.</span><span class="sxs-lookup"><span data-stu-id="3ef46-115">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="3ef46-116">Tento souhrn se vyplní z vložené `StatisticsService`.</span><span class="sxs-lookup"><span data-stu-id="3ef46-116">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="3ef46-117">Tato služba je zaregistrovaný pro injektáž závislostí v `ConfigureServices` v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3ef46-117">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="3ef46-118">`StatisticsService` Provádí pár výpočtů na sadu `ToDoItem` instance, které přistupuje prostřednictvím úložiště:</span><span class="sxs-lookup"><span data-stu-id="3ef46-118">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="3ef46-119">Ukázkové úložiště používá kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="3ef46-119">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="3ef46-120">Implementace uvedené výše (která funguje na všechna data v paměti) se nedoporučuje pro velké a vzdáleně jenom datové sady.</span><span class="sxs-lookup"><span data-stu-id="3ef46-120">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="3ef46-121">Ukázka zobrazí data z model vázán k zobrazení a service vložené do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3ef46-121">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Chcete-li zobrazit výpis celkový počet položek dokončení položky, průměrná priority a seznam úkolů s jejich úrovně priority a logické hodnoty označující dokončení.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="3ef46-123">Naplňování vyhledávání dat</span><span class="sxs-lookup"><span data-stu-id="3ef46-123">Populating Lookup Data</span></span>

<span data-ttu-id="3ef46-124">Vkládání zobrazení může být užitečné k naplnění možnosti v prvky uživatelského rozhraní, jako je například rozevíracích seznamů.</span><span class="sxs-lookup"><span data-stu-id="3ef46-124">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="3ef46-125">Vezměte v úvahu formuláře profilu uživatele, který obsahuje možnosti pro určení rovnosti, stavu a další předvolby.</span><span class="sxs-lookup"><span data-stu-id="3ef46-125">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="3ef46-126">Vykreslování formulář, pomocí standardní metoda MVC by vyžadovaly kontroleru na žádost o služby data access services pro každou z těchto sad možností a potom naplnit modelu nebo `ViewBag` s každou sadu možností vázat.</span><span class="sxs-lookup"><span data-stu-id="3ef46-126">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="3ef46-127">Alternativním přístupem vloží přímo do zobrazení získat možnosti služby.</span><span class="sxs-lookup"><span data-stu-id="3ef46-127">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="3ef46-128">Tím se minimalizují množství kódu vyžadovaného adaptérem přesun tuto logiku element vytváření zobrazení v samotném zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3ef46-128">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="3ef46-129">Akce kontroleru k zobrazení formuláře úprav profilu stačí předat formuláři instance profilu:</span><span class="sxs-lookup"><span data-stu-id="3ef46-129">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="3ef46-130">Formuláře HTML, který používá k aktualizaci těchto předvolby zahrnuje rozevíracích seznamů pro tři vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3ef46-130">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Zobrazení profil aktualizujte formulář umožňuje položka jméno, pohlaví, stavu a oblíbené barvy.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="3ef46-132">Tyto seznamy se vyplní podle služby, které byly vloženy do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3ef46-132">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="3ef46-133">`ProfileOptionsService` Je navrženo pro zajištění pouze data potřebná pro tento formulář služba úrovni uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="3ef46-133">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="3ef46-134">Nezapomeňte k registraci typů, které požadujete prostřednictvím injektáž závislostí v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3ef46-134">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3ef46-135">Neregistrovaný typ dojde k výjimce za běhu, protože poskytovatel služeb je interně dotazován prostřednictvím [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="3ef46-135">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="3ef46-136">Přepsání služby</span><span class="sxs-lookup"><span data-stu-id="3ef46-136">Overriding Services</span></span>

<span data-ttu-id="3ef46-137">Kromě vkládání nových služeb, můžete tento postup použít také k přepsání dříve vložený služby na stránce.</span><span class="sxs-lookup"><span data-stu-id="3ef46-137">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="3ef46-138">Následující obrázek znázorňuje všechna pole, které jsou k dispozici na stránce použít v prvním příkladu:</span><span class="sxs-lookup"><span data-stu-id="3ef46-138">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Kontextové nabídky technologie IntelliSense na zadaný symbol výpis pole Html, komponenty, StatsService a adresa Url @](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="3ef46-140">Jak je vidět, výchozí pole obsahují `Html`, `Component`, a `Url` (stejně jako `StatsService` , který jsme vloží).</span><span class="sxs-lookup"><span data-stu-id="3ef46-140">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="3ef46-141">Například Kdybyste chtěli nahraďte pomocných rutin HTML výchozí vlastní, můžete snadno udělat pomocí `@inject`:</span><span class="sxs-lookup"><span data-stu-id="3ef46-141">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="3ef46-142">Pokud chcete rozšířit stávající služby, můžete jednoduše použít tento postup při dědění z nebo obtékání stávající implementaci vlastní.</span><span class="sxs-lookup"><span data-stu-id="3ef46-142">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="3ef46-143">Viz také</span><span class="sxs-lookup"><span data-stu-id="3ef46-143">See Also</span></span>

* <span data-ttu-id="3ef46-144">Simon Timms blogu: [Načítání dat vyhledávací do zobrazení](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="3ef46-144">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
