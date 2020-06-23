---
title: ASP.NET Core hostitele a nasazeníBlazor
author: guardrex
description: Objevte, jak hostovat a nasazovat Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 0cd21e6b4930fb6112aa448a8a44be80cc8fbf61
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243561"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="b62b1-103">ASP.NET Core hostitele a nasazeníBlazor</span><span class="sxs-lookup"><span data-stu-id="b62b1-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="b62b1-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b62b1-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="b62b1-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="b62b1-105">Publish the app</span></span>

<span data-ttu-id="b62b1-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="b62b1-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b62b1-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b62b1-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b62b1-108">V navigačním panelu vyberte **sestavení**  >  **publikovat {aplikace}** .</span><span class="sxs-lookup"><span data-stu-id="b62b1-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="b62b1-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="b62b1-109">Select the *publish target*.</span></span> <span data-ttu-id="b62b1-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="b62b1-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="b62b1-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="b62b1-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="b62b1-112">Vyberte **`Publish`** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="b62b1-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b62b1-113">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b62b1-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b62b1-114">Vyberte **sestavení**  >  **publikovat do složky**.</span><span class="sxs-lookup"><span data-stu-id="b62b1-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="b62b1-115">Potvrďte, že složka obdrží publikované prostředky a vyberte **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="b62b1-115">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b62b1-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b62b1-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b62b1-117">[`dotnet publish`](/dotnet/core/tools/dotnet-publish)K publikování aplikace s konfigurací vydané verze použijte příkaz:</span><span class="sxs-lookup"><span data-stu-id="b62b1-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="b62b1-118">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="b62b1-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b62b1-119">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="b62b1-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="b62b1-120">Umístění pro publikování:</span><span class="sxs-lookup"><span data-stu-id="b62b1-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="b62b1-121">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b62b1-121"> WebAssembly</span></span>
  * <span data-ttu-id="b62b1-122">Samostatná: aplikace se publikuje do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="b62b1-122">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="b62b1-123">Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah `wwwroot` složky na hostitele statických webů.</span><span class="sxs-lookup"><span data-stu-id="b62b1-123">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="b62b1-124">Hosted: klientská Blazor aplikace WebAssembly je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-124">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="b62b1-125">Nasaďte obsah `publish` složky na hostitele.</span><span class="sxs-lookup"><span data-stu-id="b62b1-125">Deploy the contents of the `publish` folder to the host.</span></span>
* Blazor<span data-ttu-id="b62b1-126">Server: aplikace je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish` složky.</span><span class="sxs-lookup"><span data-stu-id="b62b1-126"> Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="b62b1-127">Nasaďte obsah `publish` složky na hostitele.</span><span class="sxs-lookup"><span data-stu-id="b62b1-127">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="b62b1-128">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="b62b1-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="b62b1-129">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="b62b1-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="b62b1-130">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="b62b1-130">App base path</span></span>

<span data-ttu-id="b62b1-131">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="b62b1-132">Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikace:</span><span class="sxs-lookup"><span data-stu-id="b62b1-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="b62b1-133">Aplikace ASP.NET Core má název `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="b62b1-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="b62b1-134">Aplikace se fyzicky nachází na adrese `d:/MyApp` .</span><span class="sxs-lookup"><span data-stu-id="b62b1-134">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="b62b1-135">Žádosti jsou přijímány na adrese `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="b62b1-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="b62b1-136">BlazorAplikace s názvem `CoolApp` je dílčí aplikace `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="b62b1-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="b62b1-137">Dílčí aplikace se fyzicky nachází v `d:/MyApp/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="b62b1-137">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="b62b1-138">Žádosti jsou přijímány na adrese `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="b62b1-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="b62b1-139">Bez zadání další konfigurace pro nemůže `CoolApp` podaplikace v tomto scénáři znát, kde se nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="b62b1-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="b62b1-140">Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě URL `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="b62b1-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="b62b1-141">Chcete-li zadat konfiguraci pro Blazor základní cestu aplikace `https://www.contoso.com/CoolApp/` , `<base>` `href` je atribut značky nastaven na relativní kořenovou cestu v `Pages/_Host.cshtml` souboru ( Blazor serveru) nebo `wwwroot/index.html` souboru ( Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="b62b1-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="b62b1-142">Serverové aplikace navíc nastavují základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti aplikace `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b62b1-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="b62b1-143">Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="b62b1-144">Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="b62b1-145">Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci prostoru identifikátoru URI základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="b62b1-146">BlazorSměrovač zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="b62b1-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="b62b1-147">V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="b62b1-148">V těchto případech je základní cestou k relativní adrese URL aplikace lomítko ( `<base href="/" />` ), což je výchozí konfigurace Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="b62b1-149">V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="b62b1-150">Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku v rámci `<head>` prvků značek `Pages/_Host.cshtml` souboru ( Blazor serveru) nebo `wwwroot/index.html` souboru ( Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="b62b1-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="b62b1-151">Nastavte `href` hodnotu atributu na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="b62b1-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="b62b1-152">V případě Blazor aplikace WebAssembly s nekořenovou cestou URL, která není kořenovým adresářem (například `<base href="/CoolApp/">` ), nemůže aplikace najít své prostředky *při místním spuštění*.</span><span class="sxs-lookup"><span data-stu-id="b62b1-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="b62b1-153">Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu.</span><span class="sxs-lookup"><span data-stu-id="b62b1-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="b62b1-154">Nezahrnovat koncové lomítko.</span><span class="sxs-lookup"><span data-stu-id="b62b1-154">Don't include a trailing slash.</span></span> <span data-ttu-id="b62b1-155">Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace s `--pathbase` možností:</span><span class="sxs-lookup"><span data-stu-id="b62b1-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="b62b1-156">Pro Blazor aplikaci typu WebAssembly s relativní cestou URL `/CoolApp/` ( `<base href="/CoolApp/">` ) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="b62b1-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="b62b1-157">BlazorAplikace WebAssembly reaguje místně na `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="b62b1-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="b62b1-158">Nasazení</span><span class="sxs-lookup"><span data-stu-id="b62b1-158">Deployment</span></span>

<span data-ttu-id="b62b1-159">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b62b1-159">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
