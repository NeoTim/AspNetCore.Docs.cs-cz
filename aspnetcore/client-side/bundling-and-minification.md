---
title: Statické prostředky sady prostředků a minimalizuje v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve ASP.NET Core webové aplikaci, a to pomocí metod sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803276"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="0fae5-103">Statické prostředky sady prostředků a minimalizuje v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0fae5-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="0fae5-104">Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [David borovice](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="0fae5-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="0fae5-105">Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak se tyto funkce dají používat s ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="0fae5-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="0fae5-106">Co je sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="0fae5-106">What is bundling and minification</span></span>

<span data-ttu-id="0fae5-107">Sdružování a minifikace jsou dvě odlišná optimalizace výkonu, které můžete použít ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0fae5-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="0fae5-108">Společně, sdružování a minifikace vylepšit výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických prostředků.</span><span class="sxs-lookup"><span data-stu-id="0fae5-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="0fae5-109">Seřízení a minifikace primárně zlepšují dobu načtení první stránky.</span><span class="sxs-lookup"><span data-stu-id="0fae5-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="0fae5-110">Po vyžádání webové stránky prohlížeč ukládá do mezipaměti statické prostředky (JavaScript, CSS a image).</span><span class="sxs-lookup"><span data-stu-id="0fae5-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="0fae5-111">V důsledku toho sdružování a minifikace nezlepšují výkon při požadavku na stejnou stránku nebo stránky ve stejné lokalitě, která vyžaduje stejné prostředky.</span><span class="sxs-lookup"><span data-stu-id="0fae5-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="0fae5-112">Pokud není hlavička Expires v prostředcích správně nastavená a pokud se nepoužívá sdružování a minifikace, budou heuristické aktuálnosti v prohlížeči označovat prostředky zastaralé po několika dnech.</span><span class="sxs-lookup"><span data-stu-id="0fae5-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="0fae5-113">Kromě toho prohlížeč vyžaduje žádost o ověření pro každý Asset.</span><span class="sxs-lookup"><span data-stu-id="0fae5-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="0fae5-114">V tomto případě přináší sdružování a minifikace vylepšení výkonu i po první žádosti stránky.</span><span class="sxs-lookup"><span data-stu-id="0fae5-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="0fae5-115">Sdružování</span><span class="sxs-lookup"><span data-stu-id="0fae5-115">Bundling</span></span>

<span data-ttu-id="0fae5-116">Sdružování kombinuje několik souborů do jediného souboru.</span><span class="sxs-lookup"><span data-stu-id="0fae5-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="0fae5-117">Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslování webového prostředku, například webové stránky.</span><span class="sxs-lookup"><span data-stu-id="0fae5-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="0fae5-118">Můžete vytvořit libovolný počet jednotlivých sad konkrétně pro šablony stylů CSS, JavaScript atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby, která poskytuje vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0fae5-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="0fae5-119">Výsledkem je lepší výkon při načítání první stránky.</span><span class="sxs-lookup"><span data-stu-id="0fae5-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="0fae5-120">Minifikace</span><span class="sxs-lookup"><span data-stu-id="0fae5-120">Minification</span></span>

<span data-ttu-id="0fae5-121">Minifikace odebere z kódu nadbytečné znaky bez změny funkčnosti.</span><span class="sxs-lookup"><span data-stu-id="0fae5-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="0fae5-122">Výsledkem je výrazné snížení velikosti u požadovaných prostředků (například CSS, obrázky a JavaScriptové soubory).</span><span class="sxs-lookup"><span data-stu-id="0fae5-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="0fae5-123">Běžné vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a nepotřebného prázdného místa.</span><span class="sxs-lookup"><span data-stu-id="0fae5-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="0fae5-124">Vezměte v úvahu následující funkci JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="0fae5-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="0fae5-125">Minifikace snižuje funkci na následující:</span><span class="sxs-lookup"><span data-stu-id="0fae5-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="0fae5-126">Kromě odebrání komentářů a nepotřebných prázdných znaků byly následující parametry a názvy proměnných přejmenovány takto:</span><span class="sxs-lookup"><span data-stu-id="0fae5-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="0fae5-127">Původně</span><span class="sxs-lookup"><span data-stu-id="0fae5-127">Original</span></span> | <span data-ttu-id="0fae5-128">Jmenovanou</span><span class="sxs-lookup"><span data-stu-id="0fae5-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="0fae5-129">Dopad sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="0fae5-129">Impact of bundling and minification</span></span>

