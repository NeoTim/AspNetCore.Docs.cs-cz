---
title: Svazek a minfika statických aktiv v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve webové aplikaci ASP.NET Core použitím technik sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658268"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="8a694-103">Svazek a minfika statických aktiv v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a694-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="8a694-104">[Scott Addie](https://twitter.com/Scott_Addie) a [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="8a694-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="8a694-105">Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak lze tyto funkce používat s webovými aplikacemi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a694-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="8a694-106">Co je svazování a minifikace</span><span class="sxs-lookup"><span data-stu-id="8a694-106">What is bundling and minification</span></span>

<span data-ttu-id="8a694-107">Sdružování a minifikace jsou dvě odlišné optimalizace výkonu, které můžete použít ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a694-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="8a694-108">Při společném sdružování a minifikaci se zvyšuje výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických datových zdrojů.</span><span class="sxs-lookup"><span data-stu-id="8a694-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="8a694-109">Sdružování a minifikace především zlepšují dobu načítání požadavku na první stránku.</span><span class="sxs-lookup"><span data-stu-id="8a694-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="8a694-110">Jakmile je webová stránka požadována, prohlížeč uloží statické datové zdroje do mezipaměti (JavaScript, CSS a obrázky).</span><span class="sxs-lookup"><span data-stu-id="8a694-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="8a694-111">V důsledku toho svazování a minifikace nezlepšují výkon při žádosti o stejnou stránku nebo stránky na stejném webu požadujícím stejné prostředky.</span><span class="sxs-lookup"><span data-stu-id="8a694-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="8a694-112">Pokud není záhlaví vypršení platnosti správně nastaveno na datových zdrojích a pokud se nepoužívá svazování a minifikace, heuristika čerstvosti prohlížeče označuje datové zdroje zastaralé po několika dnech.</span><span class="sxs-lookup"><span data-stu-id="8a694-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="8a694-113">Kromě toho prohlížeč vyžaduje žádost o ověření pro každý prostředek.</span><span class="sxs-lookup"><span data-stu-id="8a694-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="8a694-114">V tomto případě sdružování a minifikace poskytují zlepšení výkonu i po první stránce požadavku.</span><span class="sxs-lookup"><span data-stu-id="8a694-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="8a694-115">Svazování</span><span class="sxs-lookup"><span data-stu-id="8a694-115">Bundling</span></span>

<span data-ttu-id="8a694-116">Sdružování kombinuje několik souborů do jediného souboru.</span><span class="sxs-lookup"><span data-stu-id="8a694-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="8a694-117">Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslení webového datového zdroje, například webové stránky.</span><span class="sxs-lookup"><span data-stu-id="8a694-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="8a694-118">Můžete vytvořit libovolný počet jednotlivých svazků speciálně pro CSS, JavaScript, atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby poskytující vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a694-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="8a694-119">To má za následek lepší výkon načítání první stránky.</span><span class="sxs-lookup"><span data-stu-id="8a694-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="8a694-120">Minification</span><span class="sxs-lookup"><span data-stu-id="8a694-120">Minification</span></span>

<span data-ttu-id="8a694-121">Minifikace odebere nepotřebné znaky z kódu bez změny funkčnosti.</span><span class="sxs-lookup"><span data-stu-id="8a694-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="8a694-122">Výsledkem je významné zmenšení velikosti požadovaných datových zdrojů (například CSS, obrázků a souborů JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="8a694-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="8a694-123">Časté vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a zbytečných mezer.</span><span class="sxs-lookup"><span data-stu-id="8a694-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="8a694-124">Zvažte následující funkci JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="8a694-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="8a694-125">Minifikace snižuje funkci na následující:</span><span class="sxs-lookup"><span data-stu-id="8a694-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="8a694-126">Kromě odebrání komentáře a zbytečné mezery, následující parametr a názvy proměnných byly přejmenovány takto:</span><span class="sxs-lookup"><span data-stu-id="8a694-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="8a694-127">Původní</span><span class="sxs-lookup"><span data-stu-id="8a694-127">Original</span></span> | <span data-ttu-id="8a694-128">Přejmenován</span><span class="sxs-lookup"><span data-stu-id="8a694-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="8a694-129">Dopad spojeného prodeje a minifikace</span><span class="sxs-lookup"><span data-stu-id="8a694-129">Impact of bundling and minification</span></span>

