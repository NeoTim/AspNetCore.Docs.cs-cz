---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 1cfe87c7194b34c2461429225c560f9e689168ae
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211698"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="f7a30-103">Hostování a nasazení ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f7a30-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="f7a30-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f7a30-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="f7a30-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="f7a30-105">Publish the app</span></span>

<span data-ttu-id="f7a30-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="f7a30-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f7a30-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7a30-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7a30-108">V navigačním panelu vyberte **sestavení** > **publikovat {aplikace}** .</span><span class="sxs-lookup"><span data-stu-id="f7a30-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="f7a30-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="f7a30-109">Select the *publish target*.</span></span> <span data-ttu-id="f7a30-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="f7a30-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="f7a30-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="f7a30-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="f7a30-112">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="f7a30-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f7a30-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7a30-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f7a30-114">K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="f7a30-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="f7a30-115">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="f7a30-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="f7a30-116">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="f7a30-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="f7a30-117">Aplikace Blazor WebAssembly je publikovaná ve složce */bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .</span><span class="sxs-lookup"><span data-stu-id="f7a30-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="f7a30-118">Aplikace Blazor serveru je publikovaná do složky */Publish/bin/Release/{Target Framework}* .</span><span class="sxs-lookup"><span data-stu-id="f7a30-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="f7a30-119">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="f7a30-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="f7a30-120">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="f7a30-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="f7a30-121">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="f7a30-121">App base path</span></span>

<span data-ttu-id="f7a30-122">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7a30-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="f7a30-123">Vezměte v úvahu následující hlavní aplikace a aplikace Blazor:</span><span class="sxs-lookup"><span data-stu-id="f7a30-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="f7a30-124">Je volána `MyApp`hlavní aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7a30-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="f7a30-125">Aplikace se fyzicky nachází v *\\d: MyApp*.</span><span class="sxs-lookup"><span data-stu-id="f7a30-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="f7a30-126">Žádosti jsou přijímány `https://www.contoso.com/{MYAPP RESOURCE}`na adrese.</span><span class="sxs-lookup"><span data-stu-id="f7a30-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="f7a30-127">Volaná `CoolApp` aplikace Blazor je dílčí `MyApp`aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7a30-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="f7a30-128">Dílčí aplikace je fyzicky umístěná v *d:\\MyApp\\CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="f7a30-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="f7a30-129">Žádosti jsou přijímány `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`na adrese.</span><span class="sxs-lookup"><span data-stu-id="f7a30-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="f7a30-130">Bez zadání další konfigurace pro `CoolApp`nemůže podaplikace v tomto scénáři znát, kde se nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="f7a30-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="f7a30-131">Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě `/CoolApp/`URL.</span><span class="sxs-lookup"><span data-stu-id="f7a30-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="f7a30-132">Chcete-li zadat konfiguraci `https://www.contoso.com/CoolApp/`pro základní cestu aplikace Blazor `<base>` , je `href` atribut značky nastaven na relativní kořenovou cestu v souboru *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="f7a30-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="f7a30-133">Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7a30-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="f7a30-134">Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7a30-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="f7a30-135">Základní cesta aplikace se také používá k zachycení hypertextového odkazu, kde `href` cíl odkazu je v rámci základní cesty k umístění&mdash;identifikátoru URI aplikace, Blazor směrovač zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="f7a30-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="f7a30-136">V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7a30-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="f7a30-137">V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="f7a30-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="f7a30-138">V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f7a30-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="f7a30-139">Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku `<head>` v rámci prvků značek souboru *wwwroot/index.html* .</span><span class="sxs-lookup"><span data-stu-id="f7a30-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="f7a30-140">Nastavte hodnotu `/{RELATIVE URL PATH}/`atributuna (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace. `href`</span><span class="sxs-lookup"><span data-stu-id="f7a30-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="f7a30-141">Pro aplikaci, která má nekořenovou cestu relativní adresy URL (například `<base href="/CoolApp/">`), aplikace *při místním spuštění*nenalezne své prostředky.</span><span class="sxs-lookup"><span data-stu-id="f7a30-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="f7a30-142">Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu.</span><span class="sxs-lookup"><span data-stu-id="f7a30-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="f7a30-143">Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace `--pathbase` s možností:</span><span class="sxs-lookup"><span data-stu-id="f7a30-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="f7a30-144">Pro aplikaci s relativní cestou `/CoolApp/` URL (`<base href="/CoolApp/">`) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="f7a30-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="f7a30-145">Aplikace odpoví místně na adrese `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="f7a30-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="f7a30-146">Nasazení</span><span class="sxs-lookup"><span data-stu-id="f7a30-146">Deployment</span></span>

<span data-ttu-id="f7a30-147">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f7a30-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
