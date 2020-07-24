---
title: Statické prostředky sady prostředků a minimalizuje v ASP.NET Core
author: scottaddie
description: Naučte se optimalizovat statické prostředky ve ASP.NET Core webové aplikaci, a to pomocí metod sdružování a minifikace.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: client-side/bundling-and-minification
ms.openlocfilehash: 5db6ab3d790257c677c0a4ed7e605eb39c2982ed
ms.sourcegitcommit: cc845634a490c49ff869c89b6e422b6d65d0e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87159720"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="3e2f6-103">Statické prostředky sady prostředků a minimalizuje v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e2f6-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="3e2f6-104">Autor – [Scott Addie](https://twitter.com/Scott_Addie) a [David borovice](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="3e2f6-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="3e2f6-105">Tento článek vysvětluje výhody použití sdružování a minifikace, včetně toho, jak se tyto funkce dají používat s ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="3e2f6-106">Co je sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="3e2f6-106">What is bundling and minification</span></span>

<span data-ttu-id="3e2f6-107">Sdružování a minifikace jsou dvě odlišná optimalizace výkonu, které můžete použít ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="3e2f6-108">Společně, sdružování a minifikace vylepšit výkon snížením počtu požadavků serveru a zmenšením velikosti požadovaných statických prostředků.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="3e2f6-109">Seřízení a minifikace primárně zlepšují dobu načtení první stránky.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="3e2f6-110">Po vyžádání webové stránky prohlížeč ukládá do mezipaměti statické prostředky (JavaScript, CSS a image).</span><span class="sxs-lookup"><span data-stu-id="3e2f6-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="3e2f6-111">V důsledku toho sdružování a minifikace nezlepšují výkon při požadavku na stejnou stránku nebo stránky ve stejné lokalitě, která vyžaduje stejné prostředky.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="3e2f6-112">Pokud není hlavička Expires v prostředcích správně nastavená a pokud se nepoužívá sdružování a minifikace, budou heuristické aktuálnosti v prohlížeči označovat prostředky zastaralé po několika dnech.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="3e2f6-113">Kromě toho prohlížeč vyžaduje žádost o ověření pro každý Asset.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="3e2f6-114">V tomto případě přináší sdružování a minifikace vylepšení výkonu i po první žádosti stránky.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="3e2f6-115">Sdružování</span><span class="sxs-lookup"><span data-stu-id="3e2f6-115">Bundling</span></span>

<span data-ttu-id="3e2f6-116">Sdružování kombinuje několik souborů do jediného souboru.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="3e2f6-117">Sdružování snižuje počet požadavků serveru, které jsou nezbytné k vykreslování webového prostředku, například webové stránky.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="3e2f6-118">Můžete vytvořit libovolný počet jednotlivých sad konkrétně pro šablony stylů CSS, JavaScript atd. Méně souborů znamená méně požadavků HTTP z prohlížeče na server nebo ze služby, která poskytuje vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="3e2f6-119">Výsledkem je lepší výkon při načítání první stránky.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="3e2f6-120">Minifikace</span><span class="sxs-lookup"><span data-stu-id="3e2f6-120">Minification</span></span>

<span data-ttu-id="3e2f6-121">Minifikace odebere z kódu nadbytečné znaky bez změny funkčnosti.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="3e2f6-122">Výsledkem je výrazné snížení velikosti u požadovaných prostředků (například CSS, obrázky a JavaScriptové soubory).</span><span class="sxs-lookup"><span data-stu-id="3e2f6-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="3e2f6-123">Běžné vedlejší účinky minifikace zahrnují zkrácení názvů proměnných na jeden znak a odstranění komentářů a nepotřebného prázdného místa.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="3e2f6-124">Vezměte v úvahu následující funkci JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="3e2f6-125">Minifikace snižuje funkci na následující:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="3e2f6-126">Kromě odebrání komentářů a nepotřebných prázdných znaků byly následující parametry a názvy proměnných přejmenovány takto:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="3e2f6-127">Původně</span><span class="sxs-lookup"><span data-stu-id="3e2f6-127">Original</span></span> | <span data-ttu-id="3e2f6-128">Jmenovanou</span><span class="sxs-lookup"><span data-stu-id="3e2f6-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="3e2f6-129">Dopad sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="3e2f6-129">Impact of bundling and minification</span></span>

