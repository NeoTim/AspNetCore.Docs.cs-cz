---
title: dotnet ASPNET-CodeGenerator – příkaz
author: rick-anderson
description: Rozhraní dotnet ASPNET-CodeGenerator příkazy ASP.NET Core projekty.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: a106654c8a37e84e9186a2f06d90605df753e8a7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405598"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="22b52-103">dotnet ASPNET – CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="22b52-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="22b52-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="22b52-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="22b52-105">`dotnet aspnet-codegenerator`– Spustí modul generování uživatelského rozhraní ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="22b52-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="22b52-106">`dotnet aspnet-codegenerator`je vyžadován pouze pro generování uživatelského rozhraní z příkazového řádku, není nutné používat generování uživatelského rozhraní se sadou Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="22b52-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="22b52-107">Tento článek se týká [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.</span><span class="sxs-lookup"><span data-stu-id="22b52-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="22b52-108">Instalace ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="22b52-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="22b52-109">`dotnet-aspnet-codegenerator`je [globální nástroj](/dotnet/core/tools/global-tools) , který musí být nainstalován.</span><span class="sxs-lookup"><span data-stu-id="22b52-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="22b52-110">Následující příkaz nainstaluje nejnovější stabilní verzi `dotnet-aspnet-codegenerator` nástroje:</span><span class="sxs-lookup"><span data-stu-id="22b52-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="22b52-111">Následující příkaz aktualizuje `dotnet-aspnet-codegenerator` na nejnovější stabilní verzi dostupnou z nainstalovaných sad SDK .NET Core:</span><span class="sxs-lookup"><span data-stu-id="22b52-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="22b52-112">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="22b52-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="22b52-113">Description</span><span class="sxs-lookup"><span data-stu-id="22b52-113">Description</span></span>

<span data-ttu-id="22b52-114">`dotnet aspnet-codegenerator`Globální příkaz spustí generátor kódu ASP.NET Core a modul generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="22b52-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="22b52-115">Arguments</span><span class="sxs-lookup"><span data-stu-id="22b52-115">Arguments</span></span>

`generator`

<span data-ttu-id="22b52-116">Generátor kódu, který se má spustit.</span><span class="sxs-lookup"><span data-stu-id="22b52-116">The code generator to run.</span></span> <span data-ttu-id="22b52-117">K dispozici jsou následující generátory:</span><span class="sxs-lookup"><span data-stu-id="22b52-117">The following generators are available:</span></span>

| <span data-ttu-id="22b52-118">Generátor</span><span class="sxs-lookup"><span data-stu-id="22b52-118">Generator</span></span> | <span data-ttu-id="22b52-119">Operace</span><span class="sxs-lookup"><span data-stu-id="22b52-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="22b52-120">oblast</span><span class="sxs-lookup"><span data-stu-id="22b52-120">area</span></span>      | [<span data-ttu-id="22b52-121">Generování uživatelského rozhraní oblasti</span><span class="sxs-lookup"><span data-stu-id="22b52-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="22b52-122">kontroler</span><span class="sxs-lookup"><span data-stu-id="22b52-122">controller</span></span>| [<span data-ttu-id="22b52-123">Generování uživatelského rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="22b52-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="22b52-124">identity</span><span class="sxs-lookup"><span data-stu-id="22b52-124">identity</span></span>  | <span data-ttu-id="22b52-125">[SCAFFOLDIdentity](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="22b52-125">[Scaffolds Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span> |
  <span data-ttu-id="22b52-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="22b52-126">razorpage</span></span> | <span data-ttu-id="22b52-127">[Stránky generování uživatelského rozhraní Razor](/aspnet/core/tutorials/razor-pages/model)</span><span class="sxs-lookup"><span data-stu-id="22b52-127">[Scaffolds Razor Pages](/aspnet/core/tutorials/razor-pages/model)</span></span> |
  <span data-ttu-id="22b52-128">zobrazení</span><span class="sxs-lookup"><span data-stu-id="22b52-128">view</span></span>      | [<span data-ttu-id="22b52-129">Generování uživatelského rozhraní zobrazení</span><span class="sxs-lookup"><span data-stu-id="22b52-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="22b52-130">Možnosti</span><span class="sxs-lookup"><span data-stu-id="22b52-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="22b52-131">Určuje adresář balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="22b52-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="22b52-132">Definuje konfiguraci sestavení.</span><span class="sxs-lookup"><span data-stu-id="22b52-132">Defines the build configuration.</span></span> <span data-ttu-id="22b52-133">Výchozí hodnota je `Debug`.</span><span class="sxs-lookup"><span data-stu-id="22b52-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="22b52-134">Cílová [Architektura](/dotnet/standard/frameworks) , která se má použít.</span><span class="sxs-lookup"><span data-stu-id="22b52-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="22b52-135">Například, `net46`.</span><span class="sxs-lookup"><span data-stu-id="22b52-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="22b52-136">Základní cesta sestavení</span><span class="sxs-lookup"><span data-stu-id="22b52-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="22b52-137">Vypíše krátkou nápovědu k příkazu.</span><span class="sxs-lookup"><span data-stu-id="22b52-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="22b52-138">Před spuštěním nevytvoří projekt.</span><span class="sxs-lookup"><span data-stu-id="22b52-138">Doesn't build the project before running.</span></span> <span data-ttu-id="22b52-139">Také implicitně nastaví `--no-restore` příznak.</span><span class="sxs-lookup"><span data-stu-id="22b52-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="22b52-140">Určuje cestu k souboru projektu, který se má spustit (název složky nebo úplná cesta).</span><span class="sxs-lookup"><span data-stu-id="22b52-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="22b52-141">Pokud není zadaný, použije se ve výchozím nastavení aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="22b52-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="22b52-142">Možnosti generátoru</span><span class="sxs-lookup"><span data-stu-id="22b52-142">Generator options</span></span>

<span data-ttu-id="22b52-143">Následující části obsahují podrobné informace o možnostech, které jsou k dispozici pro podporovaná generátory:</span><span class="sxs-lookup"><span data-stu-id="22b52-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="22b52-144">Oblast</span><span class="sxs-lookup"><span data-stu-id="22b52-144">Area</span></span>
* <span data-ttu-id="22b52-145">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="22b52-145">Controller</span></span>
* Identity  
* <span data-ttu-id="22b52-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="22b52-146">Razorpage</span></span>
* <span data-ttu-id="22b52-147">Zobrazit</span><span class="sxs-lookup"><span data-stu-id="22b52-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="22b52-148">Možnosti oblasti</span><span class="sxs-lookup"><span data-stu-id="22b52-148">Area options</span></span>

<span data-ttu-id="22b52-149">Tento nástroj je určený pro ASP.NET Core webové projekty s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="22b52-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="22b52-150">Není určena pro Razor aplikace stránky.</span><span class="sxs-lookup"><span data-stu-id="22b52-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="22b52-151">Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="22b52-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="22b52-152">Předchozí příkaz vygeneruje následující složky:</span><span class="sxs-lookup"><span data-stu-id="22b52-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="22b52-153">*Oblasti*</span><span class="sxs-lookup"><span data-stu-id="22b52-153">*Areas*</span></span>
  * <span data-ttu-id="22b52-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="22b52-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="22b52-155">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="22b52-155">*Controllers*</span></span>
    * <span data-ttu-id="22b52-156">*Data*</span><span class="sxs-lookup"><span data-stu-id="22b52-156">*Data*</span></span>
    * <span data-ttu-id="22b52-157">*Modely*</span><span class="sxs-lookup"><span data-stu-id="22b52-157">*Models*</span></span>
    * <span data-ttu-id="22b52-158">*Zobrazení*</span><span class="sxs-lookup"><span data-stu-id="22b52-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="22b52-159">Možnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="22b52-159">Controller options</span></span>

<span data-ttu-id="22b52-160">V následující tabulce jsou uvedeny možnosti pro `aspnet-codegenerator` `controller` a `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="22b52-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="22b52-161">Následující tabulka uvádí možnosti, které jsou jedinečné pro `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="22b52-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="22b52-162">Možnost</span><span class="sxs-lookup"><span data-stu-id="22b52-162">Option</span></span>               | <span data-ttu-id="22b52-163">Description</span><span class="sxs-lookup"><span data-stu-id="22b52-163">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="22b52-164">--Controller nebo-Name</span><span class="sxs-lookup"><span data-stu-id="22b52-164">--controllerName or -name</span></span> | <span data-ttu-id="22b52-165">Název kontroleru</span><span class="sxs-lookup"><span data-stu-id="22b52-165">Name of the controller.</span></span> |
| <span data-ttu-id="22b52-166">--useAsyncActions nebo-Async</span><span class="sxs-lookup"><span data-stu-id="22b52-166">--useAsyncActions or -async</span></span> | <span data-ttu-id="22b52-167">Vygenerujte akce asynchronního kontroleru.</span><span class="sxs-lookup"><span data-stu-id="22b52-167">Generate async controller actions.</span></span> |
| <span data-ttu-id="22b52-168">--nezobrazení nebo-NV</span><span class="sxs-lookup"><span data-stu-id="22b52-168">--noViews or -nv</span></span> | <span data-ttu-id="22b52-169">Negenerovat **žádná** zobrazení</span><span class="sxs-lookup"><span data-stu-id="22b52-169">Generate **no** views.</span></span> |
| <span data-ttu-id="22b52-170">--restWithNoViews nebo-API</span><span class="sxs-lookup"><span data-stu-id="22b52-170">--restWithNoViews or -api</span></span>  | <span data-ttu-id="22b52-171">Vygenerujte kontrolér s rozhraním API stylu REST.</span><span class="sxs-lookup"><span data-stu-id="22b52-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="22b52-172">`noViews`je předpokládaná a všechny možnosti zobrazení jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="22b52-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="22b52-173">--readWriteActions nebo-Actions</span><span class="sxs-lookup"><span data-stu-id="22b52-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="22b52-174">Vygeneruje kontroler s akcemi čtení/zápisu bez modelu.</span><span class="sxs-lookup"><span data-stu-id="22b52-174">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="22b52-175">`-h`Pro nápovědu k příkazu použijte přepínač `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="22b52-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="22b52-176">Příklad naleznete v tématu [generování modelu filmu](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator controller` .</span><span class="sxs-lookup"><span data-stu-id="22b52-176">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="22b52-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="22b52-177">Razorpage</span></span>

<a name="rp"></a>

Razor<span data-ttu-id="22b52-178">Stránky mohou být jednotlivě vygenerované pomocí uživatelského rozhraní zadáním názvu nové stránky a šablony, která se má použít.</span><span class="sxs-lookup"><span data-stu-id="22b52-178"> Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="22b52-179">Podporované šablony:</span><span class="sxs-lookup"><span data-stu-id="22b52-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="22b52-180">Například následující příkaz používá šablonu Edit pro generování *MyEdit. cshtml* a *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="22b52-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="22b52-181">Obvykle není zadána šablona a vygenerovaný název souboru a jsou vytvořeny následující šablony:</span><span class="sxs-lookup"><span data-stu-id="22b52-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="22b52-182">V následující tabulce jsou uvedeny možnosti pro `aspnet-codegenerator` `razorpage` a `controller` :</span><span class="sxs-lookup"><span data-stu-id="22b52-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="22b52-183">Následující tabulka uvádí možnosti, které jsou jedinečné pro `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="22b52-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="22b52-184">Možnost</span><span class="sxs-lookup"><span data-stu-id="22b52-184">Option</span></span>               | <span data-ttu-id="22b52-185">Description</span><span class="sxs-lookup"><span data-stu-id="22b52-185">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="22b52-186">--Namespace nebo-Namespace</span><span class="sxs-lookup"><span data-stu-id="22b52-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="22b52-187">Název oboru názvů, který se má použít pro vygenerovaný PageModel</span><span class="sxs-lookup"><span data-stu-id="22b52-187">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="22b52-188">--partialView nebo-Partial</span><span class="sxs-lookup"><span data-stu-id="22b52-188">--partialView or -partial</span></span> | <span data-ttu-id="22b52-189">Vygeneruje částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="22b52-189">Generate a partial view.</span></span> <span data-ttu-id="22b52-190">Možnosti rozložení – l a-UDL se při zadání tohoto nastavení ignorují.</span><span class="sxs-lookup"><span data-stu-id="22b52-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="22b52-191">--noPageModel nebo-npm</span><span class="sxs-lookup"><span data-stu-id="22b52-191">--noPageModel or -npm</span></span> | <span data-ttu-id="22b52-192">Přepněte na negenerovat třídu PageModel pro prázdnou šablonu.</span><span class="sxs-lookup"><span data-stu-id="22b52-192">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="22b52-193">`-h`Pro nápovědu k příkazu použijte přepínač `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="22b52-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="22b52-194">Příklad naleznete v tématu [generování modelu filmu](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .</span><span class="sxs-lookup"><span data-stu-id="22b52-194">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="22b52-195">Viz [generování Identity uživatelského rozhraní](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="22b52-195">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
