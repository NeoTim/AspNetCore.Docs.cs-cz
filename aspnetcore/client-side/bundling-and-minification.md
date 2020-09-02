---
title: Statické prostředky sady prostředků a minimalizuje v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve ASP.NET Core webové aplikaci, a to pomocí metod sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: f696df0b421e5aab6f50cfaec3ca8edac894cea9
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379390"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="af0b1-103">Statické prostředky sady prostředků a minimalizuje v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af0b1-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="af0b1-104">Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [David borovice](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="af0b1-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="af0b1-105">Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak se tyto funkce dají používat s ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="af0b1-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="af0b1-106">Co je sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="af0b1-106">What is bundling and minification</span></span>

<span data-ttu-id="af0b1-107">Sdružování a minifikace jsou dvě odlišná optimalizace výkonu, které můžete použít ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="af0b1-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="af0b1-108">Společně, sdružování a minifikace vylepšit výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických prostředků.</span><span class="sxs-lookup"><span data-stu-id="af0b1-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="af0b1-109">Seřízení a minifikace primárně zlepšují dobu načtení první stránky.</span><span class="sxs-lookup"><span data-stu-id="af0b1-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="af0b1-110">Po vyžádání webové stránky prohlížeč ukládá do mezipaměti statické prostředky (JavaScript, CSS a image).</span><span class="sxs-lookup"><span data-stu-id="af0b1-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="af0b1-111">V důsledku toho sdružování a minifikace nezlepšují výkon při požadavku na stejnou stránku nebo stránky ve stejné lokalitě, která vyžaduje stejné prostředky.</span><span class="sxs-lookup"><span data-stu-id="af0b1-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="af0b1-112">Pokud není hlavička Expires v prostředcích správně nastavená a pokud se nepoužívá sdružování a minifikace, budou heuristické aktuálnosti v prohlížeči označovat prostředky zastaralé po několika dnech.</span><span class="sxs-lookup"><span data-stu-id="af0b1-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="af0b1-113">Kromě toho prohlížeč vyžaduje žádost o ověření pro každý Asset.</span><span class="sxs-lookup"><span data-stu-id="af0b1-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="af0b1-114">V tomto případě přináší sdružování a minifikace vylepšení výkonu i po první žádosti stránky.</span><span class="sxs-lookup"><span data-stu-id="af0b1-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="af0b1-115">Sdružování</span><span class="sxs-lookup"><span data-stu-id="af0b1-115">Bundling</span></span>

<span data-ttu-id="af0b1-116">Sdružování kombinuje několik souborů do jediného souboru.</span><span class="sxs-lookup"><span data-stu-id="af0b1-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="af0b1-117">Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslování webového prostředku, například webové stránky.</span><span class="sxs-lookup"><span data-stu-id="af0b1-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="af0b1-118">Můžete vytvořit libovolný počet jednotlivých sad konkrétně pro šablony stylů CSS, JavaScript atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby, která poskytuje vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="af0b1-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="af0b1-119">Výsledkem je lepší výkon při načítání první stránky.</span><span class="sxs-lookup"><span data-stu-id="af0b1-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="af0b1-120">Minifikace</span><span class="sxs-lookup"><span data-stu-id="af0b1-120">Minification</span></span>

<span data-ttu-id="af0b1-121">Minifikace odebere z kódu nadbytečné znaky bez změny funkčnosti.</span><span class="sxs-lookup"><span data-stu-id="af0b1-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="af0b1-122">Výsledkem je výrazné snížení velikosti u požadovaných prostředků (například CSS, obrázky a JavaScriptové soubory).</span><span class="sxs-lookup"><span data-stu-id="af0b1-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="af0b1-123">Běžné vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a nepotřebného prázdného místa.</span><span class="sxs-lookup"><span data-stu-id="af0b1-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="af0b1-124">Vezměte v úvahu následující funkci JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="af0b1-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="af0b1-125">Minifikace snižuje funkci na následující:</span><span class="sxs-lookup"><span data-stu-id="af0b1-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="af0b1-126">Kromě odebrání komentářů a nepotřebných prázdných znaků byly následující parametry a názvy proměnných přejmenovány takto:</span><span class="sxs-lookup"><span data-stu-id="af0b1-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="af0b1-127">Původně</span><span class="sxs-lookup"><span data-stu-id="af0b1-127">Original</span></span> | <span data-ttu-id="af0b1-128">Jmenovanou</span><span class="sxs-lookup"><span data-stu-id="af0b1-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="af0b1-129">Dopad sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="af0b1-129">Impact of bundling and minification</span></span>