<span data-ttu-id="3e2f6-130">Následující tabulka popisuje rozdíly mezi jednotlivými nasazováním prostředků a používáním sdružování a minifikace:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="3e2f6-131">Akce</span><span class="sxs-lookup"><span data-stu-id="3e2f6-131">Action</span></span> | <span data-ttu-id="3e2f6-132">S B/M</span><span class="sxs-lookup"><span data-stu-id="3e2f6-132">With B/M</span></span> | <span data-ttu-id="3e2f6-133">Bez B/M</span><span class="sxs-lookup"><span data-stu-id="3e2f6-133">Without B/M</span></span> | <span data-ttu-id="3e2f6-134">Změnit</span><span class="sxs-lookup"><span data-stu-id="3e2f6-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="3e2f6-135">Požadavky na soubory</span><span class="sxs-lookup"><span data-stu-id="3e2f6-135">File Requests</span></span>  | <span data-ttu-id="3e2f6-136">7</span><span class="sxs-lookup"><span data-stu-id="3e2f6-136">7</span></span>   | <span data-ttu-id="3e2f6-137">18</span><span class="sxs-lookup"><span data-stu-id="3e2f6-137">18</span></span>     | <span data-ttu-id="3e2f6-138">157%</span><span class="sxs-lookup"><span data-stu-id="3e2f6-138">157%</span></span>
<span data-ttu-id="3e2f6-139">Přenesené KB</span><span class="sxs-lookup"><span data-stu-id="3e2f6-139">KB Transferred</span></span> | <span data-ttu-id="3e2f6-140">156</span><span class="sxs-lookup"><span data-stu-id="3e2f6-140">156</span></span> | <span data-ttu-id="3e2f6-141">264,68</span><span class="sxs-lookup"><span data-stu-id="3e2f6-141">264.68</span></span> | <span data-ttu-id="3e2f6-142">70 %</span><span class="sxs-lookup"><span data-stu-id="3e2f6-142">70%</span></span>
<span data-ttu-id="3e2f6-143">Čas načtení (MS)</span><span class="sxs-lookup"><span data-stu-id="3e2f6-143">Load Time (ms)</span></span> | <span data-ttu-id="3e2f6-144">885</span><span class="sxs-lookup"><span data-stu-id="3e2f6-144">885</span></span> | <span data-ttu-id="3e2f6-145">2360</span><span class="sxs-lookup"><span data-stu-id="3e2f6-145">2360</span></span>   | <span data-ttu-id="3e2f6-146">167%</span><span class="sxs-lookup"><span data-stu-id="3e2f6-146">167%</span></span>

<span data-ttu-id="3e2f6-147">Prohlížeče jsou poměrně podrobné, s ohledem na hlavičky požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="3e2f6-148">Metrika odeslaných celkových bajtů se při sdružování výrazně snížila.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="3e2f6-149">Doba načítání ukazuje výrazné zlepšení, ale tento příklad běžel místně.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="3e2f6-150">Zvýšení výkonu se využívá při použití sdružování a minifikace s prostředky přenesených přes síť.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="3e2f6-151">Výběr strategie sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="3e2f6-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="3e2f6-152">:::no-loc(Razor):::Šablony projektů MVC a stránky poskytují řešení pro sdružování a minifikace, které se skládají z konfiguračního souboru JSON.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-152">The MVC and :::no-loc(Razor)::: Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="3e2f6-153">Nástroje třetích stran, jako je třeba Spouštěč úloh [grunt](xref:client-side/using-grunt) , provádějí stejné úlohy s větší složitou složitostí.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="3e2f6-154">Nástroj třetí strany je skvěle vhodný, když váš vývojový pracovní postup vyžaduje zpracování mimo sdružování a minifikace, jako &mdash; je linting a optimalizace obrázků.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="3e2f6-155">Když použijete sdružování a minifikace v době návrhu, vytvoří se soubory minifikovaného před nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="3e2f6-156">Sdružování a minifikace před nasazením přináší výhodu omezeného zatížení serveru.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="3e2f6-157">Je ale důležité pochopit, že sdružování v době návrhu a minifikace zvyšuje složitost sestavení a funguje jenom se statickými soubory.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="3e2f6-158">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="3e2f6-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="3e2f6-159">V ASP.NET Core 2,0 nebo starších verzích :::no-loc(Razor)::: představují šablony projektů MVC a pages *bundleconfig.jsv* konfiguračním souboru, který definuje možnosti pro jednotlivé sady prostředků:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-159">In ASP.NET Core 2.0 or earlier, the MVC and :::no-loc(Razor)::: Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3e2f6-160">V ASP.NET Core 2,1 nebo novějším přidejte nový soubor JSON s názvem *bundleconfig.js*do :::no-loc(Razor)::: kořenového adresáře projektu MVC nebo Pages.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or :::no-loc(Razor)::: Pages project root.</span></span> <span data-ttu-id="3e2f6-161">Do tohoto souboru vložte následující JSON jako výchozí bod:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="3e2f6-162">*bundleconfig.jsv* souboru definuje možnosti pro jednotlivé sady.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="3e2f6-163">V předchozím příkladu je definována jedna konfigurace sady pro vlastní soubory jazyka JavaScript (*wwwroot/js/site.js*) a šablony stylů (*wwwroot/CSS/Web. CSS*).</span><span class="sxs-lookup"><span data-stu-id="3e2f6-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="3e2f6-164">Mezi možnosti konfigurace patří:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-164">Configuration options include:</span></span>

