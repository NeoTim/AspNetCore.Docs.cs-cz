---
title: Hostování a nasazení ASP.NET Core Blazor
author: guardrex
description: Objevte, jak hostovat a nasazovat aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 271135a0ebe67d31fd8e2bcf672e723814727147
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391343"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="e7327-103">Hostování a nasazení ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e7327-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="e7327-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e7327-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="e7327-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="e7327-105">Publish the app</span></span>

<span data-ttu-id="e7327-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="e7327-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7327-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7327-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e7327-108">V navigačním panelu vyberte **Build** > **publikovat aplikaci {Application}** .</span><span class="sxs-lookup"><span data-stu-id="e7327-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="e7327-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="e7327-109">Select the *publish target*.</span></span> <span data-ttu-id="e7327-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="e7327-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="e7327-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="e7327-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="e7327-112">Vyberte tlačítko **Publikovat**.</span><span class="sxs-lookup"><span data-stu-id="e7327-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7327-113">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="e7327-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e7327-114">K publikování aplikace s konfigurací vydané verze použijte příkaz [dotnet Publish](/dotnet/core/tools/dotnet-publish) :</span><span class="sxs-lookup"><span data-stu-id="e7327-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="e7327-115">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="e7327-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="e7327-116">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="e7327-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="e7327-117">Aplikace Blazor WebAssembly je publikovaná ve složce */bin/Release/{Target Framework}/PUBLISH/{Assembly Name}/DIST* .</span><span class="sxs-lookup"><span data-stu-id="e7327-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="e7327-118">Aplikace Blazor serveru je publikovaná do složky */Publish/bin/Release/{Target Framework}* .</span><span class="sxs-lookup"><span data-stu-id="e7327-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="e7327-119">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="e7327-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="e7327-120">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="e7327-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="e7327-121">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="e7327-121">App base path</span></span>

<span data-ttu-id="e7327-122">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7327-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="e7327-123">Vezměte v úvahu následující hlavní aplikace a aplikace Blazor:</span><span class="sxs-lookup"><span data-stu-id="e7327-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="e7327-124">Hlavní aplikace se nazývá `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="e7327-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="e7327-125">Aplikace se fyzicky nachází v *d:\\MyApp*.</span><span class="sxs-lookup"><span data-stu-id="e7327-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="e7327-126">Žádosti jsou přijímány na `https://www.contoso.com/{MYAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="e7327-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="e7327-127">Blazor aplikace s názvem `CoolApp` je dílčí aplikace `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="e7327-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="e7327-128">Dílčí aplikace je fyzicky umístěná v *d:\\MyApp\\CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="e7327-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="e7327-129">Žádosti jsou přijímány na `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span><span class="sxs-lookup"><span data-stu-id="e7327-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="e7327-130">Bez zadání další konfigurace pro `CoolApp`nemá dílčí aplikace v tomto scénáři žádné znalosti o tom, kde se nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="e7327-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="e7327-131">Aplikace například nemůže vytvořit správné relativní adresy URL ke svým prostředkům bez vědomí, že se nachází na relativní cestě URL `/CoolApp/`.</span><span class="sxs-lookup"><span data-stu-id="e7327-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="e7327-132">Chcete-li zadat konfiguraci pro základní cestu aplikace Blazor `https://www.contoso.com/CoolApp/`, je atribut `href` značky `<base>` nastaven na relativní kořenovou cestu v souboru *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="e7327-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="e7327-133">Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7327-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="e7327-134">Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7327-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="e7327-135">Základní cesta aplikace se také používá k zachycení hypertextového odkazu, kde `href` cíl odkazu se nachází v rámci základní cesty k umístění identifikátoru URI aplikace&mdash;směrovač Blazor zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="e7327-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="e7327-136">V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7327-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="e7327-137">V těchto případech je základní cestou k relativní adrese URL aplikace lomítko (`<base href="/" />`), což je výchozí konfigurace aplikace Blazor.</span><span class="sxs-lookup"><span data-stu-id="e7327-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="e7327-138">V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e7327-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="e7327-139">Chcete-li nastavit základní cestu aplikace, aktualizujte značku `<base>` v rámci `<head>` prvků značek souboru *wwwroot/index.html* .</span><span class="sxs-lookup"><span data-stu-id="e7327-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="e7327-140">Nastavte hodnotu atributu `href` na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7327-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="e7327-141">Pro aplikaci, která má nekořenovou cestu relativní adresy URL (například `<base href="/CoolApp/">`), aplikace *při místním spuštění*nenalezne své prostředky.</span><span class="sxs-lookup"><span data-stu-id="e7327-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="e7327-142">Chcete-li vyřešit tento problém během místního vývoje a testování, můžete dodat *základní argument Path* , který odpovídá hodnotě `href` `<base>` značce za běhu.</span><span class="sxs-lookup"><span data-stu-id="e7327-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="e7327-143">Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte příkaz `dotnet run` z adresáře aplikace s možností `--pathbase`:</span><span class="sxs-lookup"><span data-stu-id="e7327-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="e7327-144">Pro aplikaci s relativní cestou URL `/CoolApp/` (`<base href="/CoolApp/">`) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="e7327-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="e7327-145">Aplikace odpoví místně na `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="e7327-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="e7327-146">Nasazení</span><span class="sxs-lookup"><span data-stu-id="e7327-146">Deployment</span></span>

<span data-ttu-id="e7327-147">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="e7327-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