<span data-ttu-id="0fae5-130">Následující tabulka popisuje rozdíly mezi jednotlivými nasazováním prostředků a používáním sdružování a minifikace:</span><span class="sxs-lookup"><span data-stu-id="0fae5-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="0fae5-131">Akce</span><span class="sxs-lookup"><span data-stu-id="0fae5-131">Action</span></span> | <span data-ttu-id="0fae5-132">S B/M</span><span class="sxs-lookup"><span data-stu-id="0fae5-132">With B/M</span></span> | <span data-ttu-id="0fae5-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="0fae5-133">Without B/M</span></span> | <span data-ttu-id="0fae5-134">Změna</span><span class="sxs-lookup"><span data-stu-id="0fae5-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="0fae5-135">Požadavky na soubory</span><span class="sxs-lookup"><span data-stu-id="0fae5-135">File Requests</span></span>  | <span data-ttu-id="0fae5-136">7</span><span class="sxs-lookup"><span data-stu-id="0fae5-136">7</span></span>   | <span data-ttu-id="0fae5-137">18</span><span class="sxs-lookup"><span data-stu-id="0fae5-137">18</span></span>     | <span data-ttu-id="0fae5-138">157%</span><span class="sxs-lookup"><span data-stu-id="0fae5-138">157%</span></span>
<span data-ttu-id="0fae5-139">Přenesené KB</span><span class="sxs-lookup"><span data-stu-id="0fae5-139">KB Transferred</span></span> | <span data-ttu-id="0fae5-140">156</span><span class="sxs-lookup"><span data-stu-id="0fae5-140">156</span></span> | <span data-ttu-id="0fae5-141">264.68</span><span class="sxs-lookup"><span data-stu-id="0fae5-141">264.68</span></span> | <span data-ttu-id="0fae5-142">70%</span><span class="sxs-lookup"><span data-stu-id="0fae5-142">70%</span></span>
<span data-ttu-id="0fae5-143">Čas načtení (MS)</span><span class="sxs-lookup"><span data-stu-id="0fae5-143">Load Time (ms)</span></span> | <span data-ttu-id="0fae5-144">885</span><span class="sxs-lookup"><span data-stu-id="0fae5-144">885</span></span> | <span data-ttu-id="0fae5-145">2360</span><span class="sxs-lookup"><span data-stu-id="0fae5-145">2360</span></span>   | <span data-ttu-id="0fae5-146">167%</span><span class="sxs-lookup"><span data-stu-id="0fae5-146">167%</span></span>