<span data-ttu-id="8a694-130">V následující tabulce jsou uvedeny rozdíly mezi individuálním zatížením aktiv a použitím spojených prodejů a minifikace:</span><span class="sxs-lookup"><span data-stu-id="8a694-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="8a694-131">Akce</span><span class="sxs-lookup"><span data-stu-id="8a694-131">Action</span></span> | <span data-ttu-id="8a694-132">S B/M</span><span class="sxs-lookup"><span data-stu-id="8a694-132">With B/M</span></span> | <span data-ttu-id="8a694-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="8a694-133">Without B/M</span></span> | <span data-ttu-id="8a694-134">Změnit</span><span class="sxs-lookup"><span data-stu-id="8a694-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="8a694-135">Žádosti o soubor</span><span class="sxs-lookup"><span data-stu-id="8a694-135">File Requests</span></span>  | <span data-ttu-id="8a694-136">7</span><span class="sxs-lookup"><span data-stu-id="8a694-136">7</span></span>   | <span data-ttu-id="8a694-137">18</span><span class="sxs-lookup"><span data-stu-id="8a694-137">18</span></span>     | <span data-ttu-id="8a694-138">157%</span><span class="sxs-lookup"><span data-stu-id="8a694-138">157%</span></span>
<span data-ttu-id="8a694-139">KB Převedeno</span><span class="sxs-lookup"><span data-stu-id="8a694-139">KB Transferred</span></span> | <span data-ttu-id="8a694-140">156</span><span class="sxs-lookup"><span data-stu-id="8a694-140">156</span></span> | <span data-ttu-id="8a694-141">264.68</span><span class="sxs-lookup"><span data-stu-id="8a694-141">264.68</span></span> | <span data-ttu-id="8a694-142">70 %</span><span class="sxs-lookup"><span data-stu-id="8a694-142">70%</span></span>
<span data-ttu-id="8a694-143">Doba načítání (ms)</span><span class="sxs-lookup"><span data-stu-id="8a694-143">Load Time (ms)</span></span> | <span data-ttu-id="8a694-144">885</span><span class="sxs-lookup"><span data-stu-id="8a694-144">885</span></span> | <span data-ttu-id="8a694-145">2360</span><span class="sxs-lookup"><span data-stu-id="8a694-145">2360</span></span>   | <span data-ttu-id="8a694-146">167%</span><span class="sxs-lookup"><span data-stu-id="8a694-146">167%</span></span>

<span data-ttu-id="8a694-147">Prohlížeče jsou poměrně podrobné s ohledem na hlavičky požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="8a694-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="8a694-148">Celkový počet bajtů odeslaných metrika viděl významné snížení při sdružování.</span><span class="sxs-lookup"><span data-stu-id="8a694-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="8a694-149">Doba načítání ukazuje významné zlepšení, ale tento příklad běžel místně.</span><span class="sxs-lookup"><span data-stu-id="8a694-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="8a694-150">Vyšší nárůst y výkonu se realizuje při použití spojených prodejů a minifikace s aktivy převedenými přes síť.</span><span class="sxs-lookup"><span data-stu-id="8a694-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="8a694-151">Vyberte si strategii sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="8a694-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="8a694-152">Šablony projektu MVC a Razor Pages poskytují předem připravenou řešení pro sdružování a minifikaci skládající se z konfiguračního souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="8a694-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="8a694-153">Nástroje třetích stran, jako je například [grunt](xref:client-side/using-grunt) úkol běžec, provádět stejné úkoly s trochu složitější.</span><span class="sxs-lookup"><span data-stu-id="8a694-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="8a694-154">Nástroj jiného výrobce je skvělý, když váš vývojový pracovní postup&mdash;vyžaduje zpracování nad rámec sdružování a minifikace, jako je linting a optimalizace obrazu.</span><span class="sxs-lookup"><span data-stu-id="8a694-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="8a694-155">Pomocí sdružování a minifikace v době návrhu se minified soubory vytvoří před nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a694-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="8a694-156">Sdružování a minifying před nasazením poskytuje výhodu snížení zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="8a694-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="8a694-157">Je však důležité si uvědomit, že sdružování a minifikace v době návrhu zvyšuje složitost sestavení a funguje pouze se statickými soubory.</span><span class="sxs-lookup"><span data-stu-id="8a694-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="8a694-158">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="8a694-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8a694-159">V ASP.NET Core 2.0 nebo starší, šablony projektu MVC a Razor Pages poskytují konfigurační soubor *bundleconfig.json,* který definuje možnosti pro každou sadu:</span><span class="sxs-lookup"><span data-stu-id="8a694-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8a694-160">V ASP.NET jádrem 2.1 nebo novějším přidejte do kořenového adresáře projektu MVC nebo Razor Pages nový soubor JSON s názvem *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="8a694-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="8a694-161">Jako výchozí bod zahrňte do tohoto souboru následující json:</span><span class="sxs-lookup"><span data-stu-id="8a694-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="8a694-162">Soubor *bundleconfig.json* definuje možnosti pro každou sadu.</span><span class="sxs-lookup"><span data-stu-id="8a694-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="8a694-163">V předchozím příkladu je definována konfigurace jednoho svazku pro vlastní soubory JavaScriptu (*wwwroot/js/site.js*) a stylů *(wwwroot/css/site.css).*</span><span class="sxs-lookup"><span data-stu-id="8a694-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="8a694-164">Možnosti konfigurace zahrnují:</span><span class="sxs-lookup"><span data-stu-id="8a694-164">Configuration options include:</span></span>

