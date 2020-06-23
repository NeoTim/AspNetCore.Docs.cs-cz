---
title: Hostování a nasazení ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7e0263200ebb9ce60f7234af3cbb18c5aeaa3e09
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243522"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7f60e-103">Hostování a nasazení ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7f60e-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7f60e-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Skořepa](https://github.com/danroth27), [Robert Adams](https://twitter.com/ben_a_adams)a [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="7f60e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="7f60e-105">S [ Blazor modelem hostování WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="7f60e-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="7f60e-106">BlazorAplikace, její závislosti a modul runtime .NET jsou stahovány do prohlížeče paralelně.</span><span class="sxs-lookup"><span data-stu-id="7f60e-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="7f60e-107">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7f60e-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="7f60e-108">Podporují se tyto strategie nasazení:</span><span class="sxs-lookup"><span data-stu-id="7f60e-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="7f60e-109">BlazorAplikaci obsluhuje aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f60e-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="7f60e-110">Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="7f60e-111">BlazorAplikace se umístí na statický hostující webový server nebo službu, kde rozhraní .NET se k obsluze aplikace nepoužívá Blazor .</span><span class="sxs-lookup"><span data-stu-id="7f60e-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="7f60e-112">Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování Blazor aplikace WebAssembly jako dílčí aplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="7f60e-113">Komprese</span><span class="sxs-lookup"><span data-stu-id="7f60e-113">Compression</span></span>

<span data-ttu-id="7f60e-114">Když Blazor je publikována aplikace WebAssembly, výstup je během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="7f60e-115">Používají se následující kompresní algoritmy:</span><span class="sxs-lookup"><span data-stu-id="7f60e-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="7f60e-116">[Brotli](https://tools.ietf.org/html/rfc7932) (nejvyšší úroveň)</span><span class="sxs-lookup"><span data-stu-id="7f60e-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="7f60e-117">GZIP</span><span class="sxs-lookup"><span data-stu-id="7f60e-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="7f60e-118">spoléhá na hostitele, který obsluhuje příslušné komprimované soubory.</span><span class="sxs-lookup"><span data-stu-id="7f60e-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="7f60e-119">Při použití hostovaného projektu ASP.NET Core je hostitelský projekt schopný provádět vyjednávání obsahu a obsluhovat staticky komprimované soubory.</span><span class="sxs-lookup"><span data-stu-id="7f60e-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="7f60e-120">Při hostování Blazor samostatné aplikace WebAssembly může být nutné provést další práci, aby bylo zajištěno, že budou obsluhovány staticky komprimované soubory:</span><span class="sxs-lookup"><span data-stu-id="7f60e-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="7f60e-121">`web.config`Konfiguraci komprese služby IIS najdete v části [IIS: Brotli a komprese GZip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="7f60e-122">Při hostování řešení statického hostování, které nepodporují vyjednávání se staticky komprimovaným souborem, jako jsou stránky GitHubu, zvažte konfiguraci aplikace pro načtení a dekódování Brotli komprimovaných souborů:</span><span class="sxs-lookup"><span data-stu-id="7f60e-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="7f60e-123">Odkažte na dekodér Brotli z [úložiště GitHub Google/Brotli](https://github.com/google/brotli/) v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7f60e-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="7f60e-124">Aktualizujte aplikaci tak, aby používala dekodér.</span><span class="sxs-lookup"><span data-stu-id="7f60e-124">Update the app to use the decoder.</span></span> <span data-ttu-id="7f60e-125">Změňte značku uvnitř uzavírací `<body>` značky v `wwwroot/index.html` následujícím formátu:</span><span class="sxs-lookup"><span data-stu-id="7f60e-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
          'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
          { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
  </script>
  ```
   
<span data-ttu-id="7f60e-126">Chcete-li vypnout kompresi, přidejte `BlazorEnableCompression` do souboru projektu aplikace vlastnost MSBuild a nastavte hodnotu na `false` :</span><span class="sxs-lookup"><span data-stu-id="7f60e-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="7f60e-127">Přepište adresy URL pro správné směrování.</span><span class="sxs-lookup"><span data-stu-id="7f60e-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="7f60e-128">Žádosti o směrování pro součásti stránky v Blazor aplikaci WebAssembly nejsou tak jednoduché jako požadavky směrování na serveru, v němž je Blazor umístěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="7f60e-129">Zvažte Blazor aplikaci WebAssembly se dvěma součástmi:</span><span class="sxs-lookup"><span data-stu-id="7f60e-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="7f60e-130">`Main.razor`: Načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="7f60e-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="7f60e-131">`About.razor`: `About` součást.</span><span class="sxs-lookup"><span data-stu-id="7f60e-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="7f60e-132">Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="7f60e-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="7f60e-133">Prohlížeč vytvoří požadavek.</span><span class="sxs-lookup"><span data-stu-id="7f60e-133">The browser makes a request.</span></span>
1. <span data-ttu-id="7f60e-134">Vrátí se výchozí stránka, což je obvykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="7f60e-135">`index.html`napředá aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7f60e-135">`index.html` bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="7f60e-136">se načte směrovač a Razor `Main` Komponenta se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="7f60e-136">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="7f60e-137">Na hlavní stránce vyberte odkaz na `About` komponentu na klientovi, protože Blazor směrovač zastaví v prohlížeči, aby odeslal požadavek na Internet `www.contoso.com` pro `About` a sloužil přímo vykreslené `About` součásti.</span><span class="sxs-lookup"><span data-stu-id="7f60e-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="7f60e-138">Všechny požadavky na vnitřní koncové body *v Blazor aplikaci WebAssembly* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="7f60e-139">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="7f60e-139">The router handles the requests internally.</span></span>

<span data-ttu-id="7f60e-140">Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About` , požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="7f60e-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="7f60e-141">Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="7f60e-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="7f60e-142">Vzhledem k tomu, že prohlížeče připravují žádosti na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou na stránce fyzicky na serveru `index.html` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="7f60e-143">Když `index.html` se vrátí, Blazor směrovač aplikace převezme a odpoví správným prostředkem.</span><span class="sxs-lookup"><span data-stu-id="7f60e-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="7f60e-144">Při nasazování na server služby IIS můžete použít modul pro přepsání adresy URL s publikovaným `web.config` souborem aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="7f60e-145">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="7f60e-146">Hostované nasazení s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f60e-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="7f60e-147">*Hostované nasazení* obsluhuje Blazor aplikaci WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="7f60e-148">Klientská Blazor aplikace WebAssembly je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="7f60e-149">Obě aplikace se nasazují dohromady.</span><span class="sxs-lookup"><span data-stu-id="7f60e-149">The two apps are deployed together.</span></span> <span data-ttu-id="7f60e-150">Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f60e-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="7f60e-151">V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu \*\* Blazor aplikace pro WebAssembly\*\* ( `blazorwasm` Šablona při použití [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu) s **`Hosted`** vybranou možností ( `-ho|--hosted` při použití `dotnet new` příkazu).</span><span class="sxs-lookup"><span data-stu-id="7f60e-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="7f60e-152">Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="7f60e-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="7f60e-153">Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="7f60e-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="7f60e-154">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="7f60e-154">Standalone deployment</span></span>

<span data-ttu-id="7f60e-155">*Samostatné nasazení* obsluhuje Blazor aplikaci WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="7f60e-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="7f60e-156">Každý statický souborový server může Blazor aplikaci zpracovat.</span><span class="sxs-lookup"><span data-stu-id="7f60e-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="7f60e-157">Samostatné prostředky nasazení jsou publikovány do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="7f60e-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="7f60e-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7f60e-158">Azure App Service</span></span>

Blazor<span data-ttu-id="7f60e-159">Aplikace WebAssembly se dají nasadit do Azure App Services v systému Windows, který hostuje aplikaci ve [službě IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="7f60e-159"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="7f60e-160">Nasazení samostatné Blazor aplikace WebAssembly do Azure App Service pro Linux není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="7f60e-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="7f60e-161">Image serveru pro Linux, která je hostitelem aplikace, není v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7f60e-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="7f60e-162">Pro povolení tohoto scénáře probíhá práce.</span><span class="sxs-lookup"><span data-stu-id="7f60e-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="7f60e-163">IIS</span><span class="sxs-lookup"><span data-stu-id="7f60e-163">IIS</span></span>

<span data-ttu-id="7f60e-164">Služba IIS je schopným statickým souborovým serverem pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="7f60e-165">Chcete-li nakonfigurovat službu IIS na hostování Blazor , přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="7f60e-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="7f60e-166">Publikované assety se vytvoří ve `/bin/Release/{TARGET FRAMEWORK}/publish` složce.</span><span class="sxs-lookup"><span data-stu-id="7f60e-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="7f60e-167">Hostovat obsah `publish` složky na webovém serveru nebo v hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="7f60e-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="7f60e-168">web.config</span><span class="sxs-lookup"><span data-stu-id="7f60e-168">web.config</span></span>

<span data-ttu-id="7f60e-169">Při Blazor publikování projektu se vytvoří `web.config` soubor s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="7f60e-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="7f60e-170">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="7f60e-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="7f60e-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="7f60e-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="7f60e-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="7f60e-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="7f60e-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="7f60e-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="7f60e-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7f60e-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="7f60e-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7f60e-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="7f60e-176">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="7f60e-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="7f60e-177">Pravidla pro přepis adres URL jsou navázána:</span><span class="sxs-lookup"><span data-stu-id="7f60e-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="7f60e-178">Slouží jako podadresáře, kde se nachází statické prostředky aplikace ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="7f60e-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="7f60e-179">Vytvořte záložní záložní postup, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce static assets ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="7f60e-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="7f60e-180">Použití vlastního web.config</span><span class="sxs-lookup"><span data-stu-id="7f60e-180">Use a custom web.config</span></span>

<span data-ttu-id="7f60e-181">Chcete-li použít vlastní `web.config` soubor, umístěte vlastní `web.config` soubor do kořenové složky projektu a publikujte projekt.</span><span class="sxs-lookup"><span data-stu-id="7f60e-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="7f60e-182">Instalace modulu URL pro přepis</span><span class="sxs-lookup"><span data-stu-id="7f60e-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="7f60e-183">Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="7f60e-184">Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="7f60e-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="7f60e-185">Modul se musí stáhnout z webu IIS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="7f60e-186">K instalaci modulu použijte instalační program webové platformy:</span><span class="sxs-lookup"><span data-stu-id="7f60e-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="7f60e-187">Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="7f60e-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="7f60e-188">V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="7f60e-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="7f60e-189">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.</span><span class="sxs-lookup"><span data-stu-id="7f60e-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="7f60e-190">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="7f60e-190">Copy the installer to the server.</span></span> <span data-ttu-id="7f60e-191">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="7f60e-191">Run the installer.</span></span> <span data-ttu-id="7f60e-192">Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="7f60e-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="7f60e-193">Po dokončení instalace není restartování serveru vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="7f60e-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="7f60e-194">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="7f60e-194">Configure the website</span></span>

<span data-ttu-id="7f60e-195">Nastavte **fyzickou cestu** webu na složku aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="7f60e-196">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="7f60e-196">The folder contains:</span></span>

* <span data-ttu-id="7f60e-197">`web.config`Soubor, který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="7f60e-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="7f60e-198">Složka statických prostředků aplikace</span><span class="sxs-lookup"><span data-stu-id="7f60e-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="7f60e-199">Hostování jako dílčí aplikace služby IIS</span><span class="sxs-lookup"><span data-stu-id="7f60e-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="7f60e-200">Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7f60e-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="7f60e-201">Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7f60e-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="7f60e-202">Odeberte obslužnou rutinu v Blazor publikovaném `web.config` souboru aplikace tak, že `<handlers>` do souboru přidáte oddíl:</span><span class="sxs-lookup"><span data-stu-id="7f60e-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="7f60e-203">Zakažte dědění kořenového oddílu (nadřazené) aplikace `<system.webServer>` pomocí `<location>` elementu s `inheritInChildApplications` nastavením na `false` :</span><span class="sxs-lookup"><span data-stu-id="7f60e-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="7f60e-204">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="7f60e-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="7f60e-205">Nastavte základní cestu aplikace v `index.html` souboru aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="7f60e-206">Komprese Brotli a gzip</span><span class="sxs-lookup"><span data-stu-id="7f60e-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="7f60e-207">Službu IIS je možné nakonfigurovat prostřednictvím služby `web.config` za účelem poskytování komprimovaných prostředků Brotli nebo gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="7f60e-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="7f60e-208">Příklad konfigurace najdete v tématu [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="7f60e-209">Odstraňování potíží</span><span class="sxs-lookup"><span data-stu-id="7f60e-209">Troubleshooting</span></span>

<span data-ttu-id="7f60e-210">Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis.</span><span class="sxs-lookup"><span data-stu-id="7f60e-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="7f60e-211">Pokud modul není nainstalován, `web.config` soubor nelze analyzovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="7f60e-212">Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web ze Blazor statických souborů obsluhy.</span><span class="sxs-lookup"><span data-stu-id="7f60e-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="7f60e-213">Další informace o řešení potíží s nasazeními služby IIS najdete v tématu <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="7f60e-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="7f60e-214">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="7f60e-214">Azure Storage</span></span>

<span data-ttu-id="7f60e-215">Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje Blazor hostování aplikací bez serveru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="7f60e-216">Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7f60e-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="7f60e-217">Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="7f60e-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="7f60e-218">Nastavte **název dokumentu indexu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="7f60e-219">Nastavte **cestu k chybovému dokumentu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-219">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="7f60e-220">součásti a jiné koncové body, které nejsou v souboru, se neukládají na fyzických cestách ve statickém obsahu uloženém službou BLOB Service.</span><span class="sxs-lookup"><span data-stu-id="7f60e-220"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="7f60e-221">Když se přijme žádost o jeden z těchto prostředků, kterou Blazor by měl směrovač zpracovat, chyba *404 – nenalezená* služba BLOB Service směruje požadavek na **cestu k chybovému dokumentu**.</span><span class="sxs-lookup"><span data-stu-id="7f60e-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="7f60e-222">`index.html`Vrátí se objekt BLOB a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="7f60e-223">Pokud nejsou za běhu načteny soubory z důvodu nevhodných typů MIME v `Content-Type` hlavičkách souborů, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7f60e-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="7f60e-224">Nakonfigurujte nástroje tak, aby při nasazení souborů nastavily správné typy MIME ( `Content-Type` hlavičky).</span><span class="sxs-lookup"><span data-stu-id="7f60e-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="7f60e-225">Změňte typy MIME ( `Content-Type` hlavičky) souborů po nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="7f60e-226">V Průzkumník služby Storage (Azure Portal) pro každý soubor:</span><span class="sxs-lookup"><span data-stu-id="7f60e-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="7f60e-227">Klikněte na soubor pravým tlačítkem a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="7f60e-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="7f60e-228">Nastavte **ContentType** a klikněte na tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="7f60e-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="7f60e-229">Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="7f60e-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="7f60e-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="7f60e-230">Nginx</span></span>

<span data-ttu-id="7f60e-231">Následující `nginx.conf` soubor je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání `index.html` souboru pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="7f60e-232">Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="7f60e-232">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="7f60e-233">Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="7f60e-233">Nginx in Docker</span></span>

<span data-ttu-id="7f60e-234">Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile na použití Nginx image založené na Alpine.</span><span class="sxs-lookup"><span data-stu-id="7f60e-234">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="7f60e-235">Aktualizujte souboru Dockerfile tak, aby se `nginx.config` soubor zkopíroval do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-235">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="7f60e-236">Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7f60e-236">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="7f60e-237">Apache</span><span class="sxs-lookup"><span data-stu-id="7f60e-237">Apache</span></span>

<span data-ttu-id="7f60e-238">Nasazení Blazor aplikace WebAssembly na CentOS 7 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="7f60e-238">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="7f60e-239">Vytvořte konfigurační soubor Apache.</span><span class="sxs-lookup"><span data-stu-id="7f60e-239">Create the Apache configuration file.</span></span> <span data-ttu-id="7f60e-240">V následujícím příkladu je zjednodušený konfigurační soubor ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="7f60e-240">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="7f60e-241">Soubor konfigurace Apache umístěte do `/etc/httpd/conf.d/` adresáře, který je výchozím adresářem konfigurace Apache v CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="7f60e-241">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="7f60e-242">Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění zadaného do `DocumentRoot` konfiguračního souboru).</span><span class="sxs-lookup"><span data-stu-id="7f60e-242">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="7f60e-243">Restartujte službu Apache.</span><span class="sxs-lookup"><span data-stu-id="7f60e-243">Restart the Apache service.</span></span>

<span data-ttu-id="7f60e-244">Další informace najdete v tématech [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="7f60e-244">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="7f60e-245">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="7f60e-245">GitHub Pages</span></span>

<span data-ttu-id="7f60e-246">Chcete-li zpracovat přepisy adresy URL, přidejte `404.html` soubor se skriptem, který zpracovává přesměrování požadavku na `index.html` stránku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-246">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="7f60e-247">Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="7f60e-247">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="7f60e-248">Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="7f60e-248">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="7f60e-249">Při použití webu projektu místo webu organizace přidejte nebo aktualizujte `<base>` značku v `index.html` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-249">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="7f60e-250">Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-250">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="7f60e-251">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="7f60e-251">Host configuration values</span></span>

<span data-ttu-id="7f60e-252">[ Blazor Aplikace WebAssembly](xref:blazor/hosting-models#blazor-webassembly) můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="7f60e-252">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="7f60e-253">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="7f60e-253">Content root</span></span>

<span data-ttu-id="7f60e-254">`--contentroot`Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="7f60e-254">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="7f60e-255">V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-255">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="7f60e-256">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7f60e-257">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="7f60e-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="7f60e-258">Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="7f60e-258">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7f60e-259">Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-259">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="7f60e-260">V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="7f60e-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7f60e-261">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.</span><span class="sxs-lookup"><span data-stu-id="7f60e-261">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="7f60e-262">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="7f60e-262">Path base</span></span>

<span data-ttu-id="7f60e-263">`--pathbase`Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL ( `<base>` značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční).</span><span class="sxs-lookup"><span data-stu-id="7f60e-263">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="7f60e-264">V následujících příkladech `/relative-URL-path` je základ cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="7f60e-264">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="7f60e-265">Další informace najdete v tématu [základní cesta k aplikaci](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="7f60e-265">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7f60e-266">Na rozdíl od cesty, která je k dispozici pro `href` `<base>` značku, nezahrnujte koncové lomítko ( `/` ) při předávání `--pathbase` hodnoty argumentu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-266">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="7f60e-267">Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="7f60e-267">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="7f60e-268">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-268">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7f60e-269">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="7f60e-269">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="7f60e-270">Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="7f60e-270">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7f60e-271">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-271">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="7f60e-272">V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="7f60e-272">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7f60e-273">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.</span><span class="sxs-lookup"><span data-stu-id="7f60e-273">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="7f60e-274">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="7f60e-274">URLs</span></span>

<span data-ttu-id="7f60e-275">`--urls`Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.</span><span class="sxs-lookup"><span data-stu-id="7f60e-275">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="7f60e-276">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-276">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7f60e-277">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="7f60e-277">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="7f60e-278">Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="7f60e-278">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7f60e-279">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-279">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="7f60e-280">V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="7f60e-280">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7f60e-281">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.</span><span class="sxs-lookup"><span data-stu-id="7f60e-281">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="7f60e-282">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="7f60e-282">Configure the Linker</span></span>

Blazor<span data-ttu-id="7f60e-283">provede propojení s mezijazykem (IL) na každém sestavení vydaných verzí a odebere z výstupních sestavení zbytečné IL.</span><span class="sxs-lookup"><span data-stu-id="7f60e-283"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="7f60e-284">Další informace naleznete v tématu <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="7f60e-284">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="7f60e-285">Načítání vlastního spouštěcího prostředku</span><span class="sxs-lookup"><span data-stu-id="7f60e-285">Custom boot resource loading</span></span>

<span data-ttu-id="7f60e-286">BlazorAplikaci WebAssembly lze inicializovat pomocí `loadBootResource` funkce pro přepsání vestavěného mechanismu načítání prostředků spouštění.</span><span class="sxs-lookup"><span data-stu-id="7f60e-286">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="7f60e-287">Použijte `loadBootResource` v následujících případech:</span><span class="sxs-lookup"><span data-stu-id="7f60e-287">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="7f60e-288">Umožňuje uživatelům načíst statické prostředky, jako jsou například data o časovém pásmu nebo `dotnet.wasm` síť CDN.</span><span class="sxs-lookup"><span data-stu-id="7f60e-288">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="7f60e-289">Načtěte komprimovaná sestavení pomocí požadavku HTTP a dekomprimujte je v klientovi pro hostitele, kteří nepodporují načítání komprimovaného obsahu ze serveru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-289">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="7f60e-290">Aliasujte prostředky na jiný název tak, že každý požadavek přesměrujete `fetch` na nový název.</span><span class="sxs-lookup"><span data-stu-id="7f60e-290">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="7f60e-291">`loadBootResource`parametry jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="7f60e-291">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="7f60e-292">Parametr</span><span class="sxs-lookup"><span data-stu-id="7f60e-292">Parameter</span></span>    | <span data-ttu-id="7f60e-293">Popis</span><span class="sxs-lookup"><span data-stu-id="7f60e-293">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="7f60e-294">Typ prostředku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-294">The type of the resource.</span></span> <span data-ttu-id="7f60e-295">Permissable typy: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="7f60e-295">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="7f60e-296">Název prostředku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-296">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="7f60e-297">Relativní nebo absolutní identifikátor URI prostředku.</span><span class="sxs-lookup"><span data-stu-id="7f60e-297">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="7f60e-298">Řetězec integrity představující očekávaný obsah v odpovědi</span><span class="sxs-lookup"><span data-stu-id="7f60e-298">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="7f60e-299">`loadBootResource`Vrátí některou z následujících možností pro přepsání procesu načítání:</span><span class="sxs-lookup"><span data-stu-id="7f60e-299">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="7f60e-300">Řetězec identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="7f60e-300">URI string.</span></span> <span data-ttu-id="7f60e-301">V následujícím příkladu ( `wwwroot/index.html` ) jsou následující soubory obsluhovány ze sítě CDN v `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="7f60e-301">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="7f60e-302">Data časového pásma</span><span class="sxs-lookup"><span data-stu-id="7f60e-302">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="7f60e-303">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="7f60e-303">`Promise<Response>`.</span></span> <span data-ttu-id="7f60e-304">Předejte `integrity` parametr v hlavičce pro zachování výchozího chování kontroly integrity.</span><span class="sxs-lookup"><span data-stu-id="7f60e-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="7f60e-305">Následující příklad ( `wwwroot/index.html` ) přidá vlastní HLAVIČKU http do odchozích požadavků a předá `integrity` parametr prostřednictvím `fetch` volání:</span><span class="sxs-lookup"><span data-stu-id="7f60e-305">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="7f60e-306">`null`/`undefined`, což vede k výchozímu chování při načítání.</span><span class="sxs-lookup"><span data-stu-id="7f60e-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="7f60e-307">Externí zdroje musí vracet požadované hlavičky CORS pro prohlížeče, aby bylo možné načíst prostředky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="7f60e-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="7f60e-308">Sítě CDN obvykle poskytuje ve výchozím nastavení požadované hlavičky.</span><span class="sxs-lookup"><span data-stu-id="7f60e-308">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="7f60e-309">Stačí zadat typy pro vlastní chování.</span><span class="sxs-lookup"><span data-stu-id="7f60e-309">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="7f60e-310">Typy neurčené pro `loadBootResource` jsou načteny rozhraním na své výchozí chování při načítání.</span><span class="sxs-lookup"><span data-stu-id="7f60e-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="7f60e-311">Změna přípony filename souborů DLL</span><span class="sxs-lookup"><span data-stu-id="7f60e-311">Change the filename extension of DLL files</span></span>

<span data-ttu-id="7f60e-312">V případě, že potřebujete změnit příponu filename pro publikované `.dll` soubory aplikace, postupujte podle pokynů v této části.</span><span class="sxs-lookup"><span data-stu-id="7f60e-312">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="7f60e-313">Po publikování aplikace použijte skript prostředí nebo kanál sestavení DevOps k přejmenování `.dll` souborů tak, aby používaly jinou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-313">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="7f60e-314">Zaměřte se na `.dll` soubory v `wwwroot` adresáři publikovaného výstupu aplikace (například `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="7f60e-314">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="7f60e-315">V následujících příkladech `.dll` jsou soubory přejmenovány, aby používaly `.bin` příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-315">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="7f60e-316">Ve Windows:</span><span class="sxs-lookup"><span data-stu-id="7f60e-316">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="7f60e-317">Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f60e-317">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="7f60e-318">V systému Linux nebo macOS:</span><span class="sxs-lookup"><span data-stu-id="7f60e-318">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="7f60e-319">Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f60e-319">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="7f60e-320">Chcete-li použít jinou příponu souboru než `.bin` , nahraďte `.bin` v předchozích příkazech.</span><span class="sxs-lookup"><span data-stu-id="7f60e-320">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="7f60e-321">Pro vyřešení komprimovaných `blazor.boot.json.gz` `blazor.boot.json.br` souborů proveďte jednu z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7f60e-321">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="7f60e-322">Odeberte komprimované `blazor.boot.json.gz` soubory a `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-322">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="7f60e-323">Komprese je u tohoto přístupu zakázána.</span><span class="sxs-lookup"><span data-stu-id="7f60e-323">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="7f60e-324">Opětovná komprimace aktualizovaného `blazor.boot.json` souboru.</span><span class="sxs-lookup"><span data-stu-id="7f60e-324">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="7f60e-325">Předchozí pokyny platí i v případě, že se aktivují prostředky pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-325">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="7f60e-326">Odeberte nebo znovu proveďte komprimaci `wwwroot/service-worker-assets.js.br` a `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="7f60e-326">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="7f60e-327">V opačném případě kontroly integrity souborů selžou v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7f60e-327">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="7f60e-328">Následující příklad Windows používá skript prostředí PowerShell umístěný v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="7f60e-328">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="7f60e-329">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="7f60e-329">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="7f60e-330">Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7f60e-330">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="7f60e-331">V souboru projektu se skript spustí po publikování aplikace:</span><span class="sxs-lookup"><span data-stu-id="7f60e-331">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="7f60e-332">Pokud chcete poskytnout zpětnou vazbu, navštivte [aspnetcore/problémy #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="7f60e-332">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