<span data-ttu-id="af0b1-130">Následující tabulka popisuje rozdíly mezi jednotlivými nasazováním prostředků a používáním sdružování a minifikace:</span><span class="sxs-lookup"><span data-stu-id="af0b1-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="af0b1-131">Akce</span><span class="sxs-lookup"><span data-stu-id="af0b1-131">Action</span></span> | <span data-ttu-id="af0b1-132">S B/M</span><span class="sxs-lookup"><span data-stu-id="af0b1-132">With B/M</span></span> | <span data-ttu-id="af0b1-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="af0b1-133">Without B/M</span></span> | <span data-ttu-id="af0b1-134">Změnit</span><span class="sxs-lookup"><span data-stu-id="af0b1-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="af0b1-135">Požadavky na soubory</span><span class="sxs-lookup"><span data-stu-id="af0b1-135">File Requests</span></span>  | <span data-ttu-id="af0b1-136">7</span><span class="sxs-lookup"><span data-stu-id="af0b1-136">7</span></span>   | <span data-ttu-id="af0b1-137">18</span><span class="sxs-lookup"><span data-stu-id="af0b1-137">18</span></span>     | <span data-ttu-id="af0b1-138">157%</span><span class="sxs-lookup"><span data-stu-id="af0b1-138">157%</span></span>
<span data-ttu-id="af0b1-139">Přenesené KB</span><span class="sxs-lookup"><span data-stu-id="af0b1-139">KB Transferred</span></span> | <span data-ttu-id="af0b1-140">156</span><span class="sxs-lookup"><span data-stu-id="af0b1-140">156</span></span> | <span data-ttu-id="af0b1-141">264,68</span><span class="sxs-lookup"><span data-stu-id="af0b1-141">264.68</span></span> | <span data-ttu-id="af0b1-142">70 %</span><span class="sxs-lookup"><span data-stu-id="af0b1-142">70%</span></span>
<span data-ttu-id="af0b1-143">Čas načtení (MS)</span><span class="sxs-lookup"><span data-stu-id="af0b1-143">Load Time (ms)</span></span> | <span data-ttu-id="af0b1-144">885</span><span class="sxs-lookup"><span data-stu-id="af0b1-144">885</span></span> | <span data-ttu-id="af0b1-145">2360</span><span class="sxs-lookup"><span data-stu-id="af0b1-145">2360</span></span>   | <span data-ttu-id="af0b1-146">167%</span><span class="sxs-lookup"><span data-stu-id="af0b1-146">167%</span></span>

<span data-ttu-id="af0b1-147">Prohlížeče jsou poměrně podrobné, s ohledem na hlavičky požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="af0b1-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="af0b1-148">Metrika odeslaných celkových bajtů se při sdružování výrazně snížila.</span><span class="sxs-lookup"><span data-stu-id="af0b1-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="af0b1-149">Doba načítání ukazuje výrazné zlepšení, ale tento příklad běžel místně.</span><span class="sxs-lookup"><span data-stu-id="af0b1-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="af0b1-150">Zvýšení výkonu se využívá při použití sdružování a minifikace s prostředky přenesených přes síť.</span><span class="sxs-lookup"><span data-stu-id="af0b1-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="af0b1-151">Výběr strategie sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="af0b1-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="af0b1-152">RazorŠablony projektů MVC a stránky poskytují řešení pro sdružování a minifikace, které se skládají z konfiguračního souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="af0b1-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="af0b1-153">Nástroje třetích stran, jako je třeba Spouštěč úloh [grunt](xref:client-side/using-grunt) , provádějí stejné úlohy s větší složitou složitostí.</span><span class="sxs-lookup"><span data-stu-id="af0b1-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="af0b1-154">Nástroj třetí strany je skvěle vhodný, když váš vývojový pracovní postup vyžaduje zpracování mimo sdružování a minifikace, jako &mdash; je linting a optimalizace obrázků.</span><span class="sxs-lookup"><span data-stu-id="af0b1-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="af0b1-155">Když použijete sdružování a minifikace v době návrhu, vytvoří se soubory minifikovaného před nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="af0b1-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="af0b1-156">Sdružování a minifikace před nasazením přináší výhodu omezeného zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="af0b1-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="af0b1-157">Je ale důležité pochopit, že sdružování v době návrhu a minifikace zvyšuje složitost sestavení a funguje jenom se statickými soubory.</span><span class="sxs-lookup"><span data-stu-id="af0b1-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="af0b1-158">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="af0b1-158">Configure bundling and minification</span></span>

