---
title: Svazek a minfika statických aktiv v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve webové aplikaci ASP.NET Core použitím technik sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488686"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="d5b8f-103">Svazek a minfika statických aktiv v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5b8f-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="d5b8f-104">[Scott Addie](https://twitter.com/Scott_Addie) a [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="d5b8f-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="d5b8f-105">Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak lze tyto funkce používat s webovými aplikacemi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="d5b8f-106">Co je svazování a minifikace</span><span class="sxs-lookup"><span data-stu-id="d5b8f-106">What is bundling and minification</span></span>

<span data-ttu-id="d5b8f-107">Sdružování a minifikace jsou dvě odlišné optimalizace výkonu, které můžete použít ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="d5b8f-108">Při společném sdružování a minifikaci se zvyšuje výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických datových zdrojů.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="d5b8f-109">Sdružování a minifikace především zlepšují dobu načítání požadavku na první stránku.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="d5b8f-110">Jakmile je webová stránka požadována, prohlížeč uloží statické datové zdroje do mezipaměti (JavaScript, CSS a obrázky).</span><span class="sxs-lookup"><span data-stu-id="d5b8f-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="d5b8f-111">V důsledku toho svazování a minifikace nezlepšují výkon při žádosti o stejnou stránku nebo stránky na stejném webu požadujícím stejné prostředky.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="d5b8f-112">Pokud není záhlaví vypršení platnosti správně nastaveno na datových zdrojích a pokud se nepoužívá svazování a minifikace, heuristika čerstvosti prohlížeče označuje datové zdroje zastaralé po několika dnech.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="d5b8f-113">Kromě toho prohlížeč vyžaduje žádost o ověření pro každý prostředek.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="d5b8f-114">V tomto případě sdružování a minifikace poskytují zlepšení výkonu i po první stránce požadavku.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="d5b8f-115">Svazování</span><span class="sxs-lookup"><span data-stu-id="d5b8f-115">Bundling</span></span>

<span data-ttu-id="d5b8f-116">Sdružování kombinuje několik souborů do jediného souboru.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="d5b8f-117">Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslení webového datového zdroje, například webové stránky.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="d5b8f-118">Můžete vytvořit libovolný počet jednotlivých svazků speciálně pro CSS, JavaScript, atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby poskytující vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="d5b8f-119">To má za následek lepší výkon načítání první stránky.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="d5b8f-120">Minification</span><span class="sxs-lookup"><span data-stu-id="d5b8f-120">Minification</span></span>

