---
title: Hostování a nasazení ASP.NET coreBlazor
author: guardrex
description: Zjistěte, jak Blazor hostovat a nasazovat aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434262"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="b6a89-103">Hostování a nasazení ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b6a89-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="b6a89-104">[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b6a89-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="b6a89-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="b6a89-105">Publish the app</span></span>

<span data-ttu-id="b6a89-106">Aplikace se publikují pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="b6a89-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b6a89-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b6a89-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b6a89-108">Na navigačním panelu vyberte **Publikovat** > **{APPLICATION}.**</span><span class="sxs-lookup"><span data-stu-id="b6a89-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="b6a89-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="b6a89-109">Select the *publish target*.</span></span> <span data-ttu-id="b6a89-110">Chcete-li publikovat místně, vyberte **složku**.</span><span class="sxs-lookup"><span data-stu-id="b6a89-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="b6a89-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="b6a89-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="b6a89-112">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="b6a89-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b6a89-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b6a89-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b6a89-114">Pomocí příkazu [dotnet publish](/dotnet/core/tools/dotnet-publish) publikujte aplikaci s konfigurací vydání:</span><span class="sxs-lookup"><span data-stu-id="b6a89-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="b6a89-115">Publikování aplikace aktivuje [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a [vytvoří](/dotnet/core/tools/dotnet-build) projekt před vytvořením prostředků pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="b6a89-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b6a89-116">V rámci procesu sestavení jsou odebrány nepoužívané metody a sestavení, aby se zmenšila velikost stahování aplikací a doba načítání.</span><span class="sxs-lookup"><span data-stu-id="b6a89-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="b6a89-117">Umístění publikování:</span><span class="sxs-lookup"><span data-stu-id="b6a89-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="b6a89-118">Webová sestava</span><span class="sxs-lookup"><span data-stu-id="b6a89-118"> WebAssembly</span></span>
  * <span data-ttu-id="b6a89-119">Samostatný &ndash; Aplikace je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="b6a89-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="b6a89-120">Chcete-li aplikaci nasadit jako statický web, zkopírujte obsah složky *wwwroot* do statického hostitele webu.</span><span class="sxs-lookup"><span data-stu-id="b6a89-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="b6a89-121">Hosted: &ndash; Blazor Klientská webová sestava aplikace je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="b6a89-122">Nasaďte obsah složky *publikování* do hostitele.</span><span class="sxs-lookup"><span data-stu-id="b6a89-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="b6a89-123">Server &ndash; Aplikace je publikována do složky */bin/Release/{TARGET FRAMEWORK}/publish.*</span><span class="sxs-lookup"><span data-stu-id="b6a89-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="b6a89-124">Nasaďte obsah složky *publikování* do hostitele.</span><span class="sxs-lookup"><span data-stu-id="b6a89-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="b6a89-125">Datové zdroje ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="b6a89-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="b6a89-126">Nasazení může být ruční nebo automatizovaný proces v závislosti na vývojových nástrojích, které jsou používány.</span><span class="sxs-lookup"><span data-stu-id="b6a89-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="b6a89-127">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="b6a89-127">App base path</span></span>

<span data-ttu-id="b6a89-128">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="b6a89-129">Zvažte následující ASP.NET Blazor základní aplikace a podaplikace:</span><span class="sxs-lookup"><span data-stu-id="b6a89-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="b6a89-130">Aplikace ASP.NET Core `MyApp`se jmenuje :</span><span class="sxs-lookup"><span data-stu-id="b6a89-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="b6a89-131">Aplikace fyzicky sídlí na *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="b6a89-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="b6a89-132">Žádosti jsou přijímány na adrese `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="b6a89-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="b6a89-133">Aplikace Blazor s `CoolApp` názvem je podaplikace `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="b6a89-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="b6a89-134">Sub-app fyzicky sídlí na *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="b6a89-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="b6a89-135">Žádosti jsou přijímány na adrese `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="b6a89-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="b6a89-136">Bez zadání další `CoolApp`konfigurace pro , dílčí aplikace v tomto scénáři nemá žádné znalosti o umístění na serveru.</span><span class="sxs-lookup"><span data-stu-id="b6a89-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="b6a89-137">Aplikace například nemůže vytvořit správné relativní adresy URL pro své prostředky, aniž by `/CoolApp/`věděla, že se nachází na cestě k relativní adrese URL .</span><span class="sxs-lookup"><span data-stu-id="b6a89-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="b6a89-138">Chcete-li poskytnout Blazor konfiguraci pro `https://www.contoso.com/CoolApp/`základní `<base>` cestu aplikace `href` , atribut značky je nastaven na relativní kořenovouBlazor cestu v souboru *Pages/_Host.cshtml* ( Server) nebo *wwwroot/index.html* souboru (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="b6a89-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="b6a89-139">Serverové aplikace navíc nastavují základní <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> cestu na straně serveru `Startup.Configure`voláním v kanálu požadavků aplikace :</span><span class="sxs-lookup"><span data-stu-id="b6a89-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="b6a89-140">Poskytnutím relativní cesty URL může komponenta, která není v kořenovém adresáři, vytvářet adresy URL vzhledem ke kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="b6a89-141">Součásti na různých úrovních struktury adresáře můžete vytvářet odkazy na jiné prostředky v umístěních v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b6a89-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="b6a89-142">Základní cesta aplikace se také používá k `href` zachycení vybraných hypertextových odkazů, kde cíl propojení je v prostoru URI základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="b6a89-143">Směrovač Blazor zpracovává vnitřní navigaci.</span><span class="sxs-lookup"><span data-stu-id="b6a89-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="b6a89-144">V mnoha scénářích hostování je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="b6a89-145">V těchto případech je relativní základní cesta url aplikace`<base href="/" />`lomítko ( ), Blazor což je výchozí konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="b6a89-146">V jiných scénářích hostování, jako jsou stránky GitHub a dílčí aplikace IIS, musí být základní cesta aplikace nastavena na relativní cestu url aplikace na serveru.</span><span class="sxs-lookup"><span data-stu-id="b6a89-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="b6a89-147">Chcete-li nastavit základní cestu aplikace, `<base>` aktualizujte značku v elementech `<head>` tagu souboru *Pages/_Host.cshtml* (Server)Blazor nebo *wwwroot/index.html* souboru (Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="b6a89-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="b6a89-148">Nastavte `href` hodnotu `/{RELATIVE URL PATH}/` atributu na (je vyžadováno koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6a89-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="b6a89-149">U Blazor aplikace WebAssembly s nekořenovou relativní adresou `<base href="/CoolApp/">`URL (například ) se aplikaci nedaří najít své prostředky *při místním spuštění*.</span><span class="sxs-lookup"><span data-stu-id="b6a89-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="b6a89-150">Chcete-li tento problém překonat během místního vývoje a testování, můžete `<base>` zadat základní argument *cesty,* který odpovídá `href` hodnotě značky za běhu.</span><span class="sxs-lookup"><span data-stu-id="b6a89-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="b6a89-151">Nezahrnejte koncové lomítko.</span><span class="sxs-lookup"><span data-stu-id="b6a89-151">Don't include a trailing slash.</span></span> <span data-ttu-id="b6a89-152">Chcete-li předat argument základní cesty při místním `dotnet run` spuštění aplikace, spusťte `--pathbase` příkaz z adresáře aplikace s možností:</span><span class="sxs-lookup"><span data-stu-id="b6a89-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="b6a89-153">Pro Blazor aplikaci WebAssembly s relativní `/CoolApp/` `<base href="/CoolApp/">`adresou URL ( ) je příkaz:</span><span class="sxs-lookup"><span data-stu-id="b6a89-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="b6a89-154">Aplikace Blazor WebAssembly odpovídá místně `http://localhost:port/CoolApp`na adrese .</span><span class="sxs-lookup"><span data-stu-id="b6a89-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="b6a89-155">Nasazení</span><span class="sxs-lookup"><span data-stu-id="b6a89-155">Deployment</span></span>

<span data-ttu-id="b6a89-156">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b6a89-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
