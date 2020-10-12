---
title: ASP.NET Core hostitele a nasazení Blazor WebAssembly
author: guardrex
description: Naučte se hostovat a nasazovat Blazor aplikaci pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 63954bd2fbb8fdb2e347d552a10adc52263c3ad6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900710"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="9be54-103">ASP.NET Core hostitele a nasazení Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9be54-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="9be54-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Skořepa](https://github.com/danroth27), [Robert Adams](https://twitter.com/ben_a_adams)a [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="9be54-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="9be54-105">S [ Blazor WebAssembly modelem hostování](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="9be54-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="9be54-106">BlazorAplikace, její závislosti a modul runtime .NET jsou stahovány do prohlížeče paralelně.</span><span class="sxs-lookup"><span data-stu-id="9be54-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="9be54-107">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9be54-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="9be54-108">Podporují se tyto strategie nasazení:</span><span class="sxs-lookup"><span data-stu-id="9be54-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="9be54-109">BlazorAplikaci obsluhuje aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9be54-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="9be54-110">Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .</span><span class="sxs-lookup"><span data-stu-id="9be54-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="9be54-111">BlazorAplikace se umístí na statický hostující webový server nebo službu, kde rozhraní .NET se k obsluze aplikace nepoužívá Blazor .</span><span class="sxs-lookup"><span data-stu-id="9be54-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="9be54-112">Tato strategie je popsaná v části [samostatné nasazení](#standalone-deployment) , která obsahuje informace o hostování Blazor WebAssembly aplikace jako dílčí aplikace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9be54-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="9be54-113">Komprese</span><span class="sxs-lookup"><span data-stu-id="9be54-113">Compression</span></span>

<span data-ttu-id="9be54-114">Při Blazor WebAssembly publikování aplikace je výstup během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu.</span><span class="sxs-lookup"><span data-stu-id="9be54-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="9be54-115">Používají se následující kompresní algoritmy:</span><span class="sxs-lookup"><span data-stu-id="9be54-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="9be54-116">[Brotli](https://tools.ietf.org/html/rfc7932) (nejvyšší úroveň)</span><span class="sxs-lookup"><span data-stu-id="9be54-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="9be54-117">GZIP</span><span class="sxs-lookup"><span data-stu-id="9be54-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="9be54-118">Blazor spoléhá na hostitele, který obsluhuje příslušné komprimované soubory.</span><span class="sxs-lookup"><span data-stu-id="9be54-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="9be54-119">Při použití hostovaného projektu ASP.NET Core je hostitelský projekt schopný provádět vyjednávání obsahu a obsluhovat staticky komprimované soubory.</span><span class="sxs-lookup"><span data-stu-id="9be54-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="9be54-120">Při hostování Blazor WebAssembly samostatné aplikace může být nutné provést další práci, aby bylo zajištěno, že budou obsluhovány staticky komprimované soubory:</span><span class="sxs-lookup"><span data-stu-id="9be54-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="9be54-121">`web.config`Konfiguraci komprese služby IIS najdete v části [IIS: Brotli a komprese GZip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="9be54-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="9be54-122">Při hostování řešení statického hostování, které nepodporují vyjednávání se staticky komprimovaným souborem, jako jsou stránky GitHubu, zvažte konfiguraci aplikace pro načtení a dekódování Brotli komprimovaných souborů:</span><span class="sxs-lookup"><span data-stu-id="9be54-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="9be54-123">Získejte dekodér JavaScript Brotli z [úložiště GitHub Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="9be54-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="9be54-124">Od září 2020 se soubor dekodéru pojmenuje `decode.js` a nalezne se ve [ `js` složce](https://github.com/google/brotli/tree/master/js)úložiště.</span><span class="sxs-lookup"><span data-stu-id="9be54-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="9be54-125">Regrese je k dispozici ve verzi minifikovaného `decode.js` skriptu ( `decode.min.js` ) v [úložišti GitHub Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="9be54-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="9be54-126">Buď minimalizuje skript sami, nebo použijte [balíček npm](https://www.npmjs.com/package/brotli) , dokud není [okno problému. BrotliDecode není nastaveno v decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) vyřešeno.</span><span class="sxs-lookup"><span data-stu-id="9be54-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="9be54-127">Vzorový kód v této části používá verzi **unminified** skriptu.</span><span class="sxs-lookup"><span data-stu-id="9be54-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="9be54-128">Aktualizujte aplikaci tak, aby používala dekodér.</span><span class="sxs-lookup"><span data-stu-id="9be54-128">Update the app to use the decoder.</span></span> <span data-ttu-id="9be54-129">Změňte značku uvnitř uzavírací `<body>` značky v následujícím formátu `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="9be54-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="9be54-130">Chcete-li vypnout kompresi, přidejte `BlazorEnableCompression` do souboru projektu aplikace vlastnost MSBuild a nastavte hodnotu na `false` :</span><span class="sxs-lookup"><span data-stu-id="9be54-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="9be54-131">`BlazorEnableCompression`Vlastnost může být předána [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s následující syntaxí v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="9be54-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="9be54-132">Přepište adresy URL pro správné směrování.</span><span class="sxs-lookup"><span data-stu-id="9be54-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="9be54-133">Požadavky směrování na součásti stránky v Blazor WebAssembly aplikaci nejsou stejně jednoduché jako požadavky směrování v Blazor Server hostované aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9be54-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="9be54-134">Vezměte v úvahu Blazor WebAssembly aplikaci se dvěma součástmi:</span><span class="sxs-lookup"><span data-stu-id="9be54-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="9be54-135">`Main.razor`: Načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="9be54-136">`About.razor`: `About` součást.</span><span class="sxs-lookup"><span data-stu-id="9be54-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="9be54-137">Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/` ):</span><span class="sxs-lookup"><span data-stu-id="9be54-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="9be54-138">Prohlížeč vytvoří požadavek.</span><span class="sxs-lookup"><span data-stu-id="9be54-138">The browser makes a request.</span></span>
1. <span data-ttu-id="9be54-139">Vrátí se výchozí stránka, což je obvykle `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9be54-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="9be54-140">`index.html` napředá aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9be54-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="9be54-141">Blazorse načte směrovač a Razor `Main` Komponenta se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="9be54-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="9be54-142">Na hlavní stránce vyberte odkaz na `About` komponentu na klientovi, protože Blazor směrovač zastaví v prohlížeči, aby odeslal požadavek na Internet `www.contoso.com` pro `About` a sloužil přímo vykreslené `About` součásti.</span><span class="sxs-lookup"><span data-stu-id="9be54-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="9be54-143">Všechny požadavky na vnitřní koncové body *v Blazor WebAssembly aplikaci* fungují stejným způsobem: požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu.</span><span class="sxs-lookup"><span data-stu-id="9be54-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="9be54-144">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="9be54-144">The router handles the requests internally.</span></span>

<span data-ttu-id="9be54-145">Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About` , požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="9be54-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="9be54-146">Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 – Nenalezeno* .</span><span class="sxs-lookup"><span data-stu-id="9be54-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="9be54-147">Vzhledem k tomu, že prohlížeče připravují žádosti na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou na stránce fyzicky na serveru `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9be54-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="9be54-148">Když `index.html` se vrátí, Blazor směrovač aplikace převezme a odpoví správným prostředkem.</span><span class="sxs-lookup"><span data-stu-id="9be54-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="9be54-149">Při nasazování na server služby IIS můžete použít modul pro přepsání adresy URL s publikovaným `web.config` souborem aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="9be54-150">Další informace najdete v části [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="9be54-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="9be54-151">Hostované nasazení s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9be54-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="9be54-152">*Hostované nasazení* obsluhuje Blazor WebAssembly aplikaci prohlížeči z [ASP.NET Core aplikace](xref:index) , která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="9be54-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="9be54-153">Klientská Blazor WebAssembly aplikace se publikuje do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="9be54-154">Obě aplikace se nasazují dohromady.</span><span class="sxs-lookup"><span data-stu-id="9be54-154">The two apps are deployed together.</span></span> <span data-ttu-id="9be54-155">Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9be54-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="9be54-156">V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu \*\* Blazor WebAssembly aplikace\*\* ( `blazorwasm` Šablona při použití [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu) s **`Hosted`** vybranou možností ( `-ho|--hosted` při použití `dotnet new` příkazu).</span><span class="sxs-lookup"><span data-stu-id="9be54-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="9be54-157">Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index> .</span><span class="sxs-lookup"><span data-stu-id="9be54-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="9be54-158">Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs> .</span><span class="sxs-lookup"><span data-stu-id="9be54-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="9be54-159">Hostované nasazení s více Blazor WebAssembly aplikacemi</span><span class="sxs-lookup"><span data-stu-id="9be54-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="9be54-160">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="9be54-160">App configuration</span></span>

<span data-ttu-id="9be54-161">Konfigurace hostovaného Blazor řešení pro poskytování více Blazor WebAssembly aplikací:</span><span class="sxs-lookup"><span data-stu-id="9be54-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="9be54-162">Použijte existující hostované Blazor řešení nebo vytvořte nové řešení ze Blazor šablony hostovaného projektu.</span><span class="sxs-lookup"><span data-stu-id="9be54-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="9be54-163">V souboru projektu klientské aplikace přidejte `<StaticWebAssetBasePath>` vlastnost do pole `<PropertyGroup>` s hodnotou, `FirstApp` abyste nastavili základní cestu pro statické prostředky projektu:</span><span class="sxs-lookup"><span data-stu-id="9be54-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="9be54-164">Přidání druhé klientské aplikace do řešení:</span><span class="sxs-lookup"><span data-stu-id="9be54-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="9be54-165">Přidejte do složky řešení složku s názvem `SecondClient` .</span><span class="sxs-lookup"><span data-stu-id="9be54-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="9be54-166">Vytvořte Blazor WebAssembly aplikaci s názvem `SecondBlazorApp.Client` ve `SecondClient` složce ze Blazor WebAssembly šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="9be54-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="9be54-167">V souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="9be54-167">In the app's project file:</span></span>

    * <span data-ttu-id="9be54-168">Přidejte `<StaticWebAssetBasePath>` vlastnost do pole `<PropertyGroup>` s hodnotou `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="9be54-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="9be54-169">Přidat odkaz na projekt do `Shared` projektu:</span><span class="sxs-lookup"><span data-stu-id="9be54-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="9be54-170">Zástupný symbol `{SOLUTION NAME}` je název řešení.</span><span class="sxs-lookup"><span data-stu-id="9be54-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="9be54-171">V souboru projektu aplikace serveru vytvořte odkaz na projekt pro přidanou klientskou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="9be54-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="9be54-172">V souboru serverové aplikace `Properties/launchSettings.json` Nakonfigurujte `applicationUrl` profil Kestrel ( `{SOLUTION NAME}.Server` ) pro přístup k klientským aplikacím na portech 5001 a 5002:</span><span class="sxs-lookup"><span data-stu-id="9be54-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="9be54-173">V metodě serverové aplikace `Startup.Configure` ( `Startup.cs` ) odeberte následující řádky, které se zobrazí po volání <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="9be54-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="9be54-174">Přidejte middleware, který mapuje požadavky na klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="9be54-175">Následující příklad konfiguruje middleware ke spuštění v následujících případech:</span><span class="sxs-lookup"><span data-stu-id="9be54-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="9be54-176">Port žádosti je buď 5001 pro původní klientskou aplikaci, nebo 5002 pro přidanou klientskou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9be54-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="9be54-177">Hostitel žádosti je buď `firstapp.com` pro původní klientskou aplikaci, nebo `secondapp.com` pro přidanou klientskou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9be54-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="9be54-178">Příklad uvedený v této části vyžaduje další konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="9be54-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="9be54-179">Přístup k aplikacím v ukázkových doménách hostitelů `firstapp.com` a `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="9be54-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="9be54-180">Certifikáty pro klientské aplikace, které umožňují zabezpečení TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9be54-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="9be54-181">Požadovaná konfigurace překračuje rozsah tohoto článku a závisí na tom, jak je řešení hostované.</span><span class="sxs-lookup"><span data-stu-id="9be54-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="9be54-182">Další informace najdete v [článcích o hostiteli a nasazení](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="9be54-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="9be54-183">Vložte následující kód, kde byly řádky odebrány dříve:</span><span class="sxs-lookup"><span data-stu-id="9be54-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="9be54-184">V řadiči pro předpověď počasí aplikace serveru ( `Controllers/WeatherForecastController.cs` ) nahraďte stávající trasu ( `[Route("[controller]")]` ) následujícími trasami `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="9be54-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="9be54-185">Middleware přidané do metody serverové aplikace `Startup.Configure` dříve mění příchozí požadavky na `/WeatherForecast` buď `/FirstApp/WeatherForecast` nebo `/SecondApp/WeatherForecast` v závislosti na portu (5001/5002) nebo doméně ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="9be54-186">Aby bylo možné vracet údaje o počasí z aplikace serveru do klientských aplikací, je nutné, aby byly předchozí trasy řadiče.</span><span class="sxs-lookup"><span data-stu-id="9be54-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="9be54-187">Statické prostředky a knihovny tříd</span><span class="sxs-lookup"><span data-stu-id="9be54-187">Static assets and class libraries</span></span>

<span data-ttu-id="9be54-188">Pro statické prostředky použijte následující přístupy:</span><span class="sxs-lookup"><span data-stu-id="9be54-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="9be54-189">Pokud je prostředek ve složce klientské aplikace `wwwroot` , zadejte jejich cesty normálně:</span><span class="sxs-lookup"><span data-stu-id="9be54-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="9be54-190">Pokud je Asset ve `wwwroot` složce [ Razor knihovny tříd (RCL)](xref:blazor/components/class-libraries), odkazujte na statický prostředek v klientské aplikaci podle pokynů v [článku RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="9be54-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9be54-191">Na součásti, které jsou k klientské aplikaci poskytovány knihovny tříd, se obvykle odkazuje.</span><span class="sxs-lookup"><span data-stu-id="9be54-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="9be54-192">Pokud některé komponenty vyžadují šablony stylů nebo soubory JavaScriptu, použijte k získání statických prostředků některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9be54-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="9be54-193">Soubor klientské aplikace `wwwroot/index.html` může propojit ( `<link>` ) se statickými prostředky.</span><span class="sxs-lookup"><span data-stu-id="9be54-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="9be54-194">Komponenta může použít [ `Link` komponentu](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) rozhraní pro získání statických prostředků.</span><span class="sxs-lookup"><span data-stu-id="9be54-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="9be54-195">Předchozí přístupy jsou znázorněné v následujících příkladech.</span><span class="sxs-lookup"><span data-stu-id="9be54-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9be54-196">Na součásti, které jsou k klientské aplikaci poskytovány knihovny tříd, se obvykle odkazuje.</span><span class="sxs-lookup"><span data-stu-id="9be54-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="9be54-197">Pokud některé komponenty vyžadují šablony stylů nebo soubory JavaScriptu, musí soubor klientské aplikace `wwwroot/index.html` zahrnovat správné propojení statických prostředků.</span><span class="sxs-lookup"><span data-stu-id="9be54-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="9be54-198">Tyto přístupy jsou znázorněné v následujících příkladech.</span><span class="sxs-lookup"><span data-stu-id="9be54-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="9be54-199">Přidejte následující `Jeep` komponentu do jedné z klientských aplikací.</span><span class="sxs-lookup"><span data-stu-id="9be54-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="9be54-200">`Jeep`Komponenta používá:</span><span class="sxs-lookup"><span data-stu-id="9be54-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="9be54-201">Obrázek ze složky klientské aplikace `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="9be54-202">Obrázek z přidané složky [ Razor knihovny součástí](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` ( `jeep-yj.png` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="9be54-203">Ukázková součást ( `Component1` ) je vytvořena automaticky šablonou projektu RCL při `JeepImage` Přidání knihovny do řešení.</span><span class="sxs-lookup"><span data-stu-id="9be54-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="9be54-204">**Nezveřejňujte** image vozidel veřejně, pokud Image nevlastníte.</span><span class="sxs-lookup"><span data-stu-id="9be54-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="9be54-205">V opačném případě riskujete porušení autorských práv.</span><span class="sxs-lookup"><span data-stu-id="9be54-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9be54-206">`jeep-yj.png`Bitovou kopii knihovny lze také přidat do `Component1` komponenty knihovny ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="9be54-207">Chcete-li poskytnout `my-component` třídu CSS na stránku klientské aplikace, propojte na šablonu stylů knihovny pomocí [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9be54-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="9be54-208">Alternativou k použití [ `Link` komponenty](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) je načtení šablony stylů ze souboru klientské aplikace `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="9be54-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="9be54-209">Tento přístup zpřístupňuje šablonu stylů všem součástem v klientské aplikaci:</span><span class="sxs-lookup"><span data-stu-id="9be54-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9be54-210">`jeep-yj.png`Bitovou kopii knihovny lze také přidat do `Component1` komponenty knihovny ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="9be54-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="9be54-211">Soubor klientské aplikace `wwwroot/index.html` požaduje šablonu stylů knihovny s následující přidanou `<link>` značkou:</span><span class="sxs-lookup"><span data-stu-id="9be54-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="9be54-212">Přidat navigaci k `Jeep` součásti v součásti klientské aplikace `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="9be54-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="9be54-213">Další informace o RCLs najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="9be54-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="9be54-214">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="9be54-214">Standalone deployment</span></span>

<span data-ttu-id="9be54-215">*Samostatné nasazení* obsluhuje Blazor WebAssembly aplikaci jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="9be54-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="9be54-216">Každý statický souborový server může Blazor aplikaci zpracovat.</span><span class="sxs-lookup"><span data-stu-id="9be54-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="9be54-217">Samostatné prostředky nasazení jsou publikovány do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="9be54-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="9be54-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9be54-218">Azure App Service</span></span>

<span data-ttu-id="9be54-219">Blazor WebAssembly aplikace se dají nasadit do Azure App Services v systému Windows, které hostují aplikaci ve [službě IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="9be54-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="9be54-220">Nasazení samostatné Blazor WebAssembly aplikace do Azure App Service pro Linux není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="9be54-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="9be54-221">Image serveru pro Linux, která je hostitelem aplikace, není v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9be54-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="9be54-222">Pro povolení tohoto scénáře probíhá práce.</span><span class="sxs-lookup"><span data-stu-id="9be54-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="9be54-223">IIS</span><span class="sxs-lookup"><span data-stu-id="9be54-223">IIS</span></span>

<span data-ttu-id="9be54-224">Služba IIS je schopným statickým souborovým serverem pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="9be54-225">Chcete-li nakonfigurovat službu IIS na hostování Blazor , přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="9be54-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="9be54-226">Publikované assety se vytvoří ve `/bin/Release/{TARGET FRAMEWORK}/publish` složce.</span><span class="sxs-lookup"><span data-stu-id="9be54-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="9be54-227">Hostovat obsah `publish` složky na webovém serveru nebo v hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="9be54-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="9be54-228">web.config</span><span class="sxs-lookup"><span data-stu-id="9be54-228">web.config</span></span>

<span data-ttu-id="9be54-229">Při Blazor publikování projektu se vytvoří `web.config` soubor s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="9be54-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="9be54-230">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="9be54-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="9be54-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="9be54-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="9be54-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="9be54-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="9be54-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="9be54-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="9be54-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9be54-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="9be54-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9be54-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="9be54-236">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="9be54-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="9be54-237">Pravidla pro přepis adres URL jsou navázána:</span><span class="sxs-lookup"><span data-stu-id="9be54-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="9be54-238">Slouží jako podadresáře, kde se nachází statické prostředky aplikace ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="9be54-239">Vytvořte záložní záložní postup, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce static assets ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="9be54-240">Použití vlastního web.config</span><span class="sxs-lookup"><span data-stu-id="9be54-240">Use a custom web.config</span></span>

<span data-ttu-id="9be54-241">Chcete-li použít vlastní `web.config` soubor, umístěte vlastní `web.config` soubor do kořenové složky projektu a publikujte projekt.</span><span class="sxs-lookup"><span data-stu-id="9be54-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="9be54-242">Instalace modulu URL pro přepis</span><span class="sxs-lookup"><span data-stu-id="9be54-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="9be54-243">Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) .</span><span class="sxs-lookup"><span data-stu-id="9be54-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="9be54-244">Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="9be54-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="9be54-245">Modul se musí stáhnout z webu IIS.</span><span class="sxs-lookup"><span data-stu-id="9be54-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="9be54-246">K instalaci modulu použijte instalační program webové platformy:</span><span class="sxs-lookup"><span data-stu-id="9be54-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="9be54-247">Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="9be54-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="9be54-248">V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="9be54-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="9be54-249">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.</span><span class="sxs-lookup"><span data-stu-id="9be54-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="9be54-250">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="9be54-250">Copy the installer to the server.</span></span> <span data-ttu-id="9be54-251">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="9be54-251">Run the installer.</span></span> <span data-ttu-id="9be54-252">Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="9be54-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="9be54-253">Po dokončení instalace není restartování serveru vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="9be54-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="9be54-254">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="9be54-254">Configure the website</span></span>

<span data-ttu-id="9be54-255">Nastavte **fyzickou cestu** webu na složku aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="9be54-256">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="9be54-256">The folder contains:</span></span>

* <span data-ttu-id="9be54-257">`web.config`Soubor, který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="9be54-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="9be54-258">Složka statických prostředků aplikace</span><span class="sxs-lookup"><span data-stu-id="9be54-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="9be54-259">Hostování jako dílčí aplikace služby IIS</span><span class="sxs-lookup"><span data-stu-id="9be54-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="9be54-260">Pokud je samostatná aplikace hostovaná jako dílčí aplikace služby IIS, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="9be54-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="9be54-261">Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9be54-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="9be54-262">Odeberte obslužnou rutinu v Blazor publikovaném `web.config` souboru aplikace tak, že `<handlers>` do souboru přidáte oddíl:</span><span class="sxs-lookup"><span data-stu-id="9be54-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="9be54-263">Zakažte dědění kořenového oddílu (nadřazené) aplikace `<system.webServer>` pomocí `<location>` elementu s `inheritInChildApplications` nastavením na `false` :</span><span class="sxs-lookup"><span data-stu-id="9be54-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="9be54-264">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě [Konfigurace základní cesty aplikace](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="9be54-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="9be54-265">Nastavte základní cestu aplikace v `index.html` souboru aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.</span><span class="sxs-lookup"><span data-stu-id="9be54-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="9be54-266">Komprese Brotli a gzip</span><span class="sxs-lookup"><span data-stu-id="9be54-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="9be54-267">Službu IIS je možné nakonfigurovat prostřednictvím služby `web.config` za účelem poskytování komprimovaných prostředků Brotli nebo gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="9be54-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="9be54-268">Příklad konfigurace najdete v tématu [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="9be54-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="9be54-269">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9be54-269">Troubleshooting</span></span>

<span data-ttu-id="9be54-270">Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis.</span><span class="sxs-lookup"><span data-stu-id="9be54-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="9be54-271">Pokud modul není nainstalován, `web.config` soubor nelze analyzovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="9be54-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="9be54-272">Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web ze Blazor statických souborů obsluhy.</span><span class="sxs-lookup"><span data-stu-id="9be54-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="9be54-273">Další informace o řešení potíží s nasazeními služby IIS najdete v tématu <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="9be54-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="9be54-274">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="9be54-274">Azure Storage</span></span>

<span data-ttu-id="9be54-275">Hostování statického souboru [Azure Storage](/azure/storage/) umožňuje Blazor hostování aplikací bez serveru.</span><span class="sxs-lookup"><span data-stu-id="9be54-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="9be54-276">Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9be54-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="9be54-277">Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="9be54-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="9be54-278">Nastavte **název dokumentu indexu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9be54-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="9be54-279">Nastavte **cestu k chybovému dokumentu** na `index.html` .</span><span class="sxs-lookup"><span data-stu-id="9be54-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="9be54-280">Razor součásti a jiné koncové body, které nejsou v souboru, se neukládají na fyzických cestách ve statickém obsahu uloženém službou BLOB Service.</span><span class="sxs-lookup"><span data-stu-id="9be54-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="9be54-281">Když se přijme žádost o jeden z těchto prostředků, kterou Blazor by měl směrovač zpracovat, chyba *404 – nenalezená* služba BLOB Service směruje požadavek na **cestu k chybovému dokumentu**.</span><span class="sxs-lookup"><span data-stu-id="9be54-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="9be54-282">`index.html`Vrátí se objekt BLOB a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="9be54-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="9be54-283">Pokud nejsou za běhu načteny soubory z důvodu nevhodných typů MIME v `Content-Type` hlavičkách souborů, proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="9be54-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="9be54-284">Nakonfigurujte nástroje tak, aby při nasazení souborů nastavily správné typy MIME ( `Content-Type` hlavičky).</span><span class="sxs-lookup"><span data-stu-id="9be54-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="9be54-285">Změňte typy MIME ( `Content-Type` hlavičky) souborů po nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="9be54-286">V Průzkumník služby Storage (Azure Portal) pro každý soubor:</span><span class="sxs-lookup"><span data-stu-id="9be54-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="9be54-287">Klikněte na soubor pravým tlačítkem a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="9be54-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="9be54-288">Nastavte **ContentType** a klikněte na tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="9be54-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="9be54-289">Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="9be54-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="9be54-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="9be54-290">Nginx</span></span>

<span data-ttu-id="9be54-291">Následující `nginx.conf` soubor je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání `index.html` souboru pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="9be54-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="9be54-292">Při nastavování [limitu přenosové rychlosti Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) pomocí můžou [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) Blazor WebAssembly aplikace vyžadovat velkou `burst` hodnotu parametru, aby odpovídala poměrně velkému počtu požadavků, které aplikace vytvořila.</span><span class="sxs-lookup"><span data-stu-id="9be54-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="9be54-293">Zpočátku nastavte hodnotu aspoň 60:</span><span class="sxs-lookup"><span data-stu-id="9be54-293">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="9be54-294">Zvyšte hodnotu, pokud nástroje pro vývojáře v prohlížeči nebo síťový provoz označují, že žádosti dostávají stavový kód *Nedostupnosti 503-Service* .</span><span class="sxs-lookup"><span data-stu-id="9be54-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="9be54-295">Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="9be54-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="9be54-296">Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="9be54-296">Nginx in Docker</span></span>

<span data-ttu-id="9be54-297">Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile na použití Nginx image založené na Alpine.</span><span class="sxs-lookup"><span data-stu-id="9be54-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="9be54-298">Aktualizujte souboru Dockerfile tak, aby se `nginx.config` soubor zkopíroval do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="9be54-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="9be54-299">Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9be54-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="9be54-300">Apache</span><span class="sxs-lookup"><span data-stu-id="9be54-300">Apache</span></span>

<span data-ttu-id="9be54-301">Nasazení Blazor WebAssembly aplikace na CentOS 7 nebo novější:</span><span class="sxs-lookup"><span data-stu-id="9be54-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="9be54-302">Vytvořte konfigurační soubor Apache.</span><span class="sxs-lookup"><span data-stu-id="9be54-302">Create the Apache configuration file.</span></span> <span data-ttu-id="9be54-303">V následujícím příkladu je zjednodušený konfigurační soubor ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="9be54-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="9be54-304">Soubor konfigurace Apache umístěte do `/etc/httpd/conf.d/` adresáře, který je výchozím adresářem konfigurace Apache v CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="9be54-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="9be54-305">Umístěte soubory aplikace do `/var/www/blazorapp` adresáře (umístění zadaného do `DocumentRoot` konfiguračního souboru).</span><span class="sxs-lookup"><span data-stu-id="9be54-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="9be54-306">Restartujte službu Apache.</span><span class="sxs-lookup"><span data-stu-id="9be54-306">Restart the Apache service.</span></span>

<span data-ttu-id="9be54-307">Další informace najdete v tématech [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) a [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="9be54-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="9be54-308">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="9be54-308">GitHub Pages</span></span>

<span data-ttu-id="9be54-309">Chcete-li zpracovat přepisy adresy URL, přidejte `wwwroot/404.html` soubor se skriptem, který zpracovává přesměrování požadavku na `index.html` stránku.</span><span class="sxs-lookup"><span data-stu-id="9be54-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="9be54-310">Příklad najdete v [ Blazor úložišti GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="9be54-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="9be54-311">[Živý web](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="9be54-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="9be54-312">Při použití webu projektu namísto webu organizace aktualizujte `<base>` značku v `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="9be54-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="9be54-313">Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="9be54-314">V [ Blazor úložišti GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)se základ `href` aktualizuje při publikování pomocí [ `.github/workflows/main.yml` konfiguračního souboru](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="9be54-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="9be54-315">[ Blazor Úložiště GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) není vlastněné, udržované ani podporované rozhraním .NET Foundation ani Microsoftem.</span><span class="sxs-lookup"><span data-stu-id="9be54-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="9be54-316">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="9be54-316">Host configuration values</span></span>

<span data-ttu-id="9be54-317">[ Blazor WebAssembly aplikace](xref:blazor/hosting-models#blazor-webassembly) mohou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9be54-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="9be54-318">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="9be54-318">Content root</span></span>

<span data-ttu-id="9be54-319">`--contentroot`Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace ([kořen obsahu](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="9be54-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="9be54-320">V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="9be54-321">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9be54-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9be54-322">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="9be54-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="9be54-323">Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9be54-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="9be54-324">Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9be54-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="9be54-325">V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9be54-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9be54-326">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.</span><span class="sxs-lookup"><span data-stu-id="9be54-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="9be54-327">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="9be54-327">Path base</span></span>

<span data-ttu-id="9be54-328">`--pathbase`Argument nastaví základní cestu aplikace pro aplikaci spouštěnou místně s nekořenovou cestou relativní adresy URL ( `<base>` značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční).</span><span class="sxs-lookup"><span data-stu-id="9be54-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="9be54-329">V následujících příkladech `/relative-URL-path` je základ cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="9be54-330">Další informace najdete v tématu [základní cesta k aplikaci](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="9be54-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9be54-331">Na rozdíl od cesty, která je k dispozici pro `href` `<base>` značku, nezahrnujte koncové lomítko ( `/` ) při předávání `--pathbase` hodnoty argumentu.</span><span class="sxs-lookup"><span data-stu-id="9be54-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="9be54-332">Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="9be54-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="9be54-333">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9be54-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9be54-334">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="9be54-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="9be54-335">Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9be54-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="9be54-336">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9be54-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="9be54-337">V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9be54-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9be54-338">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.</span><span class="sxs-lookup"><span data-stu-id="9be54-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="9be54-339">Adresy URL</span><span class="sxs-lookup"><span data-stu-id="9be54-339">URLs</span></span>

<span data-ttu-id="9be54-340">`--urls`Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.</span><span class="sxs-lookup"><span data-stu-id="9be54-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="9be54-341">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9be54-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9be54-342">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="9be54-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="9be54-343">Přidejte položku do `launchSettings.json` souboru aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="9be54-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="9be54-344">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="9be54-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="9be54-345">V aplikaci Visual Studio zadejte argument v části **vlastnosti**  >  **ladit**  >  **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9be54-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9be54-346">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá do `launchSettings.json` souboru Argument.</span><span class="sxs-lookup"><span data-stu-id="9be54-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="9be54-347">Konfigurace ořezávání</span><span class="sxs-lookup"><span data-stu-id="9be54-347">Configure the Trimmer</span></span>

<span data-ttu-id="9be54-348">Blazor provede oříznutí mezilehlého jazyka (IL) pro každé sestavení vydané verze, aby se odebralo zbytečné IL z výstupních sestavení.</span><span class="sxs-lookup"><span data-stu-id="9be54-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="9be54-349">Další informace naleznete v tématu <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="9be54-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="9be54-350">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="9be54-350">Configure the Linker</span></span>

<span data-ttu-id="9be54-351">Blazor provede propojení s mezijazykem (IL) na každém sestavení vydaných verzí a odebere z výstupních sestavení zbytečné IL.</span><span class="sxs-lookup"><span data-stu-id="9be54-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="9be54-352">Další informace naleznete v tématu <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="9be54-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="9be54-353">Načítání vlastního spouštěcího prostředku</span><span class="sxs-lookup"><span data-stu-id="9be54-353">Custom boot resource loading</span></span>

<span data-ttu-id="9be54-354">Blazor WebAssemblyAplikaci lze inicializovat pomocí `loadBootResource` funkce pro přepsání integrovaného mechanismu načítání spouštěcích prostředků.</span><span class="sxs-lookup"><span data-stu-id="9be54-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="9be54-355">Použijte `loadBootResource` v následujících případech:</span><span class="sxs-lookup"><span data-stu-id="9be54-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="9be54-356">Umožňuje uživatelům načíst statické prostředky, jako jsou například data o časovém pásmu nebo `dotnet.wasm` síť CDN.</span><span class="sxs-lookup"><span data-stu-id="9be54-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="9be54-357">Načtěte komprimovaná sestavení pomocí požadavku HTTP a dekomprimujte je v klientovi pro hostitele, kteří nepodporují načítání komprimovaného obsahu ze serveru.</span><span class="sxs-lookup"><span data-stu-id="9be54-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="9be54-358">Aliasujte prostředky na jiný název tak, že každý požadavek přesměrujete `fetch` na nový název.</span><span class="sxs-lookup"><span data-stu-id="9be54-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="9be54-359">`loadBootResource` parametry jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9be54-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="9be54-360">Parametr</span><span class="sxs-lookup"><span data-stu-id="9be54-360">Parameter</span></span>    | <span data-ttu-id="9be54-361">Popis</span><span class="sxs-lookup"><span data-stu-id="9be54-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="9be54-362">Typ prostředku.</span><span class="sxs-lookup"><span data-stu-id="9be54-362">The type of the resource.</span></span> <span data-ttu-id="9be54-363">Permissable typy: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="9be54-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="9be54-364">Název prostředku.</span><span class="sxs-lookup"><span data-stu-id="9be54-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="9be54-365">Relativní nebo absolutní identifikátor URI prostředku.</span><span class="sxs-lookup"><span data-stu-id="9be54-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="9be54-366">Řetězec integrity představující očekávaný obsah v odpovědi</span><span class="sxs-lookup"><span data-stu-id="9be54-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="9be54-367">`loadBootResource` Vrátí některou z následujících možností pro přepsání procesu načítání:</span><span class="sxs-lookup"><span data-stu-id="9be54-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="9be54-368">Řetězec identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="9be54-368">URI string.</span></span> <span data-ttu-id="9be54-369">V následujícím příkladu ( `wwwroot/index.html` ) jsou následující soubory obsluhovány ze sítě CDN v `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="9be54-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="9be54-370">Data časového pásma</span><span class="sxs-lookup"><span data-stu-id="9be54-370">Timezone data</span></span>

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

* <span data-ttu-id="9be54-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="9be54-371">`Promise<Response>`.</span></span> <span data-ttu-id="9be54-372">Předejte `integrity` parametr v hlavičce pro zachování výchozího chování kontroly integrity.</span><span class="sxs-lookup"><span data-stu-id="9be54-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="9be54-373">Následující příklad ( `wwwroot/index.html` ) přidá vlastní HLAVIČKU http do odchozích požadavků a předá `integrity` parametr prostřednictvím `fetch` volání:</span><span class="sxs-lookup"><span data-stu-id="9be54-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="9be54-374">`null`/`undefined`, což vede k výchozímu chování při načítání.</span><span class="sxs-lookup"><span data-stu-id="9be54-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="9be54-375">Externí zdroje musí vracet požadované hlavičky CORS pro prohlížeče, aby bylo možné načíst prostředky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="9be54-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="9be54-376">Sítě CDN obvykle poskytuje ve výchozím nastavení požadované hlavičky.</span><span class="sxs-lookup"><span data-stu-id="9be54-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="9be54-377">Stačí zadat typy pro vlastní chování.</span><span class="sxs-lookup"><span data-stu-id="9be54-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="9be54-378">Typy neurčené pro `loadBootResource` jsou načteny rozhraním na své výchozí chování při načítání.</span><span class="sxs-lookup"><span data-stu-id="9be54-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="9be54-379">Změna přípony filename souborů DLL</span><span class="sxs-lookup"><span data-stu-id="9be54-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="9be54-380">V případě, že potřebujete změnit příponu filename pro publikované `.dll` soubory aplikace, postupujte podle pokynů v této části.</span><span class="sxs-lookup"><span data-stu-id="9be54-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="9be54-381">Po publikování aplikace použijte skript prostředí nebo kanál sestavení DevOps k přejmenování `.dll` souborů tak, aby používaly jinou příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="9be54-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="9be54-382">Zaměřte se na `.dll` soubory v `wwwroot` adresáři publikovaného výstupu aplikace (například `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="9be54-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="9be54-383">V následujících příkladech `.dll` jsou soubory přejmenovány, aby používaly `.bin` příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="9be54-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="9be54-384">Ve Windows:</span><span class="sxs-lookup"><span data-stu-id="9be54-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="9be54-385">Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9be54-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="9be54-386">V systému Linux nebo macOS:</span><span class="sxs-lookup"><span data-stu-id="9be54-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="9be54-387">Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9be54-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="9be54-388">Chcete-li použít jinou příponu souboru než `.bin` , nahraďte `.bin` v předchozích příkazech.</span><span class="sxs-lookup"><span data-stu-id="9be54-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="9be54-389">Pro vyřešení komprimovaných `blazor.boot.json.gz` `blazor.boot.json.br` souborů proveďte jednu z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9be54-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="9be54-390">Odeberte komprimované `blazor.boot.json.gz` soubory a `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="9be54-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="9be54-391">Komprese je u tohoto přístupu zakázána.</span><span class="sxs-lookup"><span data-stu-id="9be54-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="9be54-392">Opětovná komprimace aktualizovaného `blazor.boot.json` souboru.</span><span class="sxs-lookup"><span data-stu-id="9be54-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="9be54-393">Předchozí pokyny platí i v případě, že se aktivují prostředky pracovního procesu.</span><span class="sxs-lookup"><span data-stu-id="9be54-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="9be54-394">Odeberte nebo znovu proveďte komprimaci `wwwroot/service-worker-assets.js.br` a `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="9be54-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="9be54-395">V opačném případě kontroly integrity souborů selžou v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9be54-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="9be54-396">Následující příklad Windows používá skript prostředí PowerShell umístěný v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="9be54-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="9be54-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="9be54-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="9be54-398">Pokud se také používají prostředky pracovního procesu služby, přidejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9be54-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="9be54-399">V souboru projektu se skript spustí po publikování aplikace:</span><span class="sxs-lookup"><span data-stu-id="9be54-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="9be54-400">Při přejmenovávání a opožděném načítání stejných sestavení, přečtěte si pokyny v tématu <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="9be54-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="9be54-401">Vyřešit selhání kontroly integrity</span><span class="sxs-lookup"><span data-stu-id="9be54-401">Resolve integrity check failures</span></span>

<span data-ttu-id="9be54-402">Při Blazor WebAssembly stažení spouštěcích souborů aplikace instruuje prohlížeč, aby prováděl kontroly integrity na odpovědích.</span><span class="sxs-lookup"><span data-stu-id="9be54-402">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="9be54-403">Používá informace v `blazor.boot.json` souboru k určení očekávaných hodnot hash SHA-256 pro `.dll` , `.wasm` a dalších souborů.</span><span class="sxs-lookup"><span data-stu-id="9be54-403">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="9be54-404">To je užitečné z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="9be54-404">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="9be54-405">Zajišťuje neriziku načtení nekonzistentní sady souborů, například pokud se na váš webový server použije nové nasazení, zatímco uživatel právě stahuje soubory aplikace.</span><span class="sxs-lookup"><span data-stu-id="9be54-405">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="9be54-406">Nekonzistentní soubory mohou vést k nedefinovanému chování.</span><span class="sxs-lookup"><span data-stu-id="9be54-406">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="9be54-407">To zajišťuje, že prohlížeč uživatele nikdy neukládá do mezipaměti nekonzistentní ani neplatné odpovědi, což by mohlo zabránit spuštění aplikace i v případě, že stránku ručně aktualizují.</span><span class="sxs-lookup"><span data-stu-id="9be54-407">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="9be54-408">Umožňuje bezpečné ukládání odpovědí do mezipaměti a dokonce i kontrolu nad změnami na straně serveru, dokud se nezmění očekávaná hodnota hash SHA-256, takže následné načtení stránky zahrnuje méně požadavků a dokončí mnohem rychleji.</span><span class="sxs-lookup"><span data-stu-id="9be54-408">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="9be54-409">Pokud váš webový server vrátí odpovědi, které neodpovídají očekávaným hodnotám hash SHA-256, zobrazí se v konzole pro vývojáře v prohlížeči chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="9be54-409">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="9be54-410">Ve většině případů to *není* problém s kontrolou integrity samotnou.</span><span class="sxs-lookup"><span data-stu-id="9be54-410">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="9be54-411">Místo toho znamená, že došlo k nějakému problému a kontrola integrity vás upozorní na tento jiný problém.</span><span class="sxs-lookup"><span data-stu-id="9be54-411">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="9be54-412">Diagnostikování problémů s integritou</span><span class="sxs-lookup"><span data-stu-id="9be54-412">Diagnosing integrity problems</span></span>

<span data-ttu-id="9be54-413">Při sestavení aplikace vygenerovaný `blazor.boot.json` manifest popisuje hodnoty hash SHA-256 svých spouštěcích prostředků (například,, `.dll` `.wasm` a dalších souborů) v době, kdy je vytvořen výstup sestavení.</span><span class="sxs-lookup"><span data-stu-id="9be54-413">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="9be54-414">Kontrola integrity projde, dokud hodnoty hash SHA-256 ve `blazor.boot.json` shodě se soubory doručenými do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9be54-414">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="9be54-415">Běžné důvody, proč se tato chyba nezdařila:</span><span class="sxs-lookup"><span data-stu-id="9be54-415">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="9be54-416">Odpověď webového serveru je chyba (například Chyba *404-nenalezena* nebo *500-interní chyba serveru*) místo souboru, který prohlížeč požadoval.</span><span class="sxs-lookup"><span data-stu-id="9be54-416">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="9be54-417">Prohlížeč tuto zprávu oznamuje jako selhání kontroly integrity a ne jako odpověď.</span><span class="sxs-lookup"><span data-stu-id="9be54-417">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="9be54-418">Něco změnilo obsah souborů mezi sestavením a doručením souborů do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9be54-418">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="9be54-419">K tomu může dojít:</span><span class="sxs-lookup"><span data-stu-id="9be54-419">This might happen:</span></span>
   * <span data-ttu-id="9be54-420">Pokud vytváříte nebo vytváříte nástroje, můžete výstup sestavení ručně změnit.</span><span class="sxs-lookup"><span data-stu-id="9be54-420">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="9be54-421">Pokud nějaký aspekt procesu nasazení změnil soubory.</span><span class="sxs-lookup"><span data-stu-id="9be54-421">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="9be54-422">Například pokud používáte mechanizmus pro nasazení na základě Gitu, pamatujte, že Git transparentně převede konce řádků ve stylu Windows na konce řádků ve stylu systému UNIX, Pokud potvrdíte soubory ve Windows a zaregistrujte se na Linux.</span><span class="sxs-lookup"><span data-stu-id="9be54-422">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="9be54-423">Změna konců řádků souboru mění hodnoty hash SHA-256.</span><span class="sxs-lookup"><span data-stu-id="9be54-423">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="9be54-424">Chcete-li se tomuto problému vyhnout, zvažte [použití `.gitattributes` pro považovat artefakty sestavení jako `binary` soubory](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span><span class="sxs-lookup"><span data-stu-id="9be54-424">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="9be54-425">Webový server upraví obsah souboru v rámci obsluhy.</span><span class="sxs-lookup"><span data-stu-id="9be54-425">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="9be54-426">Například některé sítě pro distribuci obsahu (sítě CDN) se automaticky pokusí [minimalizuje](xref:client-side/bundling-and-minification#minification) HTML, takže se upraví.</span><span class="sxs-lookup"><span data-stu-id="9be54-426">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="9be54-427">Tyto funkce možná budete muset zakázat.</span><span class="sxs-lookup"><span data-stu-id="9be54-427">You may need to disable such features.</span></span>

<span data-ttu-id="9be54-428">Postup diagnostiky, které z těchto případů platí:</span><span class="sxs-lookup"><span data-stu-id="9be54-428">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="9be54-429">Všimněte si, že soubor spouští chybu načtením chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="9be54-429">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="9be54-430">Otevřete vývojářské nástroje v prohlížeči a podívejte se na kartu *síť* . V případě potřeby stránku znovu načtěte, aby se zobrazil seznam požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="9be54-430">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="9be54-431">Vyhledejte soubor, který aktivuje chybu v tomto seznamu.</span><span class="sxs-lookup"><span data-stu-id="9be54-431">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="9be54-432">V odpovědi ověřte stavový kód protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="9be54-432">Check the HTTP status code in the response.</span></span> <span data-ttu-id="9be54-433">Pokud server vrátí jinou hodnotu než *200-OK* (nebo jiný stavový kód 2xx), budete mít problém s diagnostikou na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="9be54-433">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="9be54-434">Například stavový kód 403 znamená problém s autorizací, zatímco stavový kód 500 znamená, že se server nedaří nespecifikovaným způsobem.</span><span class="sxs-lookup"><span data-stu-id="9be54-434">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="9be54-435">Diagnostikujte a opravte aplikaci v protokolech na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="9be54-435">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="9be54-436">Pokud je stavový kód *200 – OK* pro prostředek, podívejte se na obsah odpovědi v vývojářských nástrojích prohlížeče a zkontrolujte, jestli se obsah shoduje s očekávanými daty.</span><span class="sxs-lookup"><span data-stu-id="9be54-436">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="9be54-437">Běžným problémem je například nesprávně nakonfigurovaná směrování, takže požadavky vrátí vaše `index.html` data i pro jiné soubory.</span><span class="sxs-lookup"><span data-stu-id="9be54-437">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="9be54-438">Ujistěte se, že odpovědi na `.wasm` požadavky jsou binární soubory WebAssembly a že odpovědi na `.dll` požadavky jsou binární soubory sestavení .NET.</span><span class="sxs-lookup"><span data-stu-id="9be54-438">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="9be54-439">V takovém případě máte k diagnostice problém s směrováním na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="9be54-439">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="9be54-440">Pokud potvrdíte, že server vrací plausibly správná data, musí být něco jiného, než je třeba upravit obsah mezi sestavením a doručením souboru.</span><span class="sxs-lookup"><span data-stu-id="9be54-440">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="9be54-441">Postup pro prošetření:</span><span class="sxs-lookup"><span data-stu-id="9be54-441">To investigate this:</span></span>

 * <span data-ttu-id="9be54-442">Projděte si mechanismus sestavení sada nástrojů and Deployment v případě, že upravujete soubory po sestavení souborů.</span><span class="sxs-lookup"><span data-stu-id="9be54-442">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="9be54-443">Příkladem je, že Git transformuje konce řádků souboru, jak je popsáno výše.</span><span class="sxs-lookup"><span data-stu-id="9be54-443">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="9be54-444">V případě, že jsou nastavená tak, aby se odpovědi dynamicky měnily (například pokus o minimalizuje HTML), Projděte si konfiguraci webového serveru nebo CDN.</span><span class="sxs-lookup"><span data-stu-id="9be54-444">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="9be54-445">Pro webový server je to pro implementaci komprese HTTP nutné `content-encoding: br` `content-encoding: gzip` , protože to neovlivní výsledek po dekompresi.</span><span class="sxs-lookup"><span data-stu-id="9be54-445">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="9be54-446">U webového serveru ale *není* dobré upravovat nekomprimovaná data.</span><span class="sxs-lookup"><span data-stu-id="9be54-446">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="9be54-447">Zakázat kontrolu integrity pro aplikace, které nejsou PWA</span><span class="sxs-lookup"><span data-stu-id="9be54-447">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="9be54-448">Ve většině případů nevypnete kontrolu integrity.</span><span class="sxs-lookup"><span data-stu-id="9be54-448">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="9be54-449">Zakázáním kontroly integrity se nevyřeší základní problém, který způsobil neočekávané odpovědi, a vede ke ztrátě výše uvedených výhod.</span><span class="sxs-lookup"><span data-stu-id="9be54-449">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="9be54-450">Můžou nastat případy, kdy se webový server nemůže spoléhat na to, že vrátí konzistentní odpovědi, a nemáte žádnou volbu, ale zakážete kontroly integrity.</span><span class="sxs-lookup"><span data-stu-id="9be54-450">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="9be54-451">Chcete-li zakázat kontroly integrity, přidejte následující do skupiny vlastností v Blazor WebAssembly `.csproj` souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="9be54-451">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="9be54-452">`BlazorCacheBootResources` také zakáže Blazor výchozí chování ukládání do mezipaměti `.dll` , `.wasm` a dalších souborů na základě jejich hash SHA-256, protože tato vlastnost označuje, že se na správnost nedají spoléhat hodnoty hash SHA-256.</span><span class="sxs-lookup"><span data-stu-id="9be54-452">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="9be54-453">I s tímto nastavením může normální mezipaměť protokolu HTTP v prohlížeči pořád tyto soubory ukládat do mezipaměti, ale bez ohledu na to, jestli se k tomu dojde, závisí na konfiguraci webového serveru a na `cache-control` hlavičkách, které obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="9be54-453">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="9be54-454">`BlazorCacheBootResources`Vlastnost nevypne kontroly integrity pro [progresivní webové aplikace (PWAs)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="9be54-454">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="9be54-455">Pokyny týkající se PWAs najdete v části [zakázání kontroly integrity pro PWAs](#disable-integrity-checking-for-pwas) .</span><span class="sxs-lookup"><span data-stu-id="9be54-455">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="9be54-456">Zakázat kontrolu integrity pro PWAs</span><span class="sxs-lookup"><span data-stu-id="9be54-456">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="9be54-457">BlazorŠablona progresivní webové aplikace (PWA) obsahuje navrhovaný `service-worker.published.js` soubor, který je zodpovědný za načítání a ukládání souborů aplikace pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="9be54-457">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="9be54-458">Jedná se o samostatný proces z normálního spouštěcího mechanismu aplikace a má svou vlastní samostatnou logiku kontroly integrity.</span><span class="sxs-lookup"><span data-stu-id="9be54-458">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="9be54-459">Uvnitř `service-worker.published.js` souboru je k dispozici následující řádek:</span><span class="sxs-lookup"><span data-stu-id="9be54-459">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="9be54-460">Chcete-li zakázat kontrolu integrity, odeberte `integrity` parametr změnou řádku na následující:</span><span class="sxs-lookup"><span data-stu-id="9be54-460">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="9be54-461">Opět se zakazováním kontroly integrity znamená, že ztratíte bezpečnostní záruky, které nabízí kontrola integrity.</span><span class="sxs-lookup"><span data-stu-id="9be54-461">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="9be54-462">Například existuje riziko, že pokud je v prohlížeči uživatele mezipaměť aplikace do mezipaměti přesně v okamžiku, kdy nasazujete novou verzi, mohli byste ukládat do mezipaměti některé soubory ze starého nasazení a některé z nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="9be54-462">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="9be54-463">Pokud k tomu dojde, aplikace se zablokuje v nefunkčním stavu, dokud nenainstalujete další aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="9be54-463">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
