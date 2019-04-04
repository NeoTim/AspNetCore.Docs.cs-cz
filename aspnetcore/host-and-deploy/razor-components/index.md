---
title: Hostitelství a nasazení součásti syntaxe Razor
author: guardrex
description: Objevte, jak hostovat a nasadit komponenty Razor a Blazor aplikace pomocí ASP.NET Core, Content Delivery Network (CDN), souborové servery a stránkách Githubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2019
uid: host-and-deploy/razor-components/index
ms.openlocfilehash: 236e8da27b80dbdb3e0ea885413b6cfd563dde60
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419417"
---
# <a name="host-and-deploy-razor-components"></a><span data-ttu-id="b4f4c-103">Hostitelství a nasazení součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="b4f4c-103">Host and deploy Razor Components</span></span>

<span data-ttu-id="b4f4c-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b4f4c-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="b4f4c-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="b4f4c-105">Publish the app</span></span>

<span data-ttu-id="b4f4c-106">Aplikace budou publikovány pro nasazení v rámci konfigurace verze se [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b4f4c-107">Integrované vývojové prostředí může zpracovat provádění `dotnet publish` příkaz automaticky pomocí jeho integrované funkce pro publikování, proto nemusí být potřeba ručně spusťte příkaz z příkazového řádku v závislosti na nástroje pro vývoj v použití.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-107">An IDE may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="b4f4c-108">`dotnet publish` aktivační události [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [sestavení](/dotnet/core/tools/dotnet-build) projektu před vytvořením prostředků pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b4f4c-109">Jako součást procesu sestavení jsou odebrány nepoužívané metod a sestavení snížit velikost ke stažení aplikace a časy načtení.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="b4f4c-110">Nasazení se vytvoří v */bin/vydání/&lt;Cílová architektura&gt;/ publish* složky.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-110">The deployment is created in the */bin/Release/&lt;target-framework&gt;/publish* folder.</span></span>

<span data-ttu-id="b4f4c-111">Prostředky v *publikovat* složky jsou nasazené na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="b4f4c-112">Nasazení může být ruční nebo automatizované proces v závislosti na vývojové nástroje, které používá.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b4f4c-113">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="b4f4c-113">Host configuration values</span></span>

<span data-ttu-id="b4f4c-114">Součásti aplikace Razor, používající [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model) může přijmout [hodnoty konfigurace webového hostitele](xref:fundamentals/host/web-host#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-114">Razor Components apps that use the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model) can accept [Web Host configuration values](xref:fundamentals/host/web-host#host-configuration-values).</span></span>

<span data-ttu-id="b4f4c-115">Blazor aplikací, které používají [model hostingu na straně klienta](xref:razor-components/hosting-models#client-side-hosting-model) může přijmout hodnoty následující konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-115">Blazor apps that use the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="b4f4c-116">Obsah kořenové</span><span class="sxs-lookup"><span data-stu-id="b4f4c-116">Content Root</span></span>

<span data-ttu-id="b4f4c-117">`--contentroot` Argument Nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-117">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span>

* <span data-ttu-id="b4f4c-118">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-118">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b4f4c-119">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-119">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* <span data-ttu-id="b4f4c-120">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-120">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b4f4c-121">Toto nastavení je neexistoval při spuštění aplikace pomocí ladicího programu sady Visual Studio a při spuštění aplikace z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-121">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* <span data-ttu-id="b4f4c-122">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-122">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b4f4c-123">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-123">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a><span data-ttu-id="b4f4c-124">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="b4f4c-124">Path base</span></span>

<span data-ttu-id="b4f4c-125">`--pathbase` Argument nastaví základní cesty aplikace pro aplikaci pro místní spuštění s virtuální cestou nekořenovými ( `<base>` značky `href` nastavený na cestu jiné než `/` pro pracovní a provozní).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-125">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="b4f4c-126">Další informace najdete v tématu [základní cesty aplikace](#app-base-path) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-126">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b4f4c-127">Na rozdíl od zadaná cesta k `href` z `<base>` značku, nemusíte zahrnovat koncové lomítko (`/`) při předávání `--pathbase` hodnota argumentu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-127">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="b4f4c-128">Pokud je základní cesta aplikace součástí `<base>` označit jako `<base href="/CoolApp/" />` (včetně koncového lomítka), předejte hodnotu argument příkazového řádku jako `--pathbase=/CoolApp` (žádného koncového lomítka).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-128">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/" />` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="b4f4c-129">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-129">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b4f4c-130">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-130">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* <span data-ttu-id="b4f4c-131">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-131">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b4f4c-132">Toto nastavení je neexistoval při spuštění aplikace pomocí ladicího programu sady Visual Studio a při spuštění aplikace z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-132">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* <span data-ttu-id="b4f4c-133">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-133">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b4f4c-134">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-134">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a><span data-ttu-id="b4f4c-135">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="b4f4c-135">URLs</span></span>

<span data-ttu-id="b4f4c-136">`--urls` Argument určuje IP adresy nebo adresy hostitele s porty a protokoly pro naslouchání požadavkům.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-136">The `--urls` argument indicates the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="b4f4c-137">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-137">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b4f4c-138">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-138">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="b4f4c-139">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-139">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b4f4c-140">Toto nastavení je neexistoval při spuštění aplikace pomocí ladicího programu sady Visual Studio a při spuštění aplikace z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-140">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="b4f4c-141">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-141">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b4f4c-142">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-142">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a><span data-ttu-id="b4f4c-143">Nasazení aplikace na straně klienta Blazor</span><span class="sxs-lookup"><span data-stu-id="b4f4c-143">Deploy a client-side Blazor app</span></span>

<span data-ttu-id="b4f4c-144">S [model hostingu na straně klienta](xref:razor-components/hosting-models#client-side-hosting-model):</span><span class="sxs-lookup"><span data-stu-id="b4f4c-144">With the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="b4f4c-145">Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-145">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="b4f4c-146">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-146">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="b4f4c-147">Je podporován některý z následujících strategií:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-147">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="b4f4c-148">Aplikace Blazor obsluhují aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-148">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="b4f4c-149">Do [Client-side Blazor hostované nasazení pomocí technologie ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-149">Covered in the [Client-side Blazor hosted deployment with ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="b4f4c-150">Blazor aplikace je umístěn na statické hostování webového serveru nebo službě, kde není .NET používají k předávání Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-150">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="b4f4c-151">Do [Client-side Blazor samostatné nasazení](#client-side-blazor-standalone-deployment) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-151">Covered in the [Client-side Blazor standalone deployment](#client-side-blazor-standalone-deployment) section.</span></span>

### <a name="configure-the-linker"></a><span data-ttu-id="b4f4c-152">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="b4f4c-152">Configure the Linker</span></span>

<span data-ttu-id="b4f4c-153">Blazor provádí Intermediate Language (IL) propojení na každé sestavení odebrat nepotřebné IL z výstupního sestavení.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-153">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="b4f4c-154">Můžete řídit propojení při sestavování sestavení.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-154">You can control assembly linking on build.</span></span> <span data-ttu-id="b4f4c-155">Další informace naleznete v tématu <xref:host-and-deploy/razor-components/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-155">For more information, see <xref:host-and-deploy/razor-components/configure-linker>.</span></span>

### <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="b4f4c-156">Přepisování adres URL pro správné směrování</span><span class="sxs-lookup"><span data-stu-id="b4f4c-156">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="b4f4c-157">Směrování požadavků pro součásti stránky v aplikaci na straně klienta není snadné – stačí směrování žádostí na aplikace na straně serveru, prostředí.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-157">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="b4f4c-158">Vezměte v úvahu aplikace na straně klienta se dvěma stránkami:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-158">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="b4f4c-159">**_Main.cshtml_**  &ndash; zatížení v kořenovém adresáři aplikace a obsahuje odkaz na stránku o (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-159">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="b4f4c-160">**_About.cshtml_**  &ndash; o stránce.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-160">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="b4f4c-161">Pokud aplikace výchozí dokument se požaduje pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="b4f4c-161">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="b4f4c-162">Prohlížeč odešle požadavek.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-162">The browser makes a request.</span></span>
1. <span data-ttu-id="b4f4c-163">Vrátí výchozí stránku, což je obvykle *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-163">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="b4f4c-164">*index.HTML* bootstraps aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-164">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="b4f4c-165">Směrovač Blazor na zatížení a Razor hlavní stránky (*Main.cshtml*) se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-165">Blazor's router loads and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="b4f4c-166">Na hlavní stránce vyberte odkaz na stránku o načte stránku o.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-166">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="b4f4c-167">Vyberte odkaz na stránku o funguje na straně klienta, protože směrovače Blazor zastaví prohlížeče z požadavku na Internetu, aby `www.contoso.com` pro `About` a slouží samotná stránka o.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-167">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="b4f4c-168">Všechny žádosti pro interní stránky *v rámci aplikace na straně klienta* stejným způsobem fungovat: Požadavky nejsou aktivace založené na prohlížeči požadavky na prostředky serveru hostované na Internetu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-168">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="b4f4c-169">Směrovač interně zpracovává požadavky.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-169">The router handles the requests internally.</span></span>

<span data-ttu-id="b4f4c-170">Pokud je vytvořena pomocí panelu Adresa prohlížeče pro žádost `www.contoso.com/About`, požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-170">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="b4f4c-171">Žádný takový prostředek neexistuje na hostiteli aplikace Internet, tak *404 Nenalezeno* vrátí odpověď.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-171">No such resource exists on the app's Internet host, so a *404 Not found* response is returned.</span></span>

<span data-ttu-id="b4f4c-172">Protože prohlížeče zasílat požadavky do Internetu na hostitele pro stránky na straně klienta, webové servery a služby hostování musí přepsat všechny požadavky na prostředky nejsou fyzicky na server, aby *index.html* stránky.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-172">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="b4f4c-173">Když *index.html* se vrátí, má aplikace na straně klienta směrovače a odpovídá zprávou správný zdroj.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-173">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

### <a name="app-base-path"></a><span data-ttu-id="b4f4c-174">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="b4f4c-174">App base path</span></span>

<span data-ttu-id="b4f4c-175">Základní cesta aplikace je kořenová cesta virtuální aplikace na serveru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-175">The app base path is the virtual app root path on the server.</span></span> <span data-ttu-id="b4f4c-176">Například aplikace, které se nacházejí na serveru Contoso ve virtuální složce na `/CoolApp/` je dosažena `https://www.contoso.com/CoolApp` a má virtuální základní cesta `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-176">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="b4f4c-177">Nastavením základní cesta aplikace na `CoolApp/`, aplikace se provádí vědět, kde se prakticky nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-177">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="b4f4c-178">Aplikace můžete použít základní cesta aplikace k vytvoření adresy URL kořeni aplikace z komponenty, která není v kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-178">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="b4f4c-179">To umožňuje součásti, které existují na různých úrovních adresářovou strukturu vytvářet odkazy na další zdroje v umístěních v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-179">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="b4f4c-180">Základní cesta aplikace se také používá k zachycení hypertextový odkaz klikne, kde `href` cíl odkazu je v rámci základní cesty aplikace místo identifikátoru URI&mdash;směrovače Blazor zpracovává vnitřní navigační.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-180">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="b4f4c-181">V mnoha scénářích hostování serveru virtuální cesta k aplikaci je kořenový adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-181">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="b4f4c-182">V těchto případech je základní cesty aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-182">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="b4f4c-183">V jiných scénářích hostování, jako jsou virtuální adresáře stránkách Githubu a služby IIS nebo dílčí aplikace základní cesty aplikace nastavte na serveru virtuální cesta k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-183">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="b4f4c-184">Nastavení základní cesty aplikace, přidat nebo aktualizovat `<base>` značku *index.html* najdete v `<head>` značky elementů.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-184">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="b4f4c-185">Nastavte `href` atribut hodnotu `<virtual-path>/` (vyžaduje se do adresy koncové lomítko), kde `<virtual-path>/` je kořenová cesta úplnou virtuální aplikace na serveru pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-185">Set the `href` attribute value to `<virtual-path>/` (the trailing slash is required), where `<virtual-path>/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="b4f4c-186">V předchozím příkladu je nastavena virtuální cesta `CoolApp/`: `<base href="CoolApp/" />`.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-186">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/" />`.</span></span>

<span data-ttu-id="b4f4c-187">Pro aplikace s virtuální cestou nekořenovými nakonfigurovali (například `<base href="CoolApp/" />`), aplikace nenajde žádné její prostředky *při spuštění místně*.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-187">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/" />`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="b4f4c-188">Chcete-li tento problém vyřešit během místní vývoj a testování, můžete zadat *základ cesty* argument, který odpovídá `href` hodnotu `<base>` značky v době běhu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-188">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="b4f4c-189">Předat argument základní cesty s kořenovou cestou (`/`) při místním spuštění aplikace, spusťte následující příkaz z adresáře aplikace:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-189">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="b4f4c-190">Reakce aplikace na místně `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-190">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="b4f4c-191">Další informace najdete v tématu [hodnota konfigurace hostitele základní cesty](#path-base) oddílu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-191">For more information, see the [path base host configuration value](#path-base) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b4f4c-192">Pokud aplikace používá [model hostingu na straně klienta](xref:razor-components/hosting-models#client-side-hosting-model) (na základě **Blazor** šablony projektu) a je hostovaný jako dílčí aplikace služby IIS v aplikaci ASP.NET Core, je potřeba zakázat zděděné ASP.NET Core Obslužná rutina modulu nebo Ujistěte se, že aplikace root (nadřazené) `<handlers>` tématu *web.config* soubor není děděný podřízeným aplikacím.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-192">If an app uses the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>
>
> <span data-ttu-id="b4f4c-193">Odebrat publikování obslužné rutiny v aplikaci *web.config* souboru tak, že přidáte `<handlers>` část do souboru:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-193">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> <span data-ttu-id="b4f4c-194">Můžete také zakázat dědičnost (nadřazené) kořenové aplikace `<system.webServer>` části pomocí `<location>` element s `inheritInChildApplications` nastavena na `false`:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-194">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> <span data-ttu-id="b4f4c-195">Kromě konfigurace základní cesty aplikace, jak je popsáno v této části se provádí odebírá obslužnou rutinu nebo zakážou dědičnost.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-195">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="b4f4c-196">Nastavení základní cesty aplikace v aplikaci prvku *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-196">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a><span data-ttu-id="b4f4c-197">Nasazení Blazor hostované na straně klienta pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b4f4c-197">Client-side Blazor hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="b4f4c-198">A *hostované nasazení* slouží Blazor aplikace na straně klienta z prohlížečů [aplikace ASP.NET Core](xref:index) , který běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-198">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="b4f4c-199">Blazor aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-199">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="b4f4c-200">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-200">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b4f4c-201">Hostované nasazení Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablonu projektu (`blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-201">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="b4f4c-202">Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-202">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="b4f4c-203">Informace o nasazení do služby Azure App Service najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-203">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>

<span data-ttu-id="b4f4c-204">Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-204">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

### <a name="client-side-blazor-standalone-deployment"></a><span data-ttu-id="b4f4c-205">Nasazení samostatného Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b4f4c-205">Client-side Blazor standalone deployment</span></span>

<span data-ttu-id="b4f4c-206">A *samostatné nasazení* slouží jako sadu statické soubory, které jsou požadovány přímo klienty Blazor aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-206">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="b4f4c-207">Webový server se používají k předávání Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-207">A web server isn't used to serve the Blazor app.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a><span data-ttu-id="b4f4c-208">Na straně klienta Blazor samostatné hostování se službou IIS</span><span class="sxs-lookup"><span data-stu-id="b4f4c-208">Client-side Blazor standalone hosting with IIS</span></span>

<span data-ttu-id="b4f4c-209">Služba IIS je schopen statický souborový server pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-209">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="b4f4c-210">Můžete nakonfigurovat službu IIS do hostitele Blazor, naleznete v tématu [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-210">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="b4f4c-211">Publikované prostředky vytvořené v  *\\bin\\vydání\\&lt;Cílová architektura&gt;\\publikovat* složky.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-211">Published assets are created in the *\\bin\\Release\\&lt;target-framework&gt;\\publish* folder.</span></span> <span data-ttu-id="b4f4c-212">Hostování obsahu *publikovat* složku na webovém serveru nebo hostující služby.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-212">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

<span data-ttu-id="b4f4c-213">**web.config**</span><span class="sxs-lookup"><span data-stu-id="b4f4c-213">**web.config**</span></span>

<span data-ttu-id="b4f4c-214">Při publikování projektu Blazor *web.config* soubor se vytvoří s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-214">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="b4f4c-215">Typy MIME jsou nastavené pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-215">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="b4f4c-216">*\*.dll*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="b4f4c-216">*\*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="b4f4c-217">*\*.json*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="b4f4c-217">*\*.json*: `application/json`</span></span>
  * <span data-ttu-id="b4f4c-218">*\*.wasm*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="b4f4c-218">*\*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="b4f4c-219">*\*.woff*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b4f4c-219">*\*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="b4f4c-220">*\*.woff2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b4f4c-220">*\*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="b4f4c-221">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-221">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="b4f4c-222">Modul přepisování adres URL pravidla jsou vytvořeny:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-222">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="b4f4c-223">Poskytovat dílčí adresáře, kde jsou umístěny statických prostředků aplikace (*&lt;název_sestavení&gt;\\dist\\&lt;path_requested&gt;*).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-223">Serve the sub-directory where the app's static assets reside (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).</span></span>
  * <span data-ttu-id="b4f4c-224">Vytvoření aplikace SPA záložní směrování tak, aby požadavky pro nesouborové prostředky se přesměrují do aplikace výchozí dokument v její složce statické prostředky (*&lt;název_sestavení&gt;\\dist\\index.html*).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-224">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*&lt;assembly_name&gt;\\dist\\index.html*).</span></span>

<span data-ttu-id="b4f4c-225">**Nainstalovat modul přepisování adres URL**</span><span class="sxs-lookup"><span data-stu-id="b4f4c-225">**Install the URL Rewrite Module**</span></span>

<span data-ttu-id="b4f4c-226">[Modul přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite) se vyžaduje k přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-226">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="b4f4c-227">Ve výchozím nastavení není nainstalován modul a není k dispozici pro instalaci jako funkce služby role Webový Server (IIS).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-227">The module isn't installed by default, and it isn't available for install as an Web Server (IIS) role service feature.</span></span> <span data-ttu-id="b4f4c-228">Modul musí stáhnout z webu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-228">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="b4f4c-229">Použití instalačního programu webové platformy nainstalovat modul:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-229">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="b4f4c-230">Místně, přejděte [modul přepisování adres URL stránky pro stažení](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-230">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="b4f4c-231">Pro anglickou verzi, vyberte **instalace webové platformy** pro stažení instalačního programu Instalace webové platformy.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-231">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="b4f4c-232">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) pro stažení instalačního programu.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-232">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="b4f4c-233">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-233">Copy the installer to the server.</span></span> <span data-ttu-id="b4f4c-234">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-234">Run the installer.</span></span> <span data-ttu-id="b4f4c-235">Vyberte **nainstalovat** tlačítko a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-235">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="b4f4c-236">Po dokončení instalace není nutné restartovat server.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-236">A server restart isn't required after the install completes.</span></span>

<span data-ttu-id="b4f4c-237">**Konfiguraci webové stránky**</span><span class="sxs-lookup"><span data-stu-id="b4f4c-237">**Configure the website**</span></span>

<span data-ttu-id="b4f4c-238">Nastavit na webu **fyzická cesta** do složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-238">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="b4f4c-239">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-239">The folder contains:</span></span>

* <span data-ttu-id="b4f4c-240">*Web.config* soubor, který služba IIS používá pro konfiguraci webové stránky, včetně pravidel požadovaná přesměrování a typy obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-240">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="b4f4c-241">Složku statických prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-241">The app's static asset folder.</span></span>

<span data-ttu-id="b4f4c-242">**Odstraňování potíží**</span><span class="sxs-lookup"><span data-stu-id="b4f4c-242">**Troubleshooting**</span></span>

<span data-ttu-id="b4f4c-243">Pokud *500 – Interní chyba serveru* přijetí a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci příslušného webu, ověřte, že je nainstalován modul přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-243">If a *500 Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="b4f4c-244">Když není nainstalován modul, *web.config* soubor nejde parsovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-244">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="b4f4c-245">To zabrání načítání konfigurace na webu a webu z obsluhy statických souborů pro Blazor Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-245">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="b4f4c-246">Další informace o řešení potíží s nasazením do služby IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-246">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a><span data-ttu-id="b4f4c-247">Na straně klienta Blazor samostatné hostování se serverem Nginx</span><span class="sxs-lookup"><span data-stu-id="b4f4c-247">Client-side Blazor standalone hosting with Nginx</span></span>

<span data-ttu-id="b4f4c-248">Následující *nginx.conf* souboru je zjednodušenou ukazují, jak nakonfigurovat Nginx tak, aby odeslat *Index.html* souboru pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-248">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *Index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

<span data-ttu-id="b4f4c-249">Další informace o konfiguraci serveru webového serveru Nginx produkčního prostředí, najdete v části [NGINX konfiguračních souborů a vytváří NGINX Plus](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-249">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a><span data-ttu-id="b4f4c-250">Hostování v Dockeru nginx samostatné Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b4f4c-250">Client-side Blazor standalone hosting with Nginx in Docker</span></span>

<span data-ttu-id="b4f4c-251">K hostování Blazor v Dockeru pomocí serveru Nginx, instalační program soubor Dockerfile, který chcete použít image serveru Nginx Alpine na základě.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-251">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="b4f4c-252">Aktualizace souboru Dockerfile zkopírovat *nginx.config* souboru do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-252">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="b4f4c-253">Přidejte jeden řádek do souboru Dockerfile, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-253">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a><span data-ttu-id="b4f4c-254">Hostování s stránkách Githubu samostatné Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b4f4c-254">Client-side Blazor standalone hosting with GitHub Pages</span></span>

<span data-ttu-id="b4f4c-255">Pro zpracování přepisů adresu URL, přidejte *404. html* soubor skriptu, který zpracovává požadavek na přesměrování *index.html* stránky.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-255">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="b4f4c-256">Příklad implementace poskytované komunitou, naleznete v tématu [jedné stránky aplikace pro stránky Githubu](http://spa-github-pages.rafrex.com/) ([rafrex/spa – github stránky na Githubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-256">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="b4f4c-257">Příklad použití komunity přístup, můžete zobrazit v [blazor-demo/blazor-demo.github.io na Githubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živého webu](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-257">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="b4f4c-258">Když použijete web projektu namísto serveru organizace, přidat nebo aktualizovat `<base>` značku *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-258">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="b4f4c-259">Nastavte `href` atribut hodnotu `<repository-name>/`, kde `<repository-name>/` je název úložiště GitHub.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-259">Set the `href` attribute value to `<repository-name>/`, where `<repository-name>/` is the GitHub repository name.</span></span>

## <a name="deploy-a-server-side-razor-components-app"></a><span data-ttu-id="b4f4c-260">Nasazení aplikace na straně serveru součásti syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="b4f4c-260">Deploy a server-side Razor Components app</span></span>

<span data-ttu-id="b4f4c-261">S [model hostingu na straně serveru](xref:razor-components/hosting-models#server-side-hosting-model), Razor komponent je provedeno na serveru z v rámci aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-261">With the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model), Razor Components is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="b4f4c-262">Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-262">UI updates, event handling, and JavaScript calls are handled over a SignalR connection.</span></span>

<span data-ttu-id="b4f4c-263">Aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-263">The app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="b4f4c-264">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-264">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b4f4c-265">Pro nasazení na straně serveru, Visual Studio obsahuje **Razor komponenty** šablonu projektu (`razorcomponents` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="b4f4c-265">For a server-side deployment, Visual Studio includes the **Razor Components** project template (`razorcomponents` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="b4f4c-266">Při publikování aplikace ASP.NET Core Razor komponenty aplikace je součástí publikovaný výstup tak, aby aplikace ASP.NET Core a Razor komponenty aplikace je možné nasadit společně.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-266">When the ASP.NET Core app is published, the Razor Components app is included in the published output so that the ASP.NET Core app and the Razor Components app can be deployed together.</span></span> <span data-ttu-id="b4f4c-267">Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-267">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="b4f4c-268">Informace o nasazení do služby Azure App Service najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b4f4c-268">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>

<span data-ttu-id="b4f4c-269">Zjistěte, jak publikovat aplikace ASP.NET Core do služby Azure App Service pomocí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b4f4c-269">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>
