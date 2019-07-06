---
title: příkaz DotNet aspnet codegenerator
author: rick-anderson
description: Příkaz dotnet aspnet codegenerator vygeneruje uživatelské rozhraní projekty ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: c96362f320efd84c35dc07294a2968a2c687ee94
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596143"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="bc976-103">DotNet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="bc976-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="bc976-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bc976-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bc976-105">`dotnet aspnet-codegenerator` -Spustí modul ASP.NET Core generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bc976-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="bc976-106">`dotnet aspnet-codegenerator` je jenom pro povinné pro generování uživatelského rozhraní z příkazového řádku, není potřeba používat generování uživatelského rozhraní pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bc976-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not need to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="bc976-107">Tento článek se týká [sady SDK .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.</span><span class="sxs-lookup"><span data-stu-id="bc976-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="bc976-108">Instalace aspnet codegenerator</span><span class="sxs-lookup"><span data-stu-id="bc976-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="bc976-109">`dotnet-aspnet-codegenerator` je [globální nástroj](/dotnet/core/tools/global-tools) , který musí být nainstalována.</span><span class="sxs-lookup"><span data-stu-id="bc976-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="bc976-110">Následující příkaz nainstaluje nejnovější stabilní verze `dotnet-aspnet-codegenerator` nástroje:</span><span class="sxs-lookup"><span data-stu-id="bc976-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="bc976-111">Zadáním následujícího příkazu aktualizace `dotnet-aspnet-codegenerator` nejnovější stabilní verzi k dispozici z nainstalovaných sad .NET Core SDK:</span><span class="sxs-lookup"><span data-stu-id="bc976-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```console
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="bc976-112">Souhrn</span><span class="sxs-lookup"><span data-stu-id="bc976-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="bc976-113">Popis</span><span class="sxs-lookup"><span data-stu-id="bc976-113">Description</span></span>

<span data-ttu-id="bc976-114">`dotnet aspnet-codegenerator` Globální příkaz spustí ASP.NET Core, generátor kódu a modulu generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="bc976-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="bc976-115">Arguments</span><span class="sxs-lookup"><span data-stu-id="bc976-115">Arguments</span></span>

`generator`

<span data-ttu-id="bc976-116">Generátor kódu pro spuštění.</span><span class="sxs-lookup"><span data-stu-id="bc976-116">The code generator to run.</span></span> <span data-ttu-id="bc976-117">K dispozici jsou následující zdroje:</span><span class="sxs-lookup"><span data-stu-id="bc976-117">The following generators are available:</span></span>

| <span data-ttu-id="bc976-118">Generátor</span><span class="sxs-lookup"><span data-stu-id="bc976-118">Generator</span></span> | <span data-ttu-id="bc976-119">Operace</span><span class="sxs-lookup"><span data-stu-id="bc976-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="bc976-120">Oblast</span><span class="sxs-lookup"><span data-stu-id="bc976-120">area</span></span>      | [<span data-ttu-id="bc976-121">Nástroj scaffold oblasti</span><span class="sxs-lookup"><span data-stu-id="bc976-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="bc976-122">kontroler</span><span class="sxs-lookup"><span data-stu-id="bc976-122">controller</span></span>| [<span data-ttu-id="bc976-123">Vygeneruje uživatelské rozhraní kontroleru</span><span class="sxs-lookup"><span data-stu-id="bc976-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="bc976-124">identita</span><span class="sxs-lookup"><span data-stu-id="bc976-124">identity</span></span>  | [<span data-ttu-id="bc976-125">Nástroj scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="bc976-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="bc976-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="bc976-126">razorpage</span></span> | [<span data-ttu-id="bc976-127">Nástroj scaffold Razor Pages</span><span class="sxs-lookup"><span data-stu-id="bc976-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="bc976-128">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="bc976-128">view</span></span>      | [<span data-ttu-id="bc976-129">Vygeneruje uživatelské rozhraní zobrazení</span><span class="sxs-lookup"><span data-stu-id="bc976-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="bc976-130">Možnosti</span><span class="sxs-lookup"><span data-stu-id="bc976-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="bc976-131">Určuje adresář balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="bc976-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="bc976-132">Definuje konfiguraci sestavení.</span><span class="sxs-lookup"><span data-stu-id="bc976-132">Defines the build configuration.</span></span> <span data-ttu-id="bc976-133">Výchozí hodnota je `Debug`.</span><span class="sxs-lookup"><span data-stu-id="bc976-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="bc976-134">Cíl [Framework](/dotnet/standard/frameworks) používat.</span><span class="sxs-lookup"><span data-stu-id="bc976-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="bc976-135">Například, `net46`.</span><span class="sxs-lookup"><span data-stu-id="bc976-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="bc976-136">Základní cesta sestavení.</span><span class="sxs-lookup"><span data-stu-id="bc976-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="bc976-137">Vytiskne krátký nápovědy pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="bc976-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="bc976-138">Nelze sestavit projekt před provozováním.</span><span class="sxs-lookup"><span data-stu-id="bc976-138">Doesn't build the project before running.</span></span> <span data-ttu-id="bc976-139">Také implicitně nastaví `--no-restore` příznak.</span><span class="sxs-lookup"><span data-stu-id="bc976-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="bc976-140">Určuje cestu k souboru projektu pro spuštění (název složky nebo úplná cesta).</span><span class="sxs-lookup"><span data-stu-id="bc976-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="bc976-141">Pokud není zadán, použije se výchozí do aktuálního adresáře.</span><span class="sxs-lookup"><span data-stu-id="bc976-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="bc976-142">Generátor možnosti</span><span class="sxs-lookup"><span data-stu-id="bc976-142">Generator options</span></span>

<span data-ttu-id="bc976-143">Následující části popisují možnosti dostupné pro podporované generátory:</span><span class="sxs-lookup"><span data-stu-id="bc976-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="bc976-144">Oblast</span><span class="sxs-lookup"><span data-stu-id="bc976-144">Area</span></span>
* <span data-ttu-id="bc976-145">Kontroler</span><span class="sxs-lookup"><span data-stu-id="bc976-145">Controller</span></span>
* <span data-ttu-id="bc976-146">Identita</span><span class="sxs-lookup"><span data-stu-id="bc976-146">Identity</span></span>  
* <span data-ttu-id="bc976-147">razorpage</span><span class="sxs-lookup"><span data-stu-id="bc976-147">Razorpage</span></span>
* <span data-ttu-id="bc976-148">Zobrazit</span><span class="sxs-lookup"><span data-stu-id="bc976-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="bc976-149">Možnosti oblasti</span><span class="sxs-lookup"><span data-stu-id="bc976-149">Area options</span></span>

<span data-ttu-id="bc976-150">Tento nástroj je určen pro webové projekty ASP.NET Core s kontrolerů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bc976-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="bc976-151">Není určena pro aplikace stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="bc976-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="bc976-152">Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="bc976-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="bc976-153">Ve výstupu předchozího příkazu generuje následující složky:</span><span class="sxs-lookup"><span data-stu-id="bc976-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="bc976-154">*Oblasti*</span><span class="sxs-lookup"><span data-stu-id="bc976-154">*Areas*</span></span>
  * <span data-ttu-id="bc976-155">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="bc976-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="bc976-156">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="bc976-156">*Controllers*</span></span>
    * <span data-ttu-id="bc976-157">*Data*</span><span class="sxs-lookup"><span data-stu-id="bc976-157">*Data*</span></span>
    * <span data-ttu-id="bc976-158">*Modely*</span><span class="sxs-lookup"><span data-stu-id="bc976-158">*Models*</span></span>
    * <span data-ttu-id="bc976-159">*Zobrazení*</span><span class="sxs-lookup"><span data-stu-id="bc976-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="bc976-160">Možnosti řadiče</span><span class="sxs-lookup"><span data-stu-id="bc976-160">Controller options</span></span>

<span data-ttu-id="bc976-161">V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` `controller` a `razorpage`:</span><span class="sxs-lookup"><span data-stu-id="bc976-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="bc976-162">V následující tabulce jsou uvedeny možnosti, které jsou jedinečné pro `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="bc976-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="bc976-163">Možnost</span><span class="sxs-lookup"><span data-stu-id="bc976-163">Option</span></span>               | <span data-ttu-id="bc976-164">Popis</span><span class="sxs-lookup"><span data-stu-id="bc976-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="bc976-165">Parametr controllerName – nebo – název</span><span class="sxs-lookup"><span data-stu-id="bc976-165">--controllerName or -name</span></span> | <span data-ttu-id="bc976-166">Název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="bc976-166">Name of the controller.</span></span> |
| <span data-ttu-id="bc976-167">--useAsyncActions nebo - asynchronní</span><span class="sxs-lookup"><span data-stu-id="bc976-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="bc976-168">Generovat asynchronní akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="bc976-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="bc976-169">--noViews nebo -nv</span><span class="sxs-lookup"><span data-stu-id="bc976-169">--noViews or -nv</span></span> | <span data-ttu-id="bc976-170">Generovat **žádné** zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bc976-170">Generate **no** views.</span></span> |
| <span data-ttu-id="bc976-171">--restWithNoViews nebo – rozhraní api</span><span class="sxs-lookup"><span data-stu-id="bc976-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="bc976-172">Generovat Kontroleru stylem REST API.</span><span class="sxs-lookup"><span data-stu-id="bc976-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="bc976-173">`noViews` předpokládá se a zobrazit všechny související možnosti jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="bc976-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="bc976-174">--readWriteActions nebo - akce</span><span class="sxs-lookup"><span data-stu-id="bc976-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="bc976-175">Generovat kontroler s akcemi čtení/zápisu bez modelu.</span><span class="sxs-lookup"><span data-stu-id="bc976-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="bc976-176">Použití `-h` zapnout o pomoc `aspnet-codegenerator controller` příkaz:</span><span class="sxs-lookup"><span data-stu-id="bc976-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```console
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="bc976-177">Zobrazit [generování uživatelského rozhraní modelu film](/aspnet/core/tutorials/razor-pages/model) příklad `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="bc976-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="bc976-178">razorpage</span><span class="sxs-lookup"><span data-stu-id="bc976-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="bc976-179">Stránky Razor můžete jednotlivě automaticky tak, že zadáte název nové stránky a šablonu, kterou chcete použít.</span><span class="sxs-lookup"><span data-stu-id="bc976-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="bc976-180">Jsou podporované šablony:</span><span class="sxs-lookup"><span data-stu-id="bc976-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="bc976-181">Například následující příkaz Upravit šablony používá ke generování *MyEdit.cshtml* a *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="bc976-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="bc976-182">Obvykle, šablony a není zadán název generovaného souboru a jsou vytvořeny následující šablony:</span><span class="sxs-lookup"><span data-stu-id="bc976-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="bc976-183">V následující tabulce jsou uvedeny možnosti `aspnet-codegenerator` `razorpage` a `controller`:</span><span class="sxs-lookup"><span data-stu-id="bc976-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="bc976-184">V následující tabulce jsou uvedeny možnosti, které jsou jedinečné pro `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="bc976-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="bc976-185">Možnost</span><span class="sxs-lookup"><span data-stu-id="bc976-185">Option</span></span>               | <span data-ttu-id="bc976-186">Popis</span><span class="sxs-lookup"><span data-stu-id="bc976-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="bc976-187">--namespaceName nebo – obor názvů</span><span class="sxs-lookup"><span data-stu-id="bc976-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="bc976-188">Název oboru názvů pro generovaný PageModel</span><span class="sxs-lookup"><span data-stu-id="bc976-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="bc976-189">--partialView nebo - partial</span><span class="sxs-lookup"><span data-stu-id="bc976-189">--partialView or -partial</span></span> | <span data-ttu-id="bc976-190">Generovat částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bc976-190">Generate a partial view.</span></span> <span data-ttu-id="bc976-191">Pokud je tento parametr zadán, jsou ignorovány rozložení možnosti -l a - udl.</span><span class="sxs-lookup"><span data-stu-id="bc976-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="bc976-192">--noPageModel nebo – npm</span><span class="sxs-lookup"><span data-stu-id="bc976-192">--noPageModel or -npm</span></span> | <span data-ttu-id="bc976-193">Přepínač nelze vygenerovat třídu PageModel pro prázdnou šablonu</span><span class="sxs-lookup"><span data-stu-id="bc976-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="bc976-194">Použití `-h` zapnout o pomoc `aspnet-codegenerator razorpage` příkaz:</span><span class="sxs-lookup"><span data-stu-id="bc976-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="bc976-195">Zobrazit [generování uživatelského rozhraní modelu film](/aspnet/core/tutorials/razor-pages/model) příklad `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="bc976-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="bc976-196">Identita</span><span class="sxs-lookup"><span data-stu-id="bc976-196">Identity</span></span>

<span data-ttu-id="bc976-197">Zobrazit [generování uživatelského rozhraní Identity](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="bc976-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