<span data-ttu-id="0fae5-147">Prohlížeče jsou poměrně podrobné, s ohledem na hlavičky požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="0fae5-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="0fae5-148">Metrika odeslaných celkových bajtů se při sdružování výrazně snížila.</span><span class="sxs-lookup"><span data-stu-id="0fae5-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="0fae5-149">Doba načítání ukazuje výrazné zlepšení, ale tento příklad běžel místně.</span><span class="sxs-lookup"><span data-stu-id="0fae5-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="0fae5-150">Zvýšení výkonu se využívá při použití sdružování a minifikace s prostředky přenesených přes síť.</span><span class="sxs-lookup"><span data-stu-id="0fae5-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="0fae5-151">Výběr strategie sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="0fae5-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="0fae5-152">Šablony projektů MVC a Razor Pages poskytují předem připravené řešení pro sdružování a minifikace, které se skládají z konfiguračního souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="0fae5-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="0fae5-153">Nástroje třetích stran, jako je třeba Spouštěč úloh [grunt](xref:client-side/using-grunt) , provádějí stejné úlohy s větší složitou složitostí.</span><span class="sxs-lookup"><span data-stu-id="0fae5-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="0fae5-154">Nástroj třetí strany je skvěle vhodný, když váš vývojový pracovní postup vyžaduje zpracování mimo sdružování a minifikace&mdash;, jako je linting a optimalizace obrázků.</span><span class="sxs-lookup"><span data-stu-id="0fae5-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="0fae5-155">Když použijete sdružování a minifikace v době návrhu, vytvoří se soubory minifikovaného před nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="0fae5-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="0fae5-156">Sdružování a minifikace před nasazením přináší výhodu omezeného zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="0fae5-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="0fae5-157">Je ale důležité pochopit, že sdružování v době návrhu a minifikace zvyšuje složitost sestavení a funguje jenom se statickými soubory.</span><span class="sxs-lookup"><span data-stu-id="0fae5-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="0fae5-158">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="0fae5-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0fae5-159">V ASP.NET Core 2,0 nebo starších šablon projektů MVC a Razor Pages poskytují konfigurační soubor *bundleconfig. JSON* , který definuje možnosti pro jednotlivé sady prostředků:</span><span class="sxs-lookup"><span data-stu-id="0fae5-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0fae5-160">V ASP.NET Core 2,1 nebo novějším přidejte nový soubor JSON s názvem *bundleconfig. JSON*do kořenového adresáře projektu MVC nebo Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="0fae5-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="0fae5-161">Do tohoto souboru vložte následující JSON jako výchozí bod:</span><span class="sxs-lookup"><span data-stu-id="0fae5-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="0fae5-162">Soubor *bundleconfig. JSON* definuje možnosti pro jednotlivé sady.</span><span class="sxs-lookup"><span data-stu-id="0fae5-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="0fae5-163">V předchozím příkladu je definována jedna konfigurace sady pro vlastní soubory jazyka JavaScript (*wwwroot/js, site. js*) a StyleSheet (*wwwroot/CSS/Web. CSS*).</span><span class="sxs-lookup"><span data-stu-id="0fae5-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="0fae5-164">Mezi možnosti konfigurace patří:</span><span class="sxs-lookup"><span data-stu-id="0fae5-164">Configuration options include:</span></span>

