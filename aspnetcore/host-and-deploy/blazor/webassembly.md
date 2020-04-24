---
title: Hostování a nasazení ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: daaaab360e93de1cf10feec2db21d3acc25920bd
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110873"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="fff29-103">Hostování a nasazení ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fff29-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="fff29-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Skořepa](https://github.com/danroth27)a [Robert Adams](https://twitter.com/ben_a_adams).</span><span class="sxs-lookup"><span data-stu-id="fff29-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), and [Ben Adams](https://twitter.com/ben_a_adams).</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fff29-105">S modelem hostování WebAssembly: [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="fff29-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="fff29-106">Blazor Aplikace, její závislosti a modul runtime .NET jsou stahovány do prohlížeče paralelně.</span><span class="sxs-lookup"><span data-stu-id="fff29-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="fff29-107">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fff29-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="fff29-108">Podporují se tyto strategie nasazení:</span><span class="sxs-lookup"><span data-stu-id="fff29-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="fff29-109">Blazor Aplikaci obsluhuje aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fff29-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="fff29-110">Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .</span><span class="sxs-lookup"><span data-stu-id="fff29-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="fff29-111">Blazor Aplikace se umístí na statický hostující webový server nebo službu, kde rozhraní .NET se k obsluze Blazor aplikace nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="fff29-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="fff29-112">Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování Blazor aplikace WebAssembly jako dílčí aplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="fff29-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="fff29-113">Předkomprese Brotli</span><span class="sxs-lookup"><span data-stu-id="fff29-113">Brotli precompression</span></span>

<span data-ttu-id="fff29-114">Blazor Při publikování aplikace WebAssembly je výstup komprimován pomocí [kompresního algoritmu Brotli](https://tools.ietf.org/html/rfc7932) na nejvyšší úrovni, aby se snížila velikost aplikace a odstranila se nutnost komprese za běhu.</span><span class="sxs-lookup"><span data-stu-id="fff29-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="fff29-115">Konfiguraci komprese *souboru Web. config* služby IIS najdete v části [IIS: Brotli a komprese GZip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="fff29-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="fff29-116">Přepište adresy URL pro správné směrování.</span><span class="sxs-lookup"><span data-stu-id="fff29-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="fff29-117">Žádosti o směrování pro součásti stránky v Blazor aplikaci WebAssembly nejsou tak jednoduché jako požadavky směrování na Blazor serveru, v němž je umístěná aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="fff29-118">Blazor Zvažte aplikaci WebAssembly se dvěma součástmi:</span><span class="sxs-lookup"><span data-stu-id="fff29-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="fff29-119">*Main. Razor* &ndash; se načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="fff29-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="fff29-120">*O komponentě. Razor* &ndash; `About` .</span><span class="sxs-lookup"><span data-stu-id="fff29-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="fff29-121">Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="fff29-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="fff29-122">Prohlížeč vytvoří požadavek.</span><span class="sxs-lookup"><span data-stu-id="fff29-122">The browser makes a request.</span></span>
1. <span data-ttu-id="fff29-123">Vrátí se výchozí stránka, což je obvykle *index. html*.</span><span class="sxs-lookup"><span data-stu-id="fff29-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="fff29-124">*index. html* se v aplikaci zabootstrap.</span><span class="sxs-lookup"><span data-stu-id="fff29-124">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="fff29-125">se načte ze směrovače a komponenta Razor `Main` se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="fff29-125">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="fff29-126">Na `About` hlavní stránce vyberte odkaz na komponentu na klientovi, protože Blazor směrovač zastaví v prohlížeči, aby `www.contoso.com` odeslal požadavek na Internet pro `About` a sloužil přímo vykreslené `About` součásti.</span><span class="sxs-lookup"><span data-stu-id="fff29-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="fff29-127">Všechny požadavky na vnitřní koncové body *v Blazor aplikaci WebAssembly* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu.</span><span class="sxs-lookup"><span data-stu-id="fff29-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="fff29-128">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="fff29-128">The router handles the requests internally.</span></span>

<span data-ttu-id="fff29-129">Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About`, požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="fff29-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="fff29-130">Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="fff29-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="fff29-131">Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="fff29-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="fff29-132">Když se vrátí *index. html* , Blazor směrovač aplikace převezme a odpoví správným prostředkem.</span><span class="sxs-lookup"><span data-stu-id="fff29-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="fff29-133">Při nasazování na server služby IIS můžete použít modul pro přepis adres URL pomocí publikovaného souboru *Web. config* aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="fff29-134">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="fff29-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="fff29-135">Hostované nasazení s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fff29-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="fff29-136">*Hostované nasazení* obsluhuje Blazor aplikaci WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="fff29-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="fff29-137">Klientská Blazor aplikace WebAssembly je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* aplikace v serverové aplikaci společně s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="fff29-138">Obě aplikace se nasazují dohromady.</span><span class="sxs-lookup"><span data-stu-id="fff29-138">The two apps are deployed together.</span></span> <span data-ttu-id="fff29-139">Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fff29-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="fff29-140">V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu \*\* Blazor aplikace pro WebAssembly\*\* (`blazorwasm` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) s vybranou možností **Hosted** (`-ho|--hosted` při použití `dotnet new` příkazu).</span><span class="sxs-lookup"><span data-stu-id="fff29-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="fff29-141">Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="fff29-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="fff29-142">Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="fff29-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="fff29-143">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="fff29-143">Standalone deployment</span></span>

<span data-ttu-id="fff29-144">*Samostatné nasazení* obsluhuje Blazor aplikaci WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="fff29-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="fff29-145">Každý statický souborový server může Blazor aplikaci zpracovat.</span><span class="sxs-lookup"><span data-stu-id="fff29-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="fff29-146">Samostatné prostředky nasazení se publikují do složky */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="fff29-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="fff29-147">IIS</span><span class="sxs-lookup"><span data-stu-id="fff29-147">IIS</span></span>

<span data-ttu-id="fff29-148">Služba IIS je schopným statickým souborovým serverem pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-148">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="fff29-149">Chcete-li nakonfigurovat službu BlazorIIS na hostování, přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="fff29-149">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="fff29-150">Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="fff29-150">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="fff29-151">Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="fff29-151">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="fff29-152">Web. config</span><span class="sxs-lookup"><span data-stu-id="fff29-152">web.config</span></span>

<span data-ttu-id="fff29-153">Při publikování Blazor projektu se vytvoří soubor *Web. config* s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="fff29-153">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="fff29-154">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="fff29-154">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="fff29-155">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="fff29-155">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="fff29-156">*. JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="fff29-156">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="fff29-157">*. wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="fff29-157">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="fff29-158">*. woff* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="fff29-158">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="fff29-159">*. woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="fff29-159">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="fff29-160">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="fff29-160">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="fff29-161">Pravidla pro přepis adres URL jsou navázána:</span><span class="sxs-lookup"><span data-stu-id="fff29-161">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="fff29-162">Slouží jako podadresáře, kde se nachází statické prostředky aplikace (*wwwroot/{cesta je požadovaná}*).</span><span class="sxs-lookup"><span data-stu-id="fff29-162">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="fff29-163">Vytvořte záložní záložní postup, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce statických prostředků (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="fff29-163">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="fff29-164">Použít vlastní web. config</span><span class="sxs-lookup"><span data-stu-id="fff29-164">Use a custom web.config</span></span>

<span data-ttu-id="fff29-165">Chcete-li použít vlastní soubor *Web. config* , umístěte vlastní soubor *Web. config* do kořenové složky projektu a publikujte projekt.</span><span class="sxs-lookup"><span data-stu-id="fff29-165">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="fff29-166">Instalace modulu URL pro přepis</span><span class="sxs-lookup"><span data-stu-id="fff29-166">Install the URL Rewrite Module</span></span>

<span data-ttu-id="fff29-167">Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) .</span><span class="sxs-lookup"><span data-stu-id="fff29-167">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="fff29-168">Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="fff29-168">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="fff29-169">Modul se musí stáhnout z webu IIS.</span><span class="sxs-lookup"><span data-stu-id="fff29-169">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="fff29-170">K instalaci modulu použijte instalační program webové platformy:</span><span class="sxs-lookup"><span data-stu-id="fff29-170">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="fff29-171">Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="fff29-171">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="fff29-172">V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="fff29-172">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="fff29-173">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.</span><span class="sxs-lookup"><span data-stu-id="fff29-173">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="fff29-174">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="fff29-174">Copy the installer to the server.</span></span> <span data-ttu-id="fff29-175">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="fff29-175">Run the installer.</span></span> <span data-ttu-id="fff29-176">Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="fff29-176">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="fff29-177">Po dokončení instalace není restartování serveru vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="fff29-177">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="fff29-178">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="fff29-178">Configure the website</span></span>

<span data-ttu-id="fff29-179">Nastavte **fyzickou cestu** webu na složku aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-179">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="fff29-180">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="fff29-180">The folder contains:</span></span>

* <span data-ttu-id="fff29-181">Soubor *Web. config* , který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="fff29-181">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="fff29-182">Složka statických prostředků aplikace</span><span class="sxs-lookup"><span data-stu-id="fff29-182">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="fff29-183">Hostování jako dílčí aplikace služby IIS</span><span class="sxs-lookup"><span data-stu-id="fff29-183">Host as an IIS sub-app</span></span>

<span data-ttu-id="fff29-184">Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="fff29-184">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="fff29-185">Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fff29-185">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="fff29-186">Odeberte obslužnou rutinu v Blazor publikovaném souboru *Web. config* aplikace přidáním `<handlers>` oddílu do souboru:</span><span class="sxs-lookup"><span data-stu-id="fff29-186">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="fff29-187">Zakažte dědění `<system.webServer>` kořenového oddílu (nadřazené) aplikace `<location>` pomocí elementu s `inheritInChildApplications` nastavením na: `false`</span><span class="sxs-lookup"><span data-stu-id="fff29-187">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="fff29-188">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="fff29-188">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="fff29-189">Nastavte základní cestu aplikace v souboru *index. html* aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.</span><span class="sxs-lookup"><span data-stu-id="fff29-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="fff29-190">Komprese Brotli a gzip</span><span class="sxs-lookup"><span data-stu-id="fff29-190">Brotli and Gzip compression</span></span>

<span data-ttu-id="fff29-191">Službu IIS lze konfigurovat pomocí *souboru Web. config* pro poskytování komprimovaných Blazor prostředků Brotli nebo gzip.</span><span class="sxs-lookup"><span data-stu-id="fff29-191">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="fff29-192">Příklad konfigurace naleznete v tématu [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="fff29-192">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="fff29-193">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="fff29-193">Troubleshooting</span></span>

<span data-ttu-id="fff29-194">Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis.</span><span class="sxs-lookup"><span data-stu-id="fff29-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="fff29-195">Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="fff29-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="fff29-196">Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web Blazorze statických souborů obsluhy.</span><span class="sxs-lookup"><span data-stu-id="fff29-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="fff29-197">Další informace o řešení potíží s nasazeními služby IIS najdete <xref:test/troubleshoot-azure-iis>v tématu.</span><span class="sxs-lookup"><span data-stu-id="fff29-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="fff29-198">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="fff29-198">Azure Storage</span></span>

<span data-ttu-id="fff29-199">Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje hostování Blazor aplikací bez serveru.</span><span class="sxs-lookup"><span data-stu-id="fff29-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="fff29-200">Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fff29-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="fff29-201">Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="fff29-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="fff29-202">Nastavte **název dokumentu indexu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="fff29-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="fff29-203">Nastavte **cestu k chybovému dokumentu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="fff29-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="fff29-204">Komponenty Razor a jiné nesouborové koncové body se neukládají na fyzických cestách se statickým obsahem uloženým ve službě BLOB Service.</span><span class="sxs-lookup"><span data-stu-id="fff29-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="fff29-205">Když se přijme žádost o jeden z těchto prostředků, kterou by Blazor měl směrovač zpracovat, Chyba *404 – nenalezená* služba BLOB Service směruje požadavek na **cestu k chybovému dokumentu**.</span><span class="sxs-lookup"><span data-stu-id="fff29-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="fff29-206">Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="fff29-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="fff29-207">Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="fff29-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="fff29-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="fff29-208">Nginx</span></span>

<span data-ttu-id="fff29-209">Následující soubor *Nginx. conf* je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání souboru *index. html* pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="fff29-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="fff29-210">Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="fff29-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="fff29-211">Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="fff29-211">Nginx in Docker</span></span>

<span data-ttu-id="fff29-212">Pokud chcete Blazor hostovat v Docker pomocí Nginx, nastavte souboru Dockerfile na použití Nginx image založené na Alpine.</span><span class="sxs-lookup"><span data-stu-id="fff29-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="fff29-213">Aktualizujte souboru Dockerfile a zkopírujte soubor *Nginx. config* do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="fff29-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="fff29-214">Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fff29-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="fff29-215">Apache</span><span class="sxs-lookup"><span data-stu-id="fff29-215">Apache</span></span>

<span data-ttu-id="fff29-216">Nasazení Blazor aplikace WebAssembly na CentOS 7 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="fff29-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="fff29-217">Vytvořte konfigurační soubor Apache.</span><span class="sxs-lookup"><span data-stu-id="fff29-217">Create the Apache configuration file.</span></span> <span data-ttu-id="fff29-218">Následující příklad je zjednodušený konfigurační soubor (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="fff29-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="fff29-219">Soubor konfigurace Apache umístěte do `/etc/httpd/conf.d/` adresáře, který je výchozím adresářem konfigurace Apache v CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="fff29-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="fff29-220">Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění zadaného do `DocumentRoot` konfiguračního souboru).</span><span class="sxs-lookup"><span data-stu-id="fff29-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="fff29-221">Restartujte službu Apache.</span><span class="sxs-lookup"><span data-stu-id="fff29-221">Restart the Apache service.</span></span>

<span data-ttu-id="fff29-222">Další informace najdete v tématu [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="fff29-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="fff29-223">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="fff29-223">GitHub Pages</span></span>

<span data-ttu-id="fff29-224">Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="fff29-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="fff29-225">Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="fff29-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="fff29-226">Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="fff29-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="fff29-227">Při použití webu projektu místo webu organizace přidejte nebo aktualizujte `<base>` značku v souboru *index. html*.</span><span class="sxs-lookup"><span data-stu-id="fff29-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="fff29-228">Nastavte hodnotu `href` atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="fff29-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="fff29-229">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="fff29-229">Host configuration values</span></span>

<span data-ttu-id="fff29-230">Aplikace WebAssembly můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="fff29-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="fff29-231">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="fff29-231">Content root</span></span>

<span data-ttu-id="fff29-232">`--contentroot` Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="fff29-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="fff29-233">V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="fff29-234">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fff29-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="fff29-235">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="fff29-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="fff29-236">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="fff29-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="fff29-237">Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="fff29-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="fff29-238">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fff29-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="fff29-239">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="fff29-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="fff29-240">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="fff29-240">Path base</span></span>

<span data-ttu-id="fff29-241">`--pathbase` Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL ( `<base>` značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční).</span><span class="sxs-lookup"><span data-stu-id="fff29-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="fff29-242">V následujících příkladech `/relative-URL-path` je základ cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="fff29-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="fff29-243">Další informace najdete v tématu [základní cesta k aplikaci](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="fff29-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fff29-244">Na rozdíl od cesty, která `href` je k `<base>` dispozici pro značku, nezahrnujte koncové lomítko`/`() při předávání `--pathbase` hodnoty argumentu.</span><span class="sxs-lookup"><span data-stu-id="fff29-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="fff29-245">Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="fff29-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="fff29-246">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fff29-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="fff29-247">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="fff29-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="fff29-248">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="fff29-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="fff29-249">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.</span><span class="sxs-lookup"><span data-stu-id="fff29-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="fff29-250">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fff29-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="fff29-251">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="fff29-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="fff29-252">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="fff29-252">URLs</span></span>

<span data-ttu-id="fff29-253">`--urls` Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.</span><span class="sxs-lookup"><span data-stu-id="fff29-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="fff29-254">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="fff29-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="fff29-255">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="fff29-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="fff29-256">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="fff29-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="fff29-257">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.</span><span class="sxs-lookup"><span data-stu-id="fff29-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="fff29-258">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="fff29-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="fff29-259">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="fff29-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="fff29-260">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="fff29-260">Configure the Linker</span></span>

Blazor<span data-ttu-id="fff29-261">provede propojení s mezijazykem (IL) na každém sestavení vydaných verzí a odebere z výstupních sestavení zbytečné IL.</span><span class="sxs-lookup"><span data-stu-id="fff29-261"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="fff29-262">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="fff29-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