* <span data-ttu-id="8a694-165">`outputFileName`: Název souboru svazku pro výstup.</span><span class="sxs-lookup"><span data-stu-id="8a694-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="8a694-166">Může obsahovat relativní cestu ze souboru *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="8a694-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="8a694-167">**Požadované**</span><span class="sxs-lookup"><span data-stu-id="8a694-167">**required**</span></span>
* <span data-ttu-id="8a694-168">`inputFiles`: Pole souborů, které chcete spojit dohromady.</span><span class="sxs-lookup"><span data-stu-id="8a694-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="8a694-169">Jedná se o relativní cesty ke konfiguračnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="8a694-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="8a694-170">**volitelné**, \*prázdná hodnota má za následek prázdný výstupní soubor.</span><span class="sxs-lookup"><span data-stu-id="8a694-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="8a694-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) vzory jsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="8a694-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="8a694-172">`minify`: Možnosti minifikace pro typ výstupu.</span><span class="sxs-lookup"><span data-stu-id="8a694-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="8a694-173">**volitelné**, \*výchozí - `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="8a694-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="8a694-174">Možnosti konfigurace jsou k dispozici pro typ výstupního souboru.</span><span class="sxs-lookup"><span data-stu-id="8a694-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="8a694-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="8a694-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="8a694-176">Minipěč JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="8a694-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="8a694-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="8a694-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="8a694-178">`includeInProject`: Příznak označující, zda mají být do souboru projektu přidávány generované soubory.</span><span class="sxs-lookup"><span data-stu-id="8a694-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="8a694-179">**volitelné**, *výchozí - false*</span><span class="sxs-lookup"><span data-stu-id="8a694-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="8a694-180">`sourceMap`: Příznak označující, zda chcete generovat zdrojovou mapu pro přibalený soubor.</span><span class="sxs-lookup"><span data-stu-id="8a694-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="8a694-181">**volitelné**, *výchozí - false*</span><span class="sxs-lookup"><span data-stu-id="8a694-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="8a694-182">`sourceMapRootPath`: Kořenová cesta pro uložení generovaného zdrojového mapového souboru.</span><span class="sxs-lookup"><span data-stu-id="8a694-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="8a694-183">Build-time provedení svazování a minifikace</span><span class="sxs-lookup"><span data-stu-id="8a694-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="8a694-184">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet balíček umožňuje provádění sdružování a minification v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="8a694-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="8a694-185">Balíček vloží [Cíle MSBuild,](/visualstudio/msbuild/msbuild-targets) které běží v době sestavení a čistého.</span><span class="sxs-lookup"><span data-stu-id="8a694-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="8a694-186">Soubor *bundleconfig.json* je analyzován procesem sestavení za účelem vytvoření výstupních souborů na základě definované konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8a694-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="8a694-187">BuildBundlerMinifier patří do komunitního projektu na GitHubu, pro který Microsoft neposkytuje žádnou podporu.</span><span class="sxs-lookup"><span data-stu-id="8a694-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="8a694-188">Otázky by měly být podány [zde](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="8a694-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8a694-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8a694-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8a694-190">Přidejte balíček *BuildBundlerMinifier* do projektu.</span><span class="sxs-lookup"><span data-stu-id="8a694-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="8a694-191">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="8a694-191">Build the project.</span></span> <span data-ttu-id="8a694-192">V okně Výstup se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="8a694-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="8a694-193">Vyčistěte projekt.</span><span class="sxs-lookup"><span data-stu-id="8a694-193">Clean the project.</span></span> <span data-ttu-id="8a694-194">V okně Výstup se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="8a694-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[<span data-ttu-id="8a694-195">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8a694-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8a694-196">Přidejte balíček *BuildBundlerMinifier* do projektu:</span><span class="sxs-lookup"><span data-stu-id="8a694-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="8a694-197">Pokud používáte ASP.NET Core 1.x, obnovte nově přidaný balíček:</span><span class="sxs-lookup"><span data-stu-id="8a694-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="8a694-198">Sestavení projektu:</span><span class="sxs-lookup"><span data-stu-id="8a694-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="8a694-199">Zobrazí se následující:</span><span class="sxs-lookup"><span data-stu-id="8a694-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="8a694-200">Vyčistěte projekt:</span><span class="sxs-lookup"><span data-stu-id="8a694-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="8a694-201">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="8a694-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="8a694-202">Ad hoc provedení spojeného prodeje a minifikace</span><span class="sxs-lookup"><span data-stu-id="8a694-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="8a694-203">Je možné spustit spojené úlohy a minifikaci na základě ad hoc, bez budování projektu.</span><span class="sxs-lookup"><span data-stu-id="8a694-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="8a694-204">Přidejte balíček [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet do projektu:</span><span class="sxs-lookup"><span data-stu-id="8a694-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="8a694-205">BundlerMinifier.Core patří do komunitního projektu na GitHubu, pro který Microsoft neposkytuje žádnou podporu.</span><span class="sxs-lookup"><span data-stu-id="8a694-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="8a694-206">Otázky by měly být podány [zde](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="8a694-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="8a694-207">Tento balíček rozšiřuje rozhraní .NET Core CLI tak, aby zahrnovalo nástroj *dotnet-bundle.*</span><span class="sxs-lookup"><span data-stu-id="8a694-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="8a694-208">Následující příkaz lze provést v okně Konzoly správce balíčků (PMC) nebo v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="8a694-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="8a694-209">NuGet Package Manager přidá závislosti do souboru `<PackageReference />` \*.csproj jako uzly.</span><span class="sxs-lookup"><span data-stu-id="8a694-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="8a694-210">Příkaz `dotnet bundle` je registrován s rozhraním příkazového `<DotNetCliToolReference />` příkazu .NET Core pouze v případě, že je použit uzel.</span><span class="sxs-lookup"><span data-stu-id="8a694-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="8a694-211">Odpovídajícím způsobem upravte soubor \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="8a694-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="8a694-212">Přidání souborů do pracovního postupu</span><span class="sxs-lookup"><span data-stu-id="8a694-212">Add files to workflow</span></span>

<span data-ttu-id="8a694-213">Vezměme si příklad, ve kterém je přidán další soubor *custom.css* připomínající následující:</span><span class="sxs-lookup"><span data-stu-id="8a694-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="8a694-214">Chcete-li minify *custom.css* a složka s *site.css* do souboru *site.min.css,* přidejte relativní cestu *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="8a694-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="8a694-215">Alternativně by mohl být použit následující globbing vzor:</span><span class="sxs-lookup"><span data-stu-id="8a694-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="8a694-216">Tento globbing vzor odpovídá všem souborům CSS a vylučuje minified soubor vzor.</span><span class="sxs-lookup"><span data-stu-id="8a694-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="8a694-217">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8a694-217">Build the application.</span></span> <span data-ttu-id="8a694-218">Otevřete *soubor site.min.css* a všimněte si, že obsah *souboru custom.css* je připojen na konec souboru.</span><span class="sxs-lookup"><span data-stu-id="8a694-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="8a694-219">Spojené spoje a minifikace založené na životním prostředí</span><span class="sxs-lookup"><span data-stu-id="8a694-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="8a694-220">Jako osvědčený postup by měly být přibalené a minifikované soubory vaší aplikace používány v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8a694-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="8a694-221">Během vývoje původní soubory usnadňují ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a694-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="8a694-222">Určete, které soubory chcete zahrnout na stránky pomocí [pomocníka pro označení prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="8a694-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="8a694-223">Pomocník značky prostředí vykreslí jeho obsah pouze při spuštění v [určitých prostředích](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="8a694-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="8a694-224">Následující `environment` značka vykreslí nezpracované soubory CSS `Development` při spuštění v prostředí:</span><span class="sxs-lookup"><span data-stu-id="8a694-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="8a694-225">Následující `environment` značka vykreslí přibalené a minified CSS soubory `Development`při spuštění v jiném prostředí než .</span><span class="sxs-lookup"><span data-stu-id="8a694-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="8a694-226">Například spuštění `Production` v `Staging` nebo aktivuje vykreslování těchto stylů:</span><span class="sxs-lookup"><span data-stu-id="8a694-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="8a694-227">Spotřebovávat bundleconfig.json od Gulp</span><span class="sxs-lookup"><span data-stu-id="8a694-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="8a694-228">Existují případy, kdy pracovní postup sdružování a minifikace aplikace vyžaduje další zpracování.</span><span class="sxs-lookup"><span data-stu-id="8a694-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="8a694-229">Mezi příklady patří optimalizace obrazu, ukládání do mezipaměti a zpracování datových zdrojů CDN.</span><span class="sxs-lookup"><span data-stu-id="8a694-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="8a694-230">Chcete-li splnit tyto požadavky, můžete převést svazování a minification pracovní postup použít Gulp.</span><span class="sxs-lookup"><span data-stu-id="8a694-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="8a694-231">Použití rozšíření Bundler & Minifier</span><span class="sxs-lookup"><span data-stu-id="8a694-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="8a694-232">Rozšíření sady Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) zpracovává převod na Gulp.</span><span class="sxs-lookup"><span data-stu-id="8a694-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="8a694-233">Rozšíření Bundler & Minifier patří do projektu řízeného komunitou na GitHubu, pro který Microsoft neposkytuje žádnou podporu.</span><span class="sxs-lookup"><span data-stu-id="8a694-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="8a694-234">Otázky by měly být podány [zde](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="8a694-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="8a694-235">Klepněte pravým tlačítkem myši na soubor *bundleconfig.json* v Průzkumníku řešení a vyberte **možnost Bundler & Minifier** > **Převést na doušek...**:</span><span class="sxs-lookup"><span data-stu-id="8a694-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Převést na položku kontextové nabídky do hlinění](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="8a694-237">Do projektu jsou přidány soubory *gulpfile.js* a *package.json.*</span><span class="sxs-lookup"><span data-stu-id="8a694-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="8a694-238">Podpůrné balíčky [npm](https://www.npmjs.com/) uvedené v `devDependencies` části souboru *package.json jsou nainstalovány.*</span><span class="sxs-lookup"><span data-stu-id="8a694-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="8a694-239">Spusťte následující příkaz v okně PMC a nainstalujte cli gulp Gulp jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="8a694-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="8a694-240">Soubor *gulpfile.js* přečte soubor *bundleconfig.json* pro vstupy, výstupy a nastavení.</span><span class="sxs-lookup"><span data-stu-id="8a694-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="8a694-241">Ruční převod</span><span class="sxs-lookup"><span data-stu-id="8a694-241">Convert manually</span></span>

<span data-ttu-id="8a694-242">Pokud Visual Studio nebo Bundler & Minifier rozšíření nejsou k dispozici, převést ručně.</span><span class="sxs-lookup"><span data-stu-id="8a694-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="8a694-243">Přidejte soubor *package.json* s `devDependencies`následujícím textem do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="8a694-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="8a694-244">Modul `gulp-uglify` nepodporuje ECMAScript (ES) 2015 / ES6 a novější.</span><span class="sxs-lookup"><span data-stu-id="8a694-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="8a694-245">Nainstalujte [gulp-terser](https://www.npmjs.com/package/gulp-terser) místo `gulp-uglify` použití ES2015 / ES6 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="8a694-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="8a694-246">Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.json*:</span><span class="sxs-lookup"><span data-stu-id="8a694-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="8a694-247">Nainstalujte cli Gulp jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="8a694-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="8a694-248">Zkopírujte níže uvedený soubor *gulpfile.js* do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="8a694-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="8a694-249">Spuštění úloh Gulp</span><span class="sxs-lookup"><span data-stu-id="8a694-249">Run Gulp tasks</span></span>

<span data-ttu-id="8a694-250">Chcete-li spustit úlohu minifikace gulpu před sestavením projektu v sadě Visual Studio, přidejte do souboru \*.csproj následující [cíl MSBuild](/visualstudio/msbuild/msbuild-targets) Target:</span><span class="sxs-lookup"><span data-stu-id="8a694-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="8a694-251">V tomto příkladu všechny `MyPreCompileTarget` úkoly definované v `Build` rámci cíle spustit před předdefinovaný cíl.</span><span class="sxs-lookup"><span data-stu-id="8a694-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="8a694-252">Výstup podobný následujícímu se zobrazí v okně Výstup sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="8a694-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="8a694-253">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8a694-253">Additional resources</span></span>

* [<span data-ttu-id="8a694-254">Použití nástroje Grunt</span><span class="sxs-lookup"><span data-stu-id="8a694-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="8a694-255">Používání více prostředí</span><span class="sxs-lookup"><span data-stu-id="8a694-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="8a694-256">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="8a694-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