* <span data-ttu-id="0fae5-165">`outputFileName`: název souboru sady prostředků pro výstup.</span><span class="sxs-lookup"><span data-stu-id="0fae5-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="0fae5-166">Může obsahovat relativní cestu ze souboru *bundleconfig. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0fae5-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="0fae5-167">**požadovanou**</span><span class="sxs-lookup"><span data-stu-id="0fae5-167">**required**</span></span>
* <span data-ttu-id="0fae5-168">`inputFiles`: pole souborů, které se mají seskupit.</span><span class="sxs-lookup"><span data-stu-id="0fae5-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="0fae5-169">Jedná se o relativní cesty ke konfiguračnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="0fae5-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="0fae5-170">**volitelné**, \* prázdná hodnota vede k prázdnému výstupnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="0fae5-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="0fae5-171">jsou podporovány vzory [expanze](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="0fae5-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="0fae5-172">`minify`: minifikace možnosti pro typ výstupu.</span><span class="sxs-lookup"><span data-stu-id="0fae5-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="0fae5-173">**volitelné**, *výchozí-`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="0fae5-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="0fae5-174">Možnosti konfigurace jsou k dispozici pro jednotlivé typy výstupních souborů.</span><span class="sxs-lookup"><span data-stu-id="0fae5-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="0fae5-175">Šablony stylů CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="0fae5-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="0fae5-176">JavaScriptový Minifier</span><span class="sxs-lookup"><span data-stu-id="0fae5-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="0fae5-177">Minifier HTML</span><span class="sxs-lookup"><span data-stu-id="0fae5-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="0fae5-178">`includeInProject`: příznak označující, zda se mají přidat generované soubory do souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="0fae5-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="0fae5-179">**volitelné**, *výchozí – NEPRAVDA*</span><span class="sxs-lookup"><span data-stu-id="0fae5-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="0fae5-180">`sourceMap`: příznak označující, zda se má generovat zdrojová mapa pro soubor v balíčku.</span><span class="sxs-lookup"><span data-stu-id="0fae5-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="0fae5-181">**volitelné**, *výchozí – NEPRAVDA*</span><span class="sxs-lookup"><span data-stu-id="0fae5-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="0fae5-182">`sourceMapRootPath`: kořenová cesta pro uložení vygenerovaného zdrojového souboru mapování.</span><span class="sxs-lookup"><span data-stu-id="0fae5-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="0fae5-183">Provádění sdružování a minifikace při sestavení</span><span class="sxs-lookup"><span data-stu-id="0fae5-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="0fae5-184">Balíček NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) umožňuje spuštění sdružování a minifikace v době sestavení.</span><span class="sxs-lookup"><span data-stu-id="0fae5-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="0fae5-185">Balíček vloží [cíle MSBuild](/visualstudio/msbuild/msbuild-targets) , které běží při sestavování a čištění.</span><span class="sxs-lookup"><span data-stu-id="0fae5-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="0fae5-186">Soubor *bundleconfig. JSON* se analyzuje procesem sestavení, aby vytvořil výstupní soubory založené na definované konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="0fae5-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="0fae5-187">BuildBundlerMinifier patří do projektu založeného na komunitě na GitHubu, pro který Microsoft neposkytuje žádnou podporu.</span><span class="sxs-lookup"><span data-stu-id="0fae5-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="0fae5-188">[Zde](https://github.com/madskristensen/BundlerMinifier/issues)by měly být uloženy problémy.</span><span class="sxs-lookup"><span data-stu-id="0fae5-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0fae5-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fae5-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0fae5-190">Přidejte do projektu balíček *BuildBundlerMinifier* .</span><span class="sxs-lookup"><span data-stu-id="0fae5-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="0fae5-191">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="0fae5-191">Build the project.</span></span> <span data-ttu-id="0fae5-192">V okně výstup se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="0fae5-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="0fae5-193">Vyčistěte projekt.</span><span class="sxs-lookup"><span data-stu-id="0fae5-193">Clean the project.</span></span> <span data-ttu-id="0fae5-194">V okně výstup se zobrazí následující:</span><span class="sxs-lookup"><span data-stu-id="0fae5-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0fae5-195">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="0fae5-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0fae5-196">Přidejte do projektu balíček *BuildBundlerMinifier* :</span><span class="sxs-lookup"><span data-stu-id="0fae5-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="0fae5-197">Pokud používáte ASP.NET Core 1. x, obnovte nově přidaný balíček:</span><span class="sxs-lookup"><span data-stu-id="0fae5-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="0fae5-198">Sestavit projekt:</span><span class="sxs-lookup"><span data-stu-id="0fae5-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="0fae5-199">Zobrazí se následující:</span><span class="sxs-lookup"><span data-stu-id="0fae5-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="0fae5-200">Vyčistit projekt:</span><span class="sxs-lookup"><span data-stu-id="0fae5-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="0fae5-201">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="0fae5-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="0fae5-202">Ad hoc provádění sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="0fae5-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="0fae5-203">Je možné spouštět úlohy sdružování a minifikace na základě ad hoc, aniž byste museli projekt sestavovat.</span><span class="sxs-lookup"><span data-stu-id="0fae5-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="0fae5-204">Přidejte do projektu balíček NuGet [BundlerMinifier. Core](https://www.nuget.org/packages/BundlerMinifier.Core/) :</span><span class="sxs-lookup"><span data-stu-id="0fae5-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="0fae5-205">BundlerMinifier. Core patří do komunitního projektu na GitHubu, pro který Microsoft neposkytuje žádnou podporu.</span><span class="sxs-lookup"><span data-stu-id="0fae5-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="0fae5-206">[Zde](https://github.com/madskristensen/BundlerMinifier/issues)by měly být uloženy problémy.</span><span class="sxs-lookup"><span data-stu-id="0fae5-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="0fae5-207">Tento balíček rozšiřuje .NET Core CLI tak, aby zahrnoval nástroj *dotnet-komplet* .</span><span class="sxs-lookup"><span data-stu-id="0fae5-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="0fae5-208">Následující příkaz se dá provést v okně konzoly Správce balíčků (PMC) nebo v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="0fae5-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="0fae5-209">Správce balíčků NuGet přidává do souboru \*. csproj závislosti jako uzly `<PackageReference />`.</span><span class="sxs-lookup"><span data-stu-id="0fae5-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="0fae5-210">Příkaz `dotnet bundle` je zaregistrován v .NET Core CLI pouze v případě, že je použit uzel `<DotNetCliToolReference />`.</span><span class="sxs-lookup"><span data-stu-id="0fae5-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="0fae5-211">Upravte soubor \*. csproj odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="0fae5-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="0fae5-212">Přidat soubory do pracovního postupu</span><span class="sxs-lookup"><span data-stu-id="0fae5-212">Add files to workflow</span></span>

<span data-ttu-id="0fae5-213">Vezměte v úvahu příklad, ve kterém se přidá další *vlastní soubor. CSS* , který se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="0fae5-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="0fae5-214">Pokud chcete minimalizuje *vlastní soubor. CSS* a seskupit ho pomocí *site. CSS* do souboru *Web. min. CSS* , přidejte relativní cestu k souboru *bundleconfig. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0fae5-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="0fae5-215">Případně můžete použít následující vzor expanze názvů:</span><span class="sxs-lookup"><span data-stu-id="0fae5-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="0fae5-216">Tento vzor expanze názvů se shoduje se všemi soubory šablon stylů CSS a vylučuje vzor souboru minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="0fae5-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="0fae5-217">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0fae5-217">Build the application.</span></span> <span data-ttu-id="0fae5-218">Otevřete *Web. min. CSS* a Všimněte si, že obsah *vlastní. CSS* je připojen na konec souboru.</span><span class="sxs-lookup"><span data-stu-id="0fae5-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="0fae5-219">Sdružování a minifikace založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="0fae5-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="0fae5-220">Jako osvědčený postup by se měly v produkčním prostředí používat balíčky a minifikovaného soubory vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="0fae5-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="0fae5-221">Během vývoje se původní soubory vytvářejí pro snazší ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0fae5-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="0fae5-222">Určete soubory, které chcete zahrnout na vaše stránky, pomocí [pomocné rutiny tag prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="0fae5-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="0fae5-223">Pomocník značek prostředí vykresluje obsah pouze při spuštění v konkrétních [prostředích](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="0fae5-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0fae5-224">Následující `environment` značka vykresluje nezpracované soubory šablon stylů CSS při spuštění v prostředí `Development`:</span><span class="sxs-lookup"><span data-stu-id="0fae5-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="0fae5-225">Následující `environment` značka vykresluje soubory šablon stylů CSS a minifikovaného, když běží v jiném prostředí než `Development`.</span><span class="sxs-lookup"><span data-stu-id="0fae5-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="0fae5-226">Například spuštění v `Production` nebo `Staging` aktivuje vykreslování těchto šablon stylů:</span><span class="sxs-lookup"><span data-stu-id="0fae5-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="0fae5-227">Využití bundleconfig. JSON z Gulp</span><span class="sxs-lookup"><span data-stu-id="0fae5-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="0fae5-228">Existují případy, kdy pracovní postup sdružování aplikace a minifikace vyžaduje další zpracování.</span><span class="sxs-lookup"><span data-stu-id="0fae5-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="0fae5-229">Mezi příklady patří Optimalizace obrázků, busting mezipaměti a zpracování prostředků CDN.</span><span class="sxs-lookup"><span data-stu-id="0fae5-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="0fae5-230">Aby bylo možné tyto požadavky splnit, můžete převést pracovní postup sdružování a minifikace na použití Gulp.</span><span class="sxs-lookup"><span data-stu-id="0fae5-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="0fae5-231">Použití rozšíření & Minifier rozšíření pro svazek</span><span class="sxs-lookup"><span data-stu-id="0fae5-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="0fae5-232">Rozšíření sady Visual Studio [& Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) zpracovává převod na Gulp.</span><span class="sxs-lookup"><span data-stu-id="0fae5-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="0fae5-233">Rozšíření & rozšíření Minifier patří do komunitního projektu na GitHubu, pro který Microsoft neposkytuje žádnou podporu.</span><span class="sxs-lookup"><span data-stu-id="0fae5-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="0fae5-234">[Zde](https://github.com/madskristensen/BundlerMinifier/issues)by měly být uloženy problémy.</span><span class="sxs-lookup"><span data-stu-id="0fae5-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="0fae5-235">Pravým tlačítkem myši klikněte na soubor *bundleconfig. JSON* v Průzkumník řešení a vyberte možnost **& Minifier** > **převést na Gulp...** :</span><span class="sxs-lookup"><span data-stu-id="0fae5-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Převést na položku kontextové nabídky Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="0fae5-237">Soubory *gulpfile. js* a *Package. JSON* jsou přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="0fae5-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="0fae5-238">Jsou nainstalovány podpůrné balíčky [npm](https://www.npmjs.com/) uvedené v oddílu souboru *package. JSON* `devDependencies`.</span><span class="sxs-lookup"><span data-stu-id="0fae5-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="0fae5-239">Spusťte následující příkaz v okně PMC a nainstalujte Gulp CLI jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="0fae5-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="0fae5-240">Soubor *gulpfile. js* přečte soubor *bundleconfig. JSON* pro vstupy, výstupy a nastavení.</span><span class="sxs-lookup"><span data-stu-id="0fae5-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="0fae5-241">Převést ručně</span><span class="sxs-lookup"><span data-stu-id="0fae5-241">Convert manually</span></span>

<span data-ttu-id="0fae5-242">Pokud není k dispozici sada Visual Studio nebo rozšíření Minifier &, proveďte převod ručně.</span><span class="sxs-lookup"><span data-stu-id="0fae5-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="0fae5-243">Přidejte soubor *Package. JSON* s následujícím `devDependencies`do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="0fae5-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="0fae5-244">Modul `gulp-uglify` nepodporuje ECMAScript (ES) 2015/ES6 a novější.</span><span class="sxs-lookup"><span data-stu-id="0fae5-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="0fae5-245">Nainstalujte [Gulp-terser](https://www.npmjs.com/package/gulp-terser) namísto `gulp-uglify` pro použití ES2015/ES6 nebo novějšího.</span><span class="sxs-lookup"><span data-stu-id="0fae5-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="0fae5-246">Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *Package. JSON*:</span><span class="sxs-lookup"><span data-stu-id="0fae5-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="0fae5-247">Nainstalujte rozhraní příkazového řádku Gulp jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="0fae5-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="0fae5-248">Zkopírujte soubor *gulpfile. js* níže do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="0fae5-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="0fae5-249">Spuštění úloh Gulp</span><span class="sxs-lookup"><span data-stu-id="0fae5-249">Run Gulp tasks</span></span>

<span data-ttu-id="0fae5-250">Chcete-li aktivovat úlohu Gulp minifikace před sestavením projektu v aplikaci Visual Studio, přidejte následující [cíl nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) do souboru \*. csproj:</span><span class="sxs-lookup"><span data-stu-id="0fae5-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="0fae5-251">V tomto příkladu jsou všechny úlohy definované v rámci `MyPreCompileTarget` Target spouštěny před předdefinovaným cílem `Build`.</span><span class="sxs-lookup"><span data-stu-id="0fae5-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="0fae5-252">Výstup podobný následujícímu se zobrazí v okně výstupu sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0fae5-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0fae5-253">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0fae5-253">Additional resources</span></span>

* [<span data-ttu-id="0fae5-254">Použití nástroje Grunt</span><span class="sxs-lookup"><span data-stu-id="0fae5-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="0fae5-255">Používání více prostředí</span><span class="sxs-lookup"><span data-stu-id="0fae5-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="0fae5-256">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="0fae5-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
