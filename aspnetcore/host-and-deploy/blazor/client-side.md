---
title: Hostování a nasazení ASP.NET Core Blazor na straně klienta
author: guardrex
description: Naučte se hostovat a nasazovat aplikaci Blazor pomocí ASP.NET Core, stránek Content Delivery Networks (CDN), souborových serverů a GitHubu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: e9a42bd4e8511d426761746047fed2d4f7dfc6dd
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994079"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a><span data-ttu-id="6d511-103">Hostování a nasazení ASP.NET Core Blazor na straně klienta</span><span class="sxs-lookup"><span data-stu-id="6d511-103">Host and deploy ASP.NET Core Blazor client-side</span></span>

<span data-ttu-id="6d511-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6d511-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6d511-105">Hodnoty konfigurace hostitele</span><span class="sxs-lookup"><span data-stu-id="6d511-105">Host configuration values</span></span>

<span data-ttu-id="6d511-106">Aplikace Blazor, které používají [model hostování na straně klienta](xref:blazor/hosting-models#client-side) , můžou přijmout následující hodnoty konfigurace hostitele jako argumenty příkazového řádku za běhu ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="6d511-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="6d511-107">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="6d511-107">Content Root</span></span>

<span data-ttu-id="6d511-108">`--contentroot` Argument nastavuje absolutní cestu k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="6d511-109">V následujících příkladech `/content-root-path` je kořenová cesta obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="6d511-110">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6d511-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6d511-111">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="6d511-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="6d511-112">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6d511-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6d511-113">Toto nastavení se používá při spuštění aplikace s ladicím programem sady Visual Studio a z příkazového řádku s `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="6d511-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="6d511-114">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="6d511-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6d511-115">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="6d511-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="6d511-116">Základ cesty</span><span class="sxs-lookup"><span data-stu-id="6d511-116">Path base</span></span>

<span data-ttu-id="6d511-117">Argument nastaví základní cestu aplikace pro aplikaci spuštěnou místně s nekořenovou virtuální cestou `<base>` (značka `href` je nastavená na jinou cestu než `/` pro pracovní a produkční). `--pathbase`</span><span class="sxs-lookup"><span data-stu-id="6d511-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="6d511-118">V následujících příkladech `/virtual-path` je základ cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="6d511-119">Další informace najdete v části [základní cesta aplikace](#app-base-path) .</span><span class="sxs-lookup"><span data-stu-id="6d511-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6d511-120">Na rozdíl od cesty, která `href` je k `<base>` dispozici pro značku, nezahrnujte koncové lomítko`/` `--pathbase` () při předávání hodnoty argumentu.</span><span class="sxs-lookup"><span data-stu-id="6d511-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="6d511-121">Pokud se základní cesta aplikace poskytuje ve `<base>` značce jako `<base href="/CoolApp/">` (zahrnuje koncové lomítko), předejte hodnotu argumentu příkazového řádku jako `--pathbase=/CoolApp` (žádné koncové lomítko).</span><span class="sxs-lookup"><span data-stu-id="6d511-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="6d511-122">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6d511-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6d511-123">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="6d511-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="6d511-124">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6d511-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6d511-125">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.</span><span class="sxs-lookup"><span data-stu-id="6d511-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="6d511-126">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="6d511-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6d511-127">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="6d511-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="6d511-128">URL – adresy</span><span class="sxs-lookup"><span data-stu-id="6d511-128">URLs</span></span>

<span data-ttu-id="6d511-129">`--urls` Argument nastaví IP adresy nebo adresy hostitelů s porty a protokoly, které se mají na požadavky naslouchat.</span><span class="sxs-lookup"><span data-stu-id="6d511-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="6d511-130">Předejte argument při místním spuštění aplikace z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6d511-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6d511-131">Z adresáře aplikace spusťte:</span><span class="sxs-lookup"><span data-stu-id="6d511-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="6d511-132">Přidejte položku do souboru *launchSettings. JSON* aplikace v profilu **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6d511-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="6d511-133">Toto nastavení se používá při spuštění aplikace pomocí ladicího programu sady Visual Studio a z příkazového řádku `dotnet run`s.</span><span class="sxs-lookup"><span data-stu-id="6d511-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="6d511-134">V aplikaci Visual Studio zadejte argument v části **vlastnosti** > **ladit** > **argumenty aplikace**.</span><span class="sxs-lookup"><span data-stu-id="6d511-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6d511-135">Nastavení argumentu na stránce vlastností aplikace Visual Studio přidá argument do souboru *launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="6d511-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="6d511-136">Nasazení</span><span class="sxs-lookup"><span data-stu-id="6d511-136">Deployment</span></span>

<span data-ttu-id="6d511-137">S [modelem hostování na straně klienta](xref:blazor/hosting-models#client-side):</span><span class="sxs-lookup"><span data-stu-id="6d511-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="6d511-138">Do prohlížeče se stáhnou aplikace Blazor, její závislosti a modul runtime .NET.</span><span class="sxs-lookup"><span data-stu-id="6d511-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="6d511-139">Aplikace se spustí přímo ve vlákně uživatelského rozhraní prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6d511-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="6d511-140">Podporuje se jedna z následujících strategií:</span><span class="sxs-lookup"><span data-stu-id="6d511-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="6d511-141">Aplikace Blazor obsluhuje aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d511-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="6d511-142">Tato strategie je popsaná v části [hostované nasazení s ASP.NET Core](#hosted-deployment-with-aspnet-core) .</span><span class="sxs-lookup"><span data-stu-id="6d511-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="6d511-143">Aplikace Blazor je umístěna na statický hostující webový server nebo službu, kde rozhraní .NET se nepoužívá k obsluze aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="6d511-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="6d511-144">Tato strategie je uvedena v části [samostatné nasazení](#standalone-deployment) .</span><span class="sxs-lookup"><span data-stu-id="6d511-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="6d511-145">Konfigurace Linkeru</span><span class="sxs-lookup"><span data-stu-id="6d511-145">Configure the Linker</span></span>

<span data-ttu-id="6d511-146">Blazor provádí propojení podle jazyka IL (Intermediate Language) na každém sestavení pro odebrání zbytečného IL z výstupních sestavení.</span><span class="sxs-lookup"><span data-stu-id="6d511-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="6d511-147">Propojení sestavení lze řídit při sestavování.</span><span class="sxs-lookup"><span data-stu-id="6d511-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="6d511-148">Další informace naleznete v tématu <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="6d511-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="6d511-149">Přepište adresy URL pro správné směrování.</span><span class="sxs-lookup"><span data-stu-id="6d511-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="6d511-150">Požadavky směrování na součásti stránky v aplikaci na straně klienta nejsou stejně jednoduché jako požadavky na směrování na straně serveru hostované aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="6d511-151">Vezměte v úvahu aplikaci na straně klienta se dvěma součástmi:</span><span class="sxs-lookup"><span data-stu-id="6d511-151">Consider a client-side app with two components:</span></span>

* <span data-ttu-id="6d511-152">*Main. Razor* &ndash; se načte do kořenového adresáře aplikace a obsahuje odkaz na `About` komponentu (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="6d511-152">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="6d511-153">*O komponentě. Razor* &ndash; `About` .</span><span class="sxs-lookup"><span data-stu-id="6d511-153">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="6d511-154">Pokud je výchozí dokument aplikace požadován pomocí panelu Adresa prohlížeče (například `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="6d511-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="6d511-155">Prohlížeč vytvoří požadavek.</span><span class="sxs-lookup"><span data-stu-id="6d511-155">The browser makes a request.</span></span>
1. <span data-ttu-id="6d511-156">Vrátí se výchozí stránka, což je obvykle *index. html*.</span><span class="sxs-lookup"><span data-stu-id="6d511-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="6d511-157">*index. html* se v aplikaci zabootstrap.</span><span class="sxs-lookup"><span data-stu-id="6d511-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="6d511-158">Blazor směrovač se načte a komponenta Razor `Main` se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="6d511-158">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="6d511-159">Na hlavní `About` stránce vyberte odkaz na komponentu na klientovi, protože směrovač Blazor zabrání v prohlížeči, aby `www.contoso.com` odeslal požadavek na Internet pro `About` a obsluhu samotné vykreslené `About` komponenty.</span><span class="sxs-lookup"><span data-stu-id="6d511-159">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="6d511-160">Všechny požadavky na vnitřní koncové body *v aplikaci na straně klienta* fungují stejným způsobem: Požadavky neaktivují požadavky založené na prohlížeči na prostředky hostované na serveru na internetu.</span><span class="sxs-lookup"><span data-stu-id="6d511-160">All of the requests for internal endpoints *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="6d511-161">Směrovač zpracovává požadavky interně.</span><span class="sxs-lookup"><span data-stu-id="6d511-161">The router handles the requests internally.</span></span>

<span data-ttu-id="6d511-162">Pokud je žádost vytvořena pomocí panelu Adresa prohlížeče pro `www.contoso.com/About`, požadavek se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="6d511-162">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="6d511-163">Žádný takový prostředek na internetovém hostiteli aplikace neexistuje, takže se vrátí odpověď *404 –* Nenalezeno.</span><span class="sxs-lookup"><span data-stu-id="6d511-163">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="6d511-164">Vzhledem k tomu, že prohlížeče vytvářejí požadavky na internetové hostitele pro stránky na straně klienta, webové servery a hostitelské služby musí přepsat všechny požadavky na prostředky, které nejsou fyzicky na serveru, na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="6d511-164">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="6d511-165">Když se vrátí *index. html* , směrovač na straně klienta aplikace převezme a odpoví správným prostředkem.</span><span class="sxs-lookup"><span data-stu-id="6d511-165">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="6d511-166">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="6d511-166">App base path</span></span>

<span data-ttu-id="6d511-167">*Základní cesta aplikace* je kořenová cesta virtuální aplikace na serveru.</span><span class="sxs-lookup"><span data-stu-id="6d511-167">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="6d511-168">Například aplikace, která se nachází na serveru contoso ve virtuální složce `/CoolApp/` v, se dosáhne v `https://www.contoso.com/CoolApp` a má virtuální základní cestu `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="6d511-168">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="6d511-169">Když nastavíte základní cestu aplikace na virtuální cestu (`<base href="/CoolApp/">`), aplikace bude vědět, kde se prakticky nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="6d511-169">By setting the app base path to the virtual path (`<base href="/CoolApp/">`), the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="6d511-170">Aplikace může použít základní cestu aplikace k vytvoření adres URL relativních k kořenu aplikace ze součásti, která není v kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="6d511-170">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="6d511-171">To umožňuje komponentám, které existují na různých úrovních adresářové struktury, vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-171">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="6d511-172">Základní cesta aplikace se také používá k zachycení hypertextového odkazu, kde `href` cíl odkazu je v rámci základní cesty k umístění&mdash;identifikátoru URI aplikace, Blazor směrovač zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="6d511-172">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="6d511-173">V mnoha hostitelských scénářích je virtuální cesta serveru k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-173">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="6d511-174">V těchto případech je základní cesta aplikace lomítkem (`<base href="/" />`), což je výchozí konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-174">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="6d511-175">V jiných scénářích hostování, jako jsou stránky GitHubu a virtuální adresáře služby IIS nebo podaplikace, musí být základní cesta aplikace nastavená na virtuální cestu serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-175">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="6d511-176">Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku `<head>` v rámci prvků značek souboru *wwwroot/index.html* .</span><span class="sxs-lookup"><span data-stu-id="6d511-176">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="6d511-177">Nastavte hodnotu `/virtual-path/`atributuna (vyžaduje se koncové lomítko), kde `/virtual-path/` je úplná kořenová cesta virtuální aplikace na serveru aplikace. `href`</span><span class="sxs-lookup"><span data-stu-id="6d511-177">Set the `href` attribute value to `/virtual-path/` (the trailing slash is required), where `/virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="6d511-178">V předchozím příkladu je virtuální cesta nastavena na `/CoolApp/`:. `<base href="/CoolApp/">`</span><span class="sxs-lookup"><span data-stu-id="6d511-178">In the preceding example, the virtual path is set to `/CoolApp/`: `<base href="/CoolApp/">`.</span></span>

<span data-ttu-id="6d511-179">Pro aplikaci, která má nakonfigurovanou nekořenovou virtuální cestu (například `<base href="/CoolApp/">`), aplikace *při místním spuštění*nenalezne své prostředky.</span><span class="sxs-lookup"><span data-stu-id="6d511-179">For an app with a non-root virtual path configured (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="6d511-180">Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu.</span><span class="sxs-lookup"><span data-stu-id="6d511-180">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="6d511-181">Pokud chcete předat základní argument Path s kořenovou cestou (`/`) při místním spuštění aplikace, `dotnet run` spusťte příkaz z adresáře `--pathbase` aplikace s možností:</span><span class="sxs-lookup"><span data-stu-id="6d511-181">To pass the path base argument with the root path (`/`) when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

<span data-ttu-id="6d511-182">Pro aplikaci s virtuální základní cestou `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="6d511-182">For an app with a virtual base path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="6d511-183">Aplikace odpoví místně na adrese `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="6d511-183">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="6d511-184">Další informace najdete v části na [hodnotě základní konfigurace hostitele Path](#path-base).</span><span class="sxs-lookup"><span data-stu-id="6d511-184">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="6d511-185">Pokud aplikace používá [model hostování na straně klienta](xref:blazor/hosting-models#client-side) (na `blazorwasm` základě šablony projektu **aplikace Blazor WebAssembly** , šablony při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) a je hostována jako podaplikace služby IIS v aplikaci ASP.NET Core, je důležité Zakažte zděděnou obslužnou rutinu modulu ASP.NET Core nebo zajistěte, aby dílčí `<handlers>` aplikace nezdědila oddíl kořenové (nadřazené) aplikace v souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="6d511-185">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side) (based on the **Blazor WebAssembly App** project template, the `blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) and is hosted as an IIS sub-app in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="6d511-186">Odeberte obslužnou rutinu v publikovaném souboru *Web. config* aplikace přidáním `<handlers>` oddílu do souboru:</span><span class="sxs-lookup"><span data-stu-id="6d511-186">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="6d511-187">Případně zakažte `<system.webServer>` dědění kořenového oddílu (nadřazené) aplikace `<location>` pomocí elementu s `inheritInChildApplications` nastavením na `false`:</span><span class="sxs-lookup"><span data-stu-id="6d511-187">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="6d511-188">Odebrání obslužné rutiny nebo zakázání dědičnosti se provádí kromě konfigurace základní cesty aplikace, jak je popsáno v této části.</span><span class="sxs-lookup"><span data-stu-id="6d511-188">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="6d511-189">Nastavte základní cestu aplikace v souboru *index. html* aplikace na alias služby IIS, který se používá při konfiguraci dílčí aplikace v IIS.</span><span class="sxs-lookup"><span data-stu-id="6d511-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="6d511-190">Hostované nasazení s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d511-190">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="6d511-191">*Hostované nasazení* obsluhuje aplikaci Blazor na straně klienta z [aplikace ASP.NET Core](xref:index) , která běží na webovém serveru.</span><span class="sxs-lookup"><span data-stu-id="6d511-191">A *hosted deployment* serves the Blazor client-side app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="6d511-192">Aplikace Blazor je součástí aplikace ASP.NET Core v publikovaném výstupu, takže se tyto dvě aplikace nasazují dohromady.</span><span class="sxs-lookup"><span data-stu-id="6d511-192">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="6d511-193">Vyžaduje se webový server, který podporuje hostování aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6d511-193">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="6d511-194">V případě hostovaného nasazení Visual Studio zahrnuje šablonu projektu **aplikace WebAssembly Blazor** (`blazorwasm` šablona při použití příkazu [dotnet New](/dotnet/core/tools/dotnet-new) ) se zvolenou možností **Hosted** .</span><span class="sxs-lookup"><span data-stu-id="6d511-194">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected.</span></span>

<span data-ttu-id="6d511-195">Další informace o ASP.NET Core hostování a nasazení aplikací najdete v tématu <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="6d511-195">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6d511-196">Informace o nasazení do Azure App Service najdete v tématu <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="6d511-196">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="6d511-197">Samostatné nasazení</span><span class="sxs-lookup"><span data-stu-id="6d511-197">Standalone deployment</span></span>

<span data-ttu-id="6d511-198">*Samostatné nasazení* obsluhuje aplikaci Blazor na straně klienta jako sadu statických souborů, které jsou požadovány přímo klienty.</span><span class="sxs-lookup"><span data-stu-id="6d511-198">A *standalone deployment* serves the Blazor client-side app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="6d511-199">Libovolný statický souborový server může obsluhovat aplikaci Blazor.</span><span class="sxs-lookup"><span data-stu-id="6d511-199">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="6d511-200">Samostatné prostředky nasazení se publikují do složky *bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .</span><span class="sxs-lookup"><span data-stu-id="6d511-200">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="6d511-201">IIS</span><span class="sxs-lookup"><span data-stu-id="6d511-201">IIS</span></span>

<span data-ttu-id="6d511-202">Služba IIS je schopným statickým souborovým serverem pro aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="6d511-202">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="6d511-203">Chcete-li nakonfigurovat službu IIS na hostování Blazor, přečtěte si téma [vytvoření statického webu ve službě IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="6d511-203">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="6d511-204">Publikované assety se vytvoří ve složce */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="6d511-204">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="6d511-205">Hostovat obsah složky pro *publikování* na webovém serveru nebo v hostitelské službě.</span><span class="sxs-lookup"><span data-stu-id="6d511-205">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="6d511-206">web.config</span><span class="sxs-lookup"><span data-stu-id="6d511-206">web.config</span></span>

<span data-ttu-id="6d511-207">Při publikování projektu Blazor se vytvoří soubor *Web. config* s následující konfigurací služby IIS:</span><span class="sxs-lookup"><span data-stu-id="6d511-207">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="6d511-208">Typy MIME jsou nastaveny pro následující přípony souborů:</span><span class="sxs-lookup"><span data-stu-id="6d511-208">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="6d511-209">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="6d511-209">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="6d511-210">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="6d511-210">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="6d511-211">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="6d511-211">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="6d511-212">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6d511-212">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="6d511-213">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6d511-213">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="6d511-214">Pro následující typy MIME je povolena komprese protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="6d511-214">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="6d511-215">Pravidla pro přepis adres URL jsou navázána:</span><span class="sxs-lookup"><span data-stu-id="6d511-215">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="6d511-216">Slouží jako podadresáře, kde se nachází statické prostředky aplikace ( *{název sestavení}/DIST/{Path požádal}* ).</span><span class="sxs-lookup"><span data-stu-id="6d511-216">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="6d511-217">Vytvořte záložní řešení zabezpečeného hesla, aby se požadavky na nesouborové prostředky přesměrovaly do výchozího dokumentu aplikace ve složce statických prostředků ( *{název sestavení}/DIST/index.html*).</span><span class="sxs-lookup"><span data-stu-id="6d511-217">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="6d511-218">Instalace modulu URL pro přepis</span><span class="sxs-lookup"><span data-stu-id="6d511-218">Install the URL Rewrite Module</span></span>

<span data-ttu-id="6d511-219">Pro přepis adres URL je vyžadován [modul URL Rewrite](https://www.iis.net/downloads/microsoft/url-rewrite) .</span><span class="sxs-lookup"><span data-stu-id="6d511-219">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="6d511-220">Modul není nainstalován ve výchozím nastavení a není k dispozici pro instalaci jako funkci služby role Webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="6d511-220">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="6d511-221">Modul se musí stáhnout z webu IIS.</span><span class="sxs-lookup"><span data-stu-id="6d511-221">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="6d511-222">K instalaci modulu použijte instalační program webové platformy:</span><span class="sxs-lookup"><span data-stu-id="6d511-222">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="6d511-223">Místně přejděte na [stránku ke stažení modulu pro přepsání adresy URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="6d511-223">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="6d511-224">V případě anglické verze vyberte **WebPI** a Stáhněte si instalační program WebPI.</span><span class="sxs-lookup"><span data-stu-id="6d511-224">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="6d511-225">Pro jiné jazyky vyberte příslušnou architekturu pro server (x86/x64) a stáhněte instalační program.</span><span class="sxs-lookup"><span data-stu-id="6d511-225">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="6d511-226">Zkopírujte instalační program na server.</span><span class="sxs-lookup"><span data-stu-id="6d511-226">Copy the installer to the server.</span></span> <span data-ttu-id="6d511-227">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="6d511-227">Run the installer.</span></span> <span data-ttu-id="6d511-228">Vyberte tlačítko **nainstalovat** a přijměte licenční podmínky.</span><span class="sxs-lookup"><span data-stu-id="6d511-228">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="6d511-229">Po dokončení instalace není restartování serveru vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="6d511-229">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="6d511-230">Konfigurace webu</span><span class="sxs-lookup"><span data-stu-id="6d511-230">Configure the website</span></span>

<span data-ttu-id="6d511-231">Nastavte **fyzickou cestu** webu na složku aplikace.</span><span class="sxs-lookup"><span data-stu-id="6d511-231">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="6d511-232">Složka obsahuje:</span><span class="sxs-lookup"><span data-stu-id="6d511-232">The folder contains:</span></span>

* <span data-ttu-id="6d511-233">Soubor *Web. config* , který služba IIS používá ke konfiguraci webu, včetně požadovaných pravidel přesměrování a typů obsahu souborů.</span><span class="sxs-lookup"><span data-stu-id="6d511-233">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="6d511-234">Složka statických prostředků aplikace</span><span class="sxs-lookup"><span data-stu-id="6d511-234">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="6d511-235">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="6d511-235">Troubleshooting</span></span>

<span data-ttu-id="6d511-236">Pokud dojde k *chybě 500 – interní chyba serveru* a správce služby IIS vyvolá chyby při pokusu o přístup ke konfiguraci webu, potvrďte, že je nainstalován modul URL pro přepis.</span><span class="sxs-lookup"><span data-stu-id="6d511-236">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="6d511-237">Pokud modul není nainstalován, soubor *Web. config* nelze analyzovat službou IIS.</span><span class="sxs-lookup"><span data-stu-id="6d511-237">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="6d511-238">Tím se zabrání tomu, aby správce služby IIS načetl konfiguraci webu a web neobsluhuje statické soubory Blazor.</span><span class="sxs-lookup"><span data-stu-id="6d511-238">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="6d511-239">Další informace o řešení potíží s nasazeními služby IIS najdete <xref:test/troubleshoot-azure-iis>v tématu.</span><span class="sxs-lookup"><span data-stu-id="6d511-239">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="6d511-240">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="6d511-240">Azure Storage</span></span>

<span data-ttu-id="6d511-241">Hostování statického souboru Azure Storage umožňuje hostování aplikace bez serveru.</span><span class="sxs-lookup"><span data-stu-id="6d511-241">Azure Storage static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="6d511-242">Podporují se názvy vlastních domén, Azure Content Delivery Network (CDN) a HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6d511-242">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="6d511-243">Když je u služby BLOB Service povolené hostování statických webů v účtu úložiště:</span><span class="sxs-lookup"><span data-stu-id="6d511-243">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="6d511-244">Nastavte **název dokumentu indexu** na `index.html`.</span><span class="sxs-lookup"><span data-stu-id="6d511-244">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="6d511-245">Nastavte **cestu k chybovému dokumentu** na. `index.html`</span><span class="sxs-lookup"><span data-stu-id="6d511-245">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="6d511-246">Komponenty Razor a jiné nesouborové koncové body se neukládají na fyzických cestách se statickým obsahem uloženým ve službě BLOB Service.</span><span class="sxs-lookup"><span data-stu-id="6d511-246">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="6d511-247">Když se obdrží požadavek na jeden z těchto prostředků, který by měl směrovač Blazor zpracovat, Chyba *404 –* chyba, kterou vygenerovala služba BLOB Service, směruje požadavek na **cestu k chybovému dokumentu**.</span><span class="sxs-lookup"><span data-stu-id="6d511-247">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="6d511-248">Vrátí se objekt BLOB *index. html* a Blazor směrovač načte a zpracuje cestu.</span><span class="sxs-lookup"><span data-stu-id="6d511-248">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="6d511-249">Další informace najdete v tématu [statické hostování webů v Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="6d511-249">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="6d511-250">Nginx</span><span class="sxs-lookup"><span data-stu-id="6d511-250">Nginx</span></span>

<span data-ttu-id="6d511-251">Následující soubor *Nginx. conf* je zjednodušený a ukazuje, jak nakonfigurovat Nginx pro odeslání souboru *index. html* pokaždé, když nemůže najít odpovídající soubor na disku.</span><span class="sxs-lookup"><span data-stu-id="6d511-251">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="6d511-252">Další informace o konfiguraci webového serveru Nginx v produkčním prostředí najdete v tématu [vytváření konfiguračních souborů Nginx plus a Nginx](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="6d511-252">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="6d511-253">Nginx v Docker</span><span class="sxs-lookup"><span data-stu-id="6d511-253">Nginx in Docker</span></span>

<span data-ttu-id="6d511-254">Pokud chcete hostovat Blazor v Docker pomocí Nginx, nastavte souboru Dockerfile pro použití image Nginx založené na Alpine.</span><span class="sxs-lookup"><span data-stu-id="6d511-254">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="6d511-255">Aktualizujte souboru Dockerfile a zkopírujte soubor *Nginx. config* do kontejneru.</span><span class="sxs-lookup"><span data-stu-id="6d511-255">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="6d511-256">Do souboru Dockerfile přidejte jeden řádek, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="6d511-256">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="6d511-257">Stránky GitHubu</span><span class="sxs-lookup"><span data-stu-id="6d511-257">GitHub Pages</span></span>

<span data-ttu-id="6d511-258">Chcete-li zpracovat přepisy adresy URL, přidejte soubor *404. html* pomocí skriptu, který zpracovává přesměrování požadavku na stránku *index. html* .</span><span class="sxs-lookup"><span data-stu-id="6d511-258">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="6d511-259">Ukázkovou implementaci poskytovanou komunitou najdete v tématu [jednostránkové aplikace pro stránky GitHubu](https://spa-github-pages.rafrex.com/) ([rafrex/Spa – GitHub-Pages na GitHubu](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="6d511-259">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="6d511-260">Příklad použití přístupu komunity se dá zobrazit v [blazor-demo/blazor-demo. GitHub. IO na GitHubu](https://github.com/blazor-demo/blazor-demo.github.io) ([živý web](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="6d511-260">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="6d511-261">Při použití webu projektu místo webu organizace přidejte nebo aktualizujte `<base>` značku v souboru *index. html*.</span><span class="sxs-lookup"><span data-stu-id="6d511-261">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="6d511-262">Nastavte hodnotu `my-repository/`atributu na název úložiště GitHub s koncovým lomítkem (například. `href`</span><span class="sxs-lookup"><span data-stu-id="6d511-262">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
