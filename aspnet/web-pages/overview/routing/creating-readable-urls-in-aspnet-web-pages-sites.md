---
uid: web-pages/overview/routing/creating-readable-urls-in-aspnet-web-pages-sites
title: Vytvoření čitelných adres URL v rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje směrování ve na webu rozhraní ASP.NET Web Pages (Razor) a jak to vám umožní používat adresy URL, které jsou čitelnější a lépe pro SEO. Co budete...
ms.author: riande
ms.date: 02/17/2014
ms.assetid: a8aac1ac-89de-4415-afe0-97a41c6423d2
msc.legacyurl: /web-pages/overview/routing/creating-readable-urls-in-aspnet-web-pages-sites
msc.type: authoredcontent
ms.openlocfilehash: 26d8f94b2e38fe5205a37e3d37b4e3bd509a3874
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021078"
---
<a name="creating-readable-urls-in-aspnet-web-pages-razor-sites"></a><span data-ttu-id="02b06-104">Vytvoření čitelných adres URL webů s ASP.NET webovými stránkami (Razor)</span><span class="sxs-lookup"><span data-stu-id="02b06-104">Creating Readable URLs in ASP.NET Web Pages (Razor) Sites</span></span>
====================
<span data-ttu-id="02b06-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="02b06-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="02b06-106">Tento článek popisuje směrování ve na webu rozhraní ASP.NET Web Pages (Razor) a jak to vám umožní používat adresy URL, které jsou čitelnější a lépe pro SEO.</span><span class="sxs-lookup"><span data-stu-id="02b06-106">This article describes routing in an ASP.NET Web Pages (Razor) website, and how this lets you use URLs that are more readable and better for SEO.</span></span>
> 
> <span data-ttu-id="02b06-107">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="02b06-107">What you'll learn:</span></span>
> 
> - <span data-ttu-id="02b06-108">Jak ASP.NET používá směrování, aby vám umožňují používat více čitelný a dohledatelné adresy URL.</span><span class="sxs-lookup"><span data-stu-id="02b06-108">How ASP.NET uses routing to let you use more readable and searchable URLs.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="02b06-109">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="02b06-109">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="02b06-110">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="02b06-110">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="02b06-111">V tomto kurzu se také pracuje s ASP.NET Web Pages 2.</span><span class="sxs-lookup"><span data-stu-id="02b06-111">This tutorial also works with ASP.NET Web Pages 2.</span></span>


## <a name="about-routing"></a><span data-ttu-id="02b06-112">O směrování</span><span class="sxs-lookup"><span data-stu-id="02b06-112">About Routing</span></span>

<span data-ttu-id="02b06-113">Adresy URL pro stránky ve vaší lokalitě může mít dopad na tom, jak dobře funguje webu.</span><span class="sxs-lookup"><span data-stu-id="02b06-113">The URLs for the pages in your site can have an impact on how well the site works.</span></span> <span data-ttu-id="02b06-114">Adresa URL, která &quot;popisný&quot; usnadnit uživatelům webu.</span><span class="sxs-lookup"><span data-stu-id="02b06-114">A URL that's &quot;friendly&quot; can make it easier for people to use the site.</span></span> <span data-ttu-id="02b06-115">Může také pomoct s optimalizací vyhledávání (SEO) pro lokalitu.</span><span class="sxs-lookup"><span data-stu-id="02b06-115">It can also help with search-engine optimization (SEO) for the site.</span></span> <span data-ttu-id="02b06-116">Webů ASP.NET zahrnuje možnost automaticky používat přátelské adresy URL.</span><span class="sxs-lookup"><span data-stu-id="02b06-116">ASP.NET websites include the ability to use friendly URLs automatically.</span></span>

<span data-ttu-id="02b06-117">Technologie ASP.NET umožňuje vytvářet smysluplné adresy URL, které popisují akce uživatelů místo jenom odkazuje na soubor na serveru.</span><span class="sxs-lookup"><span data-stu-id="02b06-117">ASP.NET lets you create meaningful URLs that describe user actions instead of just pointing to a file on the server.</span></span> <span data-ttu-id="02b06-118">Vezměte v úvahu tyto adresy URL pro fiktivní blog:</span><span class="sxs-lookup"><span data-stu-id="02b06-118">Consider these URLs for a fictional blog:</span></span>

