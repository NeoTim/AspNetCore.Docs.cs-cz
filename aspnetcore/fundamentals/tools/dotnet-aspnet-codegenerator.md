---
title: dotnet aspnet-codegenerator, příkaz
author: rick-anderson
description: Dotnet aspnet-codegenerator příkaz oválašiny ASP.NET základní projekty.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665184"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="cb391-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cb391-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="cb391-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb391-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb391-105">`dotnet aspnet-codegenerator`- Provozuje ASP.NET lešení Core motoru.</span><span class="sxs-lookup"><span data-stu-id="cb391-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="cb391-106">`dotnet aspnet-codegenerator`je vyžadováno pouze pro zaslístvení z příkazového řádku, není nutné používat přisycování uživatelského přisporu.</span><span class="sxs-lookup"><span data-stu-id="cb391-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="cb391-107">Tento článek se vztahuje na [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) a novější.</span><span class="sxs-lookup"><span data-stu-id="cb391-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="cb391-108">Instalace generátoru kódů aspnet</span><span class="sxs-lookup"><span data-stu-id="cb391-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="cb391-109">`dotnet-aspnet-codegenerator`je [globální nástroj,](/dotnet/core/tools/global-tools) který musí být nainstalován.</span><span class="sxs-lookup"><span data-stu-id="cb391-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="cb391-110">Následující příkaz nainstaluje nejnovější stabilní `dotnet-aspnet-codegenerator` verzi nástroje:</span><span class="sxs-lookup"><span data-stu-id="cb391-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="cb391-111">Následující příkaz `dotnet-aspnet-codegenerator` se aktualizuje na nejnovější stabilní verzi dostupnou z nainstalovaných sad SDK jádra .NET:</span><span class="sxs-lookup"><span data-stu-id="cb391-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="cb391-112">Synopse</span><span class="sxs-lookup"><span data-stu-id="cb391-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="cb391-113">Popis</span><span class="sxs-lookup"><span data-stu-id="cb391-113">Description</span></span>

<span data-ttu-id="cb391-114">Globální `dotnet aspnet-codegenerator` příkaz spouští ASP.NET generátor kódu Core a modul lešení.</span><span class="sxs-lookup"><span data-stu-id="cb391-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="cb391-115">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb391-115">Arguments</span></span>

`generator`

<span data-ttu-id="cb391-116">Generátor kódu ke spuštění.</span><span class="sxs-lookup"><span data-stu-id="cb391-116">The code generator to run.</span></span> <span data-ttu-id="cb391-117">K dispozici jsou následující generátory:</span><span class="sxs-lookup"><span data-stu-id="cb391-117">The following generators are available:</span></span>

