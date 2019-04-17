---
title: Hostitelství a nasazení Blazor na straně klienta
author: guardrex
description: Zjistěte, jak hostovat a nasazení Blazor aplikace pomocí ASP.NET Core, Content Delivery Network (CDN), souborové servery a stránkách Githubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 01a612029f415f583908c3bf2adc2e6d35167acb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614821"
---
# <a name="host-and-deploy-blazor-client-side"></a><span data-ttu-id="472c9-103">Hostitelství a nasazení Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="472c9-103">Host and deploy Blazor client-side</span></span>

<span data-ttu-id="472c9-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="472c9-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="host-configuration-values"></a><span data-ttu-id="472c9-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="472c9-105">Host configuration values</span></span>

<span data-ttu-id="472c9-106">Blazor aplikací, které používají [model hostingu na straně klienta](xref:blazor/hosting-models#client-side-hosting-model) může přijmout hodnoty následující konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="472c9-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="472c9-107">Obsah kořenové</span><span class="sxs-lookup"><span data-stu-id="472c9-107">Content Root</span></span>

<span data-ttu-id="472c9-108">`--contentroot` Argument Nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="472c9-109">V následujících příkladech `/content-root-path` je obsahu kořenovou cestu aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="472c9-110">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="472c9-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="472c9-111">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="472c9-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="472c9-112">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="472c9-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="472c9-113">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="472c9-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="472c9-114">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="472c9-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="472c9-115">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="472c9-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="472c9-116">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="472c9-116">Path base</span></span>

<span data-ttu-id="472c9-117">`--pathbase` Argument nastaví základní cesty aplikace pro aplikaci pro místní spuštění s virtuální cestou nekořenovými ( `<base>` značky `href` nastavený na cestu jiné než `/` pro pracovní a provozní).</span><span class="sxs-lookup"><span data-stu-id="472c9-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="472c9-118">V následujících příkladech `/virtual-path` je základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="472c9-119">Další informace najdete v tématu [základní cesty aplikace](#app-base-path) oddílu.</span><span class="sxs-lookup"><span data-stu-id="472c9-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="472c9-120">Na rozdíl od zadaná cesta k `href` z `<base>` značku, nemusíte zahrnovat koncové lomítko (`/`) při předávání `--pathbase` hodnota argumentu.</span><span class="sxs-lookup"><span data-stu-id="472c9-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="472c9-121">Pokud je základní cesta aplikace součástí `<base>` označit jako `<base href="/CoolApp/">` (včetně koncového lomítka), předejte hodnotu argument příkazového řádku jako `--pathbase=/CoolApp` (žádného koncového lomítka).</span><span class="sxs-lookup"><span data-stu-id="472c9-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="472c9-122">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="472c9-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="472c9-123">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="472c9-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="472c9-124">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="472c9-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="472c9-125">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="472c9-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="472c9-126">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="472c9-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="472c9-127">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="472c9-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="472c9-128">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="472c9-128">URLs</span></span>

<span data-ttu-id="472c9-129">`--urls` Argument nastaví IP adres nebo adres hostitele s porty a protokoly pro naslouchání požadavkům.</span><span class="sxs-lookup"><span data-stu-id="472c9-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="472c9-130">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="472c9-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="472c9-131">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="472c9-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="472c9-132">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="472c9-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="472c9-133">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="472c9-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="472c9-134">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="472c9-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="472c9-135">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="472c9-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="472c9-136">Nasazení</span><span class="sxs-lookup"><span data-stu-id="472c9-136">Deployment</span></span>

<span data-ttu-id="472c9-137">S [model hostingu na straně klienta](xref:blazor/hosting-models#client-side-hosting-model):</span><span class="sxs-lookup"><span data-stu-id="472c9-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="472c9-138">Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="472c9-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="472c9-139">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="472c9-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="472c9-140">Je podporován některý z následujících strategií:</span><span class="sxs-lookup"><span data-stu-id="472c9-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="472c9-141">Aplikace Blazor obsluhují aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="472c9-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="472c9-142">Tato strategie je obsažen v [hostované nasazení pomocí technologie ASP.NET Core](#hosted-deployment-with-aspnet-core) oddílu.</span><span class="sxs-lookup"><span data-stu-id="472c9-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="472c9-143">Blazor aplikace je umístěn na statické hostování webového serveru nebo službě, kde není .NET používají k předávání Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="472c9-144">Tato strategie je obsažen v [samostatné nasazení](#standalone-deployment) oddílu.</span><span class="sxs-lookup"><span data-stu-id="472c9-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="472c9-145">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="472c9-145">Configure the Linker</span></span>

<span data-ttu-id="472c9-146">Blazor provádí Intermediate Language (IL) propojení na každé sestavení odebrat nepotřebné IL z výstupního sestavení.</span><span class="sxs-lookup"><span data-stu-id="472c9-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="472c9-147">Propojování sestavení je možné řídit na sestavení.</span><span class="sxs-lookup"><span data-stu-id="472c9-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="472c9-148">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="472c9-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="472c9-149">Přepisování adres URL pro správné směrování</span><span class="sxs-lookup"><span data-stu-id="472c9-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="472c9-150">Směrování požadavků pro součásti stránky v aplikaci na straně klienta není snadné – stačí směrování žádostí na aplikace na straně serveru, prostředí.</span><span class="sxs-lookup"><span data-stu-id="472c9-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="472c9-151">Vezměte v úvahu aplikace na straně klienta se dvěma stránkami:</span><span class="sxs-lookup"><span data-stu-id="472c9-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="472c9-152">**_Main.cshtml_**  &ndash; zatížení v kořenovém adresáři aplikace a obsahuje odkaz na stránku o (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="472c9-152">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="472c9-153">**_About.cshtml_**  &ndash; o stránce.</span><span class="sxs-lookup"><span data-stu-id="472c9-153">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="472c9-154">Pokud aplikace výchozí dokument se požaduje pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="472c9-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="472c9-155">Prohlížeč odešle požadavek.</span><span class="sxs-lookup"><span data-stu-id="472c9-155">The browser makes a request.</span></span>
1. <span data-ttu-id="472c9-156">Vrátí výchozí stránku, což je obvykle *index.html*.</span><span class="sxs-lookup"><span data-stu-id="472c9-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="472c9-157">*index.HTML* bootstraps aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="472c9-158">Směrovač Blazor na zatížení a Razor hlavní stránky (*Main.cshtml*) se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="472c9-158">Blazor's router loads, and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="472c9-159">Na hlavní stránce vyberte odkaz na stránku o načte stránku o.</span><span class="sxs-lookup"><span data-stu-id="472c9-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="472c9-160">Vyberte odkaz na stránku o funguje na straně klienta, protože směrovače Blazor zastaví prohlížeče z požadavku na Internetu, aby `www.contoso.com` pro `About` a slouží samotná stránka o.</span><span class="sxs-lookup"><span data-stu-id="472c9-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="472c9-161">Všechny žádosti pro interní stránky *v rámci aplikace na straně klienta* stejným způsobem fungovat: Požadavky nejsou aktivace založené na prohlížeči požadavky na prostředky serveru hostované na Internetu.</span><span class="sxs-lookup"><span data-stu-id="472c9-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="472c9-162">Směrovač interně zpracovává požadavky.</span><span class="sxs-lookup"><span data-stu-id="472c9-162">The router handles the requests internally.</span></span>

<span data-ttu-id="472c9-163">Pokud je vytvořena pomocí panelu Adresa prohlížeče pro žádost `www.contoso.com/About`, požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="472c9-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="472c9-164">Žádný takový prostředek neexistuje na hostiteli aplikace Internet, tak *404 - Nenalezeno* vrátí odpověď.</span><span class="sxs-lookup"><span data-stu-id="472c9-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="472c9-165">Protože prohlížeče zasílat požadavky do Internetu na hostitele pro stránky na straně klienta, webové servery a služby hostování musí přepsat všechny požadavky na prostředky nejsou fyzicky na server, aby *index.html* stránky.</span><span class="sxs-lookup"><span data-stu-id="472c9-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="472c9-166">Když *index.html* se vrátí, má aplikace na straně klienta směrovače a odpovídá zprávou správný zdroj.</span><span class="sxs-lookup"><span data-stu-id="472c9-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="472c9-167">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="472c9-167">App base path</span></span>

<span data-ttu-id="472c9-168">*Základní cesty aplikace* je kořenová cesta virtuální aplikace na serveru.</span><span class="sxs-lookup"><span data-stu-id="472c9-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="472c9-169">Například aplikace, které se nacházejí na serveru Contoso ve virtuální složce na `/CoolApp/` je dosažena `https://www.contoso.com/CoolApp` a má virtuální základní cesta `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="472c9-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="472c9-170">Nastavením základní cesta aplikace na `CoolApp/`, aplikace se provádí vědět, kde se prakticky nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="472c9-170">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="472c9-171">Aplikace můžete použít základní cesta aplikace k vytvoření adresy URL kořeni aplikace z komponenty, která není v kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="472c9-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="472c9-172">To umožňuje součásti, které existují na různých úrovních adresářovou strukturu vytvářet odkazy na další zdroje v umístěních v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="472c9-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="472c9-173">Základní cesta aplikace se také používá k zachycení hypertextový odkaz klikne, kde `href` cíl odkazu je v rámci základní cesty aplikace místo identifikátoru URI&mdash;směrovače Blazor zpracovává vnitřní navigační.</span><span class="sxs-lookup"><span data-stu-id="472c9-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="472c9-174">V mnoha scénářích hostování serveru virtuální cesta k aplikaci je kořenový adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="472c9-175">V těchto případech je základní cesty aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="472c9-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="472c9-176">V jiných scénářích hostování, jako jsou virtuální adresáře stránkách Githubu a služby IIS nebo dílčí aplikace základní cesty aplikace nastavte na serveru virtuální cesta k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="472c9-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="472c9-177">Nastavení základní cesty aplikace, přidat nebo aktualizovat `<base>` značku *index.html* najdete v `<head>` značky elementů.</span><span class="sxs-lookup"><span data-stu-id="472c9-177">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="472c9-178">Nastavte `href` atribut hodnotu `virtual-path/` (vyžaduje se do adresy koncové lomítko), kde `virtual-path/` je kořenová cesta úplnou virtuální aplikace na serveru pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="472c9-178">Set the `href` attribute value to `virtual-path/` (the trailing slash is required), where `virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="472c9-179">V předchozím příkladu je nastavena virtuální cesta `CoolApp/`: `<base href="CoolApp/">`.</span><span class="sxs-lookup"><span data-stu-id="472c9-179">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/">`.</span></span>

<span data-ttu-id="472c9-180">Pro aplikace s virtuální cestou nekořenovými nakonfigurovali (například `<base href="CoolApp/">`), aplikace nenajde žádné její prostředky *při spuštění místně*.</span><span class="sxs-lookup"><span data-stu-id="472c9-180">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="472c9-181">Chcete-li tento problém vyřešit během místní vývoj a testování, můžete zadat *základ cesty* argument, který odpovídá `href` hodnotu `<base>` značky v době běhu.</span><span class="sxs-lookup"><span data-stu-id="472c9-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="472c9-182">Předat argument základní cesty s kořenovou cestou (`/`) při místním spuštění aplikace, spusťte následující příkaz z adresáře aplikace:</span><span class="sxs-lookup"><span data-stu-id="472c9-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="472c9-183">Reakce aplikace na místně `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="472c9-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="472c9-184">Další informace najdete v části na [hodnota konfigurace hostitele základní cesty](#path-base).</span><span class="sxs-lookup"><span data-stu-id="472c9-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="472c9-185">Pokud aplikace používá [model hostingu na straně klienta](xref:blazor/hosting-models#client-side-hosting-model) (na základě **Blazor** šablony projektu) a je hostovaný jako dílčí aplikace služby IIS v aplikaci ASP.NET Core, je potřeba zakázat zděděné ASP.NET Core Obslužná rutina modulu nebo Ujistěte se, že aplikace root (nadřazené) `<handlers>` tématu *web.config* soubor není děděný podřízeným aplikacím.</span><span class="sxs-lookup"><span data-stu-id="472c9-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="472c9-186">Odebrat publikování obslužné rutiny v aplikaci *web.config* souboru tak, že přidáte `<handlers>` část do souboru:</span><span class="sxs-lookup"><span data-stu-id="472c9-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="472c9-187">Můžete také zakázat dědičnost (nadřazené) kořenové aplikace `<system.webServer>` části pomocí `<location>` element s `inheritInChildApplications` nastavena na `false`:</span><span class="sxs-lookup"><span data-stu-id="472c9-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="472c9-188">Kromě konfigurace základní cesty aplikace, jak je popsáno v této části se provádí odebírá obslužnou rutinu nebo zakážou dědičnost.</span><span class="sxs-lookup"><span data-stu-id="472c9-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="472c9-189">Nastavení základní cesty aplikace v aplikaci prvku *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="472c9-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="472c9-190">Hostované nasazení pomocí technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="472c9-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="472c9-191">A *hostované nasazení* slouží Blazor aplikace na straně klienta z prohlížečů [aplikace ASP.NET Core](xref:index) , který běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="472c9-191">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="472c9-192">Blazor aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="472c9-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="472c9-193">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="472c9-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="472c9-194">Hostované nasazení Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablonu projektu (`blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="472c9-194">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="472c9-195">Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="472c9-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="472c9-196">Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="472c9-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="472c9-197">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="472c9-197">Standalone deployment</span></span>

<span data-ttu-id="472c9-198">A *samostatné nasazení* slouží jako sadu statické soubory, které jsou požadovány přímo klienty Blazor aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="472c9-198">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="472c9-199">Webový server se používají k předávání Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-199">A web server isn't used to serve the Blazor app.</span></span>

### <a name="iis"></a><span data-ttu-id="472c9-200">IIS</span><span class="sxs-lookup"><span data-stu-id="472c9-200">IIS</span></span>

<span data-ttu-id="472c9-201">Služba IIS je schopen statický souborový server pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-201">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="472c9-202">Můžete nakonfigurovat službu IIS do hostitele Blazor, naleznete v tématu [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="472c9-202">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="472c9-203">Publikované prostředky vytvořené v */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky.</span><span class="sxs-lookup"><span data-stu-id="472c9-203">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="472c9-204">Hostování obsahu *publikovat* složku na webovém serveru nebo hostující služby.</span><span class="sxs-lookup"><span data-stu-id="472c9-204">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="472c9-205">web.config</span><span class="sxs-lookup"><span data-stu-id="472c9-205">web.config</span></span>

<span data-ttu-id="472c9-206">Při publikování projektu Blazor *web.config* soubor se vytvoří s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="472c9-206">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="472c9-207">Typy MIME jsou nastavené pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="472c9-207">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="472c9-208">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="472c9-208">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="472c9-209">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="472c9-209">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="472c9-210">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="472c9-210">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="472c9-211">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="472c9-211">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="472c9-212">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="472c9-212">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="472c9-213">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="472c9-213">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="472c9-214">Modul přepisování adres URL pravidla jsou vytvořeny:</span><span class="sxs-lookup"><span data-stu-id="472c9-214">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="472c9-215">Poskytovat dílčí adresáře, kde jsou umístěny statických prostředků aplikace (*/dist/ {název sestavení} {cesta k požadované}*).</span><span class="sxs-lookup"><span data-stu-id="472c9-215">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="472c9-216">Vytvoření aplikace SPA záložní směrování tak, aby požadavky pro nesouborové prostředky se přesměrují do aplikace výchozí dokument v její složce statické prostředky (*{sestavení NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="472c9-216">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="472c9-217">Nainstalovat modul přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="472c9-217">Install the URL Rewrite Module</span></span>

<span data-ttu-id="472c9-218">[Modul přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite) se vyžaduje k přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="472c9-218">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="472c9-219">Ve výchozím nastavení není nainstalován modul a není k dispozici pro instalaci jako funkci služby role Webový Server (IIS).</span><span class="sxs-lookup"><span data-stu-id="472c9-219">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="472c9-220">Modul musí stáhnout z webu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="472c9-220">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="472c9-221">Použití instalačního programu webové platformy nainstalovat modul:</span><span class="sxs-lookup"><span data-stu-id="472c9-221">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="472c9-222">Místně, přejděte [modul přepisování adres URL stránky pro stažení](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="472c9-222">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="472c9-223">Pro anglickou verzi, vyberte **instalace webové platformy** pro stažení instalačního programu Instalace webové platformy.</span><span class="sxs-lookup"><span data-stu-id="472c9-223">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="472c9-224">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) pro stažení instalačního programu.</span><span class="sxs-lookup"><span data-stu-id="472c9-224">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="472c9-225">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="472c9-225">Copy the installer to the server.</span></span> <span data-ttu-id="472c9-226">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="472c9-226">Run the installer.</span></span> <span data-ttu-id="472c9-227">Vyberte **nainstalovat** tlačítko a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="472c9-227">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="472c9-228">Po dokončení instalace není nutné restartovat server.</span><span class="sxs-lookup"><span data-stu-id="472c9-228">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="472c9-229">Konfiguraci webové stránky</span><span class="sxs-lookup"><span data-stu-id="472c9-229">Configure the website</span></span>

<span data-ttu-id="472c9-230">Nastavit na webu **fyzická cesta** do složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-230">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="472c9-231">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="472c9-231">The folder contains:</span></span>

* <span data-ttu-id="472c9-232">*Web.config* soubor, který služba IIS používá pro konfiguraci webové stránky, včetně pravidel požadovaná přesměrování a typy obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="472c9-232">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="472c9-233">Složku statických prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="472c9-233">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="472c9-234">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="472c9-234">Troubleshooting</span></span>

<span data-ttu-id="472c9-235">Pokud *500 – Interní chyba serveru* přijetí a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci příslušného webu, ověřte, že je nainstalován modul přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="472c9-235">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="472c9-236">Když není nainstalován modul, *web.config* soubor nejde parsovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="472c9-236">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="472c9-237">To zabrání načítání konfigurace na webu a webu z obsluhy statických souborů pro Blazor Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="472c9-237">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="472c9-238">Další informace o řešení potíží s nasazením do služby IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="472c9-238">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="nginx"></a><span data-ttu-id="472c9-239">Server Nginx</span><span class="sxs-lookup"><span data-stu-id="472c9-239">Nginx</span></span>

<span data-ttu-id="472c9-240">Následující *nginx.conf* souboru je zjednodušenou ukazují, jak nakonfigurovat Nginx tak, aby odeslat *index.html* souboru pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="472c9-240">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="472c9-241">Další informace o konfiguraci serveru webového serveru Nginx produkčního prostředí, najdete v části [NGINX konfiguračních souborů a vytváří NGINX Plus](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="472c9-241">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="472c9-242">Server Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="472c9-242">Nginx in Docker</span></span>

<span data-ttu-id="472c9-243">K hostování Blazor v Dockeru pomocí serveru Nginx, instalační program soubor Dockerfile, který chcete použít image serveru Nginx Alpine na základě.</span><span class="sxs-lookup"><span data-stu-id="472c9-243">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="472c9-244">Aktualizace souboru Dockerfile zkopírovat *nginx.config* souboru do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="472c9-244">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="472c9-245">Přidejte jeden řádek do souboru Dockerfile, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="472c9-245">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="472c9-246">Stránkách Githubu</span><span class="sxs-lookup"><span data-stu-id="472c9-246">GitHub Pages</span></span>

<span data-ttu-id="472c9-247">Pro zpracování přepisů adresu URL, přidejte *404. html* soubor skriptu, který zpracovává požadavek na přesměrování *index.html* stránky.</span><span class="sxs-lookup"><span data-stu-id="472c9-247">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="472c9-248">Příklad implementace poskytované komunitou, naleznete v tématu [jedné stránky aplikace pro stránky Githubu](http://spa-github-pages.rafrex.com/) ([rafrex/spa – github stránky na Githubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="472c9-248">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="472c9-249">Příklad použití komunity přístup, můžete zobrazit v [blazor-demo/blazor-demo.github.io na Githubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živého webu](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="472c9-249">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="472c9-250">Když použijete web projektu namísto serveru organizace, přidat nebo aktualizovat `<base>` značku *index.html*.</span><span class="sxs-lookup"><span data-stu-id="472c9-250">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="472c9-251">Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="472c9-251">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
