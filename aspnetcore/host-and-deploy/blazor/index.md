---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434262"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="d2eea-103">Hostování a nasazení ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d2eea-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="d2eea-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d2eea-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="d2eea-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="d2eea-105">Publish the app</span></span>

<span data-ttu-id="d2eea-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="d2eea-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2eea-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2eea-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d2eea-108">V navigačním panelu vyberte **Build** > **publikovat aplikaci {Application}** .</span><span class="sxs-lookup"><span data-stu-id="d2eea-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="d2eea-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="d2eea-109">Select the *publish target*.</span></span> <span data-ttu-id="d2eea-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="d2eea-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="d2eea-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="d2eea-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="d2eea-112">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="d2eea-112">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d2eea-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2eea-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d2eea-114">K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="d2eea-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="d2eea-115">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="d2eea-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="d2eea-116">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="d2eea-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="d2eea-117">Umístění pro publikování:</span><span class="sxs-lookup"><span data-stu-id="d2eea-117">Publish locations:</span></span>

* Blazor<span data-ttu-id="d2eea-118"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d2eea-118"> WebAssembly</span></span>
  * <span data-ttu-id="d2eea-119">Samostatná &ndash; aplikace je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="d2eea-119">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="d2eea-120">Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah složky *wwwroot* na hostitele statických webů.</span><span class="sxs-lookup"><span data-stu-id="d2eea-120">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="d2eea-121">Hostovaná &ndash; aplikace klientské Blazor WebAssembly je publikovaná do složky */bin/Release/{Target Framework}/Publish/wwwroot* aplikace v serverové aplikaci společně s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-121">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="d2eea-122">Nasaďte obsah složky pro *publikování* na hostitele.</span><span class="sxs-lookup"><span data-stu-id="d2eea-122">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="d2eea-123"> Server &ndash; aplikace se publikuje do složky */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="d2eea-123"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="d2eea-124">Nasaďte obsah složky pro *publikování* na hostitele.</span><span class="sxs-lookup"><span data-stu-id="d2eea-124">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="d2eea-125">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="d2eea-125">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="d2eea-126">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="d2eea-126">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="d2eea-127">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="d2eea-127">App base path</span></span>

<span data-ttu-id="d2eea-128">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-128">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="d2eea-129">Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d2eea-129">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="d2eea-130">Aplikace ASP.NET Core má název `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="d2eea-130">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="d2eea-131">Aplikace se fyzicky nachází v *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="d2eea-131">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="d2eea-132">Žádosti jsou přijímány na `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="d2eea-132">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="d2eea-133">Blazor aplikace s názvem `CoolApp` je dílčí aplikací `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="d2eea-133">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="d2eea-134">Dílčí aplikace je fyzicky umístěná v *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="d2eea-134">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="d2eea-135">Žádosti jsou přijímány na `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="d2eea-135">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="d2eea-136">Bez zadání další konfigurace pro `CoolApp`nemá dílčí aplikace v tomto scénáři žádné znalosti o tom, kde se nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="d2eea-136">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="d2eea-137">Aplikace například nemůže vytvořit správné relativní adresy URL ke svým prostředkům bez vědomí, že se nachází na relativní cestě URL `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="d2eea-137">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="d2eea-138">Chcete-li zadat konfiguraci pro základní cestu aplikace Blazor `https://www.contoso.com/CoolApp/`, je atribut `href` značky `<base>` nastaven na relativní kořenovou cestu v souboru *Pages/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="d2eea-138">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="d2eea-139"> serverové aplikace navíc nastaví základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti o aplikaci `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d2eea-139"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="d2eea-140">Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-140">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="d2eea-141">Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-141">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="d2eea-142">Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci umístění identifikátoru URI základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-142">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="d2eea-143">Směrovač Blazor zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="d2eea-143">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="d2eea-144">V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-144">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="d2eea-145">V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-145">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="d2eea-146">V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-146">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="d2eea-147">Chcete-li nastavit základní cestu aplikace, aktualizujte značku `<base>` v rámci `<head>` prvků značek souboru *Page/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="d2eea-147">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="d2eea-148">Nastavte hodnotu atributu `href` na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2eea-148">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="d2eea-149">Pro Blazor aplikaci WebAssembly s nekořenovou cestou URL jiného typu než root (například `<base href="/CoolApp/">`) se aplikace nepovede najít své prostředky *při místním spuštění*.</span><span class="sxs-lookup"><span data-stu-id="d2eea-149">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="d2eea-150">Chcete-li vyřešit tento problém během místního vývoje a testování, můžete dodat *základní argument Path* , který odpovídá hodnotě `href` `<base>` značce za běhu.</span><span class="sxs-lookup"><span data-stu-id="d2eea-150">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="d2eea-151">Nezahrnovat koncové lomítko.</span><span class="sxs-lookup"><span data-stu-id="d2eea-151">Don't include a trailing slash.</span></span> <span data-ttu-id="d2eea-152">Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte příkaz `dotnet run` z adresáře aplikace s možností `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="d2eea-152">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="d2eea-153">Pro Blazor aplikaci WebAssembly s relativní cestou URL `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="d2eea-153">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="d2eea-154">Aplikace Blazor WebAssembly reaguje místně na `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="d2eea-154">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="d2eea-155">Nasazení</span><span class="sxs-lookup"><span data-stu-id="d2eea-155">Deployment</span></span>

<span data-ttu-id="d2eea-156">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="d2eea-156">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
