---
title: Hostitelství a nasazení aplikace ASP.NET Core Blazor na straně klienta
author: guardrex
description: Zjistěte, jak hostovat a nasazení Blazor aplikace pomocí ASP.NET Core, Content Delivery Network (CDN), souborové servery a stránkách Githubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: b6d08189280e9ca58362265d9b6f2536a3ad1d38
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500495"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a><span data-ttu-id="9a186-103">Hostitelství a nasazení aplikace ASP.NET Core Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9a186-103">Host and deploy ASP.NET Core Blazor client-side</span></span>

<span data-ttu-id="9a186-104">Podle [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9a186-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="9a186-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="9a186-105">Host configuration values</span></span>

<span data-ttu-id="9a186-106">Blazor aplikací, které používají [model hostingu na straně klienta](xref:blazor/hosting-models#client-side) může přijmout hodnoty následující konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9a186-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="9a186-107">Obsah kořenové</span><span class="sxs-lookup"><span data-stu-id="9a186-107">Content Root</span></span>

<span data-ttu-id="9a186-108">`--contentroot` Argument Nastaví absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="9a186-109">V následujících příkladech `/content-root-path` je obsahu kořenovou cestu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="9a186-110">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="9a186-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9a186-111">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="9a186-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="9a186-112">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="9a186-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="9a186-113">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9a186-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="9a186-114">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9a186-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9a186-115">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="9a186-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="9a186-116">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="9a186-116">Path base</span></span>

<span data-ttu-id="9a186-117">`--pathbase` Argument nastaví základní cesty aplikace pro aplikaci pro místní spuštění s virtuální cestou nekořenovými ( `<base>` značky `href` nastavený na cestu jiné než `/` pro pracovní a provozní).</span><span class="sxs-lookup"><span data-stu-id="9a186-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="9a186-118">V následujících příkladech `/virtual-path` je základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="9a186-119">Další informace najdete v tématu [základní cesty aplikace](#app-base-path) oddílu.</span><span class="sxs-lookup"><span data-stu-id="9a186-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9a186-120">Na rozdíl od zadaná cesta k `href` z `<base>` značku, nemusíte zahrnovat koncové lomítko (`/`) při předávání `--pathbase` hodnota argumentu.</span><span class="sxs-lookup"><span data-stu-id="9a186-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="9a186-121">Pokud je základní cesta aplikace součástí `<base>` označit jako `<base href="/CoolApp/">` (včetně koncového lomítka), předejte hodnotu argument příkazového řádku jako `--pathbase=/CoolApp` (žádného koncového lomítka).</span><span class="sxs-lookup"><span data-stu-id="9a186-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="9a186-122">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="9a186-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9a186-123">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="9a186-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="9a186-124">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="9a186-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="9a186-125">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9a186-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="9a186-126">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9a186-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9a186-127">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="9a186-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="9a186-128">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="9a186-128">URLs</span></span>

<span data-ttu-id="9a186-129">`--urls` Argument nastaví IP adres nebo adres hostitele s porty a protokoly pro naslouchání požadavkům.</span><span class="sxs-lookup"><span data-stu-id="9a186-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="9a186-130">Předejte argument při spuštění aplikace místně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="9a186-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="9a186-131">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="9a186-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="9a186-132">Přidejte záznam do aplikace *launchSettings.json* soubor **služby IIS Express** profilu.</span><span class="sxs-lookup"><span data-stu-id="9a186-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="9a186-133">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="9a186-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="9a186-134">V sadě Visual Studio, zadat argument v **vlastnosti** > **ladění** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="9a186-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="9a186-135">Nastavení argumentu na stránce vlastností Visual Studio přidá argument *launchSettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="9a186-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="9a186-136">Nasazení</span><span class="sxs-lookup"><span data-stu-id="9a186-136">Deployment</span></span>

<span data-ttu-id="9a186-137">S [model hostingu na straně klienta](xref:blazor/hosting-models#client-side):</span><span class="sxs-lookup"><span data-stu-id="9a186-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="9a186-138">Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="9a186-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="9a186-139">Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9a186-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="9a186-140">Je podporován některý z následujících strategií:</span><span class="sxs-lookup"><span data-stu-id="9a186-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="9a186-141">Aplikace Blazor obsluhují aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9a186-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="9a186-142">Tato strategie je obsažen v [hostované nasazení pomocí technologie ASP.NET Core](#hosted-deployment-with-aspnet-core) oddílu.</span><span class="sxs-lookup"><span data-stu-id="9a186-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="9a186-143">Blazor aplikace je umístěn na statické hostování webového serveru nebo službě, kde není .NET používají k předávání Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="9a186-144">Tato strategie je obsažen v [samostatné nasazení](#standalone-deployment) oddílu.</span><span class="sxs-lookup"><span data-stu-id="9a186-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="9a186-145">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="9a186-145">Configure the Linker</span></span>

<span data-ttu-id="9a186-146">Blazor provádí Intermediate Language (IL) propojení na každé sestavení odebrat nepotřebné IL z výstupního sestavení.</span><span class="sxs-lookup"><span data-stu-id="9a186-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="9a186-147">Propojování sestavení je možné řídit na sestavení.</span><span class="sxs-lookup"><span data-stu-id="9a186-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="9a186-148">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="9a186-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="9a186-149">Přepisování adres URL pro správné směrování</span><span class="sxs-lookup"><span data-stu-id="9a186-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="9a186-150">Směrování požadavků pro součásti stránky v aplikaci na straně klienta není snadné – stačí směrování žádostí na aplikace na straně serveru, prostředí.</span><span class="sxs-lookup"><span data-stu-id="9a186-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="9a186-151">Vezměte v úvahu aplikace na straně klienta se dvěma stránkami:</span><span class="sxs-lookup"><span data-stu-id="9a186-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="9a186-152">**_Main.Razor** &ndash; zatížení v kořenovém adresáři aplikace a obsahuje odkaz na stránku o (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="9a186-152">**_Main.razor** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="9a186-153">**_About.Razor** &ndash; o stránce.</span><span class="sxs-lookup"><span data-stu-id="9a186-153">**_About.razor** &ndash; About page.</span></span>

<span data-ttu-id="9a186-154">Pokud aplikace výchozí dokument se požaduje pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="9a186-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="9a186-155">Prohlížeč odešle požadavek.</span><span class="sxs-lookup"><span data-stu-id="9a186-155">The browser makes a request.</span></span>
1. <span data-ttu-id="9a186-156">Vrátí výchozí stránku, což je obvykle *index.html*.</span><span class="sxs-lookup"><span data-stu-id="9a186-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="9a186-157">*index.HTML* bootstraps aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="9a186-158">Směrovač Blazor na zatížení a Razor hlavní stránky (*Main.razor*) se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="9a186-158">Blazor's router loads, and the Razor Main page (*Main.razor*) is displayed.</span></span>

<span data-ttu-id="9a186-159">Na hlavní stránce vyberte odkaz na stránku o načte stránku o.</span><span class="sxs-lookup"><span data-stu-id="9a186-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="9a186-160">Vyberte odkaz na stránku o funguje na straně klienta, protože směrovače Blazor zastaví prohlížeče z požadavku na Internetu, aby `www.contoso.com` pro `About` a slouží samotná stránka o.</span><span class="sxs-lookup"><span data-stu-id="9a186-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="9a186-161">Všechny žádosti pro interní stránky *v rámci aplikace na straně klienta* stejným způsobem fungovat: Požadavky nejsou aktivace založené na prohlížeči požadavky na prostředky serveru hostované na Internetu.</span><span class="sxs-lookup"><span data-stu-id="9a186-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="9a186-162">Směrovač interně zpracovává požadavky.</span><span class="sxs-lookup"><span data-stu-id="9a186-162">The router handles the requests internally.</span></span>

<span data-ttu-id="9a186-163">Pokud je vytvořena pomocí panelu Adresa prohlížeče pro žádost `www.contoso.com/About`, požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="9a186-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="9a186-164">Žádný takový prostředek neexistuje na hostiteli aplikace Internet, tak *404 - Nenalezeno* vrátí odpověď.</span><span class="sxs-lookup"><span data-stu-id="9a186-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="9a186-165">Protože prohlížeče zasílat požadavky do Internetu na hostitele pro stránky na straně klienta, webové servery a služby hostování musí přepsat všechny požadavky na prostředky nejsou fyzicky na server, aby *index.html* stránky.</span><span class="sxs-lookup"><span data-stu-id="9a186-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="9a186-166">Když *index.html* se vrátí, má aplikace na straně klienta směrovače a odpovídá zprávou správný zdroj.</span><span class="sxs-lookup"><span data-stu-id="9a186-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="9a186-167">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="9a186-167">App base path</span></span>

<span data-ttu-id="9a186-168">*Základní cesty aplikace* je kořenová cesta virtuální aplikace na serveru.</span><span class="sxs-lookup"><span data-stu-id="9a186-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="9a186-169">Například aplikace, které se nacházejí na serveru Contoso ve virtuální složce na `/CoolApp/` je dosažena `https://www.contoso.com/CoolApp` a má virtuální základní cesta `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="9a186-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="9a186-170">Nastavení základní cesty aplikace na virtuální cestu (`<base href="/CoolApp/">`), aplikace se provádí vědět, kde se prakticky nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="9a186-170">By setting the app base path to the virtual path (`<base href="/CoolApp/">`), the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="9a186-171">Aplikace můžete použít základní cesta aplikace k vytvoření adresy URL kořeni aplikace z komponenty, která není v kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="9a186-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="9a186-172">To umožňuje součásti, které existují na různých úrovních adresářovou strukturu vytvářet odkazy na další zdroje v umístěních v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a186-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="9a186-173">Základní cesta aplikace se také používá k zachycení hypertextový odkaz klikne, kde `href` cíl odkazu je v rámci základní cesty aplikace místo identifikátoru URI&mdash;směrovače Blazor zpracovává vnitřní navigační.</span><span class="sxs-lookup"><span data-stu-id="9a186-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="9a186-174">V mnoha scénářích hostování serveru virtuální cesta k aplikaci je kořenový adresář aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="9a186-175">V těchto případech je základní cesty aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a186-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="9a186-176">V jiných scénářích hostování, jako jsou virtuální adresáře stránkách Githubu a služby IIS nebo dílčí aplikace základní cesty aplikace nastavte na serveru virtuální cesta k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a186-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="9a186-177">Nastavení základní cesty aplikace, aktualizujte `<base>` značky v rámci `<head>` značky elementů *wwwroot/index.html* souboru.</span><span class="sxs-lookup"><span data-stu-id="9a186-177">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="9a186-178">Nastavte `href` atribut hodnotu `/virtual-path/` (vyžaduje se do adresy koncové lomítko), kde `/virtual-path/` je kořenová cesta úplnou virtuální aplikace na serveru pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9a186-178">Set the `href` attribute value to `/virtual-path/` (the trailing slash is required), where `/virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="9a186-179">V předchozím příkladu je nastavena virtuální cesta `/CoolApp/`: `<base href="/CoolApp/">`.</span><span class="sxs-lookup"><span data-stu-id="9a186-179">In the preceding example, the virtual path is set to `/CoolApp/`: `<base href="/CoolApp/">`.</span></span>

<span data-ttu-id="9a186-180">Pro aplikace s virtuální cestou nekořenovými nakonfigurovali (například `<base href="/CoolApp/">`), aplikace nenajde žádné její prostředky *při spuštění místně*.</span><span class="sxs-lookup"><span data-stu-id="9a186-180">For an app with a non-root virtual path configured (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="9a186-181">Chcete-li tento problém vyřešit během místní vývoj a testování, můžete zadat *základ cesty* argument, který odpovídá `href` hodnotu `<base>` značky v době běhu.</span><span class="sxs-lookup"><span data-stu-id="9a186-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="9a186-182">Předat argument základní cesty s kořenovou cestou (`/`) při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace se `--pathbase` možnost:</span><span class="sxs-lookup"><span data-stu-id="9a186-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

<span data-ttu-id="9a186-183">Pro aplikace s virtuální cestou základní `/CoolApp/` (`<base href="/CoolApp/">`), má příkaz tuto podobu:</span><span class="sxs-lookup"><span data-stu-id="9a186-183">For an app with a virtual base path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="9a186-184">Reakce aplikace na místně `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="9a186-184">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="9a186-185">Další informace najdete v části na [hodnota konfigurace hostitele základní cesty](#path-base).</span><span class="sxs-lookup"><span data-stu-id="9a186-185">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="9a186-186">Pokud aplikace používá [model hostingu na straně klienta](xref:blazor/hosting-models#client-side) (na základě **Blazor** šablonu projektu; `blazor` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz) a je hostovaná jako dílčí aplikace služby IIS v aplikaci ASP.NET Core, je potřeba zakázat obslužnou rutinu zděděné modul ASP.NET Core nebo Ujistěte se, že aplikace root (nadřazené) `<handlers>` tématu *web.config* soubor není děděný Sub – aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-186">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side) (based on the **Blazor** project template; the `blazor` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="9a186-187">Odebrat publikování obslužné rutiny v aplikaci *web.config* souboru tak, že přidáte `<handlers>` část do souboru:</span><span class="sxs-lookup"><span data-stu-id="9a186-187">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="9a186-188">Můžete také zakázat dědičnost (nadřazené) kořenové aplikace `<system.webServer>` části pomocí `<location>` element s `inheritInChildApplications` nastavena na `false`:</span><span class="sxs-lookup"><span data-stu-id="9a186-188">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="9a186-189">Kromě konfigurace základní cesty aplikace, jak je popsáno v této části se provádí odebírá obslužnou rutinu nebo zakážou dědičnost.</span><span class="sxs-lookup"><span data-stu-id="9a186-189">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="9a186-190">Nastavení základní cesty aplikace v aplikaci prvku *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="9a186-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="9a186-191">Hostované nasazení pomocí technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9a186-191">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="9a186-192">A *hostované nasazení* slouží Blazor aplikace na straně klienta z prohlížečů [aplikace ASP.NET Core](xref:index) , který běží na serveru.</span><span class="sxs-lookup"><span data-stu-id="9a186-192">A *hosted deployment* serves the Blazor client-side app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="9a186-193">Blazor aplikace je součástí aplikace ASP.NET Core v publikované výstup tak, aby dvě aplikace se nasazují společně.</span><span class="sxs-lookup"><span data-stu-id="9a186-193">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="9a186-194">Webový server, který dokáže hostovat aplikace ASP.NET Core je povinný.</span><span class="sxs-lookup"><span data-stu-id="9a186-194">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="9a186-195">Hostované nasazení Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablonu projektu (`blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz).</span><span class="sxs-lookup"><span data-stu-id="9a186-195">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="9a186-196">Další informace o nasazení a hostování aplikací ASP.NET Core najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="9a186-196">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="9a186-197">Informace o nasazení do služby Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="9a186-197">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="9a186-198">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="9a186-198">Standalone deployment</span></span>

<span data-ttu-id="9a186-199">A *samostatné nasazení* slouží jako sadu statické soubory, které jsou požadovány přímo klienty Blazor aplikace na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9a186-199">A *standalone deployment* serves the Blazor client-side app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="9a186-200">Žádné statický souborový server je schopná odesílat Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-200">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="9a186-201">Jsou publikovány v samostatných nasazení prostředků *bin/Release / {TARGET FRAMEWORK} /publish/ {název sestavení} / dist* složky.</span><span class="sxs-lookup"><span data-stu-id="9a186-201">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="9a186-202">IIS</span><span class="sxs-lookup"><span data-stu-id="9a186-202">IIS</span></span>

<span data-ttu-id="9a186-203">Služba IIS je schopen statický souborový server pro Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-203">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="9a186-204">Můžete nakonfigurovat službu IIS do hostitele Blazor, naleznete v tématu [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="9a186-204">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="9a186-205">Publikované prostředky vytvořené v */bin/vydání / {CÍLOVÁ ARCHITEKTURA} / publish* složky.</span><span class="sxs-lookup"><span data-stu-id="9a186-205">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="9a186-206">Hostování obsahu *publikovat* složku na webovém serveru nebo hostující služby.</span><span class="sxs-lookup"><span data-stu-id="9a186-206">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="9a186-207">web.config</span><span class="sxs-lookup"><span data-stu-id="9a186-207">web.config</span></span>

<span data-ttu-id="9a186-208">Při publikování projektu Blazor *web.config* soubor se vytvoří s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="9a186-208">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="9a186-209">Typy MIME jsou nastavené pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="9a186-209">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="9a186-210">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="9a186-210">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="9a186-211">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="9a186-211">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="9a186-212">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="9a186-212">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="9a186-213">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9a186-213">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="9a186-214">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="9a186-214">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="9a186-215">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="9a186-215">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="9a186-216">Modul přepisování adres URL pravidla jsou vytvořeny:</span><span class="sxs-lookup"><span data-stu-id="9a186-216">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="9a186-217">Poskytovat dílčí adresáře, kde jsou umístěny statických prostředků aplikace ( */dist/ {název sestavení} {cesta k požadované}* ).</span><span class="sxs-lookup"><span data-stu-id="9a186-217">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="9a186-218">Vytvoření aplikace SPA záložní směrování tak, aby požadavky pro nesouborové prostředky se přesměrují do aplikace výchozí dokument v její složce statické prostředky ( *{sestavení NAME}/dist/index.html*).</span><span class="sxs-lookup"><span data-stu-id="9a186-218">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="9a186-219">Nainstalovat modul přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="9a186-219">Install the URL Rewrite Module</span></span>

<span data-ttu-id="9a186-220">[Modul přepisování adres URL](https://www.iis.net/downloads/microsoft/url-rewrite) se vyžaduje k přepsání adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9a186-220">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="9a186-221">Ve výchozím nastavení není nainstalován modul a není k dispozici pro instalaci jako funkci služby role Webový Server (IIS).</span><span class="sxs-lookup"><span data-stu-id="9a186-221">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="9a186-222">Modul musí stáhnout z webu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9a186-222">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="9a186-223">Použití instalačního programu webové platformy nainstalovat modul:</span><span class="sxs-lookup"><span data-stu-id="9a186-223">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="9a186-224">Místně, přejděte [modul přepisování adres URL stránky pro stažení](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="9a186-224">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="9a186-225">Pro anglickou verzi, vyberte **instalace webové platformy** pro stažení instalačního programu Instalace webové platformy.</span><span class="sxs-lookup"><span data-stu-id="9a186-225">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="9a186-226">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) pro stažení instalačního programu.</span><span class="sxs-lookup"><span data-stu-id="9a186-226">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="9a186-227">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="9a186-227">Copy the installer to the server.</span></span> <span data-ttu-id="9a186-228">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="9a186-228">Run the installer.</span></span> <span data-ttu-id="9a186-229">Vyberte **nainstalovat** tlačítko a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="9a186-229">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="9a186-230">Po dokončení instalace není nutné restartovat server.</span><span class="sxs-lookup"><span data-stu-id="9a186-230">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="9a186-231">Konfiguraci webové stránky</span><span class="sxs-lookup"><span data-stu-id="9a186-231">Configure the website</span></span>

<span data-ttu-id="9a186-232">Nastavit na webu **fyzická cesta** do složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-232">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="9a186-233">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="9a186-233">The folder contains:</span></span>

* <span data-ttu-id="9a186-234">*Web.config* soubor, který služba IIS používá pro konfiguraci webové stránky, včetně pravidel požadovaná přesměrování a typy obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="9a186-234">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="9a186-235">Složku statických prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="9a186-235">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="9a186-236">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="9a186-236">Troubleshooting</span></span>

<span data-ttu-id="9a186-237">Pokud *500 – Interní chyba serveru* přijetí a Správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci příslušného webu, ověřte, že je nainstalován modul přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="9a186-237">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="9a186-238">Když není nainstalován modul, *web.config* soubor nejde parsovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="9a186-238">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="9a186-239">To zabrání načítání konfigurace na webu a webu z obsluhy statických souborů pro Blazor Správce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="9a186-239">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="9a186-240">Další informace o řešení potíží s nasazením do služby IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="9a186-240">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="9a186-241">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="9a186-241">Azure Storage</span></span>

<span data-ttu-id="9a186-242">Hostování statického souboru v Azure Storage umožňuje hostování bez serveru Blazor aplikací.</span><span class="sxs-lookup"><span data-stu-id="9a186-242">Azure Storage static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="9a186-243">Vlastní názvy domén, Azure Content Delivery Network (CDN) a protokolu HTTPS se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="9a186-243">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="9a186-244">Když je povolena služba objektů blob pro hostování statického webu v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="9a186-244">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="9a186-245">Nastavte **název dokumentu indexu** k `index.html`.</span><span class="sxs-lookup"><span data-stu-id="9a186-245">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="9a186-246">Nastavte **cesta dokumentu chyby** k `index.html`.</span><span class="sxs-lookup"><span data-stu-id="9a186-246">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="9a186-247">Součásti Razor a ostatní koncové body nesouborové není nachází na fyzické cesty v statického obsahu, které jsou uložené ve službě blob.</span><span class="sxs-lookup"><span data-stu-id="9a186-247">Razor Components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="9a186-248">Při přijetí požadavku z jednoho z těchto prostředků je, že by měl zpracovat Blazor směrovače, *404 - Nenalezeno* chyby vygenerované službou blob směruje žádosti **cesta dokumentu chyby**.</span><span class="sxs-lookup"><span data-stu-id="9a186-248">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="9a186-249">*Index.html* je vrácen objekt blob a směrovač Blazor načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="9a186-249">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="9a186-250">Další informace najdete v tématu [hostoval statický web ve službě Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="9a186-250">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="9a186-251">Server Nginx</span><span class="sxs-lookup"><span data-stu-id="9a186-251">Nginx</span></span>

<span data-ttu-id="9a186-252">Následující *nginx.conf* souboru je zjednodušenou ukazují, jak nakonfigurovat Nginx tak, aby odeslat *index.html* souboru pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="9a186-252">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="9a186-253">Další informace o konfiguraci serveru webového serveru Nginx produkčního prostředí, najdete v části [NGINX konfiguračních souborů a vytváří NGINX Plus](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="9a186-253">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="9a186-254">Server Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="9a186-254">Nginx in Docker</span></span>

<span data-ttu-id="9a186-255">K hostování Blazor v Dockeru pomocí serveru Nginx, instalační program soubor Dockerfile, který chcete použít image serveru Nginx Alpine na základě.</span><span class="sxs-lookup"><span data-stu-id="9a186-255">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="9a186-256">Aktualizace souboru Dockerfile zkopírovat *nginx.config* souboru do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="9a186-256">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="9a186-257">Přidejte jeden řádek do souboru Dockerfile, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9a186-257">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="9a186-258">Stránkách Githubu</span><span class="sxs-lookup"><span data-stu-id="9a186-258">GitHub Pages</span></span>

<span data-ttu-id="9a186-259">Pro zpracování přepisů adresu URL, přidejte *404. html* soubor skriptu, který zpracovává požadavek na přesměrování *index.html* stránky.</span><span class="sxs-lookup"><span data-stu-id="9a186-259">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="9a186-260">Příklad implementace poskytované komunitou, naleznete v tématu [jedné stránky aplikace pro stránky Githubu](http://spa-github-pages.rafrex.com/) ([rafrex/spa – github stránky na Githubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="9a186-260">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="9a186-261">Příklad použití komunity přístup, můžete zobrazit v [blazor-demo/blazor-demo.github.io na Githubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živého webu](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="9a186-261">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="9a186-262">Když použijete web projektu namísto serveru organizace, přidat nebo aktualizovat `<base>` značku *index.html*.</span><span class="sxs-lookup"><span data-stu-id="9a186-262">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="9a186-263">Nastavte `href` hodnotu atributu na název úložiště GitHub s koncovým lomítkem (například `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="9a186-263">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
