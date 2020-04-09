---
title: Metabalíček Microsoft.AspNetCore.All pro ASP.NET Core 2.0
author: Rick-Anderson
description: Metabalíček Microsoft.AspNetCore.All se nedoporučuje pro ASP.NET Core 2.1 a novější.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511064"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="80122-103">Metabalíček Microsoft.AspNetCore.All pro ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="80122-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="80122-104">Metabalíček `Microsoft.AspNetCore.All` není součástí ASP.NET Core 3.0 a novější.</span><span class="sxs-lookup"><span data-stu-id="80122-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="80122-105">Další informace naleznete v [tomto problému GitHub](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="80122-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="80122-106">Doporučujeme aplikace zaměřené ASP.NET Core 2.1 a novější používat [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) spíše než tento balíček.</span><span class="sxs-lookup"><span data-stu-id="80122-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="80122-107">V tomto článku najdete [téma Migrace z Microsoft.AspNetCore.All na Microsoft.AspNetCore.App.](#migrate)</span><span class="sxs-lookup"><span data-stu-id="80122-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="80122-108">Tato funkce vyžaduje ASP.NET cílení na jádro 2.x .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="80122-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="80122-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) je metabalíček, který odkazuje na sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="80122-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="80122-110">*Sdílená architektura* je sada sestavení (*soubory DLL),* které nejsou ve složkách aplikace.</span><span class="sxs-lookup"><span data-stu-id="80122-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="80122-111">Sdílená architektura musí být nainstalována v počítači, aby bylo možné aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="80122-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="80122-112">Další informace naleznete [v tématu sdílený rámec](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="80122-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="80122-113">Sdílený rámec, `Microsoft.AspNetCore.All` který odkazuje, zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="80122-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="80122-114">Všechny podporované balíčky ASP.NET core týmu.</span><span class="sxs-lookup"><span data-stu-id="80122-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="80122-115">Všechny podporované balíčky jádrem entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="80122-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="80122-116">Interní závislosti a závislosti třetích stran používané ASP.NET jádrem jádra a entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="80122-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="80122-117">Všechny funkce ASP.NET Core 2.x a Entity Framework Core `Microsoft.AspNetCore.All` 2.x jsou součástí balíčku.</span><span class="sxs-lookup"><span data-stu-id="80122-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="80122-118">Výchozí šablony projektu zaměřené na ASP.NET Core 2.0 používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="80122-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="80122-119">Číslo verze `Microsoft.AspNetCore.All` metabalíčku představuje minimální verzi ASP.NET Core a core verze entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="80122-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="80122-120">Následující soubor *.csproj* odkazuje `Microsoft.AspNetCore.All` na metabalíček pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="80122-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="80122-121">Implicitní správa verzí</span><span class="sxs-lookup"><span data-stu-id="80122-121">Implicit versioning</span></span>