| <span data-ttu-id="cb391-118">Generátor</span><span class="sxs-lookup"><span data-stu-id="cb391-118">Generator</span></span> | <span data-ttu-id="cb391-119">Operace</span><span class="sxs-lookup"><span data-stu-id="cb391-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="cb391-120">oblast</span><span class="sxs-lookup"><span data-stu-id="cb391-120">area</span></span>      | [<span data-ttu-id="cb391-121">Lešení a oblast</span><span class="sxs-lookup"><span data-stu-id="cb391-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="cb391-122">kontroler</span><span class="sxs-lookup"><span data-stu-id="cb391-122">controller</span></span>| [<span data-ttu-id="cb391-123">Lešení regulátor</span><span class="sxs-lookup"><span data-stu-id="cb391-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="cb391-124">identity</span><span class="sxs-lookup"><span data-stu-id="cb391-124">identity</span></span>  | [<span data-ttu-id="cb391-125">Identita lešení</span><span class="sxs-lookup"><span data-stu-id="cb391-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="cb391-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="cb391-126">razorpage</span></span> | [<span data-ttu-id="cb391-127">Lešení Žiletky Stránky</span><span class="sxs-lookup"><span data-stu-id="cb391-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="cb391-128">Prohlédni</span><span class="sxs-lookup"><span data-stu-id="cb391-128">view</span></span>      | [<span data-ttu-id="cb391-129">Zobrazení ve lešení</span><span class="sxs-lookup"><span data-stu-id="cb391-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="cb391-130">Možnosti</span><span class="sxs-lookup"><span data-stu-id="cb391-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="cb391-131">Určuje adresář balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="cb391-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="cb391-132">Definuje konfiguraci sestavení.</span><span class="sxs-lookup"><span data-stu-id="cb391-132">Defines the build configuration.</span></span> <span data-ttu-id="cb391-133">Výchozí hodnota je `Debug`.</span><span class="sxs-lookup"><span data-stu-id="cb391-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="cb391-134">Cílové [rozhraní](/dotnet/standard/frameworks) použít.</span><span class="sxs-lookup"><span data-stu-id="cb391-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="cb391-135">Například, `net46`.</span><span class="sxs-lookup"><span data-stu-id="cb391-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="cb391-136">Základní cesta sestavení.</span><span class="sxs-lookup"><span data-stu-id="cb391-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="cb391-137">Vytiskne krátkou nápovědu pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="cb391-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="cb391-138">Nesestavuje projekt před spuštěním.</span><span class="sxs-lookup"><span data-stu-id="cb391-138">Doesn't build the project before running.</span></span> <span data-ttu-id="cb391-139">Také implicitně nastaví příznak. `--no-restore`</span><span class="sxs-lookup"><span data-stu-id="cb391-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="cb391-140">Určuje cestu ke spuštění souboru projektu (název složky nebo úplná cesta).</span><span class="sxs-lookup"><span data-stu-id="cb391-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="cb391-141">Pokud není zadán, je výchozí aktuální adresář.</span><span class="sxs-lookup"><span data-stu-id="cb391-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="cb391-142">Možnosti generátoru</span><span class="sxs-lookup"><span data-stu-id="cb391-142">Generator options</span></span>

<span data-ttu-id="cb391-143">V následujících částech jsou podrobně popsány možnosti, které jsou k dispozici pro podporované generátory:</span><span class="sxs-lookup"><span data-stu-id="cb391-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="cb391-144">Oblast</span><span class="sxs-lookup"><span data-stu-id="cb391-144">Area</span></span>
* <span data-ttu-id="cb391-145">Řadič</span><span class="sxs-lookup"><span data-stu-id="cb391-145">Controller</span></span>
* <span data-ttu-id="cb391-146">Identita</span><span class="sxs-lookup"><span data-stu-id="cb391-146">Identity</span></span>  
* <span data-ttu-id="cb391-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="cb391-147">Razorpage</span></span>
* <span data-ttu-id="cb391-148">Zobrazit</span><span class="sxs-lookup"><span data-stu-id="cb391-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="cb391-149">Možnosti oblasti</span><span class="sxs-lookup"><span data-stu-id="cb391-149">Area options</span></span>

<span data-ttu-id="cb391-150">Tento nástroj je určen pro ASP.NET základní webové projekty s řadiči a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="cb391-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="cb391-151">Není určen pro aplikace Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cb391-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="cb391-152">Použití: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="cb391-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="cb391-153">Předchozí příkaz generuje následující složky:</span><span class="sxs-lookup"><span data-stu-id="cb391-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="cb391-154">*Oblasti*</span><span class="sxs-lookup"><span data-stu-id="cb391-154">*Areas*</span></span>
  * <span data-ttu-id="cb391-155">*Název_oblastiTogenerovat*</span><span class="sxs-lookup"><span data-stu-id="cb391-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="cb391-156">*Kontrolery*</span><span class="sxs-lookup"><span data-stu-id="cb391-156">*Controllers*</span></span>
    * <span data-ttu-id="cb391-157">*Data*</span><span class="sxs-lookup"><span data-stu-id="cb391-157">*Data*</span></span>
    * <span data-ttu-id="cb391-158">*Modely*</span><span class="sxs-lookup"><span data-stu-id="cb391-158">*Models*</span></span>
    * <span data-ttu-id="cb391-159">*Zobrazení*</span><span class="sxs-lookup"><span data-stu-id="cb391-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="cb391-160">Možnosti řadiče</span><span class="sxs-lookup"><span data-stu-id="cb391-160">Controller options</span></span>

<span data-ttu-id="cb391-161">V následující tabulce `aspnet-codegenerator` `controller` jsou `razorpage`uvedeny možnosti pro a :</span><span class="sxs-lookup"><span data-stu-id="cb391-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="cb391-162">V následující tabulce jsou `aspnet-codegenerator controller`uvedeny možnosti jedinečné :</span><span class="sxs-lookup"><span data-stu-id="cb391-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="cb391-163">Možnost</span><span class="sxs-lookup"><span data-stu-id="cb391-163">Option</span></span>               | <span data-ttu-id="cb391-164">Popis</span><span class="sxs-lookup"><span data-stu-id="cb391-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="cb391-165">--controllerName nebo -name</span><span class="sxs-lookup"><span data-stu-id="cb391-165">--controllerName or -name</span></span> | <span data-ttu-id="cb391-166">Název řadiče.</span><span class="sxs-lookup"><span data-stu-id="cb391-166">Name of the controller.</span></span> |
| <span data-ttu-id="cb391-167">--useAsyncActions nebo -async</span><span class="sxs-lookup"><span data-stu-id="cb391-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="cb391-168">Generovat akce asynchronního řadiče.</span><span class="sxs-lookup"><span data-stu-id="cb391-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="cb391-169">--noViews nebo -nv</span><span class="sxs-lookup"><span data-stu-id="cb391-169">--noViews or -nv</span></span> | <span data-ttu-id="cb391-170">Generovat **žádná** zobrazení.</span><span class="sxs-lookup"><span data-stu-id="cb391-170">Generate **no** views.</span></span> |
| <span data-ttu-id="cb391-171">--restWithNoViews nebo -api</span><span class="sxs-lookup"><span data-stu-id="cb391-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="cb391-172">Vygenerujte řadič s rozhraním API stylu REST.</span><span class="sxs-lookup"><span data-stu-id="cb391-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="cb391-173">`noViews`a všechny možnosti související s zobrazením jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="cb391-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="cb391-174">--readWriteActions nebo -actions</span><span class="sxs-lookup"><span data-stu-id="cb391-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="cb391-175">Generovat řadič s akcemi pro čtení a zápis bez modelu.</span><span class="sxs-lookup"><span data-stu-id="cb391-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="cb391-176">Pomocí `-h` přepínače pro `aspnet-codegenerator controller` pomoc na příkaz:</span><span class="sxs-lookup"><span data-stu-id="cb391-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="cb391-177">Příklad obrázku najdete [v tématu Kreaffold filmového](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator controller`modelu .</span><span class="sxs-lookup"><span data-stu-id="cb391-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="cb391-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="cb391-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="cb391-179">Razor Pages lze jednotlivě šetřený kód zadáním názvu nové stránky a šablony, která má být používána.</span><span class="sxs-lookup"><span data-stu-id="cb391-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="cb391-180">Podporované šablony jsou:</span><span class="sxs-lookup"><span data-stu-id="cb391-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="cb391-181">Například následující příkaz používá šablonu Edit ke generování *myedit.cshtml* a *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="cb391-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="cb391-182">Šablona a název generovaného souboru obvykle nejsou zadány a jsou vytvořeny následující šablony:</span><span class="sxs-lookup"><span data-stu-id="cb391-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="cb391-183">V následující tabulce `aspnet-codegenerator` `razorpage` jsou `controller`uvedeny možnosti pro a :</span><span class="sxs-lookup"><span data-stu-id="cb391-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="cb391-184">V následující tabulce jsou `aspnet-codegenerator razorpage`uvedeny možnosti jedinečné :</span><span class="sxs-lookup"><span data-stu-id="cb391-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="cb391-185">Možnost</span><span class="sxs-lookup"><span data-stu-id="cb391-185">Option</span></span>               | <span data-ttu-id="cb391-186">Popis</span><span class="sxs-lookup"><span data-stu-id="cb391-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="cb391-187">--namespaceName nebo -namespace</span><span class="sxs-lookup"><span data-stu-id="cb391-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="cb391-188">Název oboru názvů, který se má použít pro generovaný model_</span><span class="sxs-lookup"><span data-stu-id="cb391-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="cb391-189">--partialView nebo -partial</span><span class="sxs-lookup"><span data-stu-id="cb391-189">--partialView or -partial</span></span> | <span data-ttu-id="cb391-190">Vygenerujte částečné zobrazení.</span><span class="sxs-lookup"><span data-stu-id="cb391-190">Generate a partial view.</span></span> <span data-ttu-id="cb391-191">Možnosti rozložení -l a -udl jsou ignorovány, pokud je zadán.</span><span class="sxs-lookup"><span data-stu-id="cb391-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="cb391-192">--noPageModel nebo -npm</span><span class="sxs-lookup"><span data-stu-id="cb391-192">--noPageModel or -npm</span></span> | <span data-ttu-id="cb391-193">Přepnout na negenerování třídy PageModel pro prázdnou šablonu</span><span class="sxs-lookup"><span data-stu-id="cb391-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="cb391-194">Pomocí `-h` přepínače pro `aspnet-codegenerator razorpage` pomoc na příkaz:</span><span class="sxs-lookup"><span data-stu-id="cb391-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="cb391-195">Příklad obrázku najdete [v tématu Kreaffold filmového](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage`modelu .</span><span class="sxs-lookup"><span data-stu-id="cb391-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="cb391-196">Identita</span><span class="sxs-lookup"><span data-stu-id="cb391-196">Identity</span></span>

<span data-ttu-id="cb391-197">Viz [Identita lešení](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="cb391-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
