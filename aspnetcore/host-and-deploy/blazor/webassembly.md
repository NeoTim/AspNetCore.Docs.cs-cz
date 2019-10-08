---
title: Hostování a nasazení ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat aplikaci Blazor pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: a0a11f3aed9035000e79844fbec7cdd17b73fdaa
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007333"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="aa65f-103">Hostování a nasazení ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="aa65f-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="aa65f-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="aa65f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="aa65f-105">S [modelem hostování WebAssembly Blazor](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="aa65f-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="aa65f-106">Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="aa65f-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="aa65f-107">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="aa65f-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="aa65f-108">Podporují se tyto strategie nasazení:</span><span class="sxs-lookup"><span data-stu-id="aa65f-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="aa65f-109">Aplikace Blazor obsluhuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa65f-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="aa65f-110">Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .</span><span class="sxs-lookup"><span data-stu-id="aa65f-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="aa65f-111">Aplikace Blazor je umístěna na statický hostující webový server nebo službu, kde rozhraní .NET se nepoužívá k obsluze aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="aa65f-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="aa65f-112">Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování aplikace Blazor WebAssembly jako dílčí aplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="aa65f-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="aa65f-113">Přepište adresy URL pro správné směrování.</span><span class="sxs-lookup"><span data-stu-id="aa65f-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="aa65f-114">Požadavky směrování na součásti stránky v aplikaci Blazor WebAssembly nejsou stejně jednoduché jako požadavky směrování na serveru Blazor a v hostované aplikaci.</span><span class="sxs-lookup"><span data-stu-id="aa65f-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="aa65f-115">Zvažte Blazor aplikaci WebAssembly se dvěma komponentami:</span><span class="sxs-lookup"><span data-stu-id="aa65f-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="aa65f-116">*Main. razor* &ndash; načte do kořenového adresáře aplikace a obsahuje odkaz na komponentu `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="aa65f-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="aa65f-117">*O komponentě. razor* &ndash; `About`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="aa65f-118">Pokud se výchozí dokument aplikace požaduje pomocí panelu Adresa v prohlížeči (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="aa65f-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="aa65f-119">Prohlížeč vytvoří požadavek.</span><span class="sxs-lookup"><span data-stu-id="aa65f-119">The browser makes a request.</span></span>
1. <span data-ttu-id="aa65f-120">Vrátí se výchozí stránka, což je obvykle *index. html*.</span><span class="sxs-lookup"><span data-stu-id="aa65f-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="aa65f-121">*index. html* se v aplikaci zabootstrap.</span><span class="sxs-lookup"><span data-stu-id="aa65f-121">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="aa65f-122">Blazor směrovač se načte a komponenta Razor `Main` se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="aa65f-122">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="aa65f-123">Na hlavní stránce můžete vybrat odkaz na součást `About` v klientovi, protože směrovač Blazor zastaví v prohlížeči žádost o `www.contoso.com` pro `About` a obsluhu samotné vykreslené součásti `About`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="aa65f-124">Všechny požadavky na vnitřní koncové body *v aplikaci Blazor WebAssembly* fungují stejným způsobem: Požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu.</span><span class="sxs-lookup"><span data-stu-id="aa65f-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="aa65f-125">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="aa65f-125">The router handles the requests internally.</span></span>

<span data-ttu-id="aa65f-126">Pokud se žádost provede pomocí panelu Adresa v prohlížeči `www.contoso.com/About`, požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="aa65f-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="aa65f-127">Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="aa65f-128">Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="aa65f-129">Když se vrátí *index. html* , Blazor směrovač aplikace převezme a odpoví správným prostředkem.</span><span class="sxs-lookup"><span data-stu-id="aa65f-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="aa65f-130">Při nasazování na server služby IIS můžete použít modul pro přepis adres URL pomocí publikovaného souboru *Web. config* aplikace.</span><span class="sxs-lookup"><span data-stu-id="aa65f-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="aa65f-131">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="aa65f-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="aa65f-132">Hostované nasazení s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa65f-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="aa65f-133">*Hostované nasazení* obsluhuje aplikaci Blazor WebAssembly pro prohlížeče z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="aa65f-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="aa65f-134">Aplikace Blazor je součástí aplikace ASP.NET Core v publikovaném výstupu, takže se tyto dvě aplikace nasazují dohromady.</span><span class="sxs-lookup"><span data-stu-id="aa65f-134">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="aa65f-135">Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa65f-135">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="aa65f-136">V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu **aplikace Blazor WebAssembly** (šablona `blazorwasm` při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) s vybranou možností **Hosted** .</span><span class="sxs-lookup"><span data-stu-id="aa65f-136">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected.</span></span>

<span data-ttu-id="aa65f-137">Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="aa65f-137">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="aa65f-138">Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="aa65f-138">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="aa65f-139">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="aa65f-139">Standalone deployment</span></span>

<span data-ttu-id="aa65f-140">*Samostatné nasazení* obsluhuje aplikaci Blazor WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="aa65f-140">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="aa65f-141">Libovolný statický souborový server může obsluhovat aplikaci Blazor.</span><span class="sxs-lookup"><span data-stu-id="aa65f-141">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="aa65f-142">Samostatné prostředky nasazení se publikují do složky *bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-142">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="aa65f-143">IIS</span><span class="sxs-lookup"><span data-stu-id="aa65f-143">IIS</span></span>

<span data-ttu-id="aa65f-144">Služba IIS je schopným statickým souborovým serverem pro aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="aa65f-144">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="aa65f-145">Chcete-li nakonfigurovat službu IIS na hostování Blazor, přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="aa65f-145">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="aa65f-146">Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-146">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="aa65f-147">Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="aa65f-147">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="aa65f-148">web.config</span><span class="sxs-lookup"><span data-stu-id="aa65f-148">web.config</span></span>

<span data-ttu-id="aa65f-149">Při publikování projektu Blazor se vytvoří soubor *Web. config* s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="aa65f-149">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="aa65f-150">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="aa65f-150">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="aa65f-151">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="aa65f-151">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="aa65f-152">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="aa65f-152">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="aa65f-153">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="aa65f-153">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="aa65f-154">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="aa65f-154">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="aa65f-155">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="aa65f-155">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="aa65f-156">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="aa65f-156">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="aa65f-157">Pravidla pro přepis adres URL jsou navázána:</span><span class="sxs-lookup"><span data-stu-id="aa65f-157">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="aa65f-158">Slouží jako podadresáře, kde se nachází statické prostředky aplikace ( *{název sestavení}/DIST/{Path požádal}* ).</span><span class="sxs-lookup"><span data-stu-id="aa65f-158">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="aa65f-159">Vytvořte záložní řešení zabezpečeného hesla, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce statických prostředků ( *{název sestavení}/DIST/index.html*).</span><span class="sxs-lookup"><span data-stu-id="aa65f-159">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="aa65f-160">Instalace modulu URL pro přepis</span><span class="sxs-lookup"><span data-stu-id="aa65f-160">Install the URL Rewrite Module</span></span>

<span data-ttu-id="aa65f-161">Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) .</span><span class="sxs-lookup"><span data-stu-id="aa65f-161">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="aa65f-162">Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="aa65f-162">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="aa65f-163">Modul se musí stáhnout z webu IIS.</span><span class="sxs-lookup"><span data-stu-id="aa65f-163">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="aa65f-164">K instalaci modulu použijte instalační program webové platformy:</span><span class="sxs-lookup"><span data-stu-id="aa65f-164">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="aa65f-165">Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="aa65f-165">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="aa65f-166">V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="aa65f-166">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="aa65f-167">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.</span><span class="sxs-lookup"><span data-stu-id="aa65f-167">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="aa65f-168">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="aa65f-168">Copy the installer to the server.</span></span> <span data-ttu-id="aa65f-169">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="aa65f-169">Run the installer.</span></span> <span data-ttu-id="aa65f-170">Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="aa65f-170">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="aa65f-171">Po dokončení instalace není restartování serveru vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="aa65f-171">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="aa65f-172">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="aa65f-172">Configure the website</span></span>

<span data-ttu-id="aa65f-173">Nastavte **fyzickou cestu** webu na složku aplikace.</span><span class="sxs-lookup"><span data-stu-id="aa65f-173">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="aa65f-174">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="aa65f-174">The folder contains:</span></span>

* <span data-ttu-id="aa65f-175">Soubor *Web. config* , který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="aa65f-175">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="aa65f-176">Složka statických prostředků aplikace</span><span class="sxs-lookup"><span data-stu-id="aa65f-176">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="aa65f-177">Hostování jako dílčí aplikace služby IIS</span><span class="sxs-lookup"><span data-stu-id="aa65f-177">Host as an IIS sub-app</span></span>

<span data-ttu-id="aa65f-178">Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="aa65f-178">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="aa65f-179">Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa65f-179">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="aa65f-180">Odeberte obslužnou rutinu v publikovaném souboru *Web. config* aplikace Blazor přidáním oddílu `<handlers>` do souboru:</span><span class="sxs-lookup"><span data-stu-id="aa65f-180">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="aa65f-181">Zakažte dědičnost oddílu `<system.webServer>` kořenové (nadřazené) aplikace pomocí elementu `<location>` s `inheritInChildApplications` nastavenou na `false`:</span><span class="sxs-lookup"><span data-stu-id="aa65f-181">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="aa65f-182">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="aa65f-182">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="aa65f-183">Nastavte základní cestu aplikace v souboru *index. html* aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.</span><span class="sxs-lookup"><span data-stu-id="aa65f-183">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="aa65f-184">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="aa65f-184">Troubleshooting</span></span>

<span data-ttu-id="aa65f-185">Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis.</span><span class="sxs-lookup"><span data-stu-id="aa65f-185">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="aa65f-186">Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="aa65f-186">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="aa65f-187">Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web neobsluhuje statické soubory Blazor.</span><span class="sxs-lookup"><span data-stu-id="aa65f-187">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="aa65f-188">Další informace o řešení potíží s nasazeními do služby IIS najdete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="aa65f-188">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="aa65f-189">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="aa65f-189">Azure Storage</span></span>

<span data-ttu-id="aa65f-190">Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje hostování aplikace bez serveru.</span><span class="sxs-lookup"><span data-stu-id="aa65f-190">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="aa65f-191">Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="aa65f-191">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="aa65f-192">Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="aa65f-192">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="aa65f-193">Nastavte **název dokumentu indexu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-193">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="aa65f-194">Nastavte **cestu k chybovému dokumentu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-194">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="aa65f-195">Komponenty Razor a jiné nesouborové koncové body se neukládají na fyzických cestách se statickým obsahem uloženým ve službě BLOB Service.</span><span class="sxs-lookup"><span data-stu-id="aa65f-195">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="aa65f-196">Když se obdrží požadavek na jeden z těchto prostředků, který by měl směrovač Blazor zpracovat, Chyba *404 –* chyba, kterou vygenerovala služba BLOB Service, směruje požadavek na **cestu k chybovému dokumentu**.</span><span class="sxs-lookup"><span data-stu-id="aa65f-196">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="aa65f-197">Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="aa65f-197">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="aa65f-198">Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="aa65f-198">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="aa65f-199">Nginx</span><span class="sxs-lookup"><span data-stu-id="aa65f-199">Nginx</span></span>

<span data-ttu-id="aa65f-200">Následující soubor *Nginx. conf* je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání souboru *index. html* pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="aa65f-200">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="aa65f-201">Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="aa65f-201">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="aa65f-202">Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="aa65f-202">Nginx in Docker</span></span>

<span data-ttu-id="aa65f-203">Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile pro použití image Nginx založené na Alpine.</span><span class="sxs-lookup"><span data-stu-id="aa65f-203">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="aa65f-204">Aktualizujte souboru Dockerfile a zkopírujte soubor *Nginx. config* do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="aa65f-204">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="aa65f-205">Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="aa65f-205">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="aa65f-206">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="aa65f-206">GitHub Pages</span></span>

<span data-ttu-id="aa65f-207">Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-207">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="aa65f-208">Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="aa65f-208">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="aa65f-209">Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="aa65f-209">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="aa65f-210">Při použití webu projektu místo webu organizace přidejte nebo aktualizujte značku `<base>` v souboru *index. html*.</span><span class="sxs-lookup"><span data-stu-id="aa65f-210">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="aa65f-211">Nastavte hodnotu atributu `href` na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-211">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="aa65f-212">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="aa65f-212">Host configuration values</span></span>

<span data-ttu-id="aa65f-213">[Aplikace Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="aa65f-213">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="aa65f-214">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="aa65f-214">Content root</span></span>

<span data-ttu-id="aa65f-215">Argument `--contentroot` nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="aa65f-215">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="aa65f-216">V následujících příkladech je `/content-root-path` kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="aa65f-216">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="aa65f-217">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aa65f-217">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="aa65f-218">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="aa65f-218">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="aa65f-219">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="aa65f-219">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="aa65f-220">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-220">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="aa65f-221">V aplikaci Visual Studio zadejte do **vlastností**argument @no__t **-1** **argumenty aplikace** > .</span><span class="sxs-lookup"><span data-stu-id="aa65f-221">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="aa65f-222">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-222">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="aa65f-223">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="aa65f-223">Path base</span></span>

<span data-ttu-id="aa65f-224">Argument `--pathbase` nastavuje základní cestu aplikace pro aplikaci spuštěnou místně s nekořenovou cestou relativní adresy URL (značka `<base>` `href` je nastavená na jinou cestu než `/` pro pracovní a produkční prostředí).</span><span class="sxs-lookup"><span data-stu-id="aa65f-224">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="aa65f-225">V následujících příkladech je `/relative-URL-path` základ cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="aa65f-225">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="aa65f-226">Další informace najdete v tématu [základní cesta k aplikaci](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="aa65f-226">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aa65f-227">Na rozdíl od cesty, kterou jste zadali `href` značky `<base>`, při předávání hodnoty argumentu `--pathbase` nezahrnujte koncové lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="aa65f-227">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="aa65f-228">Pokud je základní cesta aplikace k dispozici ve značce `<base>` jako `<base href="/CoolApp/">` (obsahuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="aa65f-228">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="aa65f-229">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aa65f-229">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="aa65f-230">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="aa65f-230">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="aa65f-231">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="aa65f-231">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="aa65f-232">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-232">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="aa65f-233">V aplikaci Visual Studio zadejte do **vlastností**argument @no__t **-1** **argumenty aplikace** > .</span><span class="sxs-lookup"><span data-stu-id="aa65f-233">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="aa65f-234">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-234">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="aa65f-235">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="aa65f-235">URLs</span></span>

<span data-ttu-id="aa65f-236">Argument `--urls` nastavuje IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.</span><span class="sxs-lookup"><span data-stu-id="aa65f-236">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="aa65f-237">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="aa65f-237">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="aa65f-238">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="aa65f-238">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="aa65f-239">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="aa65f-239">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="aa65f-240">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="aa65f-240">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="aa65f-241">V aplikaci Visual Studio zadejte do **vlastností**argument @no__t **-1** **argumenty aplikace** > .</span><span class="sxs-lookup"><span data-stu-id="aa65f-241">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="aa65f-242">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="aa65f-242">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="aa65f-243">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="aa65f-243">Configure the Linker</span></span>

<span data-ttu-id="aa65f-244">Blazor provádí propojení podle jazyka IL (Intermediate Language) na každém sestavení pro odebrání zbytečného IL z výstupních sestavení.</span><span class="sxs-lookup"><span data-stu-id="aa65f-244">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="aa65f-245">Propojení sestavení lze řídit při sestavování.</span><span class="sxs-lookup"><span data-stu-id="aa65f-245">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="aa65f-246">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="aa65f-246">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