<span data-ttu-id="80122-122">V ASP.NET Jádrem 2.1 nebo `Microsoft.AspNetCore.All` novějším můžete zadat odkaz na balíček bez verze.</span><span class="sxs-lookup"><span data-stu-id="80122-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="80122-123">Pokud není zadána verze, implicitní verze je určena`Microsoft.NET.Sdk.Web`sadou SDK ( ).</span><span class="sxs-lookup"><span data-stu-id="80122-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="80122-124">Doporučujeme spoléhat se na implicitní verzi určenou sadou SDK a neexplicitně nastavit číslo verze v odkazu na balíček.</span><span class="sxs-lookup"><span data-stu-id="80122-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="80122-125">Pokud máte dotazy týkající se tohoto přístupu, zanechte komentář GitHub u [implicitní verze Diskuse o microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="80122-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="80122-126">Implicitní verze je `major.minor.0` nastavena na pro přenosné aplikace.</span><span class="sxs-lookup"><span data-stu-id="80122-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="80122-127">Mechanismus přetáčení sdíleného rozhraní spouští aplikaci na nejnovější kompatibilní verzi mezi nainstalovanými sdílenými rámci.</span><span class="sxs-lookup"><span data-stu-id="80122-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="80122-128">Chcete-li zaručit, že stejná verze se používá ve vývoji, testování a výrobě, ujistěte se, že stejná verze sdíleného rozhraní je nainstalována ve všech prostředích.</span><span class="sxs-lookup"><span data-stu-id="80122-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="80122-129">U samostatných aplikací je implicitní číslo verze `major.minor.patch` nastaveno na sdílenou architekturu, která je součástí nainstalované sady SDK.</span><span class="sxs-lookup"><span data-stu-id="80122-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="80122-130">Zadání čísla verze v `Microsoft.AspNetCore.All` odkazu na balíček **nezaručuje,** že je vybrána verze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="80122-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="80122-131">Předpokládejme například, že je zadána verze "2.1.1", ale je nainstalována verze "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="80122-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="80122-132">V takovém případě bude aplikace používat "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="80122-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="80122-133">I když se nedoporučuje, můžete zakázat roll vpřed (patch a / nebo menší).</span><span class="sxs-lookup"><span data-stu-id="80122-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="80122-134">Další informace týkající se dotnet hostitele roll-forward a jak nakonfigurovat jeho chování, naleznete [v tématu dotnet hostitele posunout vpřed](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="80122-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="80122-135">Sada SDK projektu musí být `Microsoft.NET.Sdk.Web` nastavena na v souboru `Microsoft.AspNetCore.All`projektu, aby bylo možné použít implicitní verzi aplikace .</span><span class="sxs-lookup"><span data-stu-id="80122-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="80122-136">Pokud `Microsoft.NET.Sdk` je zadána sada`<Project Sdk="Microsoft.NET.Sdk">` SDK (v horní části souboru projektu), je generováno následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="80122-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="80122-137">*Upozornění NU1604: Závislost projektu Microsoft.AspNetCore.All neobsahuje včetně dolní mez. Zahrnout dolní mez ve verzi závislosti zajistit konzistentní výsledky obnovení.*</span><span class="sxs-lookup"><span data-stu-id="80122-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="80122-138">Jedná se o známý problém s sadou .NET Core 2.1 SDK a bude opraven v sada .NET Core 2.2 SDK.</span><span class="sxs-lookup"><span data-stu-id="80122-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="80122-139">Migrace z Microsoft.AspNetCore.All na Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="80122-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="80122-140">Následující balíčky jsou `Microsoft.AspNetCore.All` zahrnuty `Microsoft.AspNetCore.App` v balíčku, ale ne balíček.</span><span class="sxs-lookup"><span data-stu-id="80122-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

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

<span data-ttu-id="80122-141">Pokud se `Microsoft.AspNetCore.All` `Microsoft.AspNetCore.App`chcete přesunout z aplikace do aplikace, pokud vaše aplikace používá nějaká api z výše uvedených balíčků nebo balíčky, které tyto balíčky přinesly, přidejte odkazy na tyto balíčky v projektu.</span><span class="sxs-lookup"><span data-stu-id="80122-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="80122-142">Všechny závislosti předchozí balíčky, které jinak nejsou `Microsoft.AspNetCore.App` závislosti nejsou implicitně zahrnuty.</span><span class="sxs-lookup"><span data-stu-id="80122-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="80122-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="80122-143">For example:</span></span>

* <span data-ttu-id="80122-144">`StackExchange.Redis`jako závislost`Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="80122-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="80122-145">`Microsoft.ApplicationInsights`jako závislost`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="80122-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="80122-146">Aktualizace ASP.NET jádra 2.1</span><span class="sxs-lookup"><span data-stu-id="80122-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="80122-147">Doporučujeme migrovat `Microsoft.AspNetCore.App` na metabalíček pro 2.1 a novější.</span><span class="sxs-lookup"><span data-stu-id="80122-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="80122-148">Chcete-li `Microsoft.AspNetCore.All` pokračovat v používání metabalíčku a zajistit nasazení nejnovější verze opravy:</span><span class="sxs-lookup"><span data-stu-id="80122-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="80122-149">Na vývojových počítačích a serverech sestavení: Nainstalujte nejnovější [sadu .NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="80122-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="80122-150">Na serverech nasazení: Nainstalujte nejnovější [runtime jádra .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="80122-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="80122-151">Aplikace se při restartování aplikace posune dopředu na nejnovější nainstalovanou verzi.</span><span class="sxs-lookup"><span data-stu-id="80122-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
