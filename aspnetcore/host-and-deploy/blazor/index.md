---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5c37c3d9f424f4c4b814e1955880623fd95179f2
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550378"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor"></a><span data-ttu-id="eabfa-103">Hostování a nasazení ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="eabfa-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="eabfa-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="eabfa-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="eabfa-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="eabfa-105">Publish the app</span></span>

<span data-ttu-id="eabfa-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="eabfa-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eabfa-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eabfa-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eabfa-108">V navigačním panelu vyberte **Build** > **publikovat aplikaci {Application}** .</span><span class="sxs-lookup"><span data-stu-id="eabfa-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="eabfa-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="eabfa-109">Select the *publish target*.</span></span> <span data-ttu-id="eabfa-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="eabfa-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="eabfa-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="eabfa-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="eabfa-112">Vyberte tlačítko **publikovat** .</span><span class="sxs-lookup"><span data-stu-id="eabfa-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eabfa-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="eabfa-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eabfa-114">K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="eabfa-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="eabfa-115">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="eabfa-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="eabfa-116">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="eabfa-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="eabfa-117">Blazor aplikace WebAssembly je publikovaná do složky */DIST/PUBLISH/{Assembly Framework} Name}* .</span><span class="sxs-lookup"><span data-stu-id="eabfa-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="eabfa-118">Aplikace Blazor serveru je publikovaná ve složce */bin/Release/{Target Framework}/Publish* .</span><span class="sxs-lookup"><span data-stu-id="eabfa-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="eabfa-119">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="eabfa-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="eabfa-120">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="eabfa-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="eabfa-121">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="eabfa-121">App base path</span></span>

<span data-ttu-id="eabfa-122">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="eabfa-123">Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="eabfa-123">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="eabfa-124">Aplikace ASP.NET Core má název `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="eabfa-124">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="eabfa-125">Aplikace se fyzicky nachází v *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="eabfa-125">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="eabfa-126">Žádosti jsou přijímány na `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="eabfa-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="eabfa-127">Blazor aplikace s názvem `CoolApp` je dílčí aplikací `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="eabfa-127">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="eabfa-128">Dílčí aplikace je fyzicky umístěná v *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="eabfa-128">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="eabfa-129">Žádosti jsou přijímány na `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="eabfa-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="eabfa-130">Bez zadání další konfigurace pro `CoolApp`nemá dílčí aplikace v tomto scénáři žádné znalosti o tom, kde se nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="eabfa-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="eabfa-131">Aplikace například nemůže vytvořit správné relativní adresy URL ke svým prostředkům bez vědomí, že se nachází na relativní cestě URL `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="eabfa-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="eabfa-132">Chcete-li zadat konfiguraci pro základní cestu aplikace Blazor `https://www.contoso.com/CoolApp/`, je atribut `href` značky `<base>` nastaven na relativní kořenovou cestu v souboru *Pages/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="eabfa-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="eabfa-133"> serverové aplikace navíc nastaví základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti o aplikaci `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="eabfa-133"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="eabfa-134">Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-134">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="eabfa-135">Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-135">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="eabfa-136">Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci umístění identifikátoru URI základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-136">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="eabfa-137">Směrovač Blazor zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="eabfa-137">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="eabfa-138">V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-138">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="eabfa-139">V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-139">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="eabfa-140">V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-140">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="eabfa-141">Chcete-li nastavit základní cestu aplikace, aktualizujte značku `<base>` v rámci `<head>` prvků značek souboru *Page/_Host. cshtml* (Blazor Server) nebo souboru *wwwroot/index.html* (Blazor WebAssembly).</span><span class="sxs-lookup"><span data-stu-id="eabfa-141">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="eabfa-142">Nastavte hodnotu atributu `href` na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="eabfa-142">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="eabfa-143">Pro Blazor aplikaci WebAssembly s nekořenovou cestou URL jiného typu než root (například `<base href="/CoolApp/">`) se aplikace nepovede najít své prostředky *při místním spuštění*.</span><span class="sxs-lookup"><span data-stu-id="eabfa-143">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="eabfa-144">Chcete-li vyřešit tento problém během místního vývoje a testování, můžete dodat *základní argument Path* , který odpovídá hodnotě `href` `<base>` značce za běhu.</span><span class="sxs-lookup"><span data-stu-id="eabfa-144">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="eabfa-145">Nezahrnovat koncové lomítko.</span><span class="sxs-lookup"><span data-stu-id="eabfa-145">Don't include a trailing slash.</span></span> <span data-ttu-id="eabfa-146">Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte příkaz `dotnet run` z adresáře aplikace s možností `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="eabfa-146">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="eabfa-147">Pro Blazor aplikaci WebAssembly s relativní cestou URL `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="eabfa-147">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="eabfa-148">Aplikace Blazor WebAssembly reaguje místně na `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="eabfa-148">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="eabfa-149">Nasazení</span><span class="sxs-lookup"><span data-stu-id="eabfa-149">Deployment</span></span>

<span data-ttu-id="eabfa-150">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="eabfa-150">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
