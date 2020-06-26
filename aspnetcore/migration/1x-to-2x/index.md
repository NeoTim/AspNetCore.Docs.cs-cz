---
title: Migrace z ASP.NET Core 1. x na 2,0
author: scottaddie
description: V tomto článku najdete popis požadavků a nejběžnějších kroků migrace projektu ASP.NET Core 1. x na ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 97fe2f36aed4a2ac60a7ffc30ede5e682a838e5e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408692"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="b35bf-103">Migrace z ASP.NET Core 1. x na 2,0</span><span class="sxs-lookup"><span data-stu-id="b35bf-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="b35bf-104">[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="b35bf-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="b35bf-105">V tomto článku Vás provedeme aktualizací existujícího projektu ASP.NET Core 1. x na ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="b35bf-106">Migrace aplikace na ASP.NET Core 2,0 vám umožní využít [spoustu nových funkcí a vylepšení výkonu](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="b35bf-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="b35bf-107">Stávající aplikace ASP.NET Core 1. x jsou založeny na šablonách projektů specifických pro danou verzi.</span><span class="sxs-lookup"><span data-stu-id="b35bf-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="b35bf-108">Jak se vyvíjí rozhraní ASP.NET Core, takže proveďte šablony projektu a počáteční kód obsažený v nich.</span><span class="sxs-lookup"><span data-stu-id="b35bf-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="b35bf-109">Kromě aktualizace ASP.NET Coreho rozhraní je potřeba aktualizovat kód pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b35bf-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="b35bf-110">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b35bf-110">Prerequisites</span></span>

<span data-ttu-id="b35bf-111">Viz Začínáme [s ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="b35bf-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="b35bf-112">Aktualizovat moniker cílového rozhraní (TFM)</span><span class="sxs-lookup"><span data-stu-id="b35bf-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="b35bf-113">Projekty cílené na .NET Core by měly používat [TFM](/dotnet/standard/frameworks) verze, která je větší nebo rovna .net Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="b35bf-114">Vyhledejte `<TargetFramework>` uzel v souboru *. csproj* a nahraďte jeho vnitřní text `netcoreapp2.0` :</span><span class="sxs-lookup"><span data-stu-id="b35bf-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="b35bf-115">Projekty cílené na .NET Framework by měly používat TFM verze, která je větší nebo rovna .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="b35bf-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="b35bf-116">Vyhledejte `<TargetFramework>` uzel v souboru *. csproj* a nahraďte jeho vnitřní text `net461` :</span><span class="sxs-lookup"><span data-stu-id="b35bf-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="b35bf-117">.NET Core 2,0 nabízí mnohem větší plochu než .NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="b35bf-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="b35bf-118">Pokud cílíte .NET Framework výhradně z důvodu chybějících rozhraní API v rozhraní .NET Core 1. x, bude pravděpodobně fungovat cílení na .NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="b35bf-119">Pokud soubor projektu obsahuje `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>` , přečtěte si [Tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="b35bf-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="b35bf-120">Aktualizace verze .NET Core SDK v global.js</span><span class="sxs-lookup"><span data-stu-id="b35bf-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="b35bf-121">Pokud vaše řešení spoléhá na [global.js](/dotnet/core/tools/global-json) souboru pro cílení na konkrétní .NET Core SDKou verzi, aktualizujte její `version` vlastnost tak, aby používala verzi 2,0 nainstalovanou na vašem počítači:</span><span class="sxs-lookup"><span data-stu-id="b35bf-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="b35bf-122">Aktualizovat odkazy na balíček</span><span class="sxs-lookup"><span data-stu-id="b35bf-122">Update package references</span></span>

<span data-ttu-id="b35bf-123">Soubor *. csproj* v projektu 1. x obsahuje seznam všech balíčků NuGet používaných projektem.</span><span class="sxs-lookup"><span data-stu-id="b35bf-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="b35bf-124">V projektu ASP.NET Core 2,0 cíleném na rozhraní .NET Core 2,0 nahrazuje jeden odkaz [Metapackage](xref:fundamentals/metapackage) v souboru *. csproj* kolekci balíčků:</span><span class="sxs-lookup"><span data-stu-id="b35bf-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="b35bf-125">Do Metapackage jsou zahrnuté všechny funkce ASP.NET Core 2,0 a Entity Framework Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="b35bf-126">Projekty ASP.NET Core 2,0 cílené na .NET Framework by měly dál odkazovat na jednotlivé balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="b35bf-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="b35bf-127">Aktualizujte `Version` atribut každého `<PackageReference />` uzlu na 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="b35bf-128">Tady je například seznam `<PackageReference />` uzlů použitých v typickém projektu ASP.NET Core 2,0, který cílí na .NET Framework:</span><span class="sxs-lookup"><span data-stu-id="b35bf-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="b35bf-129">Aktualizovat .NET Core CLI nástroje</span><span class="sxs-lookup"><span data-stu-id="b35bf-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="b35bf-130">V souboru *. csproj* aktualizujte `Version` atribut každého `<DotNetCliToolReference />` uzlu na 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="b35bf-131">Tady je například seznam nástrojů rozhraní příkazového řádku, které se používají v typickém ASP.NET Core 2,0 projektu cílící na .NET Core 2,0:</span><span class="sxs-lookup"><span data-stu-id="b35bf-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="b35bf-132">Přejmenovat záložní vlastnost cílového balíčku</span><span class="sxs-lookup"><span data-stu-id="b35bf-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="b35bf-133">Soubor *. csproj* v projektu 1. x používal `PackageTargetFallback` uzel a proměnnou:</span><span class="sxs-lookup"><span data-stu-id="b35bf-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="b35bf-134">Přejmenujte uzel i proměnnou na `AssetTargetFallback` :</span><span class="sxs-lookup"><span data-stu-id="b35bf-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="b35bf-135">Aktualizace metody Main v Program.cs</span><span class="sxs-lookup"><span data-stu-id="b35bf-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="b35bf-136">V projektech 1. x `Main` metoda *program.cs* vypadala takto:</span><span class="sxs-lookup"><span data-stu-id="b35bf-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="b35bf-137">V projektech 2,0 je `Main` metoda *program.cs* zjednodušená:</span><span class="sxs-lookup"><span data-stu-id="b35bf-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="b35bf-138">Přijetí tohoto nového vzoru 2,0 se důrazně doporučuje a vyžaduje se pro funkce produktu, jako jsou [základní migrace pro Entity Framework (EF)](xref:data/ef-mvc/migrations) .</span><span class="sxs-lookup"><span data-stu-id="b35bf-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="b35bf-139">Například spuštění `Update-Database` z okna konzoly Správce balíčků nebo `dotnet ef database update` z příkazového řádku (v projektech převedených na ASP.NET Core 2,0) generuje následující chybu:</span><span class="sxs-lookup"><span data-stu-id="b35bf-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="b35bf-140">Přidat poskytovatele konfigurace</span><span class="sxs-lookup"><span data-stu-id="b35bf-140">Add configuration providers</span></span>

<span data-ttu-id="b35bf-141">V projektech 1. x bylo přidání poskytovatelů konfigurace do aplikace provedeno prostřednictvím `Startup` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b35bf-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="b35bf-142">Kroky při vytváření instance `ConfigurationBuilder` , načtení použitelných zprostředkovatelů (proměnné prostředí, nastavení aplikace atd.) a inicializace člena `IConfigurationRoot` .</span><span class="sxs-lookup"><span data-stu-id="b35bf-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="b35bf-143">Předchozí příklad načte `Configuration` člena s nastavením konfigurace z *appsettings.jsna* , a také všechny *appSettings. \<EnvironmentName\> . soubor JSON* , který odpovídá `IHostingEnvironment.EnvironmentName` vlastnosti</span><span class="sxs-lookup"><span data-stu-id="b35bf-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="b35bf-144">Umístění těchto souborů je na stejné cestě jako *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b35bf-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="b35bf-145">V projektech 2,0 se standardní konfigurační kód, který je podstatný pro 1. x, spouští za pozadí.</span><span class="sxs-lookup"><span data-stu-id="b35bf-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="b35bf-146">Například proměnné prostředí a nastavení aplikace jsou načítány při spuštění.</span><span class="sxs-lookup"><span data-stu-id="b35bf-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="b35bf-147">Ekvivalentní kód *Startup.cs* se zkracuje na `IConfiguration` inicializaci s vloženou instancí:</span><span class="sxs-lookup"><span data-stu-id="b35bf-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="b35bf-148">Chcete-li odebrat výchozí zprostředkovatele přidaných pomocí `WebHostBuilder.CreateDefaultBuilder` , volejte `Clear` metodu `IConfigurationBuilder.Sources` vlastnosti v rámci `ConfigureAppConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="b35bf-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="b35bf-149">Chcete-li přidat poskytovatele zpět, využijte `ConfigureAppConfiguration` metodu v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b35bf-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="b35bf-150">Konfiguraci použitou `CreateDefaultBuilder` metodou v předchozím fragmentu kódu lze zobrazit [zde](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="b35bf-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="b35bf-151">Další informace najdete v tématu [konfigurace v ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b35bf-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="b35bf-152">Přesunout inicializační kód databáze</span><span class="sxs-lookup"><span data-stu-id="b35bf-152">Move database initialization code</span></span>

<span data-ttu-id="b35bf-153">V projektech 1. x, které používají EF Core 1. x, příkaz například `dotnet ef migrations add` provede následující:</span><span class="sxs-lookup"><span data-stu-id="b35bf-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="b35bf-154">Vytvoří instanci `Startup` instance.</span><span class="sxs-lookup"><span data-stu-id="b35bf-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="b35bf-155">Vyvolá `ConfigureServices` metodu pro registraci všech služeb pomocí injektáže závislosti (včetně `DbContext` typů).</span><span class="sxs-lookup"><span data-stu-id="b35bf-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="b35bf-156">Provede požadované úlohy.</span><span class="sxs-lookup"><span data-stu-id="b35bf-156">Performs its requisite tasks</span></span>

<span data-ttu-id="b35bf-157">V 2,0ch projektech, které používají EF Core 2,0, `Program.BuildWebHost` je vyvoláno získání aplikačních služeb.</span><span class="sxs-lookup"><span data-stu-id="b35bf-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="b35bf-158">Na rozdíl od 1. x to má další vedlejší účinek vyvolání `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b35bf-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="b35bf-159">Pokud vaše aplikace 1. x vyvolala inicializační kód databáze ve své `Configure` metodě, může dojít k neočekávaným problémům.</span><span class="sxs-lookup"><span data-stu-id="b35bf-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="b35bf-160">Například pokud databáze ještě neexistuje, kód pro osazení se spustí před spuštěním příkazu EF Core migrace.</span><span class="sxs-lookup"><span data-stu-id="b35bf-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="b35bf-161">Tento problém způsobí `dotnet ef migrations list` selhání příkazu, pokud databáze ještě neexistuje.</span><span class="sxs-lookup"><span data-stu-id="b35bf-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="b35bf-162">V metodě Startup.cs Vezměte v úvahu následující inicializační kód počátečního použití: 1. x `Configure` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b35bf-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="b35bf-163">V projektech 2,0 přesuňte `SeedData.Initialize` volání `Main` metody *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b35bf-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="b35bf-164">Od 2,0 se nejedná o špatný postup, který se dá dělat `BuildWebHost` s výjimkou sestavení a konfigurace webového hostitele.</span><span class="sxs-lookup"><span data-stu-id="b35bf-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="b35bf-165">Cokoli, co je o spuštění aplikace, by mělo být zpracováno mimo `BuildWebHost` &mdash; obvykle v `Main` metodě *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="b35bf-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a><span data-ttu-id="b35bf-166">Kontrola Razor nastavení kompilace zobrazení</span><span class="sxs-lookup"><span data-stu-id="b35bf-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="b35bf-167">Rychlejší čas spuštění aplikace a menší publikované sady mají největší důležitost.</span><span class="sxs-lookup"><span data-stu-id="b35bf-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="b35bf-168">Z těchto důvodů je ve výchozím nastavení povoleno [ Razor zobrazení kompilace](xref:mvc/views/view-compilation) v ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="b35bf-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="b35bf-169">Nastavení `MvcRazorCompileOnPublish` vlastnosti na hodnotu true již není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="b35bf-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="b35bf-170">Pokud nezakážete kompilaci zobrazení, vlastnost může být odebrána ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="b35bf-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="b35bf-171">Při cílení na .NET Framework stále musíte explicitně odkazovat na [Microsoft. AspNetCore. Mvc. Razor ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) balíček NuGet v souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b35bf-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="b35bf-172">Spoléhá se na Application Insights funkce "světlého".</span><span class="sxs-lookup"><span data-stu-id="b35bf-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="b35bf-173">Snadné nastavení instrumentace výkonu aplikace je důležité.</span><span class="sxs-lookup"><span data-stu-id="b35bf-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="b35bf-174">Nyní se můžete spoléhat na [Application Insights](/azure/application-insights/app-insights-overview) nové funkce "světlého", které jsou k dispozici v nástrojích sady Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="b35bf-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="b35bf-175">V aplikaci Visual Studio 2017 byly ve výchozím nastavení přidány Application Insights projekty ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="b35bf-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="b35bf-176">Pokud nepoužíváte Application Insights SDK přímo, mimo *program.cs* a *Startup.cs*, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="b35bf-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="b35bf-177">Pokud cílíte na rozhraní .NET Core, odeberte následující `<PackageReference />` uzel ze souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b35bf-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="b35bf-178">Pokud cílíte na rozhraní .NET Core, odeberte `UseApplicationInsights` volání rozšiřující metody z *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b35bf-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="b35bf-179">Odeberte Application Insights volání rozhraní API na straně klienta z *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b35bf-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="b35bf-180">Obsahuje následující dva řádky kódu:</span><span class="sxs-lookup"><span data-stu-id="b35bf-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="b35bf-181">Pokud používáte sadu Application Insights SDK přímo, pokračujte v tomto případě.</span><span class="sxs-lookup"><span data-stu-id="b35bf-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="b35bf-182">2,0 [Metapackage](xref:fundamentals/metapackage) zahrnuje nejnovější verzi Application Insights, takže při odkazování na starší verzi se zobrazí chyba downgrade balíčku.</span><span class="sxs-lookup"><span data-stu-id="b35bf-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a><span data-ttu-id="b35bf-183">Přijmout ověřování/ Identity vylepšení</span><span class="sxs-lookup"><span data-stu-id="b35bf-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="b35bf-184">ASP.NET Core 2,0 má nový model ověřování a řadu významných změn, které se ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b35bf-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="b35bf-185">Pokud jste vytvořili projekt s povolenými jednotlivými uživatelskými účty, nebo pokud jste ručně přidali ověřování Identity , přečtěte si téma [migrace ověřování a Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="b35bf-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b35bf-186">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b35bf-186">Additional resources</span></span>

* [<span data-ttu-id="b35bf-187">Přerušující změny v ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="b35bf-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