* <span data-ttu-id="3e2f6-165">`outputFileName`: Název souboru sady pro výstup.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="3e2f6-166">Může obsahovat relativní cestu z *bundleconfig.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="3e2f6-167">**požadovanou**</span><span class="sxs-lookup"><span data-stu-id="3e2f6-167">**required**</span></span>
* <span data-ttu-id="3e2f6-168">`inputFiles`: Pole souborů, které se mají seskupit.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="3e2f6-169">Jedná se o relativní cesty ke konfiguračnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="3e2f6-170">**volitelné**, \* prázdná hodnota vede k prázdnému výstupnímu souboru.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="3e2f6-171">jsou podporovány vzory [expanze](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="3e2f6-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="3e2f6-172">`minify`: Možnosti minifikace pro typ výstupu.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="3e2f6-173">**volitelné**, \*výchozí – `minify: { enabled: true }` \*</span><span class="sxs-lookup"><span data-stu-id="3e2f6-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="3e2f6-174">Možnosti konfigurace jsou k dispozici pro jednotlivé typy výstupních souborů.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="3e2f6-175">Šablony stylů CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="3e2f6-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="3e2f6-176">JavaScriptový Minifier</span><span class="sxs-lookup"><span data-stu-id="3e2f6-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="3e2f6-177">Minifier HTML</span><span class="sxs-lookup"><span data-stu-id="3e2f6-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="3e2f6-178">`includeInProject`: Příznak označující, zda se mají přidat generované soubory do souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="3e2f6-179">**volitelné**, *výchozí – NEPRAVDA*</span><span class="sxs-lookup"><span data-stu-id="3e2f6-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="3e2f6-180">`sourceMap`: Příznak označující, zda se má generovat zdrojová mapa pro soubor zahrnutý do balíčku.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="3e2f6-181">**volitelné**, *výchozí – NEPRAVDA*</span><span class="sxs-lookup"><span data-stu-id="3e2f6-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="3e2f6-182">`sourceMapRootPath`: Kořenová cesta pro uložení vygenerovaného zdrojového souboru mapování.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="3e2f6-183">Přidat soubory do pracovního postupu</span><span class="sxs-lookup"><span data-stu-id="3e2f6-183">Add files to workflow</span></span>

<span data-ttu-id="3e2f6-184">Vezměte v úvahu příklad, ve kterém se přidá další *vlastní soubor. CSS* , který se podobá následujícímu:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="3e2f6-185">Pokud chcete minimalizuje *vlastní soubor. CSS* a seskupit ho pomocí *site. CSS* do souboru *Web. min. CSS* , přidejte relativní cestu k *bundleconfig.js*:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="3e2f6-186">Případně můžete použít následující vzor expanze názvů:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="3e2f6-187">Tento vzor expanze názvů se shoduje se všemi soubory šablon stylů CSS a vylučuje vzor souboru minifikovaného.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="3e2f6-188">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-188">Build the application.</span></span> <span data-ttu-id="3e2f6-189">Otevřete *Web. min. CSS* a Všimněte si, že obsah *vlastní. CSS* je připojen na konec souboru.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="3e2f6-190">Sdružování a minifikace založené na prostředí</span><span class="sxs-lookup"><span data-stu-id="3e2f6-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="3e2f6-191">Jako osvědčený postup by se měly v produkčním prostředí používat balíčky a minifikovaného soubory vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="3e2f6-192">Během vývoje se původní soubory vytvářejí pro snazší ladění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="3e2f6-193">Určete soubory, které chcete zahrnout na vaše stránky, pomocí [pomocné rutiny tag prostředí](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="3e2f6-194">Pomocník značek prostředí vykresluje obsah pouze při spuštění v konkrétních [prostředích](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="3e2f6-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="3e2f6-195">Následující `environment` značka vykresluje nezpracované soubory šablon stylů CSS při spuštění v `Development` prostředí:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="3e2f6-196">Následující `environment` značka vykresluje soubory šablon stylů CSS a minifikovaného při spuštění v jiném prostředí než `Development` .</span><span class="sxs-lookup"><span data-stu-id="3e2f6-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="3e2f6-197">Například spuštění v `Production` nebo `Staging` spustí vykreslování těchto šablon stylů:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="3e2f6-198">Využívání bundleconfig.jsod Gulp</span><span class="sxs-lookup"><span data-stu-id="3e2f6-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="3e2f6-199">Existují případy, kdy pracovní postup sdružování aplikace a minifikace vyžaduje další zpracování.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="3e2f6-200">Mezi příklady patří Optimalizace obrázků, busting mezipaměti a zpracování prostředků CDN.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="3e2f6-201">Aby bylo možné tyto požadavky splnit, můžete převést pracovní postup sdružování a minifikace na použití Gulp.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="3e2f6-202">Ručně převeďte pracovní postup sdružování a minifikace na použití Gulp</span><span class="sxs-lookup"><span data-stu-id="3e2f6-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="3e2f6-203">*package.json* `devDependencies` Do kořenového adresáře projektu přidejtepackage.jsdo souboru s následujícím:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="3e2f6-204">`gulp-uglify`Modul nepodporuje ECMAScript (ES) 2015/ES6 a novější.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="3e2f6-205">Nainstalujte [Gulp-terser](https://www.npmjs.com/package/gulp-terser) namísto `gulp-uglify` použití ES2015/ES6 nebo novějšího.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="3e2f6-206">Nainstalujte závislosti spuštěním následujícího příkazu na stejné úrovni jako *package.jsna*:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="3e2f6-207">Nainstalujte rozhraní příkazového řádku Gulp jako globální závislost:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-207">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="3e2f6-208">Zkopírujte soubor *gulpfile.js* níže do kořenového adresáře projektu:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="3e2f6-209">Spuštění úloh Gulp</span><span class="sxs-lookup"><span data-stu-id="3e2f6-209">Run Gulp tasks</span></span>

<span data-ttu-id="3e2f6-210">Aktivace úlohy minifikace Gulp před sestavením projektu v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-210">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="3e2f6-211">Nainstalujte balíček NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="3e2f6-211">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="3e2f6-212">Do souboru projektu přidejte následující [cíl nástroje MSBuild](/visualstudio/msbuild/msbuild-targets) :</span><span class="sxs-lookup"><span data-stu-id="3e2f6-212">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="3e2f6-213">V tomto příkladu všechny úlohy, které jsou definovány v rámci cílového prostředí, `MyPreCompileTarget` před předdefinovaným `Build` cílem.</span><span class="sxs-lookup"><span data-stu-id="3e2f6-213">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="3e2f6-214">Výstup podobný následujícímu se zobrazí v okně výstupu sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3e2f6-214">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3e2f6-215">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3e2f6-215">Additional resources</span></span>

* [<span data-ttu-id="3e2f6-216">Použití nástroje Grunt</span><span class="sxs-lookup"><span data-stu-id="3e2f6-216">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="3e2f6-217">Používání více prostředí</span><span class="sxs-lookup"><span data-stu-id="3e2f6-217">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="3e2f6-218">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="3e2f6-218">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
