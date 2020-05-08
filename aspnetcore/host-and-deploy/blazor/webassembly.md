---
title: Hostování a nasazení ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: e136a401beffe9cc7e29906b3631ab3f068b30fd
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967594"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="2bc53-103">Hostování a nasazení ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2bc53-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="2bc53-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Skořepa](https://github.com/danroth27), [Robert Adams](https://twitter.com/ben_a_adams)a [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="2bc53-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2bc53-105">S [modelem hostování WebAssembly Blazor](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="2bc53-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="2bc53-106">Aplikace Blazor, její závislosti a modul runtime .NET jsou stahovány do prohlížeče paralelně.</span><span class="sxs-lookup"><span data-stu-id="2bc53-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="2bc53-107">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2bc53-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="2bc53-108">Podporují se tyto strategie nasazení:</span><span class="sxs-lookup"><span data-stu-id="2bc53-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="2bc53-109">Aplikace Blazor obsluhuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bc53-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="2bc53-110">Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .</span><span class="sxs-lookup"><span data-stu-id="2bc53-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="2bc53-111">Aplikace Blazor je umístěna na statický hostující webový server nebo službu, kde rozhraní .NET se nepoužívá k obsluze aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="2bc53-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="2bc53-112">Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování aplikace Blazor WebAssembly jako dílčí aplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2bc53-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="2bc53-113">Předkomprese Brotli</span><span class="sxs-lookup"><span data-stu-id="2bc53-113">Brotli precompression</span></span>

<span data-ttu-id="2bc53-114">Při publikování aplikace Blazor WebAssembly je výstup zkomprimován pomocí [kompresního algoritmu Brotli](https://tools.ietf.org/html/rfc7932) na nejvyšší úrovni, aby se snížila velikost aplikace a odstranila se nutnost komprese za běhu.</span><span class="sxs-lookup"><span data-stu-id="2bc53-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="2bc53-115">Konfiguraci komprese *souboru Web. config* služby IIS najdete v části [IIS: Brotli a komprese GZip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="2bc53-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="2bc53-116">Přepište adresy URL pro správné směrování.</span><span class="sxs-lookup"><span data-stu-id="2bc53-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="2bc53-117">Požadavky směrování na součásti stránky v aplikaci Blazor WebAssembly nejsou stejně jednoduché jako požadavky směrování na serveru Blazor a v hostované aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2bc53-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="2bc53-118">Zvažte Blazor aplikaci WebAssembly se dvěma komponentami:</span><span class="sxs-lookup"><span data-stu-id="2bc53-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="2bc53-119">*Main. Razor* &ndash; se načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="2bc53-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="2bc53-120">*O komponentě. Razor* &ndash; `About` .</span><span class="sxs-lookup"><span data-stu-id="2bc53-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="2bc53-121">Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="2bc53-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="2bc53-122">Prohlížeč vytvoří požadavek.</span><span class="sxs-lookup"><span data-stu-id="2bc53-122">The browser makes a request.</span></span>
1. <span data-ttu-id="2bc53-123">Vrátí se výchozí stránka, což je obvykle *index. html*.</span><span class="sxs-lookup"><span data-stu-id="2bc53-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="2bc53-124">*index. html* se v aplikaci zabootstrap.</span><span class="sxs-lookup"><span data-stu-id="2bc53-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="2bc53-125">Blazor směrovač se načte a komponenta Razor `Main` se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="2bc53-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="2bc53-126">Na hlavní stránce `About` vyberte odkaz na komponentu na klientovi, protože směrovač Blazor zabrání v prohlížeči, aby `www.contoso.com` odeslal požadavek na Internet pro `About` a obsluhu samotné vykreslené `About` komponenty.</span><span class="sxs-lookup"><span data-stu-id="2bc53-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="2bc53-127">Všechny požadavky na vnitřní koncové body *v aplikaci Blazor WebAssembly* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu.</span><span class="sxs-lookup"><span data-stu-id="2bc53-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="2bc53-128">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="2bc53-128">The router handles the requests internally.</span></span>

<span data-ttu-id="2bc53-129">Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About`, požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="2bc53-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="2bc53-130">Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="2bc53-131">Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="2bc53-132">Když se vrátí *index. html* , Blazor směrovač aplikace převezme a odpoví správným prostředkem.</span><span class="sxs-lookup"><span data-stu-id="2bc53-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="2bc53-133">Při nasazování na server služby IIS můžete použít modul pro přepis adres URL pomocí publikovaného souboru *Web. config* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2bc53-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="2bc53-134">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="2bc53-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="2bc53-135">Hostované nasazení s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2bc53-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="2bc53-136">*Hostované nasazení* obsluhuje aplikaci Blazor WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="2bc53-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="2bc53-137">Klientská aplikace WebAssembly Blazor je publikovaná ve složce */bin/Release/{Target Framework}/Publish/wwwroot* aplikace, společně s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2bc53-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="2bc53-138">Obě aplikace se nasazují dohromady.</span><span class="sxs-lookup"><span data-stu-id="2bc53-138">The two apps are deployed together.</span></span> <span data-ttu-id="2bc53-139">Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bc53-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="2bc53-140">V případě hostovaného nasazení Visual Studio zahrnuje šablonu **projektu aplikace Blazor WebAssembly** (`blazorwasm` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) s vybranou možností **Hosted** (`-ho|--hosted` při použití `dotnet new` příkazu).</span><span class="sxs-lookup"><span data-stu-id="2bc53-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="2bc53-141">Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="2bc53-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2bc53-142">Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="2bc53-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="2bc53-143">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="2bc53-143">Standalone deployment</span></span>

<span data-ttu-id="2bc53-144">*Samostatné nasazení* obsluhuje aplikaci Blazor WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="2bc53-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="2bc53-145">Libovolný statický souborový server může obsluhovat aplikaci Blazor.</span><span class="sxs-lookup"><span data-stu-id="2bc53-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="2bc53-146">Samostatné prostředky nasazení se publikují do složky */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="2bc53-147">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="2bc53-147">Azure App Service</span></span>

<span data-ttu-id="2bc53-148">Blazor aplikace WebAssembly se dají nasadit do Azure App Services v systému Windows, který hostuje aplikaci ve [službě IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="2bc53-148">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="2bc53-149">Nasazení samostatné aplikace WebAssembly Blazor do Azure App Service pro Linux není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="2bc53-149">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="2bc53-150">Image serveru pro Linux, která je hostitelem aplikace, není v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2bc53-150">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="2bc53-151">Pro povolení tohoto scénáře probíhá práce.</span><span class="sxs-lookup"><span data-stu-id="2bc53-151">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="2bc53-152">IIS</span><span class="sxs-lookup"><span data-stu-id="2bc53-152">IIS</span></span>

<span data-ttu-id="2bc53-153">Služba IIS je schopným statickým souborovým serverem pro aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="2bc53-153">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="2bc53-154">Chcete-li nakonfigurovat službu IIS na hostování Blazor, přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="2bc53-154">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="2bc53-155">Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-155">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="2bc53-156">Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="2bc53-156">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="2bc53-157">Web. config</span><span class="sxs-lookup"><span data-stu-id="2bc53-157">web.config</span></span>

<span data-ttu-id="2bc53-158">Při publikování projektu Blazor se vytvoří soubor *Web. config* s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="2bc53-158">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="2bc53-159">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="2bc53-159">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="2bc53-160">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="2bc53-160">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="2bc53-161">*. JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="2bc53-161">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="2bc53-162">*. wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="2bc53-162">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="2bc53-163">*. woff* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2bc53-163">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="2bc53-164">*. woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2bc53-164">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="2bc53-165">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="2bc53-165">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="2bc53-166">Pravidla pro přepis adres URL jsou navázána:</span><span class="sxs-lookup"><span data-stu-id="2bc53-166">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="2bc53-167">Slouží jako podadresáře, kde se nachází statické prostředky aplikace (*wwwroot/{cesta je požadovaná}*).</span><span class="sxs-lookup"><span data-stu-id="2bc53-167">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="2bc53-168">Vytvořte záložní záložní postup, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce statických prostředků (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="2bc53-168">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="2bc53-169">Použít vlastní web. config</span><span class="sxs-lookup"><span data-stu-id="2bc53-169">Use a custom web.config</span></span>

<span data-ttu-id="2bc53-170">Chcete-li použít vlastní soubor *Web. config* , umístěte vlastní soubor *Web. config* do kořenové složky projektu a publikujte projekt.</span><span class="sxs-lookup"><span data-stu-id="2bc53-170">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="2bc53-171">Instalace modulu URL pro přepis</span><span class="sxs-lookup"><span data-stu-id="2bc53-171">Install the URL Rewrite Module</span></span>

<span data-ttu-id="2bc53-172">Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) .</span><span class="sxs-lookup"><span data-stu-id="2bc53-172">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="2bc53-173">Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="2bc53-173">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="2bc53-174">Modul se musí stáhnout z webu IIS.</span><span class="sxs-lookup"><span data-stu-id="2bc53-174">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="2bc53-175">K instalaci modulu použijte instalační program webové platformy:</span><span class="sxs-lookup"><span data-stu-id="2bc53-175">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="2bc53-176">Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="2bc53-176">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="2bc53-177">V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="2bc53-177">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="2bc53-178">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.</span><span class="sxs-lookup"><span data-stu-id="2bc53-178">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="2bc53-179">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="2bc53-179">Copy the installer to the server.</span></span> <span data-ttu-id="2bc53-180">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="2bc53-180">Run the installer.</span></span> <span data-ttu-id="2bc53-181">Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="2bc53-181">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="2bc53-182">Po dokončení instalace není restartování serveru vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="2bc53-182">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="2bc53-183">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="2bc53-183">Configure the website</span></span>

<span data-ttu-id="2bc53-184">Nastavte **fyzickou cestu** webu na složku aplikace.</span><span class="sxs-lookup"><span data-stu-id="2bc53-184">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="2bc53-185">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="2bc53-185">The folder contains:</span></span>

* <span data-ttu-id="2bc53-186">Soubor *Web. config* , který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="2bc53-186">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="2bc53-187">Složka statických prostředků aplikace</span><span class="sxs-lookup"><span data-stu-id="2bc53-187">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="2bc53-188">Hostování jako dílčí aplikace služby IIS</span><span class="sxs-lookup"><span data-stu-id="2bc53-188">Host as an IIS sub-app</span></span>

<span data-ttu-id="2bc53-189">Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="2bc53-189">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="2bc53-190">Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2bc53-190">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="2bc53-191">Odeberte obslužnou rutinu v publikovaném souboru *Web. config* aplikace Blazor přidáním `<handlers>` oddílu do souboru:</span><span class="sxs-lookup"><span data-stu-id="2bc53-191">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="2bc53-192">Zakažte dědění `<system.webServer>` kořenového oddílu (nadřazené) aplikace `<location>` pomocí elementu s `inheritInChildApplications` nastavením na: `false`</span><span class="sxs-lookup"><span data-stu-id="2bc53-192">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="2bc53-193">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2bc53-193">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="2bc53-194">Nastavte základní cestu aplikace v souboru *index. html* aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.</span><span class="sxs-lookup"><span data-stu-id="2bc53-194">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="2bc53-195">Komprese Brotli a gzip</span><span class="sxs-lookup"><span data-stu-id="2bc53-195">Brotli and Gzip compression</span></span>

<span data-ttu-id="2bc53-196">Službu IIS je možné nakonfigurovat pomocí *souboru Web. config* , aby sloužila Blazorm prostředkům Brotli nebo gzip.</span><span class="sxs-lookup"><span data-stu-id="2bc53-196">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="2bc53-197">Příklad konfigurace naleznete v tématu [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="2bc53-197">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="2bc53-198">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="2bc53-198">Troubleshooting</span></span>

<span data-ttu-id="2bc53-199">Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis.</span><span class="sxs-lookup"><span data-stu-id="2bc53-199">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="2bc53-200">Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="2bc53-200">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="2bc53-201">Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web neobsluhuje statické soubory Blazor.</span><span class="sxs-lookup"><span data-stu-id="2bc53-201">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="2bc53-202">Další informace o řešení potíží s nasazeními služby IIS najdete <xref:test/troubleshoot-azure-iis>v tématu.</span><span class="sxs-lookup"><span data-stu-id="2bc53-202">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="2bc53-203">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="2bc53-203">Azure Storage</span></span>

<span data-ttu-id="2bc53-204">Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje hostování aplikace bez serveru.</span><span class="sxs-lookup"><span data-stu-id="2bc53-204">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="2bc53-205">Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2bc53-205">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="2bc53-206">Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="2bc53-206">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="2bc53-207">Nastavte **název dokumentu indexu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="2bc53-207">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="2bc53-208">Nastavte **cestu k chybovému dokumentu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="2bc53-208">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="2bc53-209">Komponenty Razor a jiné nesouborové koncové body se neukládají na fyzických cestách se statickým obsahem uloženým ve službě BLOB Service.</span><span class="sxs-lookup"><span data-stu-id="2bc53-209">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="2bc53-210">Když se obdrží požadavek na jeden z těchto prostředků, který by měl směrovač Blazor zpracovat, Chyba *404 –* chyba, kterou vygenerovala služba BLOB Service, směruje požadavek na **cestu k chybovému dokumentu**.</span><span class="sxs-lookup"><span data-stu-id="2bc53-210">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="2bc53-211">Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="2bc53-211">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="2bc53-212">Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="2bc53-212">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="2bc53-213">Nginx</span><span class="sxs-lookup"><span data-stu-id="2bc53-213">Nginx</span></span>

<span data-ttu-id="2bc53-214">Následující soubor *Nginx. conf* je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání souboru *index. html* pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-214">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="2bc53-215">Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="2bc53-215">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="2bc53-216">Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="2bc53-216">Nginx in Docker</span></span>

<span data-ttu-id="2bc53-217">Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile pro použití image Nginx založené na Alpine.</span><span class="sxs-lookup"><span data-stu-id="2bc53-217">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="2bc53-218">Aktualizujte souboru Dockerfile a zkopírujte soubor *Nginx. config* do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="2bc53-218">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="2bc53-219">Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2bc53-219">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="2bc53-220">Apache</span><span class="sxs-lookup"><span data-stu-id="2bc53-220">Apache</span></span>

<span data-ttu-id="2bc53-221">Nasazení aplikace Blazor WebAssembly na CentOS 7 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="2bc53-221">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="2bc53-222">Vytvořte konfigurační soubor Apache.</span><span class="sxs-lookup"><span data-stu-id="2bc53-222">Create the Apache configuration file.</span></span> <span data-ttu-id="2bc53-223">Následující příklad je zjednodušený konfigurační soubor (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="2bc53-223">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="2bc53-224">Soubor konfigurace Apache umístěte do `/etc/httpd/conf.d/` adresáře, který je výchozím adresářem konfigurace Apache v CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="2bc53-224">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="2bc53-225">Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění zadaného do `DocumentRoot` konfiguračního souboru).</span><span class="sxs-lookup"><span data-stu-id="2bc53-225">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="2bc53-226">Restartujte službu Apache.</span><span class="sxs-lookup"><span data-stu-id="2bc53-226">Restart the Apache service.</span></span>

<span data-ttu-id="2bc53-227">Další informace najdete v tématu [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="2bc53-227">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="2bc53-228">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="2bc53-228">GitHub Pages</span></span>

<span data-ttu-id="2bc53-229">Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-229">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="2bc53-230">Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="2bc53-230">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="2bc53-231">Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="2bc53-231">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="2bc53-232">Při použití webu projektu místo webu organizace přidejte nebo aktualizujte `<base>` značku v souboru *index. html*.</span><span class="sxs-lookup"><span data-stu-id="2bc53-232">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="2bc53-233">Nastavte hodnotu `href` atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="2bc53-233">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="2bc53-234">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="2bc53-234">Host configuration values</span></span>

<span data-ttu-id="2bc53-235">[Aplikace Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="2bc53-235">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="2bc53-236">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="2bc53-236">Content root</span></span>

<span data-ttu-id="2bc53-237">`--contentroot` Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="2bc53-237">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="2bc53-238">V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2bc53-238">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="2bc53-239">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-239">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2bc53-240">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="2bc53-240">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="2bc53-241">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="2bc53-241">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2bc53-242">Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2bc53-242">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="2bc53-243">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="2bc53-243">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2bc53-244">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-244">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="2bc53-245">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="2bc53-245">Path base</span></span>

<span data-ttu-id="2bc53-246">`--pathbase` Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL ( `<base>` značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční).</span><span class="sxs-lookup"><span data-stu-id="2bc53-246">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="2bc53-247">V následujících příkladech `/relative-URL-path` je základ cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="2bc53-247">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="2bc53-248">Další informace najdete v tématu [základní cesta k aplikaci](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2bc53-248">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2bc53-249">Na rozdíl od cesty, která `href` je k `<base>` dispozici pro značku, nezahrnujte koncové lomítko`/`() při předávání `--pathbase` hodnoty argumentu.</span><span class="sxs-lookup"><span data-stu-id="2bc53-249">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="2bc53-250">Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="2bc53-250">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="2bc53-251">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-251">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2bc53-252">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="2bc53-252">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="2bc53-253">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="2bc53-253">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2bc53-254">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.</span><span class="sxs-lookup"><span data-stu-id="2bc53-254">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="2bc53-255">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="2bc53-255">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2bc53-256">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-256">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="2bc53-257">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="2bc53-257">URLs</span></span>

<span data-ttu-id="2bc53-258">`--urls` Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.</span><span class="sxs-lookup"><span data-stu-id="2bc53-258">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="2bc53-259">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-259">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2bc53-260">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="2bc53-260">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="2bc53-261">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="2bc53-261">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2bc53-262">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.</span><span class="sxs-lookup"><span data-stu-id="2bc53-262">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="2bc53-263">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="2bc53-263">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2bc53-264">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-264">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="2bc53-265">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="2bc53-265">Configure the Linker</span></span>

<span data-ttu-id="2bc53-266">Blazor provádí propojení s mezijazykem (IL) na každém sestavení vydaných verzí, aby z výstupních sestavení odstranila zbytečné IL.</span><span class="sxs-lookup"><span data-stu-id="2bc53-266">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="2bc53-267">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="2bc53-267">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="2bc53-268">Načítání vlastního spouštěcího prostředku</span><span class="sxs-lookup"><span data-stu-id="2bc53-268">Custom boot resource loading</span></span>

<span data-ttu-id="2bc53-269">Aplikaci Blazor WebAssembly lze inicializovat pomocí `loadBootResource` funkce pro přepsání vestavěného mechanismu načítání prostředků spouštění.</span><span class="sxs-lookup"><span data-stu-id="2bc53-269">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="2bc53-270">Použijte `loadBootResource` v následujících případech:</span><span class="sxs-lookup"><span data-stu-id="2bc53-270">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="2bc53-271">Umožňuje uživatelům načíst statické prostředky, jako jsou data o časovém pásmu nebo *dotnet. wasm* z CDN.</span><span class="sxs-lookup"><span data-stu-id="2bc53-271">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="2bc53-272">Načtěte komprimovaná sestavení pomocí požadavku HTTP a dekomprimujte je v klientovi pro hostitele, kteří nepodporují načítání komprimovaného obsahu ze serveru.</span><span class="sxs-lookup"><span data-stu-id="2bc53-272">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="2bc53-273">Aliasujte prostředky na jiný název tak, že každý `fetch` požadavek přesměrujete na nový název.</span><span class="sxs-lookup"><span data-stu-id="2bc53-273">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="2bc53-274">`loadBootResource`parametry jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="2bc53-274">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="2bc53-275">Parametr</span><span class="sxs-lookup"><span data-stu-id="2bc53-275">Parameter</span></span>    | <span data-ttu-id="2bc53-276">Popis</span><span class="sxs-lookup"><span data-stu-id="2bc53-276">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="2bc53-277">Typ prostředku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-277">The type of the resource.</span></span> <span data-ttu-id="2bc53-278">Permissable typy: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="2bc53-278">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="2bc53-279">Název prostředku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-279">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="2bc53-280">Relativní nebo absolutní identifikátor URI prostředku.</span><span class="sxs-lookup"><span data-stu-id="2bc53-280">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="2bc53-281">Řetězec integrity představující očekávaný obsah v odpovědi</span><span class="sxs-lookup"><span data-stu-id="2bc53-281">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="2bc53-282">`loadBootResource`Vrátí některou z následujících možností pro přepsání procesu načítání:</span><span class="sxs-lookup"><span data-stu-id="2bc53-282">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="2bc53-283">Řetězec identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="2bc53-283">URI string.</span></span> <span data-ttu-id="2bc53-284">V následujícím příkladu (*wwwroot/index.html*) jsou následující soubory obsluhovány ze sítě CDN v `https://my-awesome-cdn.com/`:</span><span class="sxs-lookup"><span data-stu-id="2bc53-284">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="2bc53-285">*dotnet. \*. js*</span><span class="sxs-lookup"><span data-stu-id="2bc53-285">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="2bc53-286">*dotnet. wasm*</span><span class="sxs-lookup"><span data-stu-id="2bc53-286">*dotnet.wasm*</span></span>
  * <span data-ttu-id="2bc53-287">Data časového pásma</span><span class="sxs-lookup"><span data-stu-id="2bc53-287">Timezone data</span></span>

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

* <span data-ttu-id="2bc53-288">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="2bc53-288">`Promise<Response>`.</span></span> <span data-ttu-id="2bc53-289">Předejte `integrity` parametr v hlavičce pro zachování výchozího chování kontroly integrity.</span><span class="sxs-lookup"><span data-stu-id="2bc53-289">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="2bc53-290">Následující příklad (*wwwroot/index.html*) přidá vlastní hlavičku protokolu HTTP do odchozích požadavků a předá `integrity` parametr prostřednictvím `fetch` volání:</span><span class="sxs-lookup"><span data-stu-id="2bc53-290">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="2bc53-291">`null`/`undefined`, což vede k výchozímu chování při načítání.</span><span class="sxs-lookup"><span data-stu-id="2bc53-291">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="2bc53-292">Externí zdroje musí vracet požadované hlavičky CORS pro prohlížeče, aby bylo možné načíst prostředky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="2bc53-292">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="2bc53-293">Sítě CDN obvykle poskytuje ve výchozím nastavení požadované hlavičky.</span><span class="sxs-lookup"><span data-stu-id="2bc53-293">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="2bc53-294">Stačí zadat typy pro vlastní chování.</span><span class="sxs-lookup"><span data-stu-id="2bc53-294">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="2bc53-295">Typy neurčené pro `loadBootResource` jsou načteny rozhraním na své výchozí chování při načítání.</span><span class="sxs-lookup"><span data-stu-id="2bc53-295">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="2bc53-296">Změna přípony filename souborů DLL</span><span class="sxs-lookup"><span data-stu-id="2bc53-296">Change the filename extension of DLL files</span></span>

<span data-ttu-id="2bc53-297">V případě, že potřebujete změnit přípony názvů souborů publikovaných *knihoven DLL* aplikace, postupujte podle pokynů v této části.</span><span class="sxs-lookup"><span data-stu-id="2bc53-297">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="2bc53-298">Po publikování aplikace použijte skript prostředí nebo kanál sestavení DevOps k přejmenování souborů *. dll* tak, aby používaly jinou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="2bc53-298">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="2bc53-299">Zaměřte se na soubory *. dll* v adresáři *wwwroot* publikovaného výstupu aplikace (například *{root obsahu}/bin/Release/netstandard2.1/Publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="2bc53-299">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="2bc53-300">V následujících příkladech jsou soubory *. dll* přejmenovány, aby používaly příponu souboru *. bin* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-300">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="2bc53-301">Ve Windows:</span><span class="sxs-lookup"><span data-stu-id="2bc53-301">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="2bc53-302">V systému Linux nebo macOS:</span><span class="sxs-lookup"><span data-stu-id="2bc53-302">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```
   
<span data-ttu-id="2bc53-303">Chcete-li použít jinou příponu souboru než *. bin*, nahraďte *. bin* v předchozích příkazech.</span><span class="sxs-lookup"><span data-stu-id="2bc53-303">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="2bc53-304">Chcete-li vyřešit komprimované soubory *blazor. boot. JSON. gz* a *blazor.boot.JSON.br* , proveďte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2bc53-304">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="2bc53-305">Odstraňte komprimované soubory *blazor. boot. JSON. gz* a *blazor.boot.JSON.br* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-305">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="2bc53-306">Komprese je u tohoto přístupu zakázána.</span><span class="sxs-lookup"><span data-stu-id="2bc53-306">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="2bc53-307">Opětovná komprimace aktualizovaného souboru *blazor. boot. JSON* .</span><span class="sxs-lookup"><span data-stu-id="2bc53-307">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="2bc53-308">Následující příklad Windows používá skript prostředí PowerShell umístěný v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="2bc53-308">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="2bc53-309">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="2bc53-309">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="2bc53-310">V souboru projektu se skript spustí po publikování aplikace:</span><span class="sxs-lookup"><span data-stu-id="2bc53-310">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="2bc53-311">Pokud chcete poskytnout zpětnou vazbu, navštivte [aspnetcore/problémy #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="2bc53-311">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
