---
title: Hostování a nasazení Blazor ASP.NET Core WebAssembly
author: guardrex
description: Zjistěte, jak hostovat a nasazovat Blazor aplikaci pomocí ASP.NET, sítí pro doručování obsahu (CDN), souborových serverů a stránek GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751128"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="2221f-103">Hostování a nasazení Blazor ASP.NET Core WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2221f-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="2221f-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2221f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2221f-105">S [ Blazor modelem hostování websestavy](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="2221f-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="2221f-106">Aplikace, Blazor její závislosti a za běhu .NET jsou staženy do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2221f-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="2221f-107">Aplikace se spouští přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2221f-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="2221f-108">Podporovány jsou následující strategie nasazení:</span><span class="sxs-lookup"><span data-stu-id="2221f-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="2221f-109">Aplikace Blazor je obsluhována aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2221f-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="2221f-110">Tato strategie je [popsána v části Hostované nasazení s ASP.NET jádrem.](#hosted-deployment-with-aspnet-core)</span><span class="sxs-lookup"><span data-stu-id="2221f-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="2221f-111">Aplikace Blazor je umístěna na statickém hostingovém webovém serveru nebo Blazor službě, kde se rozhraní .NET nepoužívá k poskytování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="2221f-112">Tato strategie je popsána v části [Samostatné nasazení,](#standalone-deployment) která obsahuje informace o hostování aplikace Blazor WebAssembly jako podaplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2221f-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="2221f-113">Přepsat adresy URL pro správné směrování</span><span class="sxs-lookup"><span data-stu-id="2221f-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="2221f-114">Směrování požadavků na komponenty Blazor stránky v aplikaci WebAssembly není tak Blazor jednoduché jako požadavky na směrování v hostované aplikaci server.</span><span class="sxs-lookup"><span data-stu-id="2221f-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="2221f-115">Zvažte Blazor aplikaci WebAssembly se dvěma součástmi:</span><span class="sxs-lookup"><span data-stu-id="2221f-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="2221f-116">*Main.razor* &ndash; načte v kořenovém adresáři `About` aplikace`href="About"`a obsahuje odkaz na komponentu ( ).</span><span class="sxs-lookup"><span data-stu-id="2221f-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="2221f-117">*Součást About.razor.* &ndash; `About`</span><span class="sxs-lookup"><span data-stu-id="2221f-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="2221f-118">Pokud je požadován výchozí dokument aplikace pomocí adresního řádku `https://www.contoso.com/`prohlížeče (například ):</span><span class="sxs-lookup"><span data-stu-id="2221f-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="2221f-119">Prohlížeč podá požadavek.</span><span class="sxs-lookup"><span data-stu-id="2221f-119">The browser makes a request.</span></span>
1. <span data-ttu-id="2221f-120">Je vrácena výchozí stránka, která je obvykle *index.html*.</span><span class="sxs-lookup"><span data-stu-id="2221f-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="2221f-121">*index.html* bootstraps aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-121">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="2221f-122">načte se směrovač a `Main` komponenta Razor je vykreslena.</span><span class="sxs-lookup"><span data-stu-id="2221f-122">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="2221f-123">Na hlavní stránce výběr odkazu na `About` komponentu funguje na Blazor straně klienta, protože směrovač zabrání `www.contoso.com` `About` prohlížeči v `About` podání požadavku na Internetu do aplikace for a slouží samotné vykreslené součásti.</span><span class="sxs-lookup"><span data-stu-id="2221f-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="2221f-124">Všechny požadavky na interní koncové body *v aplikaci Blazor WebAssembly* fungují stejným způsobem: Požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru v Internetu.</span><span class="sxs-lookup"><span data-stu-id="2221f-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="2221f-125">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="2221f-125">The router handles the requests internally.</span></span>

<span data-ttu-id="2221f-126">Pokud je požadavek podán pomocí adresního řádku prohlížeče pro `www.contoso.com/About`, požadavek se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="2221f-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="2221f-127">Žádný takový prostředek neexistuje na internetovém hostiteli aplikace, takže je vrácena odpověď *404 - Not Found.*</span><span class="sxs-lookup"><span data-stu-id="2221f-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="2221f-128">Vzhledem k tomu, že prohlížeče pořazují internetové hostitele pro stránky na straně klienta, musí webové servery a hostitelské služby přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index.html.*</span><span class="sxs-lookup"><span data-stu-id="2221f-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="2221f-129">Po vrácení *souboru index.html* Blazor převezme směrovač aplikace a odpoví správným zdrojem.</span><span class="sxs-lookup"><span data-stu-id="2221f-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="2221f-130">Při nasazování na server služby IIS můžete použít modul přepisování adres URL s publikovaným souborem *web.config* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="2221f-131">Další informace naleznete v části [IIS.](#iis)</span><span class="sxs-lookup"><span data-stu-id="2221f-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="2221f-132">Hostované nasazení s ASP.NET jádrem</span><span class="sxs-lookup"><span data-stu-id="2221f-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="2221f-133">*Hostované nasazení* slouží Blazor aplikaci WebAssembly do prohlížečů z [aplikace ASP.NET Core,](xref:index) která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="2221f-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="2221f-134">Klientská Blazor aplikace WebAssembly je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="2221f-135">Tyto dvě aplikace se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="2221f-135">The two apps are deployed together.</span></span> <span data-ttu-id="2221f-136">Webový server, který je schopen hostování aplikace ASP.NET Core je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="2221f-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="2221f-137">Pro`-ho|--hosted` hostované nasazení visual studio obsahuje šablonu `dotnet new` `blazorwasm` \*\* Blazor \*\* projektu aplikace WebAssembly App (šablona při použití [dotnet new](/dotnet/core/tools/dotnet-new) command) s vybranou volbou **Hosted** (při použití příkazu).</span><span class="sxs-lookup"><span data-stu-id="2221f-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="2221f-138">Další informace o hostování a nasazení <xref:host-and-deploy/index>aplikací ASP.NET Core najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="2221f-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2221f-139">Informace o nasazení do služby <xref:tutorials/publish-to-azure-webapp-using-vs>Azure App Service najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="2221f-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="2221f-140">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="2221f-140">Standalone deployment</span></span>

<span data-ttu-id="2221f-141">*Samostatné nasazení* slouží Blazor aplikaci WebAssembly jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="2221f-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="2221f-142">Aplikace je schopna obsluhovat Blazor libovolný statický souborový server.</span><span class="sxs-lookup"><span data-stu-id="2221f-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="2221f-143">Prostředky samostatného nasazení jsou publikovány do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="2221f-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="2221f-144">IIS</span><span class="sxs-lookup"><span data-stu-id="2221f-144">IIS</span></span>

<span data-ttu-id="2221f-145">Služba IIS je schopný Blazor statický souborový server pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="2221f-146">Informace o konfiguraci Blazorslužby IIS jako hostitele naleznete [v tématu Vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="2221f-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="2221f-147">Publikované datové zdroje jsou vytvářeny ve složce */bin/Release/{TARGET FRAMEWORK}/publish.*</span><span class="sxs-lookup"><span data-stu-id="2221f-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="2221f-148">Hostujte obsah složky *publikování* na webovém serveru nebo hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="2221f-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="2221f-149">Souboru web.config</span><span class="sxs-lookup"><span data-stu-id="2221f-149">web.config</span></span>

<span data-ttu-id="2221f-150">Při Blazor publikování projektu je vytvořen soubor *web.config* s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="2221f-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="2221f-151">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="2221f-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="2221f-152">*.dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="2221f-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="2221f-153">*.json* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="2221f-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="2221f-154">*.wasm* &ndash;`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="2221f-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="2221f-155">*.woff* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2221f-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="2221f-156">*.woff2* &ndash;`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2221f-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="2221f-157">Komprese HTTP je povolena pro následující typy MIME:</span><span class="sxs-lookup"><span data-stu-id="2221f-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="2221f-158">Pravidla modulu přepisování adres URL jsou stanovena:</span><span class="sxs-lookup"><span data-stu-id="2221f-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="2221f-159">Obsluhuj podadresář, ve kterém se nacházejí statické datové zdroje aplikace *(wwwroot/{PATH REQUESTED}*).</span><span class="sxs-lookup"><span data-stu-id="2221f-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="2221f-160">Vytvořte záložní směrování spa, aby byly požadavky na nesouborové datové zdroje přesměrovány na výchozí dokument aplikace ve složce statických datových zdrojů (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="2221f-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="2221f-161">Použití vlastního souboru web.config</span><span class="sxs-lookup"><span data-stu-id="2221f-161">Use a custom web.config</span></span>

<span data-ttu-id="2221f-162">Použití vlastního souboru *web.config:*</span><span class="sxs-lookup"><span data-stu-id="2221f-162">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="2221f-163">Umístěte vlastní soubor *web.config* do kořenového adresáře složky projektu.</span><span class="sxs-lookup"><span data-stu-id="2221f-163">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="2221f-164">Přidejte do souboru projektu následující cíl (*.csproj*):</span><span class="sxs-lookup"><span data-stu-id="2221f-164">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="2221f-165">Použití nastavené vlastnosti `<IsWebConfigTransformDisabled>` MSBuild na `true` Blazor není v aplikacích WebAssembly podporováno [stejně jako pro ASP.NET aplikace Core nasazené do služby IIS](xref:host-and-deploy/iis/index#webconfig-file).</span><span class="sxs-lookup"><span data-stu-id="2221f-165">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="2221f-166">Další informace naleznete v [tématu Blazor Copy target required to provide custom WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span><span class="sxs-lookup"><span data-stu-id="2221f-166">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="2221f-167">Instalace modulu přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="2221f-167">Install the URL Rewrite Module</span></span>

<span data-ttu-id="2221f-168">K přepsání adres URL je [nutný modul přepisování adres URL.](https://www.iis.net/downloads/microsoft/url-rewrite)</span><span class="sxs-lookup"><span data-stu-id="2221f-168">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="2221f-169">Modul není ve výchozím nastavení nainstalován a není k dispozici pro instalaci jako funkce služby role Web Server (IIS).</span><span class="sxs-lookup"><span data-stu-id="2221f-169">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="2221f-170">Modul musí být stažen z webových stránek iis.</span><span class="sxs-lookup"><span data-stu-id="2221f-170">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="2221f-171">Modul nainstalujte pomocí Instalační služby webové platformy:</span><span class="sxs-lookup"><span data-stu-id="2221f-171">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="2221f-172">Místně přejděte na [stránku stažení modulu přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="2221f-172">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="2221f-173">V anglické verzi vyberte **WebPI** a stáhněte instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="2221f-173">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="2221f-174">Pro ostatní jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte si instalační program.</span><span class="sxs-lookup"><span data-stu-id="2221f-174">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="2221f-175">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="2221f-175">Copy the installer to the server.</span></span> <span data-ttu-id="2221f-176">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="2221f-176">Run the installer.</span></span> <span data-ttu-id="2221f-177">Vyberte tlačítko **Instalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="2221f-177">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="2221f-178">Restartování serveru není po dokončení instalace nutné.</span><span class="sxs-lookup"><span data-stu-id="2221f-178">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="2221f-179">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="2221f-179">Configure the website</span></span>

<span data-ttu-id="2221f-180">Nastavte **fyzickou cestu** k složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-180">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="2221f-181">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="2221f-181">The folder contains:</span></span>

* <span data-ttu-id="2221f-182">Soubor *web.config,* který službu IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="2221f-182">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="2221f-183">Složka statických datových zdrojů aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-183">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="2221f-184">Hostovat jako podaplikaci iis</span><span class="sxs-lookup"><span data-stu-id="2221f-184">Host as an IIS sub-app</span></span>

<span data-ttu-id="2221f-185">Pokud je samostatná aplikace hostovaná jako podaplikace iis, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="2221f-185">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="2221f-186">Zakažte zděděnou obslužnou rutinu ASP.NET základního modulu.</span><span class="sxs-lookup"><span data-stu-id="2221f-186">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="2221f-187">Odeberte obslužnou rutinu v Blazor publikovaném `<handlers>` souboru *web.config* aplikace přidáním oddílu do souboru:</span><span class="sxs-lookup"><span data-stu-id="2221f-187">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="2221f-188">Zakažte `<system.webServer>` dědičnost oddílu kořenové `<location>` (nadřazené) aplikace pomocí prvku s `inheritInChildApplications` nastavenou na `false`:</span><span class="sxs-lookup"><span data-stu-id="2221f-188">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="2221f-189">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [konfigurace základní cesty aplikace](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2221f-189">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="2221f-190">Nastavte základní cestu aplikace v souboru *index.html* aplikace na alias služby IIS používaný při konfiguraci podaplikace ve službách IIS.</span><span class="sxs-lookup"><span data-stu-id="2221f-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="2221f-191">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="2221f-191">Troubleshooting</span></span>

<span data-ttu-id="2221f-192">Pokud je *přijata interní chyba serveru 500* a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, zkontrolujte, zda je nainstalován modul přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="2221f-192">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="2221f-193">Není-li modul nainstalován, nemůže být soubor *web.config* službou IIS analyzován.</span><span class="sxs-lookup"><span data-stu-id="2221f-193">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="2221f-194">Správce služby IIS tak nemůže načíst konfiguraci webu Blazora web ve zobrazování statických souborů webu.</span><span class="sxs-lookup"><span data-stu-id="2221f-194">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="2221f-195">Další informace o řešení potíží s nasazením ve službě IIS naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="2221f-195">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="2221f-196">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="2221f-196">Azure Storage</span></span>

<span data-ttu-id="2221f-197">Azure [Storage](/azure/storage/) statický soubor Blazor hosting umožňuje serverbez aplikace hosting.</span><span class="sxs-lookup"><span data-stu-id="2221f-197">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="2221f-198">Vlastní názvy domén, síť pro doručování obsahu Azure (CDN) a HTTPS jsou podporované.</span><span class="sxs-lookup"><span data-stu-id="2221f-198">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="2221f-199">Když je služba blob povolená pro statické hostování webových stránek na účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="2221f-199">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="2221f-200">Nastavte **název dokumentu Rejstříku** na . `index.html`</span><span class="sxs-lookup"><span data-stu-id="2221f-200">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="2221f-201">Nastavte **cestu dokumentu Chyby** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="2221f-201">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="2221f-202">Komponenty holicího strojku a jiné koncové body mimo soubor nejsou umístěny na fyzické cesty ve statickém obsahu uloženém službou objektů blob.</span><span class="sxs-lookup"><span data-stu-id="2221f-202">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="2221f-203">Pokud je přijat požadavek na jeden Blazor z těchto prostředků, který by měl směrovač zpracovat, chyba *404 - Not Found* generovaná službou objektů blob směruje požadavek na **cestu dokumentu Chyba**.</span><span class="sxs-lookup"><span data-stu-id="2221f-203">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="2221f-204">Je vrácen objekt blob *index.html* a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="2221f-204">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="2221f-205">Další informace najdete [v tématu Statický web hosting v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="2221f-205">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="2221f-206">Nginx</span><span class="sxs-lookup"><span data-stu-id="2221f-206">Nginx</span></span>

<span data-ttu-id="2221f-207">Následující soubor *nginx.conf* je zjednodušen, aby ukázal, jak nakonfigurovat Nginx pro odeslání souboru *index.html,* kdykoli nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="2221f-207">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="2221f-208">Další informace o konfiguraci produkčního webového serveru Nginx naleznete v [tématu Vytváření konfiguračních souborů NGINX Plus a NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="2221f-208">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="2221f-209">Nginx v Dockeru</span><span class="sxs-lookup"><span data-stu-id="2221f-209">Nginx in Docker</span></span>

<span data-ttu-id="2221f-210">Chcete-li hostovat Blazor v Dockeru pomocí Nginx, nastavte Dockerfile pro použití image Nginx založené na alpském.</span><span class="sxs-lookup"><span data-stu-id="2221f-210">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="2221f-211">Aktualizujte soubor Dockerfile a zkopírujte soubor *nginx.config* do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="2221f-211">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="2221f-212">Přidejte jeden řádek do dockerfile, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2221f-212">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="2221f-213">Apache</span><span class="sxs-lookup"><span data-stu-id="2221f-213">Apache</span></span>

<span data-ttu-id="2221f-214">Nasazení aplikace Blazor WebAssembly do CentOS 7 nebo novějšího:</span><span class="sxs-lookup"><span data-stu-id="2221f-214">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="2221f-215">Vytvořte konfigurační soubor Apache.</span><span class="sxs-lookup"><span data-stu-id="2221f-215">Create the Apache configuration file.</span></span> <span data-ttu-id="2221f-216">Následující příklad je zjednodušený konfigurační soubor (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="2221f-216">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="2221f-217">Umístěte konfigurační `/etc/httpd/conf.d/` soubor Apache do adresáře, což je výchozí konfigurační adresář Apache v CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="2221f-217">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="2221f-218">Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění `DocumentRoot` určené v konfiguračním souboru).</span><span class="sxs-lookup"><span data-stu-id="2221f-218">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="2221f-219">Restartujte službu Apache.</span><span class="sxs-lookup"><span data-stu-id="2221f-219">Restart the Apache service.</span></span>

<span data-ttu-id="2221f-220">Další informace naleznete [v tématu mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="2221f-220">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="2221f-221">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="2221f-221">GitHub Pages</span></span>

<span data-ttu-id="2221f-222">Chcete-li zpracovat přepsání adresy URL, přidejte soubor *404.html* se skriptem, který zpracovává přesměrování požadavku na stránku *index.html.*</span><span class="sxs-lookup"><span data-stu-id="2221f-222">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="2221f-223">Příklad implementace poskytované komunitou najdete [v tématu Jednostránkové aplikace pro stránky GitHub](https://spa-github-pages.rafrex.com/) [(rafrex/spa-github-pages na GitHubu).](https://github.com/rafrex/spa-github-pages#readme)</span><span class="sxs-lookup"><span data-stu-id="2221f-223">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="2221f-224">Příklad použití komunitního přístupu lze vidět na [blazor-demo/blazor-demo.github.io na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) [(live site](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="2221f-224">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="2221f-225">Pokud používáte web projektu místo webu organizace, `<base>` přidejte nebo aktualizujte značku v *souboru index.html*.</span><span class="sxs-lookup"><span data-stu-id="2221f-225">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="2221f-226">Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="2221f-226">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="2221f-227">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="2221f-227">Host configuration values</span></span>

<span data-ttu-id="2221f-228">Aplikace WebAssembly mohou přijímat následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="2221f-228">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="2221f-229">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="2221f-229">Content root</span></span>

<span data-ttu-id="2221f-230">Argument `--contentroot` nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace[(kořen obsahu).](xref:fundamentals/index#content-root)</span><span class="sxs-lookup"><span data-stu-id="2221f-230">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="2221f-231">V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-231">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="2221f-232">Předajte argument při místním spuštění aplikace na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="2221f-232">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2221f-233">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="2221f-233">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="2221f-234">Přidejte položku do souboru *launchSettings.json* aplikace v profilu **Služby IIS Express.**</span><span class="sxs-lookup"><span data-stu-id="2221f-234">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2221f-235">Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run`aplikací .</span><span class="sxs-lookup"><span data-stu-id="2221f-235">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="2221f-236">V sadě Visual Studio zadejte argument v**argumentech aplikace\*\*\*\*ladění** >  **vlastností** > .</span><span class="sxs-lookup"><span data-stu-id="2221f-236">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2221f-237">Nastavení argumentu na stránce vlastností sady Visual Studio přidá argument do souboru *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2221f-237">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="2221f-238">Základna cesty</span><span class="sxs-lookup"><span data-stu-id="2221f-238">Path base</span></span>

<span data-ttu-id="2221f-239">Argument `--pathbase` nastaví základní cestu aplikace pro aplikaci spuštěnou místně s `<base>` nekořenovou relativní adresou URL (značka `href` je nastavena na jinou cestu než `/` pro pracovní a produkční).</span><span class="sxs-lookup"><span data-stu-id="2221f-239">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="2221f-240">V následujících příkladech `/relative-URL-path` je základem cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="2221f-240">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="2221f-241">Další informace naleznete v [tématu Základní cesta aplikace](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="2221f-241">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2221f-242">Na rozdíl od `href` cesty `<base>` poskytnuté ke značce nezahrnte při `--pathbase` předávání hodnoty argumentu koncové lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="2221f-242">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="2221f-243">Pokud je základní cesta aplikace `<base>` k `<base href="/CoolApp/">` dispozici ve značce jako (obsahuje koncové lomítko), předaj hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="2221f-243">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="2221f-244">Předajte argument při místním spuštění aplikace na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="2221f-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2221f-245">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="2221f-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="2221f-246">Přidejte položku do souboru *launchSettings.json* aplikace v profilu **Služby IIS Express.**</span><span class="sxs-lookup"><span data-stu-id="2221f-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2221f-247">Toto nastavení se používá při spuštění aplikace s ladicím `dotnet run`programem sady Visual Studio a z příkazového řádku s aplikací .</span><span class="sxs-lookup"><span data-stu-id="2221f-247">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="2221f-248">V sadě Visual Studio zadejte argument v**argumentech aplikace\*\*\*\*ladění** >  **vlastností** > .</span><span class="sxs-lookup"><span data-stu-id="2221f-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2221f-249">Nastavení argumentu na stránce vlastností sady Visual Studio přidá argument do souboru *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2221f-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="2221f-250">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="2221f-250">URLs</span></span>

<span data-ttu-id="2221f-251">Argument `--urls` nastaví adresy IP nebo hostitelské adresy s porty a protokoly pro naslouchání požadavkům.</span><span class="sxs-lookup"><span data-stu-id="2221f-251">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="2221f-252">Předajte argument při místním spuštění aplikace na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="2221f-252">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2221f-253">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="2221f-253">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="2221f-254">Přidejte položku do souboru *launchSettings.json* aplikace v profilu **Služby IIS Express.**</span><span class="sxs-lookup"><span data-stu-id="2221f-254">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2221f-255">Toto nastavení se používá při spuštění aplikace s ladicím `dotnet run`programem sady Visual Studio a z příkazového řádku s aplikací .</span><span class="sxs-lookup"><span data-stu-id="2221f-255">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="2221f-256">V sadě Visual Studio zadejte argument v**argumentech aplikace\*\*\*\*ladění** >  **vlastností** > .</span><span class="sxs-lookup"><span data-stu-id="2221f-256">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2221f-257">Nastavení argumentu na stránce vlastností sady Visual Studio přidá argument do souboru *launchSettings.json.*</span><span class="sxs-lookup"><span data-stu-id="2221f-257">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="2221f-258">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="2221f-258">Configure the Linker</span></span>

Blazor<span data-ttu-id="2221f-259">provede propojení zprostředkujícíjazyk (IL) na každé sestavení verze odebrat zbytečné IL z výstupních sestavení.</span><span class="sxs-lookup"><span data-stu-id="2221f-259"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="2221f-260">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="2221f-260">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
