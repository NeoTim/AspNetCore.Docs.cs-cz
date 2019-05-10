---
title: Metabalíček Microsoft.aspnetcore.all pro ASP.NET Core 2.0
author: Rick-Anderson
description: Metabalíček Microsoft.aspnetcore.all se nedoporučuje pro ASP.NET Core 2.1 nebo novější.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: 5d49213e6d694f121d8301c94ba71782b2dc45cf
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086936"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="3f044-103">Metabalíček Microsoft.aspnetcore.all pro ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="3f044-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

> [!NOTE]
> <span data-ttu-id="3f044-104">Doporučujeme aplikace pro ASP.NET Core 2.1 a později použít [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) namísto tohoto balíčku.</span><span class="sxs-lookup"><span data-stu-id="3f044-104">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="3f044-105">Zobrazit [migrace z metabalíček na Microsoft.AspNetCore.App](#migrate) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="3f044-105">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="3f044-106">Tato funkce vyžaduje ASP.NET Core 2.x cílení na .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="3f044-106">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="3f044-107">[Metabalíček](https://www.nuget.org/packages/Microsoft.AspNetCore.All) je Microsoft.aspnetcore.all, odkazující na sdílené architektuře.</span><span class="sxs-lookup"><span data-stu-id="3f044-107">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="3f044-108">A *sdílené architektuře* je sada sestavení (*.dll* soubory), které nejsou ve složkách aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f044-108">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="3f044-109">Sdílené architektuře musí být nainstalována na počítači spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3f044-109">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="3f044-110">Další informace najdete v tématu [sdílené architektuře](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="3f044-110">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="3f044-111">Sdílené architektuře, která `Microsoft.AspNetCore.All` odkazuje na zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="3f044-111">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="3f044-112">Všechny podporované balíčky vytvořené týmem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3f044-112">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="3f044-113">Všechny podporované balíčky pomocí Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3f044-113">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="3f044-114">Interní závislosti a závislosti třetích stran používané v rámci ASP.NET Core a Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3f044-114">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="3f044-115">Všechny funkce aplikace ASP.NET Core 2.x a Entity Framework Core 2.x jsou součástí `Microsoft.AspNetCore.All` balíčku.</span><span class="sxs-lookup"><span data-stu-id="3f044-115">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="3f044-116">Tento balíček použít výchozí šablony projektu cílení ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="3f044-116">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="3f044-117">Číslo verze `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all představuje minimální verzi technologie ASP.NET Core a Entity Framework Core verze.</span><span class="sxs-lookup"><span data-stu-id="3f044-117">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="3f044-118">Následující *.csproj* souboru odkazy `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="3f044-118">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="3f044-119">Implicitní vytváření verzí</span><span class="sxs-lookup"><span data-stu-id="3f044-119">Implicit versioning</span></span>

<span data-ttu-id="3f044-120">V ASP.NET Core 2.1 nebo novější, můžete zadat `Microsoft.AspNetCore.All` odkaz bez verze balíčku.</span><span class="sxs-lookup"><span data-stu-id="3f044-120">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="3f044-121">Pokud není zadána verze, je určen implicitní verze sady SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="3f044-121">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="3f044-122">Doporučujeme, abyste spoléhat na implicitní verze určené sady SDK a nastavení nejsou explicitně číslo verze na odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="3f044-122">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="3f044-123">Pokud máte dotazy týkající se tento přístup, ponechte v Githubu komentář [diskuse implicitní verze Microsoft.AspNetCore.App](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="3f044-123">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="3f044-124">Implicitní verze je nastaveno na `major.minor.0` pro přenosné aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f044-124">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="3f044-125">Mechanismus vpřed sdílené architektuře aplikace spouští nejnovější kompatibilní verzi mezi nainstalovaných sdílené architektury.</span><span class="sxs-lookup"><span data-stu-id="3f044-125">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="3f044-126">Chcete-li zaručit, že při vývoji se používá stejnou verzi, testovacím i produkčním prostředí, ujistěte se, že stejná verze modulu sdílené framework je nainstalována ve všech prostředích.</span><span class="sxs-lookup"><span data-stu-id="3f044-126">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="3f044-127">Samostatná aplikace, číslo verze implicitní je nastavena na `major.minor.patch` sdílené Framework dodávat v nainstalované sady SDK.</span><span class="sxs-lookup"><span data-stu-id="3f044-127">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="3f044-128">Zadáním čísla verze na `Microsoft.AspNetCore.All` nemá odkaz na balíček **není** zaručit, že tato verze sdíleného framework je vybrán.</span><span class="sxs-lookup"><span data-stu-id="3f044-128">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="3f044-129">Předpokládejme například, ale není určená verze "2.1.1", ale je nainstalovaná "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="3f044-129">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="3f044-130">V takovém případě bude aplikace používat "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="3f044-130">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="3f044-131">Však není doporučena, můžete zakázat Posunutí vpřed (opravy a/nebo menší).</span><span class="sxs-lookup"><span data-stu-id="3f044-131">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="3f044-132">Další informace týkající se příkaz dotnet hostitele vpřed a jak nakonfigurovat své chování najdete v tématu [dotnet hostitele dopředné posunutí](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="3f044-132">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="3f044-133">Sady SDK projektu musí být nastaveno na `Microsoft.NET.Sdk.Web` v souboru projektu použije implicitní verzi `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="3f044-133">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="3f044-134">Když `Microsoft.NET.Sdk` SDK je zadána (`<Project Sdk="Microsoft.NET.Sdk">` v horní části souboru projektu), následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="3f044-134">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="3f044-135">*Upozornění NU1604: Závislosti projektu metabalíček neobsahuje inkluzivní dolní mez. Dolní mez zahrňte do verze závislosti zajistit výsledky obnovení budou konzistentní.*</span><span class="sxs-lookup"><span data-stu-id="3f044-135">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="3f044-136">Jedná se o známý problém s .NET Core 2.1 SDK a bude opravený v .NET Core 2.2 SDK.</span><span class="sxs-lookup"><span data-stu-id="3f044-136">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="3f044-137">Migrace z metabalíček na Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3f044-137">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="3f044-138">Následující balíčky jsou součástí `Microsoft.AspNetCore.All` , ale ne `Microsoft.AspNetCore.App` balíčku.</span><span class="sxs-lookup"><span data-stu-id="3f044-138">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="3f044-139">K přesunutí z `Microsoft.AspNetCore.All` k `Microsoft.AspNetCore.App`, když vaše aplikace používá libovolné rozhraní API z výše uvedených balíčků, nebo v režimu balíčky těchto balíčků, přidejte odkazy na tyto balíčky v projektu.</span><span class="sxs-lookup"><span data-stu-id="3f044-139">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="3f044-140">Všechny závislosti z předchozí balíčky, které jinak nejsou závislosti `Microsoft.AspNetCore.App` implicitně nejsou zahrnuty.</span><span class="sxs-lookup"><span data-stu-id="3f044-140">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="3f044-141">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3f044-141">For example:</span></span>

* <span data-ttu-id="3f044-142">`StackExchange.Redis` jako závislost `Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="3f044-142">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="3f044-143">`Microsoft.ApplicationInsights` jako závislost `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="3f044-143">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="3f044-144">Aktualizace ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="3f044-144">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="3f044-145">Doporučujeme migrovat na `Microsoft.AspNetCore.App` Microsoft.aspnetcore.all 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3f044-145">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="3f044-146">Pokud chcete dál používat `Microsoft.AspNetCore.All` Microsoft.aspnetcore.all a ověřte nasazení nejnovější verze opravy:</span><span class="sxs-lookup"><span data-stu-id="3f044-146">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="3f044-147">Na počítačích vývojářů a buildovací servery: Nainstalujte nejnovější [.NET Core SDK](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="3f044-147">On development machines and build servers: Install the latest [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="3f044-148">Na serverech nasazení: Nainstalujte nejnovější [.NET Core runtime](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="3f044-148">On deployment servers: Install the latest [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>
 <span data-ttu-id="3f044-149">Vaše aplikace se posunout vpřed byla nejnovější nainstalovaná verze na restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f044-149">Your app will roll forward to the latest installed version on an application restart.</span></span>
