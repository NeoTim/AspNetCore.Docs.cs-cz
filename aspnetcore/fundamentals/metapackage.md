---
title: Microsoft. AspNetCore. All Metapackage for ASP.NET Core 2,0
author: Rick-Anderson
description: Microsoft. AspNetCore. All Metapackage se nedoporučuje pro ASP.NET Core 2,1 a novější.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: 91f39fc59e5682fb19f8cbc6e9ebe5b30e5dcf3c
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219132"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="d22fa-103">Microsoft. AspNetCore. All Metapackage for ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="d22fa-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d22fa-104">`Microsoft.AspNetCore.All` Metapackage není součástí ASP.NET Core 3,0 a novějších.</span><span class="sxs-lookup"><span data-stu-id="d22fa-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="d22fa-105">Další informace najdete v tématu [tento problém Githubu](https://github.com/aspnet/Announcements/issues/314).</span><span class="sxs-lookup"><span data-stu-id="d22fa-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="d22fa-106">Doporučujeme, abyste pro aplikace cílí na ASP.NET Core 2,1 a později používali [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a ne tento balíček.</span><span class="sxs-lookup"><span data-stu-id="d22fa-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="d22fa-107">Viz téma [migrace z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app](#migrate) v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="d22fa-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="d22fa-108">Tato funkce vyžaduje ASP.NET Core 2. x cílící na rozhraní .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="d22fa-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="d22fa-109">[Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) je Metapackage, který odkazuje na sdílené rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d22fa-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="d22fa-110">*Sdílené rozhraní* je sada sestavení (soubory *. dll* ), která nejsou ve složkách aplikace.</span><span class="sxs-lookup"><span data-stu-id="d22fa-110">A *shared framework* is a set of assemblies (*.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="d22fa-111">Aby bylo možné aplikaci spustit, musí být na počítači nainstalována sdílená rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d22fa-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="d22fa-112">Další informace najdete v tématu [sdílené rozhraní](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="d22fa-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="d22fa-113">Sdílené rozhraní, které `Microsoft.AspNetCore.All` odkazuje na:</span><span class="sxs-lookup"><span data-stu-id="d22fa-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="d22fa-114">Všechny podporované balíčky vytvořené týmem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d22fa-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="d22fa-115">Všechny podporované balíčky Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d22fa-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="d22fa-116">Interní závislosti a závislosti třetích stran používané v rámci ASP.NET Core a Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d22fa-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="d22fa-117">Do `Microsoft.AspNetCore.All` balíčku jsou zahrnuté všechny funkce ASP.NET Core 2. x a Entity Framework Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="d22fa-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="d22fa-118">Výchozí šablony projektu, které cílí na ASP.NET Core 2,0, používají tento balíček.</span><span class="sxs-lookup"><span data-stu-id="d22fa-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="d22fa-119">Číslo `Microsoft.AspNetCore.All` verze Metapackage představuje minimální verzi ASP.NET Core a Entity Framework Core verzi.</span><span class="sxs-lookup"><span data-stu-id="d22fa-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="d22fa-120">Následující soubor *. csproj* odkazuje na `Microsoft.AspNetCore.All` Metapackage pro ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d22fa-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="d22fa-121">Implicitní Správa verzí</span><span class="sxs-lookup"><span data-stu-id="d22fa-121">Implicit versioning</span></span>

<span data-ttu-id="d22fa-122">V ASP.NET Core 2,1 nebo novější můžete zadat odkaz na `Microsoft.AspNetCore.All` balíček bez verze.</span><span class="sxs-lookup"><span data-stu-id="d22fa-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="d22fa-123">Není-li zadána verze, je implicitní verze určena sadou SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="d22fa-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="d22fa-124">Doporučujeme, abyste se spoléhali na implicitní verzi určenou sadou SDK a neexplicitně nastavoval číslo verze na odkaz na balíček.</span><span class="sxs-lookup"><span data-stu-id="d22fa-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="d22fa-125">Pokud máte dotazy týkající se tohoto přístupu, ponechte komentář GitHubu v [diskuzi o implicitní verzi Microsoft. AspNetCore. app](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="d22fa-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/aspnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="d22fa-126">Implicitní verze je nastavená na `major.minor.0` pro přenosné aplikace.</span><span class="sxs-lookup"><span data-stu-id="d22fa-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="d22fa-127">Mechanismus pro přetečení sdíleného rozhraní spustí aplikaci na nejnovější kompatibilní verzi z nainstalovaných sdílených rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d22fa-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="d22fa-128">Aby bylo zaručeno, že stejná verze se používá ve vývoji, testování a produkčním prostředí, zajistěte, aby byla stejná verze sdíleného rozhraní nainstalovaná ve všech prostředích.</span><span class="sxs-lookup"><span data-stu-id="d22fa-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="d22fa-129">U samostatných aplikací je implicitní číslo verze nastaveno na `major.minor.patch` sdílené rozhraní, které je v nainstalované sadě SDK součástí sady.</span><span class="sxs-lookup"><span data-stu-id="d22fa-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="d22fa-130">Zadání čísla verze na `Microsoft.AspNetCore.All` odkaz na balíček nezaručuje **,** že je zvolena verze sdíleného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d22fa-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="d22fa-131">Například Předpokládejme, že je zadána verze "2.1.1", ale je nainstalována aplikace "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="d22fa-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="d22fa-132">V takovém případě bude aplikace používat "2.1.3".</span><span class="sxs-lookup"><span data-stu-id="d22fa-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="d22fa-133">I když se to nedoporučuje, můžete zablokovat přeposlání (oprava nebo podverze).</span><span class="sxs-lookup"><span data-stu-id="d22fa-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="d22fa-134">Další informace o tom, jak integrovat hostitele dotnet a jak nakonfigurovat jeho chování, najdete v tématu [dotnet Host – přesměrování](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="d22fa-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="d22fa-135">Sada SDK projektu musí být nastavena na `Microsoft.NET.Sdk.Web` hodnotu v souboru projektu, aby používala implicitní `Microsoft.AspNetCore.All`verzi.</span><span class="sxs-lookup"><span data-stu-id="d22fa-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="d22fa-136">`<Project Sdk="Microsoft.NET.Sdk">` Při zadání `Microsoft.NET.Sdk` sady SDK (v horní části souboru projektu) se vygeneruje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="d22fa-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="d22fa-137">*Upozornění NU1604: Závislost projektu Microsoft. AspNetCore. All neobsahuje žádné zahrnutí dolní meze. Zahrňte do verze závislosti dolní mez, aby se zajistilo konzistentní výsledky obnovení.*</span><span class="sxs-lookup"><span data-stu-id="d22fa-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="d22fa-138">Jedná se o známý problém se sadou .NET Core 2,1 SDK a bude opraven v sadě .NET Core 2,2 SDK.</span><span class="sxs-lookup"><span data-stu-id="d22fa-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="d22fa-139">Migruje se z Microsoft. AspNetCore. All do Microsoft. AspNetCore. app.</span><span class="sxs-lookup"><span data-stu-id="d22fa-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="d22fa-140">K dispozici `Microsoft.AspNetCore.All` jsou tyto balíčky, ale `Microsoft.AspNetCore.App` ne balíček.</span><span class="sxs-lookup"><span data-stu-id="d22fa-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

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

<span data-ttu-id="d22fa-141">Pokud chcete přejít `Microsoft.AspNetCore.All` z `Microsoft.AspNetCore.App`na, pokud vaše aplikace používá rozhraní API z výše uvedených balíčků nebo balíčky, které tyto balíčky přidávají, přidejte odkazy na tyto balíčky v projektu.</span><span class="sxs-lookup"><span data-stu-id="d22fa-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="d22fa-142">Všechny závislosti předchozích balíčků, které jinak nejsou závislé `Microsoft.AspNetCore.App` na, nejsou implicitně zahrnuté.</span><span class="sxs-lookup"><span data-stu-id="d22fa-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="d22fa-143">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d22fa-143">For example:</span></span>

* <span data-ttu-id="d22fa-144">`StackExchange.Redis`jako závislost`Microsoft.Extensions.Caching.Redis`</span><span class="sxs-lookup"><span data-stu-id="d22fa-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="d22fa-145">`Microsoft.ApplicationInsights`jako závislost`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d22fa-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="d22fa-146">Aktualizace ASP.NET Core 2,1</span><span class="sxs-lookup"><span data-stu-id="d22fa-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="d22fa-147">Doporučujeme migrovat na `Microsoft.AspNetCore.App` Metapackage pro 2,1 a novější.</span><span class="sxs-lookup"><span data-stu-id="d22fa-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="d22fa-148">Chcete-li nadále `Microsoft.AspNetCore.All` používat Metapackage a zajistit, aby byla nasazena nejnovější verze opravy:</span><span class="sxs-lookup"><span data-stu-id="d22fa-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="d22fa-149">Ve vývojových počítačích a serverech sestavení: Nainstalujte nejnovější [.NET Core SDK](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="d22fa-149">On development machines and build servers: Install the latest [.NET Core SDK](https://www.microsoft.com/net/download).</span></span>
* <span data-ttu-id="d22fa-150">Na serverech nasazení: Nainstalujte nejnovější [modul runtime .NET Core](https://www.microsoft.com/net/download).</span><span class="sxs-lookup"><span data-stu-id="d22fa-150">On deployment servers: Install the latest [.NET Core runtime](https://www.microsoft.com/net/download).</span></span>
 <span data-ttu-id="d22fa-151">Vaše aplikace bude předána na nejnovější nainstalovanou verzi při restartu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d22fa-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
