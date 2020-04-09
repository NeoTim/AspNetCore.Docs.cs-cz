---
title: Migrace z ASP.NET jádra 1.x na 2.0
author: scottaddie
description: Tento článek popisuje požadavky a nejběžnější kroky pro migraci ASP.NET projektu Core 1.x do ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/1x-to-2x/index
ms.openlocfilehash: c46f50a418cf630980ac2ba94407e4370d36e7d5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667613"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="8cbe3-103">Migrace z ASP.NET jádra 1.x na 2.0</span><span class="sxs-lookup"><span data-stu-id="8cbe3-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="8cbe3-104">Podle [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="8cbe3-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="8cbe3-105">V tomto článku vás provedeme aktualizací existujícího projektu ASP.NET Core 1.x, který ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="8cbe3-106">Migrace aplikace do ASP.NET jádrem 2.0 umožňuje využívat [mnoho nových funkcí a vylepšení výkonu](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="8cbe3-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="8cbe3-107">Existující aplikace ASP.NET Core 1.x jsou založeny mimo šablony projektů specifické pro verzi.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="8cbe3-108">Jak se vyvíjí ASP.NET core framework, tak se šablony projektu a počáteční kód obsažený v nich.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="8cbe3-109">Kromě aktualizace ASP.NET core framework, je třeba aktualizovat kód pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="8cbe3-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8cbe3-110">Prerequisites</span></span>

<span data-ttu-id="8cbe3-111">Viz [Začínáme s ASP.NET jádrem](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="8cbe3-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="8cbe3-112">Aktualizovat zástupný název cílového rámce (TFM)</span><span class="sxs-lookup"><span data-stu-id="8cbe3-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="8cbe3-113">Projekty zaměřené na rozhraní .NET Core by měly používat [TFM](/dotnet/standard/frameworks) verze větší nebo rovna .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="8cbe3-114">Vyhledejte `<TargetFramework>` uzel v souboru *.csproj* a nahraďte jeho vnitřní text `netcoreapp2.0`:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="8cbe3-115">Projekty zaměřené na rozhraní .NET Framework by měly používat TFM verze větší nebo rovna rozhraní .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="8cbe3-116">Vyhledejte `<TargetFramework>` uzel v souboru *.csproj* a nahraďte jeho vnitřní text `net461`:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="8cbe3-117">.NET Core 2.0 nabízí mnohem větší plochu než .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="8cbe3-118">Pokud cílíte na rozhraní .NET Framework výhradně z důvodu chybějících rozhraní API v rozhraní .NET Core 1.x, cílení na rozhraní .NET Core 2.0 bude pravděpodobně fungovat.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="8cbe3-119">Pokud soubor projektu `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`obsahuje , podívejte se na [tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="8cbe3-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="8cbe3-120">Aktualizace verze sady .NET Core SDK v souboru global.json</span><span class="sxs-lookup"><span data-stu-id="8cbe3-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="8cbe3-121">Pokud vaše řešení spoléhá na soubor [global.json,](/dotnet/core/tools/global-json) aby se zaměřilo na `version` konkrétní verzi sady .NET Core SDK, aktualizujte jeho vlastnost tak, aby používala verzi 2.0 nainstalovanou v počítači:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="8cbe3-122">Aktualizovat odkazy na balíčky</span><span class="sxs-lookup"><span data-stu-id="8cbe3-122">Update package references</span></span>

<span data-ttu-id="8cbe3-123">Soubor *.csproj* v projektu 1.x uvádí každý balíček NuGet používaný v projektu.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="8cbe3-124">V ASP.NET projektu Core 2.0 zaměřeného na rozhraní .NET Core 2.0 nahradí kolekci balíčků jeden odkaz [na metabalíček](xref:fundamentals/metapackage) v souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="8cbe3-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="8cbe3-125">Všechny funkce ASP.NET Core 2.0 a Entity Framework Core 2.0 jsou zahrnuty v metabalíčku.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="8cbe3-126">ASP.NET Core 2.0 projekty cílení .NET Framework by měl y nadále odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="8cbe3-127">Aktualizujte `Version` atribut `<PackageReference />` každého uzlu na 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="8cbe3-128">Zde je například seznam `<PackageReference />` uzlů používaných v typickém ASP.NET cílování projektu Core 2.0 .NET Framework:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="8cbe3-129">Aktualizace nástrojů rozhraní CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="8cbe3-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="8cbe3-130">V souboru *.csproj* `Version` aktualizujte `<DotNetCliToolReference />` atribut každého uzlu na 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="8cbe3-131">Tady je například seznam nástrojů rozhraní CLI používaných v typickém ASP.NET cílení na projekt Core 2.0 .NET Core 2.0:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="8cbe3-132">Přejmenovat záložní cíl balíčku, vlastnost</span><span class="sxs-lookup"><span data-stu-id="8cbe3-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="8cbe3-133">Soubor *.csproj* projektu 1.x používal `PackageTargetFallback` uzel a proměnnou:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="8cbe3-134">Přejmenujte uzel i proměnnou na `AssetTargetFallback`:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="8cbe3-135">Aktualizovat hlavní metodu v Program.cs</span><span class="sxs-lookup"><span data-stu-id="8cbe3-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="8cbe3-136">V projektech 1.x vypadala `Main` metoda *Program.cs* takto:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="8cbe3-137">U projektů 2.0 `Main` byla metoda *Program.cs* zjednodušena:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="8cbe3-138">Přijetí tohoto nového vzoru 2.0 je vysoce doporučeno a je vyžadováno pro funkce produktu, jako je [core migrace entity framework (EF)](xref:data/ef-mvc/migrations) pro práci.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="8cbe3-139">Například spuštění `Update-Database` z okna konzoly `dotnet ef database update` Správce balíčků nebo z příkazového řádku (u projektů převedených na ASP.NET jádrem 2.0) generuje následující chybu:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="8cbe3-140">Přidání zprostředkovatelů konfigurace</span><span class="sxs-lookup"><span data-stu-id="8cbe3-140">Add configuration providers</span></span>

<span data-ttu-id="8cbe3-141">V projektech 1.x bylo přidání poskytovatelů konfigurace `Startup` do aplikace provedeno prostřednictvím konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="8cbe3-142">Kroky zahrnovaly vytvoření `ConfigurationBuilder`instance , načítání příslušných zprostředkovatelů (proměnné prostředí, nastavení aplikace `IConfigurationRoot`atd.) a inicializaci člena aplikace .</span><span class="sxs-lookup"><span data-stu-id="8cbe3-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="8cbe3-143">Předchozí příklad načte `Configuration` člena s nastavením konfigurace z *appsettings.json,* stejně jako všechny *appsettings.\< EnvironmentName\>.json* soubor `IHostingEnvironment.EnvironmentName` odpovídající vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="8cbe3-144">Umístění těchto souborů je na stejné cestě jako *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="8cbe3-145">V projektech 2.0 je standardní konfigurační kód vlastní 1.x projekty běží na pozadí.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="8cbe3-146">Například proměnné prostředí a nastavení aplikace se načítají při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="8cbe3-147">Ekvivalentní *Startup.cs* kód je `IConfiguration` snížena na inicializaci s instřikované instance:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="8cbe3-148">Chcete-li odebrat `WebHostBuilder.CreateDefaultBuilder`výchozí zprostředkovatele přidané `IConfigurationBuilder.Sources` aplikace `ConfigureAppConfiguration`, vyvolat `Clear` metodu na vlastnost uvnitř .</span><span class="sxs-lookup"><span data-stu-id="8cbe3-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="8cbe3-149">Chcete-li přidat zprostředkovatele zpět, použijte metodu `ConfigureAppConfiguration` v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="8cbe3-150">Konfigurace použitá `CreateDefaultBuilder` metodou v předchozím fragmentu kódu je k vidění [zde](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="8cbe3-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="8cbe3-151">Další informace naleznete [v tématu Konfigurace v ASP.NET jádru](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8cbe3-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="8cbe3-152">Přesunutí kódu inicializace databáze</span><span class="sxs-lookup"><span data-stu-id="8cbe3-152">Move database initialization code</span></span>

<span data-ttu-id="8cbe3-153">V projektech 1.x pomocí EF Core 1.x, příkaz, jako `dotnet ef migrations add` je například následující:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="8cbe3-154">Vytvoření instance instance `Startup`</span><span class="sxs-lookup"><span data-stu-id="8cbe3-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="8cbe3-155">Vyvolá metodu `ConfigureServices` registrace všech služeb s vkládání `DbContext` závislostí (včetně typů)</span><span class="sxs-lookup"><span data-stu-id="8cbe3-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="8cbe3-156">Provádí požadované úkoly</span><span class="sxs-lookup"><span data-stu-id="8cbe3-156">Performs its requisite tasks</span></span>

<span data-ttu-id="8cbe3-157">V 2.0 projekty pomocí EF `Program.BuildWebHost` Core 2.0, je vyvolána k získání aplikačních služeb.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="8cbe3-158">Na rozdíl od 1.x, to má další `Startup.Configure`vedlejší účinek vyvolání .</span><span class="sxs-lookup"><span data-stu-id="8cbe3-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="8cbe3-159">Pokud vaše aplikace 1.x vyvolala inicializační kód databáze ve své `Configure` metodě, může dojít k neočekávaným problémům.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="8cbe3-160">Například pokud databáze ještě neexistuje, kód nastavení se spustí před spuštěním příkazu EF Core Migrations.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="8cbe3-161">Tento problém `dotnet ef migrations list` způsobí selhání příkazu, pokud databáze ještě neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="8cbe3-162">Vezměme si následující 1.x počáteční `Configure` inicializační kód v metodě *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="8cbe3-163">V projektech 2.0 `SeedData.Initialize` přesuňte `Main` volání na metodu *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="8cbe3-164">Od 2.0 je špatné dělat cokoli v `BuildWebHost` jiném než vytvořit a nakonfigurovat webhosting.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="8cbe3-165">Cokoli, co je o spuštění aplikace `BuildWebHost` &mdash; by měly `Main` být zpracovány mimo obvykle v metodě *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a><span data-ttu-id="8cbe3-166">Zkontrolovat nastavení kompilace zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="8cbe3-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="8cbe3-167">Rychlejší spuštění aplikace a menší publikované balíčky jsou pro vás nesmírně důležité.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="8cbe3-168">Z těchto důvodů je [kompilace Razor view](xref:mvc/views/view-compilation) ve výchozím nastavení povolena v ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="8cbe3-169">Nastavení `MvcRazorCompileOnPublish` vlastnosti na hodnotu true již není nutné.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="8cbe3-170">Pokud nezakážete kompilaci zobrazení, může být vlastnost odebrána ze souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="8cbe3-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="8cbe3-171">Při cílení na rozhraní .NET Framework je stále nutné explicitně odkazovat na balíček [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet ve vašem souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="8cbe3-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="8cbe3-172">Spolehněte se na funkce Application Insights "light-up"</span><span class="sxs-lookup"><span data-stu-id="8cbe3-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="8cbe3-173">Snadné nastavení přístrojové desky výkonu aplikace je důležité.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="8cbe3-174">Nyní se můžete spolehnout na nové funkce [Application Insights](/azure/application-insights/app-insights-overview) "light-up", které jsou k dispozici v nástrojích Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="8cbe3-175">ASP.NET Core 1.1 projekty vytvořené ve Visual Studiu 2017 přidalapplication insights ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="8cbe3-176">Pokud nepoužíváte sdk application insights přímo, mimo *Program.cs* a *Startup.cs*, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="8cbe3-177">Pokud cílíte na jádro `<PackageReference />` .NET Core, odeberte ze souboru *.csproj* následující uzel:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="8cbe3-178">Pokud cílení .NET Core, odeberte vyvolání metody `UseApplicationInsights` rozšíření z *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="8cbe3-179">Odeberte volání rozhraní API na straně klienta Application Insights z *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="8cbe3-180">Skládá se z těchto dvou řádků kódu:</span><span class="sxs-lookup"><span data-stu-id="8cbe3-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="8cbe3-181">Pokud používáte sady Application Insights SDK přímo, pokračujte v tom.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="8cbe3-182">[Metabalíček](xref:fundamentals/metapackage) 2.0 obsahuje nejnovější verzi Application Insights, takže se zobrazí chyba downgradu balíčku, pokud odkazujete na starší verzi.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a><span data-ttu-id="8cbe3-183">Přijmout vylepšení ověřování a identity</span><span class="sxs-lookup"><span data-stu-id="8cbe3-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="8cbe3-184">ASP.NET Core 2.0 má nový model ověřování a řadu významných změn ASP.NET základní identity.</span><span class="sxs-lookup"><span data-stu-id="8cbe3-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="8cbe3-185">Pokud jste projekt vytvořili s povolenými individuálními uživatelskými účty nebo jste ručně přidali ověřování nebo identitu, přečtěte si informace [o migraci ověřování a identity do ASP.NET jádra 2.0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="8cbe3-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8cbe3-186">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8cbe3-186">Additional resources</span></span>

* [<span data-ttu-id="8cbe3-187">Prolomení změn v ASP.NET jádra 2.0</span><span class="sxs-lookup"><span data-stu-id="8cbe3-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