<span data-ttu-id="d5b8f-121">Minifikace odebere nepotřebné znaky z kódu bez změny funkčnosti.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="d5b8f-122">Výsledkem je významné zmenšení velikosti požadovaných datových zdrojů (například CSS, obrázků a souborů JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="d5b8f-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="d5b8f-123">Časté vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a zbytečných mezer.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="d5b8f-124">Zvažte následující funkci JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="d5b8f-125">Minifikace snižuje funkci na následující:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="d5b8f-126">Kromě odebrání komentáře a zbytečné mezery, následující parametr a názvy proměnných byly přejmenovány takto:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="d5b8f-127">Původní</span><span class="sxs-lookup"><span data-stu-id="d5b8f-127">Original</span></span> | <span data-ttu-id="d5b8f-128">Přejmenován</span><span class="sxs-lookup"><span data-stu-id="d5b8f-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="d5b8f-129">Dopad spojeného prodeje a minifikace</span><span class="sxs-lookup"><span data-stu-id="d5b8f-129">Impact of bundling and minification</span></span>

<span data-ttu-id="d5b8f-130">V následující tabulce jsou uvedeny rozdíly mezi individuálním zatížením aktiv a použitím spojených prodejů a minifikace:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="d5b8f-131">Akce</span><span class="sxs-lookup"><span data-stu-id="d5b8f-131">Action</span></span> | <span data-ttu-id="d5b8f-132">S B/M</span><span class="sxs-lookup"><span data-stu-id="d5b8f-132">With B/M</span></span> | <span data-ttu-id="d5b8f-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="d5b8f-133">Without B/M</span></span> | <span data-ttu-id="d5b8f-134">Změnit</span><span class="sxs-lookup"><span data-stu-id="d5b8f-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="d5b8f-135">Žádosti o soubor</span><span class="sxs-lookup"><span data-stu-id="d5b8f-135">File Requests</span></span>  | <span data-ttu-id="d5b8f-136">7</span><span class="sxs-lookup"><span data-stu-id="d5b8f-136">7</span></span>   | <span data-ttu-id="d5b8f-137">18</span><span class="sxs-lookup"><span data-stu-id="d5b8f-137">18</span></span>     | <span data-ttu-id="d5b8f-138">157%</span><span class="sxs-lookup"><span data-stu-id="d5b8f-138">157%</span></span>
<span data-ttu-id="d5b8f-139">KB Převedeno</span><span class="sxs-lookup"><span data-stu-id="d5b8f-139">KB Transferred</span></span> | <span data-ttu-id="d5b8f-140">156</span><span class="sxs-lookup"><span data-stu-id="d5b8f-140">156</span></span> | <span data-ttu-id="d5b8f-141">264.68</span><span class="sxs-lookup"><span data-stu-id="d5b8f-141">264.68</span></span> | <span data-ttu-id="d5b8f-142">70 %</span><span class="sxs-lookup"><span data-stu-id="d5b8f-142">70%</span></span>
<span data-ttu-id="d5b8f-143">Doba načítání (ms)</span><span class="sxs-lookup"><span data-stu-id="d5b8f-143">Load Time (ms)</span></span> | <span data-ttu-id="d5b8f-144">885</span><span class="sxs-lookup"><span data-stu-id="d5b8f-144">885</span></span> | <span data-ttu-id="d5b8f-145">2360</span><span class="sxs-lookup"><span data-stu-id="d5b8f-145">2360</span></span>   | <span data-ttu-id="d5b8f-146">167%</span><span class="sxs-lookup"><span data-stu-id="d5b8f-146">167%</span></span>

<span data-ttu-id="d5b8f-147">Prohlížeče jsou poměrně podrobné s ohledem na hlavičky požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="d5b8f-148">Celkový počet bajtů odeslaných metrika viděl významné snížení při sdružování.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="d5b8f-149">Doba načítání ukazuje významné zlepšení, ale tento příklad běžel místně.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="d5b8f-150">Vyšší nárůst y výkonu se realizuje při použití spojených prodejů a minifikace s aktivy převedenými přes síť.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="d5b8f-151">Vyberte si strategii sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="d5b8f-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="d5b8f-152">Šablony projektu MVC a Razor Pages poskytují řešení pro sdružování a minifikaci skládající se z konfiguračního souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="d5b8f-153">Nástroje třetích stran, jako je například [grunt](xref:client-side/using-grunt) úkol běžec, provádět stejné úkoly s trochu složitější.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="d5b8f-154">Nástroj jiného výrobce je skvělý, když váš vývojový pracovní postup&mdash;vyžaduje zpracování nad rámec sdružování a minifikace, jako je linting a optimalizace obrazu.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="d5b8f-155">Pomocí sdružování a minifikace v době návrhu se minified soubory vytvoří před nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="d5b8f-156">Sdružování a minifying před nasazením poskytuje výhodu snížení zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="d5b8f-157">Je však důležité si uvědomit, že sdružování a minifikace v době návrhu zvyšuje složitost sestavení a funguje pouze se statickými soubory.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="d5b8f-158">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="d5b8f-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="d5b8f-159">V ASP.NET Core 2.0 nebo starší, šablony projektu MVC a Razor Pages poskytují konfigurační soubor *bundleconfig.json,* který definuje možnosti pro každou sadu:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="d5b8f-160">V ASP.NET jádrem 2.1 nebo novějším přidejte do kořenového adresáře projektu MVC nebo Razor Pages nový soubor JSON s názvem *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="d5b8f-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="d5b8f-161">Jako výchozí bod zahrňte do tohoto souboru následující json:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="d5b8f-162">Soubor *bundleconfig.json* definuje možnosti pro každou sadu.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="d5b8f-163">V předchozím příkladu je definována konfigurace jednoho svazku pro vlastní soubory JavaScriptu (*wwwroot/js/site.js*) a stylů *(wwwroot/css/site.css).*</span><span class="sxs-lookup"><span data-stu-id="d5b8f-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="d5b8f-164">Možnosti konfigurace zahrnují:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-164">Configuration options include:</span></span>

* <span data-ttu-id="d5b8f-165">`outputFileName`: Název souboru svazku pro výstup.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="d5b8f-166">Může obsahovat relativní cestu ze souboru *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="d5b8f-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="d5b8f-167">**Požadované**</span><span class="sxs-lookup"><span data-stu-id="d5b8f-167">**required**</span></span>
* <span data-ttu-id="d5b8f-168">`inputFiles`: Pole souborů, které chcete spojit dohromady.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="d5b8f-169">Jedná se o relativní cesty ke konfiguračnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="d5b8f-170">**volitelné**, \*prázdná hodnota má za následek prázdný výstupní soubor.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="d5b8f-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) vzory jsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="d5b8f-172">`minify`: Možnosti minifikace pro typ výstupu.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="d5b8f-173">**volitelné**, \*výchozí - `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="d5b8f-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="d5b8f-174">Možnosti konfigurace jsou k dispozici pro typ výstupního souboru.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="d5b8f-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="d5b8f-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="d5b8f-176">Minipěč JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d5b8f-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="d5b8f-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="d5b8f-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="d5b8f-178">`includeInProject`: Příznak označující, zda mají být do souboru projektu přidávány generované soubory.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="d5b8f-179">**volitelné**, *výchozí - false*</span><span class="sxs-lookup"><span data-stu-id="d5b8f-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="d5b8f-180">`sourceMap`: Příznak označující, zda chcete generovat zdrojovou mapu pro přibalený soubor.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="d5b8f-181">**volitelné**, *výchozí - false*</span><span class="sxs-lookup"><span data-stu-id="d5b8f-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="d5b8f-182">`sourceMapRootPath`: Kořenová cesta pro uložení generovaného zdrojového mapového souboru.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="d5b8f-183">Přidání souborů do pracovního postupu</span><span class="sxs-lookup"><span data-stu-id="d5b8f-183">Add files to workflow</span></span>

<span data-ttu-id="d5b8f-184">Vezměme si příklad, ve kterém je přidán další soubor *custom.css* připomínající následující:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="d5b8f-185">Chcete-li minify *custom.css* a složka s *site.css* do souboru *site.min.css,* přidejte relativní cestu *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="d5b8f-186">Alternativně by mohl být použit následující globbing vzor:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="d5b8f-187">Tento globbing vzor odpovídá všem souborům CSS a vylučuje minified soubor vzor.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="d5b8f-188">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-188">Build the application.</span></span> <span data-ttu-id="d5b8f-189">Otevřete *soubor site.min.css* a všimněte si, že obsah *souboru custom.css* je připojen na konec souboru.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="d5b8f-190">Spojené spoje a minifikace založené na životním prostředí</span><span class="sxs-lookup"><span data-stu-id="d5b8f-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="d5b8f-191">Jako osvědčený postup by měly být přibalené a minifikované soubory vaší aplikace používány v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="d5b8f-192">Během vývoje původní soubory usnadňují ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="d5b8f-193">Určete, které soubory chcete zahrnout na stránky pomocí [pomocníka pro označení prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="d5b8f-194">Pomocník značky prostředí vykreslí jeho obsah pouze při spuštění v [určitých prostředích](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="d5b8f-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d5b8f-195">Následující `environment` značka vykreslí nezpracované soubory CSS `Development` při spuštění v prostředí:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="d5b8f-196">Následující `environment` značka vykreslí přibalené a minified CSS soubory `Development`při spuštění v jiném prostředí než .</span><span class="sxs-lookup"><span data-stu-id="d5b8f-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="d5b8f-197">Například spuštění `Production` v `Staging` nebo aktivuje vykreslování těchto stylů:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="d5b8f-198">Spotřebovávat bundleconfig.json od Gulp</span><span class="sxs-lookup"><span data-stu-id="d5b8f-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="d5b8f-199">Existují případy, kdy pracovní postup sdružování a minifikace aplikace vyžaduje další zpracování.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="d5b8f-200">Mezi příklady patří optimalizace obrazu, ukládání do mezipaměti a zpracování datových zdrojů CDN.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="d5b8f-201">Chcete-li splnit tyto požadavky, můžete převést svazování a minification pracovní postup použít Gulp.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="d5b8f-202">Ruční převod sdružovacího a minifikačního pracovního postupu na použití gulpu</span><span class="sxs-lookup"><span data-stu-id="d5b8f-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="d5b8f-203">Přidejte soubor *package.json* s `devDependencies`následujícím textem do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="d5b8f-204">Modul `gulp-uglify` nepodporuje ECMAScript (ES) 2015 / ES6 a novější.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="d5b8f-205">Nainstalujte [gulp-terser](https://www.npmjs.com/package/gulp-terser) místo `gulp-uglify` použití ES2015 / ES6 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="d5b8f-206">Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.json*:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="d5b8f-207">Nainstalujte cli Gulp jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="d5b8f-208">Zkopírujte níže uvedený soubor *gulpfile.js* do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="d5b8f-209">Spuštění úloh Gulp</span><span class="sxs-lookup"><span data-stu-id="d5b8f-209">Run Gulp tasks</span></span>

<span data-ttu-id="d5b8f-210">Chcete-li spustit úlohu minifikace gulpu před sestavením projektu v sadě Visual Studio, přidejte do souboru \*.csproj následující [cíl MSBuild](/visualstudio/msbuild/msbuild-targets) Target:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="d5b8f-211">V tomto příkladu všechny `MyPreCompileTarget` úkoly definované v `Build` rámci cíle spustit před předdefinovaný cíl.</span><span class="sxs-lookup"><span data-stu-id="d5b8f-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="d5b8f-212">Výstup podobný následujícímu se zobrazí v okně Výstup sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d5b8f-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d5b8f-213">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5b8f-213">Additional resources</span></span>

* [<span data-ttu-id="d5b8f-214">Použití nástroje Grunt</span><span class="sxs-lookup"><span data-stu-id="d5b8f-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="d5b8f-215">Používání více prostředí</span><span class="sxs-lookup"><span data-stu-id="d5b8f-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="d5b8f-216">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="d5b8f-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