> [!NOTE]
> <span data-ttu-id="af0b1-159">Aby to fungovalo, musí se do projektu přidat balíček NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="af0b1-159">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package needs to be added to your project for this to work.</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="af0b1-160">V ASP.NET Core 2,0 nebo starších verzích Razor představují šablony projektů MVC a pages *bundleconfig.jsv* konfiguračním souboru, který definuje možnosti pro jednotlivé sady prostředků:</span><span class="sxs-lookup"><span data-stu-id="af0b1-160">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="af0b1-161">V ASP.NET Core 2,1 nebo novějším přidejte nový soubor JSON s názvem *bundleconfig.js*do Razor kořenového adresáře projektu MVC nebo Pages.</span><span class="sxs-lookup"><span data-stu-id="af0b1-161">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="af0b1-162">Do tohoto souboru vložte následující JSON jako výchozí bod:</span><span class="sxs-lookup"><span data-stu-id="af0b1-162">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="af0b1-163">*bundleconfig.jsv* souboru definuje možnosti pro jednotlivé sady.</span><span class="sxs-lookup"><span data-stu-id="af0b1-163">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="af0b1-164">V předchozím příkladu je definována jedna konfigurace sady pro vlastní soubory jazyka JavaScript (*wwwroot/js/site.js*) a šablony stylů (*wwwroot/CSS/Web. CSS*).</span><span class="sxs-lookup"><span data-stu-id="af0b1-164">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="af0b1-165">Mezi možnosti konfigurace patří:</span><span class="sxs-lookup"><span data-stu-id="af0b1-165">Configuration options include:</span></span>

