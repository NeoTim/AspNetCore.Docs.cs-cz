---
title: ASP.NET Core hostitele a nasazeníBlazor
author: guardrex
description: Objevte, jak hostovat a nasazovat Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 8a5172db22a17138f7462d140fd97316586e5282
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014201"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a><span data-ttu-id="f4839-103">ASP.NET Core hostitele a nasazeníBlazor</span><span class="sxs-lookup"><span data-stu-id="f4839-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="f4839-104">Od [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f4839-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="f4839-105">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="f4839-105">Publish the app</span></span>

<span data-ttu-id="f4839-106">Aplikace jsou publikované pro nasazení v konfiguraci vydání.</span><span class="sxs-lookup"><span data-stu-id="f4839-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f4839-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f4839-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f4839-108">V navigačním panelu vyberte **sestavení**  >  **publikovat {aplikace}** .</span><span class="sxs-lookup"><span data-stu-id="f4839-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="f4839-109">Vyberte *cíl publikování*.</span><span class="sxs-lookup"><span data-stu-id="f4839-109">Select the *publish target*.</span></span> <span data-ttu-id="f4839-110">Chcete-li publikovat místně, vyberte **Složka**.</span><span class="sxs-lookup"><span data-stu-id="f4839-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="f4839-111">Přijměte výchozí umístění v poli **Zvolte složku** nebo zadejte jiné umístění.</span><span class="sxs-lookup"><span data-stu-id="f4839-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="f4839-112">Vyberte **`Publish`** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="f4839-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f4839-113">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f4839-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f4839-114">Vyberte **sestavení**  >  **publikovat do složky**.</span><span class="sxs-lookup"><span data-stu-id="f4839-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="f4839-115">Potvrďte, že složka obdrží publikované prostředky a vyberte **`Publish`** .</span><span class="sxs-lookup"><span data-stu-id="f4839-115">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f4839-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f4839-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f4839-117">[`dotnet publish`](/dotnet/core/tools/dotnet-publish)K publikování aplikace s konfigurací vydané verze použijte příkaz:</span><span class="sxs-lookup"><span data-stu-id="f4839-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="f4839-118">Publikování aplikace spustí [obnovení](/dotnet/core/tools/dotnet-restore) závislostí projektu a před vytvořením prostředků pro nasazení [vytvoří](/dotnet/core/tools/dotnet-build) projekt.</span><span class="sxs-lookup"><span data-stu-id="f4839-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="f4839-119">V rámci procesu sestavení se odeberou nepoužívané metody a sestavení, aby se snížila velikost stahovaných aplikací a doby načítání.</span><span class="sxs-lookup"><span data-stu-id="f4839-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="f4839-120">Umístění pro publikování:</span><span class="sxs-lookup"><span data-stu-id="f4839-120">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="f4839-121">Samostatná: aplikace se publikuje do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky.</span><span class="sxs-lookup"><span data-stu-id="f4839-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="f4839-122">Chcete-li nasadit aplikaci jako statickou lokalitu, zkopírujte obsah `wwwroot` složky na hostitele statických webů.</span><span class="sxs-lookup"><span data-stu-id="f4839-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="f4839-123">Hosted: klientská Blazor WebAssembly aplikace je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` složky serverové aplikace spolu s dalšími statickými webovými prostředky serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-123">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="f4839-124">Nasaďte obsah `publish` složky na hostitele.</span><span class="sxs-lookup"><span data-stu-id="f4839-124">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="f4839-125">Blazor Server: Aplikace je publikovaná do `/bin/Release/{TARGET FRAMEWORK}/publish` složky.</span><span class="sxs-lookup"><span data-stu-id="f4839-125">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="f4839-126">Nasaďte obsah `publish` složky na hostitele.</span><span class="sxs-lookup"><span data-stu-id="f4839-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="f4839-127">Prostředky ve složce jsou nasazeny na webový server.</span><span class="sxs-lookup"><span data-stu-id="f4839-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="f4839-128">Nasazení může být ruční nebo automatizovaný proces v závislosti na používaných vývojářských nástrojích.</span><span class="sxs-lookup"><span data-stu-id="f4839-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="f4839-129">Základní cesta aplikace</span><span class="sxs-lookup"><span data-stu-id="f4839-129">App base path</span></span>

<span data-ttu-id="f4839-130">*Základní cesta aplikace* je kořenová cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="f4839-131">Vezměte v úvahu následující ASP.NET Core aplikace a Blazor dílčí aplikace:</span><span class="sxs-lookup"><span data-stu-id="f4839-131">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="f4839-132">Aplikace ASP.NET Core má název `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="f4839-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="f4839-133">Aplikace se fyzicky nachází na adrese `d:/MyApp` .</span><span class="sxs-lookup"><span data-stu-id="f4839-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="f4839-134">Žádosti jsou přijímány na adrese `https://www.contoso.com/{MYAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="f4839-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="f4839-135">BlazorAplikace s názvem `CoolApp` je dílčí aplikace `MyApp` :</span><span class="sxs-lookup"><span data-stu-id="f4839-135">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="f4839-136">Dílčí aplikace se fyzicky nachází v `d:/MyApp/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="f4839-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="f4839-137">Žádosti jsou přijímány na adrese `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .</span><span class="sxs-lookup"><span data-stu-id="f4839-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="f4839-138">Bez zadání další konfigurace pro nemůže `CoolApp` podaplikace v tomto scénáři znát, kde se nachází na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4839-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="f4839-139">Aplikace například nemůže sestavovat správné relativní adresy URL k prostředkům bez vědomí, že se nachází v relativní cestě URL `/CoolApp/` .</span><span class="sxs-lookup"><span data-stu-id="f4839-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="f4839-140">Chcete-li zadat konfiguraci pro Blazor základní cestu aplikace `https://www.contoso.com/CoolApp/` , `<base>` `href` je atribut značky nastaven na relativní kořenovou cestu v `Pages/_Host.cshtml` souboru ( Blazor Server ) nebo `wwwroot/index.html` souboru ( Blazor WebAssembly ):</span><span class="sxs-lookup"><span data-stu-id="f4839-140">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="f4839-141">Blazor Serveraplikace navíc nastaví základní cestu na straně serveru voláním <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> v kanálu žádosti aplikace `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f4839-141">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="f4839-142">Když zadáte relativní cestu URL, komponenta, která není v kořenovém adresáři, může vytvářet adresy URL relativní k kořenové cestě aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-142">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="f4839-143">Komponenty na různých úrovních adresářové struktury můžou vytvářet odkazy na jiné prostředky v umístění v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-143">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="f4839-144">Základní cesta aplikace se používá také k zachycení vybraných hypertextových odkazů, kde `href` cíl odkazu je v rámci prostoru identifikátoru URI základní cesty aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-144">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="f4839-145">BlazorSměrovač zpracovává interní navigaci.</span><span class="sxs-lookup"><span data-stu-id="f4839-145">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="f4839-146">V mnoha hostitelských scénářích je relativní cesta URL k aplikaci kořenem aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-146">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="f4839-147">V těchto případech je základní cestou k relativní adrese URL aplikace lomítko ( `<base href="/" />` ), což je výchozí konfigurace Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-147">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="f4839-148">V jiných scénářích hostování, jako jsou stránky GitHubu a podaplikace služby IIS, musí být základní cesta aplikace nastavená na relativní cestu URL serveru aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-148">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="f4839-149">Chcete-li nastavit základní cestu aplikace, aktualizujte `<base>` značku v rámci `<head>` prvků tagu `Pages/_Host.cshtml` souboru ( Blazor Server ) nebo `wwwroot/index.html` souboru ( Blazor WebAssembly ).</span><span class="sxs-lookup"><span data-stu-id="f4839-149">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="f4839-150">Nastavte `href` hodnotu atributu na `/{RELATIVE URL PATH}/` (vyžaduje se koncové lomítko), kde `{RELATIVE URL PATH}` je úplná relativní cesta URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4839-150">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="f4839-151">Pro Blazor WebAssembly aplikaci, která má nekořenovou cestu relativní adresy URL (například `<base href="/CoolApp/">` ), aplikace *při místním spuštění*nenalezne své prostředky.</span><span class="sxs-lookup"><span data-stu-id="f4839-151">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="f4839-152">Chcete-li tento problém překonat při místním vývoji a testování, můžete dodat *základní argument Path* , který odpovídá `href` hodnotě `<base>` značky za běhu.</span><span class="sxs-lookup"><span data-stu-id="f4839-152">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="f4839-153">Nezahrnovat koncové lomítko.</span><span class="sxs-lookup"><span data-stu-id="f4839-153">Don't include a trailing slash.</span></span> <span data-ttu-id="f4839-154">Pokud chcete předat základní argument Path při místním spuštění aplikace, spusťte `dotnet run` příkaz z adresáře aplikace s `--pathbase` možností:</span><span class="sxs-lookup"><span data-stu-id="f4839-154">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="f4839-155">Pro Blazor WebAssembly aplikaci s relativní cestou URL `/CoolApp/` ( `<base href="/CoolApp/">` ) je tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="f4839-155">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="f4839-156">Blazor WebAssemblyAplikace odpoví místně na adrese `http://localhost:port/CoolApp` .</span><span class="sxs-lookup"><span data-stu-id="f4839-156">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="f4839-157">**Blazor Server`MapFallbackToPage`Konfigurace**</span><span class="sxs-lookup"><span data-stu-id="f4839-157">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="f4839-158">Předat následující cestu do <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f4839-158">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="f4839-159">Zástupný symbol `{RELATIVE PATH}` je nekořenová cesta na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4839-159">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="f4839-160">Například `CoolApp` je segment zástupného symbolu, pokud není kořenová adresa URL aplikace `https://{HOST}:{PORT}/CoolApp/` ):</span><span class="sxs-lookup"><span data-stu-id="f4839-160">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="f4839-161">**Hostování více Blazor WebAssembly aplikací**</span><span class="sxs-lookup"><span data-stu-id="f4839-161">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="f4839-162">Další informace o hostování více Blazor WebAssembly aplikací v hostovaném Blazor řešení najdete v tématu <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> .</span><span class="sxs-lookup"><span data-stu-id="f4839-162">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="f4839-163">Nasazení</span><span class="sxs-lookup"><span data-stu-id="f4839-163">Deployment</span></span>

<span data-ttu-id="f4839-164">Pokyny k nasazení najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f4839-164">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
