---
title: Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core
author: scottaddie
description: Přečtěte si o výhodách používání služeb JavaScript k vytvoření jednostránkové aplikace (SPA), kterou zajišťuje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/spa-services
ms.openlocfilehash: 05f76a7d341fc5c55b8234b6ff6d2be5aa61d6fd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401828"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="257cd-103">Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="257cd-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="257cd-104">[Scottem Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="257cd-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="257cd-105">Jediná stránková aplikace (SPA) je oblíbeným typem webové aplikace z důvodu svého vlastního prostředí pro práci s uživateli.</span><span class="sxs-lookup"><span data-stu-id="257cd-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="257cd-106">Integrace rozhraní a knihoven SPA na straně klienta, jako je například [úhlová](https://angular.io/) nebo [reakce](https://facebook.github.io/react/), s architekturami na straně serveru, jako je například ASP.NET Core, mohou být obtížné.</span><span class="sxs-lookup"><span data-stu-id="257cd-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="257cd-107">Služba JavaScript Services byla vyvinuta za účelem snížení tření v procesu integrace.</span><span class="sxs-lookup"><span data-stu-id="257cd-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="257cd-108">Umožňuje bezproblémovou operaci mezi různými serverovými a technologickými zásobníky pro klienty.</span><span class="sxs-lookup"><span data-stu-id="257cd-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="257cd-109">Funkce popsané v tomto článku jsou zastaralé od ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="257cd-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="257cd-110">V balíčku NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) je k dispozici jednodušší mechanismus pro integraci rozhraní Spa.</span><span class="sxs-lookup"><span data-stu-id="257cd-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="257cd-111">Další informace najdete v tématech [[oznámení] obsoleting Microsoft. AspNetCore. SpaServices a Microsoft. AspNetCore. NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="257cd-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="257cd-112">Co je JavaScript Services</span><span class="sxs-lookup"><span data-stu-id="257cd-112">What is JavaScript Services</span></span>

<span data-ttu-id="257cd-113">Služba JavaScript Services je kolekce technologií na straně klienta pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="257cd-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="257cd-114">Jeho cílem je umístit ASP.NET Core jako preferovanou platformu na straně serveru vývojářů pro sestavování jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="257cd-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="257cd-115">Služby JavaScriptu se skládají ze dvou různých balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="257cd-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="257cd-116">[Microsoft. AspNetCore. NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="257cd-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="257cd-117">[Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="257cd-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="257cd-118">Tyto balíčky jsou užitečné v následujících scénářích:</span><span class="sxs-lookup"><span data-stu-id="257cd-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="257cd-119">Spuštění JavaScriptu na serveru</span><span class="sxs-lookup"><span data-stu-id="257cd-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="257cd-120">Použití architektury nebo knihovny SPA</span><span class="sxs-lookup"><span data-stu-id="257cd-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="257cd-121">Sestavení prostředků na straně klienta pomocí webpacku</span><span class="sxs-lookup"><span data-stu-id="257cd-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="257cd-122">Většina zaměření v tomto článku je umístěna na používání balíčku SpaServices.</span><span class="sxs-lookup"><span data-stu-id="257cd-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="257cd-123">Co je SpaServices</span><span class="sxs-lookup"><span data-stu-id="257cd-123">What is SpaServices</span></span>

<span data-ttu-id="257cd-124">SpaServices byl vytvořen tak, aby bylo možné umístit ASP.NET Core jako preferovanou platformu na straně serveru vývojářů pro stavební jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="257cd-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="257cd-125">SpaServices se nevyžaduje k vývoji jednostránkové s ASP.NET Core a nezamkne vývojáře do konkrétního klientského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="257cd-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="257cd-126">SpaServices poskytuje užitečnou infrastrukturu, jako je:</span><span class="sxs-lookup"><span data-stu-id="257cd-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="257cd-127">Předběžné vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="257cd-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="257cd-128">Middleware pro vývoj pro Webpack</span><span class="sxs-lookup"><span data-stu-id="257cd-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="257cd-129">Výměna horkého modulu</span><span class="sxs-lookup"><span data-stu-id="257cd-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="257cd-130">Helpdesky směrování</span><span class="sxs-lookup"><span data-stu-id="257cd-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="257cd-131">Souhrnně tyto komponenty infrastruktury rozšiřují pracovní postup vývoje i prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="257cd-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="257cd-132">Komponenty lze jednotlivě přijmout.</span><span class="sxs-lookup"><span data-stu-id="257cd-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="257cd-133">Předpoklady pro použití SpaServices</span><span class="sxs-lookup"><span data-stu-id="257cd-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="257cd-134">Pokud chcete pracovat s SpaServices, nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="257cd-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="257cd-135">[Node.js](https://nodejs.org/) (verze 6 nebo novější) s npm</span><span class="sxs-lookup"><span data-stu-id="257cd-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="257cd-136">Chcete-li ověřit, zda jsou tyto součásti nainstalovány a lze je najít, spusťte následující příkaz z příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="257cd-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="257cd-137">Pokud nasazujete na web Azure, není nutná žádná akce &mdash;Node.js je nainstalována a k dispozici v prostředích serveru.</span><span class="sxs-lookup"><span data-stu-id="257cd-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="257cd-138">V systému Windows pomocí sady Visual Studio 2017 se sada SDK nainstaluje výběrem úlohy **vývoje .NET Core pro různé platformy** .</span><span class="sxs-lookup"><span data-stu-id="257cd-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="257cd-139">Balíček NuGet [Microsoft. AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="257cd-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="257cd-140">Předběžné vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="257cd-140">Server-side prerendering</span></span>

<span data-ttu-id="257cd-141">Univerzální (označovaná také jako isomorphic) aplikace je JavaScriptová aplikace schopná běžet na serveru i v klientovi.</span><span class="sxs-lookup"><span data-stu-id="257cd-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="257cd-142">Úhlů, reakce a další oblíbená rozhraní poskytují univerzální platformu pro tento styl vývoje aplikace.</span><span class="sxs-lookup"><span data-stu-id="257cd-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="257cd-143">Nápad je nejprve vykreslovat komponenty architektury na serveru přes Node.js a poté delegovat další provádění na klienta.</span><span class="sxs-lookup"><span data-stu-id="257cd-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="257cd-144">ASP.NET Core [pomocníků značek](xref:mvc/views/tag-helpers/intro) , které poskytuje SpaServices, zjednoduší implementaci předvykreslování na straně serveru vyvoláním funkcí JavaScriptu na serveru.</span><span class="sxs-lookup"><span data-stu-id="257cd-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="257cd-145">Požadavky na předběžné vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="257cd-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="257cd-146">Nainstalujte balíček npm [s předvykreslováním ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="257cd-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="257cd-147">Konfigurace předvykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="257cd-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="257cd-148">Pomocníky značek jsou v souboru *_ViewImports. cshtml* projektu Zjistitelnější prostřednictvím registrace oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="257cd-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="257cd-149">Tyto značky pomocníka složitými rozhranímiy, které jsou v zobrazení k dispozici při přímé komunikaci s rozhraními API nízké úrovně pomocí syntaxe podobné HTML Razor .</span><span class="sxs-lookup"><span data-stu-id="257cd-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="257cd-150">ASP-PreRender-pomocník značek modulu</span><span class="sxs-lookup"><span data-stu-id="257cd-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="257cd-151">`asp-prerender-module`Pomocník značek použitý v předchozím příkladu kódu spouští *clientapp/dist/main-server.js* na serveru přes Node.js.</span><span class="sxs-lookup"><span data-stu-id="257cd-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="257cd-152">V zájmu srozumitelnosti je *main-server.js* soubor artefaktem úlohy TypeScript-to-JavaScript transpilation v procesu sestavení pro [sadu Webpack](https://webpack.github.io/) .</span><span class="sxs-lookup"><span data-stu-id="257cd-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="257cd-153">Webpack definuje alias vstupního bodu `main-server` pro. a přechod grafu závislostí pro tento alias začíná v souboru *clientapp/boot-server. TS* :</span><span class="sxs-lookup"><span data-stu-id="257cd-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="257cd-154">V následujících úhlových příkladech používá soubor *clientapp/boot-server. TS* `createServerRenderer` funkci a `RenderResult` typ `aspnet-prerendering` balíčku npm ke konfiguraci vykreslování serveru prostřednictvím Node.js.</span><span class="sxs-lookup"><span data-stu-id="257cd-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="257cd-155">Označení HTML určené pro vykreslování na straně serveru je předáno volání funkce přeložit, které je zabaleno do silně typovaného `Promise` objektu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="257cd-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="257cd-156">`Promise`Hodnota významnosti objektu je, že asynchronně poskytuje kód HTML na stránce pro vložení do elementu zástupného prvku modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="257cd-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="257cd-157">ASP-PreRender-Pomocník s datovou značkou</span><span class="sxs-lookup"><span data-stu-id="257cd-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="257cd-158">Při spojení s `asp-prerender-module` pomocníkem značek `asp-prerender-data` lze pomoc pomocí značky použít k předání kontextových informací ze Razor zobrazení na straně serveru JavaScript.</span><span class="sxs-lookup"><span data-stu-id="257cd-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="257cd-159">Například následující kód předává uživatelská data do `main-server` modulu:</span><span class="sxs-lookup"><span data-stu-id="257cd-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="257cd-160">Přijatý `UserName` argument je serializován pomocí integrovaného serializátoru JSON a je uložen v `params.data` objektu.</span><span class="sxs-lookup"><span data-stu-id="257cd-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="257cd-161">V následujícím úhlovém příkladu se data používají k sestavení vlastního pozdravu v rámci `h1` elementu:</span><span class="sxs-lookup"><span data-stu-id="257cd-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="257cd-162">Názvy vlastností předané v Pomocníkech značek jsou reprezentovány **PascalCase** Notation.</span><span class="sxs-lookup"><span data-stu-id="257cd-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="257cd-163">Na rozdíl od jazyka JavaScript, kde jsou stejné názvy vlastností, které jsou reprezentovány s **CamelCase**.</span><span class="sxs-lookup"><span data-stu-id="257cd-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="257cd-164">Výchozí konfigurace serializace JSON zodpovídá za tento rozdíl.</span><span class="sxs-lookup"><span data-stu-id="257cd-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="257cd-165">Pro rozšíření na předchozí příklad kódu mohou být data předána ze serveru do zobrazení tím, že se dobalí `globals` vlastnost poskytnutá `resolve` funkci:</span><span class="sxs-lookup"><span data-stu-id="257cd-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="257cd-166">`postList`Pole definované uvnitř `globals` objektu je připojeno k globálnímu `window` objektu prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="257cd-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="257cd-167">Tato proměnná výtahem do globálního rozsahu eliminuje duplicity, zejména v případě, že se týká načítání stejných dat na serveru a znovu v klientovi.</span><span class="sxs-lookup"><span data-stu-id="257cd-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globální proměnná postList připojená k objektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="257cd-169">Middleware pro vývoj pro Webpack</span><span class="sxs-lookup"><span data-stu-id="257cd-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="257cd-170">[Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) pro vývoj pro Webpack zavádí zjednodušený vývojový pracovní postup, který v sadě Webpack vytváří prostředky na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="257cd-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="257cd-171">Middleware automaticky zkompiluje a zachová prostředky na straně klienta při opětovném načtení stránky v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="257cd-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="257cd-172">Alternativním přístupem je ruční vyvolání sady Webpack prostřednictvím skriptu sestavení npm projektu, když se změní závislost třetí strany nebo vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="257cd-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="257cd-173">V následujícím příkladu je zobrazen skript sestavení npm v souboru *package.js* .</span><span class="sxs-lookup"><span data-stu-id="257cd-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="257cd-174">Požadavky na middleware pro vývoj pro Webpack</span><span class="sxs-lookup"><span data-stu-id="257cd-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="257cd-175">Instalace balíčku [ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) npm:</span><span class="sxs-lookup"><span data-stu-id="257cd-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="257cd-176">Konfigurace pro vývoj middlewaru pro Webpack</span><span class="sxs-lookup"><span data-stu-id="257cd-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="257cd-177">Middleware pro vývoj v sadě Webpack je zaregistrovaný do kanálu požadavků HTTP prostřednictvím následujícího kódu v metodě souboru *Startup.cs* `Configure` :</span><span class="sxs-lookup"><span data-stu-id="257cd-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="257cd-178">`UseWebpackDevMiddleware`Před [registrací statického souboru hostujícího](xref:fundamentals/static-files) prostřednictvím metody rozšíření je nutné volat metodu rozšíření `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="257cd-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="257cd-179">Z bezpečnostních důvodů Zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="257cd-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="257cd-180">Vlastnost souboru *webpack.config.js* dává `output.publicPath` middlewaru pokyn, aby ve `dist` složce sledoval změny:</span><span class="sxs-lookup"><span data-stu-id="257cd-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="257cd-181">Výměna horkého modulu</span><span class="sxs-lookup"><span data-stu-id="257cd-181">Hot Module Replacement</span></span>

<span data-ttu-id="257cd-182">Zamyslete se nad funkcí HMR ( [Hot Module nahradilo](https://webpack.js.org/concepts/hot-module-replacement/) ) sady Webpack jako vývoj middlewaru pro vývoj pro [Webpack](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="257cd-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="257cd-183">HMR zavádí všechny stejné výhody, ale ještě více zjednodušuje pracovní postup vývoje díky automatické aktualizaci obsahu stránky po zkompilování změn.</span><span class="sxs-lookup"><span data-stu-id="257cd-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="257cd-184">Nepleťte si to s aktualizací prohlížeče, což by vedlo ke konfliktu s aktuálním stavem v paměti a relacemi ladění zabezpečeného hesla.</span><span class="sxs-lookup"><span data-stu-id="257cd-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="257cd-185">Mezi službou Webpack dev middleware a prohlížečem existuje živý odkaz, který znamená, že se do prohlížeče dostanou změny.</span><span class="sxs-lookup"><span data-stu-id="257cd-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="257cd-186">Požadavky na nahrazení horkého modulu</span><span class="sxs-lookup"><span data-stu-id="257cd-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="257cd-187">Nainstalujte balíček [Webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:</span><span class="sxs-lookup"><span data-stu-id="257cd-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="257cd-188">Konfigurace nahrazení horkého modulu</span><span class="sxs-lookup"><span data-stu-id="257cd-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="257cd-189">Komponenta HMR musí být zaregistrovaná do kanálu požadavků HTTP MVC v `Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="257cd-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="257cd-190">Stejně jako u rozhraní [Webpack dev middleware](#webpack-dev-middleware) `UseWebpackDevMiddleware` musí být metoda rozšíření volána před `UseStaticFiles` metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="257cd-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="257cd-191">Z bezpečnostních důvodů Zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="257cd-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="257cd-192">*webpack.config.js* soubor musí definovat `plugins` pole, i když je ponecháno prázdné:</span><span class="sxs-lookup"><span data-stu-id="257cd-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="257cd-193">Po načtení aplikace v prohlížeči se na kartě konzola nástroje pro vývojáře zobrazí potvrzení aktivace HMR:</span><span class="sxs-lookup"><span data-stu-id="257cd-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Nepřipojená zpráva o nahrazení horkého modulu](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="257cd-195">Helpdesky směrování</span><span class="sxs-lookup"><span data-stu-id="257cd-195">Routing helpers</span></span>

<span data-ttu-id="257cd-196">Ve většině ASP.NET Core jednostránkové na straně klienta je často žádoucí směrování na straně klienta kromě směrování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="257cd-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="257cd-197">Systémy směrování SPA a MVC můžou pracovat nezávisle bez rušivých zásahů.</span><span class="sxs-lookup"><span data-stu-id="257cd-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="257cd-198">Existuje ale jeden hraniční případ s výzvami k identifikaci 404 odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="257cd-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="257cd-199">Vezměte v úvahu scénář, ve kterém se používá trasa s příponou `/some/page` .</span><span class="sxs-lookup"><span data-stu-id="257cd-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="257cd-200">Předpokládat, že požadavek neodpovídá vzorové trase na straně serveru, ale jeho vzor odpovídá trase na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="257cd-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="257cd-201">Nyní zvažte příchozí požadavek na `/images/user-512.png` , který obecně očekává, že na serveru najde soubor s obrázkem.</span><span class="sxs-lookup"><span data-stu-id="257cd-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="257cd-202">Pokud tato požadovaná cesta prostředku neodpovídá žádné cestě na straně serveru ani ke statickému souboru, je pravděpodobné, že aplikace na straně klienta nebude obvykle zpracovávat &mdash; stavový kód protokolu HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="257cd-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="257cd-203">Požadavky na helpdesky směrování</span><span class="sxs-lookup"><span data-stu-id="257cd-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="257cd-204">Nainstalujte balíček npm pro směrování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="257cd-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="257cd-205">Jako příklad použijte úhlové:</span><span class="sxs-lookup"><span data-stu-id="257cd-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="257cd-206">Konfigurace pomocníků pro směrování</span><span class="sxs-lookup"><span data-stu-id="257cd-206">Routing helpers configuration</span></span>

<span data-ttu-id="257cd-207">Metoda rozšíření s názvem `MapSpaFallbackRoute` se používá v `Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="257cd-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="257cd-208">Trasy jsou vyhodnocovány v pořadí, ve kterém jsou konfigurovány.</span><span class="sxs-lookup"><span data-stu-id="257cd-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="257cd-209">V důsledku toho `default` je trasa v předchozím příkladu kódu použita jako první pro porovnávání vzorů.</span><span class="sxs-lookup"><span data-stu-id="257cd-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="257cd-210">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="257cd-210">Create a new project</span></span>

<span data-ttu-id="257cd-211">Služby JavaScriptu poskytují předem nakonfigurované šablony aplikací.</span><span class="sxs-lookup"><span data-stu-id="257cd-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="257cd-212">SpaServices se používá v těchto šablonách společně s různými rozhraními a knihovnami, jako je například úhlová, reakce a Redux.</span><span class="sxs-lookup"><span data-stu-id="257cd-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="257cd-213">Tyto šablony lze nainstalovat prostřednictvím .NET Core CLI spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="257cd-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="257cd-214">Zobrazí se seznam dostupných šablon SPA:</span><span class="sxs-lookup"><span data-stu-id="257cd-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="257cd-215">Šablony</span><span class="sxs-lookup"><span data-stu-id="257cd-215">Templates</span></span>                                 | <span data-ttu-id="257cd-216">Krátký název</span><span class="sxs-lookup"><span data-stu-id="257cd-216">Short Name</span></span> | <span data-ttu-id="257cd-217">Jazyk</span><span class="sxs-lookup"><span data-stu-id="257cd-217">Language</span></span> | <span data-ttu-id="257cd-218">Značky</span><span class="sxs-lookup"><span data-stu-id="257cd-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="257cd-219">ASP.NET Core MVC s úhlovým</span><span class="sxs-lookup"><span data-stu-id="257cd-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="257cd-220">Angular</span><span class="sxs-lookup"><span data-stu-id="257cd-220">angular</span></span>    | <span data-ttu-id="257cd-221">Jazyk</span><span class="sxs-lookup"><span data-stu-id="257cd-221">[C#]</span></span>     | <span data-ttu-id="257cd-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="257cd-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="257cd-223">ASP.NET Core MVC s React.js</span><span class="sxs-lookup"><span data-stu-id="257cd-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="257cd-224">react</span><span class="sxs-lookup"><span data-stu-id="257cd-224">react</span></span>      | <span data-ttu-id="257cd-225">Jazyk</span><span class="sxs-lookup"><span data-stu-id="257cd-225">[C#]</span></span>     | <span data-ttu-id="257cd-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="257cd-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="257cd-227">ASP.NET Core MVC s React.js a Redux</span><span class="sxs-lookup"><span data-stu-id="257cd-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="257cd-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="257cd-228">reactredux</span></span> | <span data-ttu-id="257cd-229">Jazyk</span><span class="sxs-lookup"><span data-stu-id="257cd-229">[C#]</span></span>     | <span data-ttu-id="257cd-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="257cd-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="257cd-231">Pokud chcete vytvořit nový projekt pomocí jedné z šablon SPA, zahrňte do příkazu [dotnet New](/dotnet/core/tools/dotnet-new) **krátký název** šablony.</span><span class="sxs-lookup"><span data-stu-id="257cd-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="257cd-232">Následující příkaz vytvoří úhlovou aplikaci s ASP.NET Core MVC nakonfigurovanou pro stranu serveru:</span><span class="sxs-lookup"><span data-stu-id="257cd-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="257cd-233">Nastavení režimu konfigurace modulu runtime</span><span class="sxs-lookup"><span data-stu-id="257cd-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="257cd-234">Existují dva režimy konfigurace primárního modulu runtime:</span><span class="sxs-lookup"><span data-stu-id="257cd-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="257cd-235">**Vývoj**:</span><span class="sxs-lookup"><span data-stu-id="257cd-235">**Development**:</span></span>
  * <span data-ttu-id="257cd-236">Obsahuje zdrojové mapování pro usnadnění ladění.</span><span class="sxs-lookup"><span data-stu-id="257cd-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="257cd-237">Neoptimalizuje kód na straně klienta pro výkon.</span><span class="sxs-lookup"><span data-stu-id="257cd-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="257cd-238">**Provozní**:</span><span class="sxs-lookup"><span data-stu-id="257cd-238">**Production**:</span></span>
  * <span data-ttu-id="257cd-239">Vyloučí zdrojové mapy.</span><span class="sxs-lookup"><span data-stu-id="257cd-239">Excludes source maps.</span></span>
  * <span data-ttu-id="257cd-240">Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.</span><span class="sxs-lookup"><span data-stu-id="257cd-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="257cd-241">ASP.NET Core používá `ASPNETCORE_ENVIRONMENT` k uložení režimu konfigurace proměnnou prostředí s názvem.</span><span class="sxs-lookup"><span data-stu-id="257cd-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="257cd-242">Další informace najdete v tématu [nastavení prostředí](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="257cd-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="257cd-243">Spustit s .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="257cd-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="257cd-244">Požadované balíčky NuGet a npm obnovte spuštěním následujícího příkazu v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="257cd-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="257cd-245">Sestavte a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="257cd-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="257cd-246">Aplikace se spouští na místním hostiteli podle [režimu konfigurace modulu runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="257cd-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="257cd-247">Navigace `http://localhost:5000` v prohlížeči zobrazí cílovou stránku.</span><span class="sxs-lookup"><span data-stu-id="257cd-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="257cd-248">Spustit se sadou Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="257cd-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="257cd-249">Otevřete soubor *. csproj* generovaný příkazem [dotnet New](/dotnet/core/tools/dotnet-new) .</span><span class="sxs-lookup"><span data-stu-id="257cd-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="257cd-250">Požadované balíčky NuGet a npm se obnoví automaticky po otevření projektu.</span><span class="sxs-lookup"><span data-stu-id="257cd-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="257cd-251">Tento proces obnovení může trvat až několik minut a aplikace je připravená ke spuštění po dokončení.</span><span class="sxs-lookup"><span data-stu-id="257cd-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="257cd-252">Klikněte na zelené tlačítko spustit nebo stiskněte klávesu `Ctrl + F5` a otevře se prohlížeč na úvodní stránce aplikace.</span><span class="sxs-lookup"><span data-stu-id="257cd-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="257cd-253">Aplikace běží na místním hostiteli podle [režimu konfigurace modulu runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="257cd-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="257cd-254">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="257cd-254">Test the app</span></span>

<span data-ttu-id="257cd-255">Šablony SpaServices jsou předem nakonfigurované tak, aby se spouštěly testy na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [jednotek Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="257cd-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="257cd-256">Jednotek Jasmine je oblíbená architektura pro testování částí pro JavaScript, zatímco Karma je Test Runner pro tyto testy.</span><span class="sxs-lookup"><span data-stu-id="257cd-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="257cd-257">Karma je nakonfigurovaná tak, aby spolupracovala s rozhraním pro [vývoj pro Webpack](#webpack-dev-middleware) . to znamená, že vývojář není nutný k zastavení a spuštění testu pokaždé, když se změny provedou.</span><span class="sxs-lookup"><span data-stu-id="257cd-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="257cd-258">Bez ohledu na to, zda se jedná o kód spuštěný proti testovacímu případu nebo samotnému testovacímu případu, se test spustí automaticky.</span><span class="sxs-lookup"><span data-stu-id="257cd-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="257cd-259">Použití úhlové aplikace jako příklad, `CounterComponent` v souboru *Counter. Component. spec. TS* jsou již k dispozici dva jednotek Jasmine testovací případy:</span><span class="sxs-lookup"><span data-stu-id="257cd-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="257cd-260">Otevřete příkazový řádek v adresáři *clientapp* .</span><span class="sxs-lookup"><span data-stu-id="257cd-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="257cd-261">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="257cd-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="257cd-262">Skript spustí Karma Test Runner, který přečte nastavení definované v souboru *karma.conf.js* .</span><span class="sxs-lookup"><span data-stu-id="257cd-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="257cd-263">Kromě dalších nastavení *karma.conf.js* identifikuje testovací soubory, které mají být provedeny prostřednictvím svého `files` pole:</span><span class="sxs-lookup"><span data-stu-id="257cd-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="257cd-264">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="257cd-264">Publish the app</span></span>

<span data-ttu-id="257cd-265">Další informace o publikování do Azure najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/12474) .</span><span class="sxs-lookup"><span data-stu-id="257cd-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="257cd-266">Kombinování generovaných prostředků na straně klienta a publikovaných artefaktů ASP.NET Core do balíčku připraveného k nasazení může být náročných.</span><span class="sxs-lookup"><span data-stu-id="257cd-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="257cd-267">Naštěstí SpaServices orchestruje, že celý proces publikace s vlastním cílem MSBuild s názvem `RunWebpack` :</span><span class="sxs-lookup"><span data-stu-id="257cd-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="257cd-268">Cíl MSBuild má následující zodpovědnosti:</span><span class="sxs-lookup"><span data-stu-id="257cd-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="257cd-269">Obnovte balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="257cd-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="257cd-270">Vytvoření výrobního sestavení prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="257cd-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="257cd-271">Vytvořte výrobní sestavení vlastních prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="257cd-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="257cd-272">Zkopírujte prostředky vygenerované v sadě Webpack do složky pro publikování.</span><span class="sxs-lookup"><span data-stu-id="257cd-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="257cd-273">Cíl nástroje MSBuild je vyvolán při spuštění:</span><span class="sxs-lookup"><span data-stu-id="257cd-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="257cd-274">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="257cd-274">Additional resources</span></span>

* [<span data-ttu-id="257cd-275">Úhlové dokumenty</span><span class="sxs-lookup"><span data-stu-id="257cd-275">Angular Docs</span></span>](https://angular.io/docs)
