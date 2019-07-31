---
title: dotnet ASPNET-CodeGenerator – příkaz
author: rick-anderson
description: Rozhraní dotnet ASPNET-CodeGenerator příkazy ASP.NET Core projekty.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: c2c815735ad1b4dcec761b26ea3992a4effebe62
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682693"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="a627a-103">dotnet ASPNET – CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="a627a-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="a627a-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a627a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a627a-105">`dotnet aspnet-codegenerator`– Spustí modul generování uživatelského rozhraní ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a627a-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="a627a-106">`dotnet aspnet-codegenerator`je vyžadován pouze pro generování uživatelského rozhraní z příkazového řádku, není nutné používat generování uživatelského rozhraní se sadou Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a627a-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="a627a-107">Tento článek se týká [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.</span><span class="sxs-lookup"><span data-stu-id="a627a-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="a627a-108">Instalace ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="a627a-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="a627a-109">`dotnet-aspnet-codegenerator`je [globální nástroj](/dotnet/core/tools/global-tools) , který musí být nainstalován.</span><span class="sxs-lookup"><span data-stu-id="a627a-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="a627a-110">Následující příkaz nainstaluje nejnovější stabilní verzi `dotnet-aspnet-codegenerator` nástroje:</span><span class="sxs-lookup"><span data-stu-id="a627a-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a627a-111">Následující příkaz aktualizuje `dotnet-aspnet-codegenerator` na nejnovější stabilní verzi dostupnou z nainstalovaných sad SDK .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a627a-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```console
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="a627a-112">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="a627a-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="a627a-113">Popis</span><span class="sxs-lookup"><span data-stu-id="a627a-113">Description</span></span>

<span data-ttu-id="a627a-114">`dotnet aspnet-codegenerator` Globální příkaz spustí generátor kódu ASP.NET Core a modul generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a627a-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="a627a-115">Arguments</span><span class="sxs-lookup"><span data-stu-id="a627a-115">Arguments</span></span>

`generator`

<span data-ttu-id="a627a-116">Generátor kódu, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="a627a-116">The code generator to run.</span></span> <span data-ttu-id="a627a-117">K dispozici jsou následující generátory:</span><span class="sxs-lookup"><span data-stu-id="a627a-117">The following generators are available:</span></span>

| <span data-ttu-id="a627a-118">Generátor</span><span class="sxs-lookup"><span data-stu-id="a627a-118">Generator</span></span> | <span data-ttu-id="a627a-119">Operace</span><span class="sxs-lookup"><span data-stu-id="a627a-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="a627a-120">Oblast</span><span class="sxs-lookup"><span data-stu-id="a627a-120">area</span></span>      | [<span data-ttu-id="a627a-121">Generování uživatelského rozhraní oblasti</span><span class="sxs-lookup"><span data-stu-id="a627a-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="a627a-122">kontroler</span><span class="sxs-lookup"><span data-stu-id="a627a-122">controller</span></span>| [<span data-ttu-id="a627a-123">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="a627a-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="a627a-124">identita</span><span class="sxs-lookup"><span data-stu-id="a627a-124">identity</span></span>  | [<span data-ttu-id="a627a-125">Identita uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="a627a-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="a627a-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="a627a-126">razorpage</span></span> | [<span data-ttu-id="a627a-127">Razor Pages generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="a627a-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="a627a-128">zobrazení</span><span class="sxs-lookup"><span data-stu-id="a627a-128">view</span></span>      | [<span data-ttu-id="a627a-129">Generování uživatelského rozhraní zobrazení</span><span class="sxs-lookup"><span data-stu-id="a627a-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="a627a-130">Možnosti</span><span class="sxs-lookup"><span data-stu-id="a627a-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="a627a-131">Určuje adresář balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="a627a-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="a627a-132">Definuje konfiguraci sestavení.</span><span class="sxs-lookup"><span data-stu-id="a627a-132">Defines the build configuration.</span></span> <span data-ttu-id="a627a-133">Výchozí hodnota je `Debug`.</span><span class="sxs-lookup"><span data-stu-id="a627a-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="a627a-134">Cílová [Architektura](/dotnet/standard/frameworks) , která se má použít.</span><span class="sxs-lookup"><span data-stu-id="a627a-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="a627a-135">Například, `net46`.</span><span class="sxs-lookup"><span data-stu-id="a627a-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="a627a-136">Základní cesta sestavení</span><span class="sxs-lookup"><span data-stu-id="a627a-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="a627a-137">Vypíše krátkou nápovědu k příkazu.</span><span class="sxs-lookup"><span data-stu-id="a627a-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="a627a-138">Před spuštěním nevytvoří projekt.</span><span class="sxs-lookup"><span data-stu-id="a627a-138">Doesn't build the project before running.</span></span> <span data-ttu-id="a627a-139">Také implicitně nastaví `--no-restore` příznak.</span><span class="sxs-lookup"><span data-stu-id="a627a-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="a627a-140">Určuje cestu k souboru projektu, který se má spustit (název složky nebo úplná cesta).</span><span class="sxs-lookup"><span data-stu-id="a627a-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="a627a-141">Pokud není zadaný, použije se ve výchozím nastavení aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="a627a-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="a627a-142">Možnosti generátoru</span><span class="sxs-lookup"><span data-stu-id="a627a-142">Generator options</span></span>

<span data-ttu-id="a627a-143">Následující části obsahují podrobné informace o možnostech, které jsou k dispozici pro podporovaná generátory:</span><span class="sxs-lookup"><span data-stu-id="a627a-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="a627a-144">Oblast</span><span class="sxs-lookup"><span data-stu-id="a627a-144">Area</span></span>
* <span data-ttu-id="a627a-145">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="a627a-145">Controller</span></span>
* <span data-ttu-id="a627a-146">Identita</span><span class="sxs-lookup"><span data-stu-id="a627a-146">Identity</span></span>  
* <span data-ttu-id="a627a-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="a627a-147">Razorpage</span></span>
* <span data-ttu-id="a627a-148">Zobrazit</span><span class="sxs-lookup"><span data-stu-id="a627a-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="a627a-149">Možnosti oblasti</span><span class="sxs-lookup"><span data-stu-id="a627a-149">Area options</span></span>

<span data-ttu-id="a627a-150">Tento nástroj je určený pro ASP.NET Core webové projekty s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="a627a-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="a627a-151">Není určena pro aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a627a-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="a627a-152">Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="a627a-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="a627a-153">Předchozí příkaz vygeneruje následující složky:</span><span class="sxs-lookup"><span data-stu-id="a627a-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="a627a-154">*Oblasti*</span><span class="sxs-lookup"><span data-stu-id="a627a-154">*Areas*</span></span>
  * <span data-ttu-id="a627a-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="a627a-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="a627a-156">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="a627a-156">*Controllers*</span></span>
    * <span data-ttu-id="a627a-157">*Data*</span><span class="sxs-lookup"><span data-stu-id="a627a-157">*Data*</span></span>
    * <span data-ttu-id="a627a-158">*Vzor*</span><span class="sxs-lookup"><span data-stu-id="a627a-158">*Models*</span></span>
    * <span data-ttu-id="a627a-159">*Zobrazení*</span><span class="sxs-lookup"><span data-stu-id="a627a-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="a627a-160">Možnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="a627a-160">Controller options</span></span>

<span data-ttu-id="a627a-161">V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` pro `razorpage` `controller` a:</span><span class="sxs-lookup"><span data-stu-id="a627a-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="a627a-162">Následující tabulka uvádí možnosti, které jsou `aspnet-codegenerator controller`jedinečné pro:</span><span class="sxs-lookup"><span data-stu-id="a627a-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="a627a-163">Možnost</span><span class="sxs-lookup"><span data-stu-id="a627a-163">Option</span></span>               | <span data-ttu-id="a627a-164">Popis</span><span class="sxs-lookup"><span data-stu-id="a627a-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="a627a-165">--Controller nebo-Name</span><span class="sxs-lookup"><span data-stu-id="a627a-165">--controllerName or -name</span></span> | <span data-ttu-id="a627a-166">Název kontroleru</span><span class="sxs-lookup"><span data-stu-id="a627a-166">Name of the controller.</span></span> |
| <span data-ttu-id="a627a-167">--useAsyncActions nebo-Async</span><span class="sxs-lookup"><span data-stu-id="a627a-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="a627a-168">Vygenerujte akce asynchronního kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a627a-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="a627a-169">--nezobrazení nebo-NV</span><span class="sxs-lookup"><span data-stu-id="a627a-169">--noViews or -nv</span></span> | <span data-ttu-id="a627a-170">Negenerovat **žádná** zobrazení</span><span class="sxs-lookup"><span data-stu-id="a627a-170">Generate **no** views.</span></span> |
| <span data-ttu-id="a627a-171">--restWithNoViews nebo-API</span><span class="sxs-lookup"><span data-stu-id="a627a-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="a627a-172">Vygenerujte kontrolér s rozhraním API stylu REST.</span><span class="sxs-lookup"><span data-stu-id="a627a-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="a627a-173">`noViews`je předpokládaná a všechny možnosti zobrazení jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="a627a-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="a627a-174">--readWriteActions nebo-Actions</span><span class="sxs-lookup"><span data-stu-id="a627a-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="a627a-175">Vygeneruje kontroler s akcemi čtení/zápisu bez modelu.</span><span class="sxs-lookup"><span data-stu-id="a627a-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="a627a-176">Pro nápovědu`aspnet-codegenerator controller` k příkazu použijte přepínač:`-h`</span><span class="sxs-lookup"><span data-stu-id="a627a-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```console
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="a627a-177">Příklad`dotnet aspnet-codegenerator controller`naleznete v tématu [generování modelu filmu](/aspnet/core/tutorials/razor-pages/model) .</span><span class="sxs-lookup"><span data-stu-id="a627a-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="a627a-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="a627a-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="a627a-179">Razor Pages může být individuálně vygenerovaného uživatelského rozhraní zadáním názvu nové stránky a šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="a627a-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="a627a-180">Podporované šablony:</span><span class="sxs-lookup"><span data-stu-id="a627a-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="a627a-181">Například následující příkaz používá šablonu Edit pro generování *MyEdit. cshtml* a *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a627a-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="a627a-182">Obvykle není zadána šablona a vygenerovaný název souboru a jsou vytvořeny následující šablony:</span><span class="sxs-lookup"><span data-stu-id="a627a-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="a627a-183">V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` pro `controller` `razorpage` a:</span><span class="sxs-lookup"><span data-stu-id="a627a-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="a627a-184">Následující tabulka uvádí možnosti, které jsou `aspnet-codegenerator razorpage`jedinečné pro:</span><span class="sxs-lookup"><span data-stu-id="a627a-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="a627a-185">Možnost</span><span class="sxs-lookup"><span data-stu-id="a627a-185">Option</span></span>               | <span data-ttu-id="a627a-186">Popis</span><span class="sxs-lookup"><span data-stu-id="a627a-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="a627a-187">--Namespace nebo-Namespace</span><span class="sxs-lookup"><span data-stu-id="a627a-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="a627a-188">Název oboru názvů, který se má použít pro vygenerovaný PageModel</span><span class="sxs-lookup"><span data-stu-id="a627a-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="a627a-189">--partialView nebo-Partial</span><span class="sxs-lookup"><span data-stu-id="a627a-189">--partialView or -partial</span></span> | <span data-ttu-id="a627a-190">Vygeneruje částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a627a-190">Generate a partial view.</span></span> <span data-ttu-id="a627a-191">Možnosti rozložení – l a-UDL se při zadání tohoto nastavení ignorují.</span><span class="sxs-lookup"><span data-stu-id="a627a-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="a627a-192">--noPageModel nebo-npm</span><span class="sxs-lookup"><span data-stu-id="a627a-192">--noPageModel or -npm</span></span> | <span data-ttu-id="a627a-193">Přepněte na negenerovat třídu PageModel pro prázdnou šablonu.</span><span class="sxs-lookup"><span data-stu-id="a627a-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="a627a-194">Pro nápovědu`aspnet-codegenerator razorpage` k příkazu použijte přepínač:`-h`</span><span class="sxs-lookup"><span data-stu-id="a627a-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="a627a-195">Příklad`dotnet aspnet-codegenerator razorpage`naleznete v tématu [generování modelu filmu](/aspnet/core/tutorials/razor-pages/model) .</span><span class="sxs-lookup"><span data-stu-id="a627a-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="a627a-196">Identita</span><span class="sxs-lookup"><span data-stu-id="a627a-196">Identity</span></span>

<span data-ttu-id="a627a-197">Zobrazit [identitu uživatelského rozhraní](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="a627a-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