* <span data-ttu-id="af0b1-166">`outputFileName`: Název souboru sady pro výstup.</span><span class="sxs-lookup"><span data-stu-id="af0b1-166">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="af0b1-167">Může obsahovat relativní cestu z *bundleconfig.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="af0b1-167">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="af0b1-168">**požadovanou**</span><span class="sxs-lookup"><span data-stu-id="af0b1-168">**required**</span></span>
* <span data-ttu-id="af0b1-169">`inputFiles`: Pole souborů, které se mají seskupit.</span><span class="sxs-lookup"><span data-stu-id="af0b1-169">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="af0b1-170">Jedná se o relativní cesty ke konfiguračnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="af0b1-170">These are relative paths to the configuration file.</span></span> <span data-ttu-id="af0b1-171">**volitelné**, \* prázdná hodnota vede k prázdnému výstupnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="af0b1-171">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="af0b1-172">jsou podporovány vzory [expanze](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="af0b1-172">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="af0b1-173">`minify`: Možnosti minifikace pro typ výstupu.</span><span class="sxs-lookup"><span data-stu-id="af0b1-173">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="af0b1-174">**volitelné**, \*výchozí – `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="af0b1-174">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="af0b1-175">Možnosti konfigurace jsou k dispozici pro jednotlivé typy výstupních souborů.</span><span class="sxs-lookup"><span data-stu-id="af0b1-175">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="af0b1-176">Šablony stylů CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="af0b1-176">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="af0b1-177">JavaScriptový Minifier</span><span class="sxs-lookup"><span data-stu-id="af0b1-177">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="af0b1-178">Minifier HTML</span><span class="sxs-lookup"><span data-stu-id="af0b1-178">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="af0b1-179">`includeInProject`: Příznak označující, zda se mají přidat generované soubory do souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="af0b1-179">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="af0b1-180">**volitelné**, *výchozí – NEPRAVDA*</span><span class="sxs-lookup"><span data-stu-id="af0b1-180">**optional**, *default - false*</span></span>
* <span data-ttu-id="af0b1-181">`sourceMap`: Příznak označující, zda se má generovat zdrojová mapa pro soubor zahrnutý do balíčku.</span><span class="sxs-lookup"><span data-stu-id="af0b1-181">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="af0b1-182">**volitelné**, *výchozí – NEPRAVDA*</span><span class="sxs-lookup"><span data-stu-id="af0b1-182">**optional**, *default - false*</span></span>
* <span data-ttu-id="af0b1-183">`sourceMapRootPath`: Kořenová cesta pro uložení vygenerovaného zdrojového souboru mapování.</span><span class="sxs-lookup"><span data-stu-id="af0b1-183">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="af0b1-184">Přidat soubory do pracovního postupu</span><span class="sxs-lookup"><span data-stu-id="af0b1-184">Add files to workflow</span></span>

<span data-ttu-id="af0b1-185">Vezměte v úvahu příklad, ve kterém se přidá další *vlastní soubor. CSS* , který se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="af0b1-185">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="af0b1-186">Pokud chcete minimalizuje *vlastní soubor. CSS* a seskupit ho pomocí *site. CSS* do souboru *Web. min. CSS* , přidejte relativní cestu k *bundleconfig.js*:</span><span class="sxs-lookup"><span data-stu-id="af0b1-186">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="af0b1-187">Případně můžete použít následující vzor expanze názvů:</span><span class="sxs-lookup"><span data-stu-id="af0b1-187">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="af0b1-188">Tento vzor expanze názvů se shoduje se všemi soubory šablon stylů CSS a vylučuje vzor souboru minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="af0b1-188">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="af0b1-189">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="af0b1-189">Build the application.</span></span> <span data-ttu-id="af0b1-190">Otevřete *Web. min. CSS* a Všimněte si, že obsah *vlastní. CSS* je připojen na konec souboru.</span><span class="sxs-lookup"><span data-stu-id="af0b1-190">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="af0b1-191">Sdružování a minifikace založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="af0b1-191">Environment-based bundling and minification</span></span>

<span data-ttu-id="af0b1-192">Jako osvědčený postup by se měly v produkčním prostředí používat balíčky a minifikovaného soubory vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="af0b1-192">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="af0b1-193">Během vývoje se původní soubory vytvářejí pro snazší ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="af0b1-193">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="af0b1-194">Určete soubory, které chcete zahrnout na vaše stránky, pomocí [pomocné rutiny tag prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="af0b1-194">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="af0b1-195">Pomocník značek prostředí vykresluje obsah pouze při spuštění v konkrétních [prostředích](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="af0b1-195">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="af0b1-196">Následující `environment` značka vykresluje nezpracované soubory šablon stylů CSS při spuštění v `Development` prostředí:</span><span class="sxs-lookup"><span data-stu-id="af0b1-196">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="af0b1-197">Následující `environment` značka vykresluje soubory šablon stylů CSS a minifikovaného při spuštění v jiném prostředí než `Development` .</span><span class="sxs-lookup"><span data-stu-id="af0b1-197">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="af0b1-198">Například spuštění v `Production` nebo `Staging` spustí vykreslování těchto šablon stylů:</span><span class="sxs-lookup"><span data-stu-id="af0b1-198">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="af0b1-199">Využívání bundleconfig.jsod Gulp</span><span class="sxs-lookup"><span data-stu-id="af0b1-199">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="af0b1-200">Existují případy, kdy pracovní postup sdružování aplikace a minifikace vyžaduje další zpracování.</span><span class="sxs-lookup"><span data-stu-id="af0b1-200">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="af0b1-201">Mezi příklady patří Optimalizace obrázků, busting mezipaměti a zpracování prostředků CDN.</span><span class="sxs-lookup"><span data-stu-id="af0b1-201">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="af0b1-202">Aby bylo možné tyto požadavky splnit, můžete převést pracovní postup sdružování a minifikace na použití Gulp.</span><span class="sxs-lookup"><span data-stu-id="af0b1-202">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="af0b1-203">Ručně převeďte pracovní postup sdružování a minifikace na použití Gulp</span><span class="sxs-lookup"><span data-stu-id="af0b1-203">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="af0b1-204">*package.json* `devDependencies` Do kořenového adresáře projektu přidejtepackage.jsdo souboru s následujícím:</span><span class="sxs-lookup"><span data-stu-id="af0b1-204">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="af0b1-205">`gulp-uglify`Modul nepodporuje ECMAScript (ES) 2015/ES6 a novější.</span><span class="sxs-lookup"><span data-stu-id="af0b1-205">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="af0b1-206">Nainstalujte [Gulp-terser](https://www.npmjs.com/package/gulp-terser) namísto `gulp-uglify` použití ES2015/ES6 nebo novějšího.</span><span class="sxs-lookup"><span data-stu-id="af0b1-206">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="af0b1-207">Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.jsna*:</span><span class="sxs-lookup"><span data-stu-id="af0b1-207">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="af0b1-208">Nainstalujte rozhraní příkazového řádku Gulp jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="af0b1-208">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="af0b1-209">Zkopírujte soubor *gulpfile.js* níže do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="af0b1-209">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="af0b1-210">Spuštění úloh Gulp</span><span class="sxs-lookup"><span data-stu-id="af0b1-210">Run Gulp tasks</span></span>

<span data-ttu-id="af0b1-211">Aktivace úlohy minifikace Gulp před sestavením projektu v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af0b1-211">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="af0b1-212">Nainstalujte balíček NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="af0b1-212">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="af0b1-213">Do souboru projektu přidejte následující [cíl nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) :</span><span class="sxs-lookup"><span data-stu-id="af0b1-213">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="af0b1-214">V tomto příkladu všechny úlohy, které jsou definovány v rámci cílového prostředí, `MyPreCompileTarget` před předdefinovaným `Build` cílem.</span><span class="sxs-lookup"><span data-stu-id="af0b1-214">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="af0b1-215">Výstup podobný následujícímu se zobrazí v okně výstupu sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af0b1-215">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="af0b1-216">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="af0b1-216">Additional resources</span></span>

* [<span data-ttu-id="af0b1-217">Použití nástroje Grunt</span><span class="sxs-lookup"><span data-stu-id="af0b1-217">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="af0b1-218">Používání více prostředí</span><span class="sxs-lookup"><span data-stu-id="af0b1-218">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="af0b1-219">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="af0b1-219">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