- `http://www.contoso.com/Blog/blog.cshtml?categories=hardware`
- `http://www.contoso.com//Blog/blog.cshtml?startdate=2009-11-01&enddate=2009-11-30`

<span data-ttu-id="02b06-119">Porovnání těchto adres URL pro následující dotazy:</span><span class="sxs-lookup"><span data-stu-id="02b06-119">Compare those URLs to the following ones:</span></span>

- `http://www.contoso.com/Blog/categories/hardware/`
- `http://www.contoso.com/Blog/2009/November`

<span data-ttu-id="02b06-120">V první pár uživatel musel vědět, že se zobrazí v blogu pomocí *blog.cshtml* stránce a pak je nutné vytvořit řetězec dotazu, který získá správné kategorie nebo rozsah.</span><span class="sxs-lookup"><span data-stu-id="02b06-120">In the first pair, a user would have to know that the blog is displayed using the *blog.cshtml* page, and would then have to construct a query string that gets the right category or date range.</span></span> <span data-ttu-id="02b06-121">Druhá řada příklady jsou mnohem snadněji pochopit a vytvořit.</span><span class="sxs-lookup"><span data-stu-id="02b06-121">The second set of examples is much easier to comprehend and create.</span></span>

<span data-ttu-id="02b06-122">Adresy URL pro první příklad také odkazovat přímo na konkrétní soubor (*blog.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="02b06-122">The URLs for the first example also point directly to a specific file (*blog.cshtml*).</span></span> <span data-ttu-id="02b06-123">Pokud z nějakého důvodu blogu byly přesunuty do jiné složky na serveru nebo na blogu nebyly přepsány, aby bylo použít jinou stránku, odkazy by být nesprávné.</span><span class="sxs-lookup"><span data-stu-id="02b06-123">If for some reason the blog were moved to another folder on the server, or if the blog were rewritten to use a different page, the links would be wrong.</span></span> <span data-ttu-id="02b06-124">Druhá sada adres URL neodkazuje na konkrétní stránce, tak i v případě implementace blogu nebo umístění změní, adresy URL by stále platit.</span><span class="sxs-lookup"><span data-stu-id="02b06-124">The second set of URLs doesn't point to a specific page, so even if the blog implementation or location changes, the URLs would still be valid.</span></span>

<span data-ttu-id="02b06-125">ASP.NET Web Pages, můžete vytvořit příjemnější adresy URL jako v předchozích příkladech vzhledem k tomu používá ASP.NET *směrování*.</span><span class="sxs-lookup"><span data-stu-id="02b06-125">In ASP.NET Web Pages, you can create friendlier URLs like those in the above examples because ASP.NET uses *routing*.</span></span> <span data-ttu-id="02b06-126">Směrování vytvoří logické mapování z adresy URL stránky (nebo stránky), který požadavek můžete splnit.</span><span class="sxs-lookup"><span data-stu-id="02b06-126">Routing creates logical mapping from a URL to a page (or pages) that can fulfill the request.</span></span> <span data-ttu-id="02b06-127">Protože je logické mapování (ne z fyzických prostředků, do konkrétního souboru), směrování poskytuje velmi flexibilní způsob definování adresy URL pro váš web.</span><span class="sxs-lookup"><span data-stu-id="02b06-127">Because the mapping is logical (not physical, to a specific file), routing provides great flexibility in how you define the URLs for your site.</span></span>

## <a name="how-routing-works"></a><span data-ttu-id="02b06-128">Jak funguje směrování</span><span class="sxs-lookup"><span data-stu-id="02b06-128">How Routing Works</span></span>

<span data-ttu-id="02b06-129">Když ASP.NET zpracovává žádost, načte adresu URL a zjistěte, jak ho směrovat.</span><span class="sxs-lookup"><span data-stu-id="02b06-129">When ASP.NET processes a request, it reads the URL to determine how to route it.</span></span> <span data-ttu-id="02b06-130">Technologie ASP.NET pokusí porovnat jednotlivých segmentů adresy URL pro soubory na disku, bude zleva doprava.</span><span class="sxs-lookup"><span data-stu-id="02b06-130">ASP.NET tries to match individual segments of the URL to files on disk, going from left to right.</span></span> <span data-ttu-id="02b06-131">Pokud se zjistí shoda, nic zbývajících v adrese URL je předána stránce jako *informace o cestě*.</span><span class="sxs-lookup"><span data-stu-id="02b06-131">If there's a match, anything remaining in the URL is passed to the page as *path information*.</span></span>

<span data-ttu-id="02b06-132">Představte si, že někdo vytvoří žádost o tuto adresu URL:</span><span class="sxs-lookup"><span data-stu-id="02b06-132">Imagine that someone makes a request using this URL:</span></span>

`http://www.contoso.com/a/b/c`

<span data-ttu-id="02b06-133">Hledání prochází tímto způsobem:</span><span class="sxs-lookup"><span data-stu-id="02b06-133">The search goes like this:</span></span>

1. <span data-ttu-id="02b06-134">Je k dispozici soubor s cestou a názvem */a/b/c.cshtml*?</span><span class="sxs-lookup"><span data-stu-id="02b06-134">Is there a file with the path and name of */a/b/c.cshtml*?</span></span> <span data-ttu-id="02b06-135">Pokud ano, spusťte tuto stránku a předat žádné informace.</span><span class="sxs-lookup"><span data-stu-id="02b06-135">If so, run that page and pass no information to it.</span></span> <span data-ttu-id="02b06-136">V opačném případě...</span><span class="sxs-lookup"><span data-stu-id="02b06-136">Otherwise ...</span></span>
2. <span data-ttu-id="02b06-137">Je k dispozici soubor s cestou a názvem */a/b.cshtml*?</span><span class="sxs-lookup"><span data-stu-id="02b06-137">Is there a file with the path and name of */a/b.cshtml*?</span></span> <span data-ttu-id="02b06-138">Pokud ano, spusťte tuto stránku a předejte hodnotu `c` k němu.</span><span class="sxs-lookup"><span data-stu-id="02b06-138">If so, run that page and pass the value `c` to it.</span></span> <span data-ttu-id="02b06-139">V opačném případě...</span><span class="sxs-lookup"><span data-stu-id="02b06-139">Otherwise …</span></span>
3. <span data-ttu-id="02b06-140">Je k dispozici soubor s cestou a názvem */a.cshtml*?</span><span class="sxs-lookup"><span data-stu-id="02b06-140">Is there a file with the path and name of */a.cshtml*?</span></span> <span data-ttu-id="02b06-141">Pokud ano, spusťte tuto stránku a předejte hodnotu `b/c` k němu.</span><span class="sxs-lookup"><span data-stu-id="02b06-141">If so, run that page and pass the value `b/c` to it.</span></span>

<span data-ttu-id="02b06-142">Pokud hledání nenašel žádné přesně odpovídá *.cshtml* soubory v jejich zadaných složek ASP.NET pokračuje v hledání pro tyto soubory pak:</span><span class="sxs-lookup"><span data-stu-id="02b06-142">If the search found no exact matches for *.cshtml* files in their specified folders, ASP.NET continues looking for these files in turn:</span></span>

1. <span data-ttu-id="02b06-143">*/a/b/c/default.cshtml* (informace o cestě).</span><span class="sxs-lookup"><span data-stu-id="02b06-143">*/a/b/c/default.cshtml* (no path information).</span></span>
2. <span data-ttu-id="02b06-144">*/a/b/c/index.cshtml* (informace o cestě).</span><span class="sxs-lookup"><span data-stu-id="02b06-144">*/a/b/c/index.cshtml* (no path information).</span></span>

> [!NOTE]
> <span data-ttu-id="02b06-145">Jasno, požadavky na konkrétní stránky (to znamená, požadavky, které obsahují *.cshtml* příponu názvu souboru) pracovat stejně jako byste očekávali.</span><span class="sxs-lookup"><span data-stu-id="02b06-145">To be clear, requests for specific pages (that is, requests that include the *.cshtml* filename extension) work just like you'd expect.</span></span> <span data-ttu-id="02b06-146">Žádost, jako jsou `http://www.contoso.com/a/b.cshtml` poběží na stránce *b.cshtml* zcela v pořádku.</span><span class="sxs-lookup"><span data-stu-id="02b06-146">A request like `http://www.contoso.com/a/b.cshtml` will run the page *b.cshtml* just fine.</span></span>


<span data-ttu-id="02b06-147">Uvnitř stránky, můžete získat informace o cestě na stránce `UrlData` vlastnost, která je do slovníku.</span><span class="sxs-lookup"><span data-stu-id="02b06-147">Inside a page, you can get the path information via the page's `UrlData` property, which is a dictionary.</span></span> <span data-ttu-id="02b06-148">Představte si, že máte soubor s názvem *ViewCustomers.cshtml* a webu získá této žádosti:</span><span class="sxs-lookup"><span data-stu-id="02b06-148">Imagine that you have a file named *ViewCustomers.cshtml* and your site gets this request:</span></span>

`http://mysite.com/myWebSite/ViewCustomers/1000`

<span data-ttu-id="02b06-149">Podle popisu ve výše uvedených pravidel, požadavek bude přejděte na stránku.</span><span class="sxs-lookup"><span data-stu-id="02b06-149">As described in the rules above, the request will go to your page.</span></span> <span data-ttu-id="02b06-150">Na stránce můžete použít k získání a zobrazit informace o cestě kód podobný tomuto (v tomto případě, hodnota &quot;1000&quot;):</span><span class="sxs-lookup"><span data-stu-id="02b06-150">Inside the page, you can use code like the following to get and display the path information (in this case, the value &quot;1000&quot;):</span></span>

[!code-html[Main](creating-readable-urls-in-aspnet-web-pages-sites/samples/sample1.html)]

> [!NOTE]
> <span data-ttu-id="02b06-151">Protože směrování nezahrnuje celý názvy, může být nejednoznačnost Pokud máte stránky, které mají stejné ale různé přípony názvu souboru (například *MyPage.cshtml* a *MyPage.html*) .</span><span class="sxs-lookup"><span data-stu-id="02b06-151">Because routing doesn't involve complete file names, there can be ambiguity if you have pages that have the same name but different file-name extensions (for example, *MyPage.cshtml* and *MyPage.html*).</span></span> <span data-ttu-id="02b06-152">Pokud se chcete vyhnout problémy se směrováním, je nejlepší, abyste měli jistotu, že není nutné stránek ve vaší lokalitě, jejichž názvy se liší pouze velikostí svého rozšíření.</span><span class="sxs-lookup"><span data-stu-id="02b06-152">In order to avoid problems with routing, it's best to make sure that you don't have pages in your site whose names differ only in their extension.</span></span>


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a><span data-ttu-id="02b06-153">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="02b06-153">Additional Resources</span></span>

<span data-ttu-id="02b06-154">[Služba WebMatrix – adresy URL, UrlData a směrování pro SEO](http://www.mikesdotnetting.com/Article/165/WebMatrix-URLs-UrlData-and-Routing-for-SEO).</span><span class="sxs-lookup"><span data-stu-id="02b06-154">[WebMatrix - URLs, UrlData and Routing for SEO](http://www.mikesdotnetting.com/Article/165/WebMatrix-URLs-UrlData-and-Routing-for-SEO).</span></span> <span data-ttu-id="02b06-155">Tuto položku blogu podle Mike Brind poskytuje některé další podrobnosti o tom, jak směrování funguje v ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="02b06-155">This blog entry by Mike Brind provides some additional details on how routing works in ASP.NET Web Pages.</span></span>
