---
title: Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core
author: scottaddie
description: Přečtěte si o výhodách používání služeb JavaScript k vytvoření jednostránkové aplikace (SPA), kterou zajišťuje ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: 7aff46f739239246191763e0590046b2d9995922
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080511"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="f14bf-103">Použití služeb JavaScriptu k vytváření aplikací s jedním stránkou v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f14bf-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="f14bf-104">Podle [Scott Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="f14bf-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="f14bf-105">Jednu stránku aplikace (SPA) je typ oblíbené webové aplikace z důvodu jeho přináší bohaté uživatelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="f14bf-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="f14bf-106">Integrace rozhraní a knihoven SPA na straně klienta, jako je například [úhlová](https://angular.io/) nebo [reakce](https://facebook.github.io/react/), s architekturami na straně serveru, jako je například ASP.NET Core, mohou být obtížné.</span><span class="sxs-lookup"><span data-stu-id="f14bf-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="f14bf-107">Služba JavaScript Services byla vyvinuta za účelem snížení tření v procesu integrace.</span><span class="sxs-lookup"><span data-stu-id="f14bf-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="f14bf-108">Umožňuje bezproblémové operace mezi různých klientských a serverových sad technologie.</span><span class="sxs-lookup"><span data-stu-id="f14bf-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="f14bf-109">Funkce popsané v tomto článku jsou zastaralé od ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f14bf-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="f14bf-110">V balíčku NuGet [Microsoft. AspNetCore. SpaServices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) je k dispozici jednodušší mechanismus pro integraci rozhraní Spa.</span><span class="sxs-lookup"><span data-stu-id="f14bf-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="f14bf-111">Další informace najdete v tématech [[oznámení] obsoleting Microsoft. AspNetCore. SpaServices a Microsoft. AspNetCore. NodeServices](https://github.com/aspnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="f14bf-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/aspnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="f14bf-112">Co je JavaScript Services</span><span class="sxs-lookup"><span data-stu-id="f14bf-112">What is JavaScript Services</span></span>

<span data-ttu-id="f14bf-113">Služba JavaScript Services je kolekce technologií na straně klienta pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14bf-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="f14bf-114">Jeho cílem je na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="f14bf-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="f14bf-115">Služby JavaScriptu se skládají ze dvou různých balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="f14bf-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="f14bf-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="f14bf-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="f14bf-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="f14bf-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="f14bf-118">Tyto balíčky jsou užitečné v následujících scénářích:</span><span class="sxs-lookup"><span data-stu-id="f14bf-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="f14bf-119">Spuštění JavaScriptu na serveru</span><span class="sxs-lookup"><span data-stu-id="f14bf-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="f14bf-120">Použít SPA architektury nebo knihovny</span><span class="sxs-lookup"><span data-stu-id="f14bf-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="f14bf-121">Vytvoření prostředků na straně klienta s Webpacku</span><span class="sxs-lookup"><span data-stu-id="f14bf-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="f14bf-122">Mnoho pozornosti v tomto článku je umístěn na použití SpaServices balíčku.</span><span class="sxs-lookup"><span data-stu-id="f14bf-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="f14bf-123">Co je SpaServices</span><span class="sxs-lookup"><span data-stu-id="f14bf-123">What is SpaServices</span></span>

<span data-ttu-id="f14bf-124">SpaServices byl vytvořen na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="f14bf-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="f14bf-125">SpaServices se nevyžaduje k vývoji jednostránkové s ASP.NET Core a nezamkne vývojáře do konkrétního klientského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f14bf-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="f14bf-126">SpaServices infrastrukturu užitečné jako:</span><span class="sxs-lookup"><span data-stu-id="f14bf-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="f14bf-127">Dokončení fáze před vykreslením na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f14bf-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="f14bf-128">Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="f14bf-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="f14bf-129">Nahrazení horké modulu</span><span class="sxs-lookup"><span data-stu-id="f14bf-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="f14bf-130">Směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="f14bf-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="f14bf-131">Společně tyto součástí infrastruktury vylepšení pracovního postupu vývoje i prostředí runtime.</span><span class="sxs-lookup"><span data-stu-id="f14bf-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="f14bf-132">Součásti může být přijata jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="f14bf-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="f14bf-133">Předpoklady pro použití SpaServices</span><span class="sxs-lookup"><span data-stu-id="f14bf-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="f14bf-134">Pro práci s SpaServices, nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="f14bf-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="f14bf-135">[Node.js](https://nodejs.org/) (verze 6 nebo novější) pomocí npm</span><span class="sxs-lookup"><span data-stu-id="f14bf-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="f14bf-136">Ověření tyto součásti jsou nainstalovány a najdete, spusťte z příkazového řádku následující:</span><span class="sxs-lookup"><span data-stu-id="f14bf-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="f14bf-137">Pokud nasazujete na web Azure, není k dispozici&mdash;žádná akce Node. js, která je k dispozici v prostředích serveru.</span><span class="sxs-lookup"><span data-stu-id="f14bf-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="f14bf-138">V systému Windows pomocí sady Visual Studio 2017 se sada SDK nainstaluje výběrem úlohy **vývoje .NET Core pro různé platformy** .</span><span class="sxs-lookup"><span data-stu-id="f14bf-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="f14bf-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="f14bf-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="f14bf-140">Dokončení fáze před vykreslením na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f14bf-140">Server-side prerendering</span></span>

<span data-ttu-id="f14bf-141">Univerzální aplikace (označované také jako isomorphic) je schopný běžet současně na serveru a klienta aplikace v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f14bf-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="f14bf-142">Angular, React a dalších oblíbených architektur poskytují univerzální platformu pro tento styl vývoje aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14bf-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="f14bf-143">Cílem je nejprve vykreslit framework komponent na serveru pomocí Node.js a následně delegovat další spuštění klientovi.</span><span class="sxs-lookup"><span data-stu-id="f14bf-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="f14bf-144">ASP.NET Core [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) poskytované SpaServices zjednodušení implementace modulů na straně serveru dokončení fáze před vykreslením vyvoláním funkce jazyka JavaScript na serveru.</span><span class="sxs-lookup"><span data-stu-id="f14bf-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="f14bf-145">Požadavky na předběžné vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f14bf-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="f14bf-146">Nainstalujte balíček npm [s předvykreslováním ASPNET](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="f14bf-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="f14bf-147">Konfigurace předvykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f14bf-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="f14bf-148">Pomocné rutiny značek se provádí objevitelný prostřednictvím registrace oboru názvů v projektu *_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="f14bf-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="f14bf-149">Tyto pomocné rutiny značek abstrakci složitými rozhraními komunikovat přímo s nižší úrovně rozhraní API s využitím syntaxe jazyka HTML v zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="f14bf-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="f14bf-150">ASP-PreRender-pomocník značek modulu</span><span class="sxs-lookup"><span data-stu-id="f14bf-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="f14bf-151">`asp-prerender-module` Pomocné rutiny značky použité v předchozím příkladu kód provede *ClientApp/dist/main-server.js* na serveru pomocí Node.js.</span><span class="sxs-lookup"><span data-stu-id="f14bf-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="f14bf-152">Pro přehlednost společnosti saké *hlavní server.js* je artefakt úlohy transpilation TypeScript pro JavaScript v souboru [Webpacku](https://webpack.github.io/) procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="f14bf-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="f14bf-153">Webpacku definuje vstupní bod aliasu `main-server`; a procházení grafu závislostí pro tento alias začíná *ClientApp/spouštěcí server.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="f14bf-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="f14bf-154">V následujícím příkladu Angular *ClientApp/spouštěcí server.ts* využívá soubor `createServerRenderer` funkce a `RenderResult` typ `aspnet-prerendering` balíček npm ke konfiguraci vykreslování na serveru pomocí Node.js.</span><span class="sxs-lookup"><span data-stu-id="f14bf-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="f14bf-155">Značka jazyka HTML, který je určený pro vykreslování na straně serveru je předána volání funkce řešení, která je zabalena v JavaScriptu se silnými typy `Promise` objektu.</span><span class="sxs-lookup"><span data-stu-id="f14bf-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="f14bf-156">`Promise` Násobek objektu je, že asynchronně poskytuje kód HTML na stránce pro vkládání v elementu modelu DOM zástupný symbol.</span><span class="sxs-lookup"><span data-stu-id="f14bf-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="f14bf-157">ASP-PreRender-Pomocník s datovou značkou</span><span class="sxs-lookup"><span data-stu-id="f14bf-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="f14bf-158">Pokud spolu s `asp-prerender-module` pomocné rutiny značky `asp-prerender-data` pomocné rutiny značky lze použít k předání kontextové informace ze zobrazení syntaxe Razor pro jazyk JavaScript na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f14bf-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="f14bf-159">Například následující kód předá data uživatele `main-server` modul:</span><span class="sxs-lookup"><span data-stu-id="f14bf-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="f14bf-160">Přijatého `UserName` argument serializován pomocí předdefinovaných serializátor JSON a je uložen v `params.data` objektu.</span><span class="sxs-lookup"><span data-stu-id="f14bf-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="f14bf-161">V následujícím příkladu Angular, data se používají k vytvoření přizpůsobené pozdrav v rámci `h1` element:</span><span class="sxs-lookup"><span data-stu-id="f14bf-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="f14bf-162">Názvy vlastností předané v Pomocníkech značek jsou reprezentovány **PascalCase** Notation.</span><span class="sxs-lookup"><span data-stu-id="f14bf-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="f14bf-163">Kontrast, JavaScript, kde jsou reprezentovány stejné názvy vlastností **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="f14bf-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="f14bf-164">Výchozí konfigurace serializace JSON je zodpovědná za tento rozdíl.</span><span class="sxs-lookup"><span data-stu-id="f14bf-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="f14bf-165">Rozbalte na předchozí příklad kódu, data mohou být předána ze serveru do zobrazení podle hydrating `globals` zadanou pro vlastnost `resolve` funkce:</span><span class="sxs-lookup"><span data-stu-id="f14bf-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="f14bf-166">`postList` Pole definované uvnitř `globals` objekt je připojen k prohlížeči globální `window` objektu.</span><span class="sxs-lookup"><span data-stu-id="f14bf-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="f14bf-167">Tato proměnná zvedání globálním rozsahem eliminuje duplikace úsilí, zvlášť, protože se týkají načítají stejná data jednou na serveru a znovu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f14bf-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globální proměnná postList připojen k objektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="f14bf-169">Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="f14bf-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="f14bf-170">[Dev Middleware Webpacku](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) zavádí zjednodušit vývoj pracovní postup, kterým Webpacku sestavení prostředků na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f14bf-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="f14bf-171">Middleware automaticky zkompiluje a slouží prostředky na straně klienta, pokud stránka opětovném načtení nástroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="f14bf-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="f14bf-172">Alternativním přístupem je vyvolat ručně Webpacku prostřednictvím skriptu buildu projektu npm při změně závislostí třetích stran nebo vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="f14bf-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="f14bf-173">Npm sestavení skriptu *package.json* souboru je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f14bf-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="f14bf-174">Požadavky na middleware pro vývoj pro Webpack</span><span class="sxs-lookup"><span data-stu-id="f14bf-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="f14bf-175">Instalace balíčku [ASPNET-Webpack](https://www.npmjs.com/package/aspnet-webpack) npm:</span><span class="sxs-lookup"><span data-stu-id="f14bf-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="f14bf-176">Konfigurace pro vývoj middlewaru pro Webpack</span><span class="sxs-lookup"><span data-stu-id="f14bf-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="f14bf-177">Webpacku Dev middlewaru je zaregistrovat do kanál požadavků HTTP prostřednictvím následující kód *Startup.cs* souboru `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f14bf-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="f14bf-178">`UseWebpackDevMiddleware` – Metoda rozšíření musí být volána před [registrace hostování statického souboru](xref:fundamentals/static-files) prostřednictvím `UseStaticFiles` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f14bf-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="f14bf-179">Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="f14bf-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="f14bf-180">*Webpack.config.js* souboru `output.publicPath` vlastnost informuje middleware a sledujte `dist` složku pro změny:</span><span class="sxs-lookup"><span data-stu-id="f14bf-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="f14bf-181">Nahrazení horké modulu</span><span class="sxs-lookup"><span data-stu-id="f14bf-181">Hot Module Replacement</span></span>

<span data-ttu-id="f14bf-182">Představte si, že na Webpacku [horké nahrazení modulu](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) funkce jako vývojem představ o [Webpacku Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="f14bf-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="f14bf-183">HMR představuje stejné výhody, ale další zjednodušuje pracovní postup vývoje pomocí automaticky aktualizuje obsah stránky po kompilaci změn.</span><span class="sxs-lookup"><span data-stu-id="f14bf-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="f14bf-184">Tento nástroj nezaměnili při aktualizaci prohlížeče, které by mohly narušit aktuálního stavu v paměti a ladicí relace tato jednostránková aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14bf-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="f14bf-185">Je aktivní propojení mezi službou Webpacku Dev Middleware a prohlížeče, což znamená, že změny jsou vloženy do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f14bf-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="f14bf-186">Požadavky na nahrazení horkého modulu</span><span class="sxs-lookup"><span data-stu-id="f14bf-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="f14bf-187">Nainstalujte balíček [Webpack – Hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:</span><span class="sxs-lookup"><span data-stu-id="f14bf-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="f14bf-188">Konfigurace nahrazení horkého modulu</span><span class="sxs-lookup"><span data-stu-id="f14bf-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="f14bf-189">Komponenta HMR musí být zaregistrovaný do kanál požadavků HTTP pro MVC v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f14bf-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="f14bf-190">Stejně jako dřív platilo s [Webpacku Dev Middleware](#webpack-dev-middleware), `UseWebpackDevMiddleware` – metoda rozšíření musí být volána před `UseStaticFiles` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f14bf-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="f14bf-191">Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="f14bf-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="f14bf-192">*Webpack.config.js* soubor musí definovat `plugins` i v případě, že je ponecháno prázdné pole:</span><span class="sxs-lookup"><span data-stu-id="f14bf-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="f14bf-193">Po načtení aplikace v prohlížeči, poskytuje nástroje pro vývojáře kartu konzoly pro potvrzení HMR aktivace:</span><span class="sxs-lookup"><span data-stu-id="f14bf-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Horké modulu nahrazení připojené zprávy](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="f14bf-195">Směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="f14bf-195">Routing helpers</span></span>

<span data-ttu-id="f14bf-196">Ve většině ASP.NET Core jednostránkové na straně klienta je často žádoucí směrování na straně klienta kromě směrování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f14bf-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="f14bf-197">Jednostránková aplikace a MVC směrovací systémy může pracovat nezávisle na bez rušení.</span><span class="sxs-lookup"><span data-stu-id="f14bf-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="f14bf-198">Existuje, ale jeden okraj případu představující vyzve: identifikace obdržíte kód odpovědi HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="f14bf-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="f14bf-199">Vezměte v úvahu scénář, ve kterém bez přípony trasu `/some/page` se používá.</span><span class="sxs-lookup"><span data-stu-id="f14bf-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="f14bf-200">Předpokládejme, požadavek nebude – porovnávací trasu na straně serveru, ale jeho vzor neshoduje trasu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f14bf-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="f14bf-201">Teď se podíváme příchozí žádosti pro `/images/user-512.png`, což obecně očekává, že se najít soubor obrázku na serveru.</span><span class="sxs-lookup"><span data-stu-id="f14bf-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="f14bf-202">Pokud tato požadovaná cesta prostředku neodpovídá žádné cestě na straně serveru ani ke statickému souboru, je pravděpodobné, že aplikace na straně klienta nebude&mdash;obvykle zpracovávat stavový kód protokolu HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="f14bf-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="f14bf-203">Požadavky na helpdesky směrování</span><span class="sxs-lookup"><span data-stu-id="f14bf-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="f14bf-204">Nainstalujte balíček npm pro směrování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f14bf-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="f14bf-205">Jako příklad použijeme Angular:</span><span class="sxs-lookup"><span data-stu-id="f14bf-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="f14bf-206">Konfigurace pomocníků pro směrování</span><span class="sxs-lookup"><span data-stu-id="f14bf-206">Routing helpers configuration</span></span>

<span data-ttu-id="f14bf-207">Rozšiřující metoda s názvem `MapSpaFallbackRoute` je používán `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f14bf-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="f14bf-208">Trasy jsou vyhodnocovány v pořadí, ve kterém jsou konfigurovány.</span><span class="sxs-lookup"><span data-stu-id="f14bf-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="f14bf-209">V důsledku toho `default` trasy v předchozím příkladu kódu je nejdříve nepoužije pro porovnávání vzorů.</span><span class="sxs-lookup"><span data-stu-id="f14bf-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="f14bf-210">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="f14bf-210">Create a new project</span></span>

<span data-ttu-id="f14bf-211">Služby JavaScriptu poskytují předem nakonfigurované šablony aplikací.</span><span class="sxs-lookup"><span data-stu-id="f14bf-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="f14bf-212">SpaServices se používá v těchto šablonách společně s různými rozhraními a knihovnami, jako je například úhlová, reakce a Redux.</span><span class="sxs-lookup"><span data-stu-id="f14bf-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="f14bf-213">Tyto šablony lze ji nainstalovat prostřednictvím rozhraní příkazového řádku .NET Core, spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="f14bf-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="f14bf-214">Zobrazí se seznam dostupných šablon SPA:</span><span class="sxs-lookup"><span data-stu-id="f14bf-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="f14bf-215">Šablony</span><span class="sxs-lookup"><span data-stu-id="f14bf-215">Templates</span></span>                                 | <span data-ttu-id="f14bf-216">Krátký název</span><span class="sxs-lookup"><span data-stu-id="f14bf-216">Short Name</span></span> | <span data-ttu-id="f14bf-217">Jazyk</span><span class="sxs-lookup"><span data-stu-id="f14bf-217">Language</span></span> | <span data-ttu-id="f14bf-218">Značky</span><span class="sxs-lookup"><span data-stu-id="f14bf-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="f14bf-219">ASP.NET Core MVC pomocí Angular</span><span class="sxs-lookup"><span data-stu-id="f14bf-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="f14bf-220">Angular</span><span class="sxs-lookup"><span data-stu-id="f14bf-220">angular</span></span>    | <span data-ttu-id="f14bf-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="f14bf-221">[C#]</span></span>     | <span data-ttu-id="f14bf-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="f14bf-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="f14bf-223">MVC ASP.NET Core pomocí React.js</span><span class="sxs-lookup"><span data-stu-id="f14bf-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="f14bf-224">react</span><span class="sxs-lookup"><span data-stu-id="f14bf-224">react</span></span>      | <span data-ttu-id="f14bf-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="f14bf-225">[C#]</span></span>     | <span data-ttu-id="f14bf-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="f14bf-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="f14bf-227">MVC ASP.NET Core pomocí React.js a Redux</span><span class="sxs-lookup"><span data-stu-id="f14bf-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="f14bf-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="f14bf-228">reactredux</span></span> | <span data-ttu-id="f14bf-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="f14bf-229">[C#]</span></span>     | <span data-ttu-id="f14bf-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="f14bf-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="f14bf-231">K vytvoření nového projektu pomocí jedné z šablon SPA, zahrnout **krátký název** šablony v [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz.</span><span class="sxs-lookup"><span data-stu-id="f14bf-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="f14bf-232">Následující příkaz vytvoří aplikaci Angular s ASP.NET Core MVC, která je nakonfigurovaná na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="f14bf-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="f14bf-233">Nastavit režim konfigurace modulu runtime</span><span class="sxs-lookup"><span data-stu-id="f14bf-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="f14bf-234">Existují dva způsoby konfigurace primární runtime:</span><span class="sxs-lookup"><span data-stu-id="f14bf-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="f14bf-235">**Vývoj**:</span><span class="sxs-lookup"><span data-stu-id="f14bf-235">**Development**:</span></span>
  * <span data-ttu-id="f14bf-236">Zahrnuje zdrojových mapování pro usnadnění ladění.</span><span class="sxs-lookup"><span data-stu-id="f14bf-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="f14bf-237">Neoptimalizuje kód na straně klienta pro výkon.</span><span class="sxs-lookup"><span data-stu-id="f14bf-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="f14bf-238">**Produkční**:</span><span class="sxs-lookup"><span data-stu-id="f14bf-238">**Production**:</span></span>
  * <span data-ttu-id="f14bf-239">Vyloučí zdrojových mapování.</span><span class="sxs-lookup"><span data-stu-id="f14bf-239">Excludes source maps.</span></span>
  * <span data-ttu-id="f14bf-240">Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.</span><span class="sxs-lookup"><span data-stu-id="f14bf-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="f14bf-241">ASP.NET Core, používá proměnnou prostředí s názvem `ASPNETCORE_ENVIRONMENT` k uložení režim konfigurace arm.</span><span class="sxs-lookup"><span data-stu-id="f14bf-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="f14bf-242">Další informace najdete v tématu [nastavení prostředí](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="f14bf-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="f14bf-243">Spustit s .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f14bf-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="f14bf-244">Obnovte spuštěním následujícího příkazu v kořenovém adresáři projektu vyžaduje NuGet a npm, balíčků:</span><span class="sxs-lookup"><span data-stu-id="f14bf-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="f14bf-245">Sestavte a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f14bf-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="f14bf-246">Spuštění aplikace v místním hostiteli podle [režim konfigurace modulu runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="f14bf-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="f14bf-247">Přejděte na `http://localhost:5000` v prohlížeči se zobrazí na úvodní stránku.</span><span class="sxs-lookup"><span data-stu-id="f14bf-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="f14bf-248">Spustit se sadou Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="f14bf-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="f14bf-249">Otevřít *.csproj* soubor generovaný nástrojem [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu.</span><span class="sxs-lookup"><span data-stu-id="f14bf-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="f14bf-250">Obnoví požadované balíčky NuGet a npm se automaticky při otevření projektu.</span><span class="sxs-lookup"><span data-stu-id="f14bf-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="f14bf-251">Tento proces obnovení může trvat několik minut a aplikace je připraven ke spuštění až po dokončení.</span><span class="sxs-lookup"><span data-stu-id="f14bf-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="f14bf-252">Klikněte na zelené tlačítko pro spuštění nebo stisknutím klávesy `Ctrl + F5`, a v prohlížeči otevře úvodní stránku aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14bf-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="f14bf-253">Aplikace bude spuštěna na místním hostiteli podle [režim konfigurace modulu runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="f14bf-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="f14bf-254">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="f14bf-254">Test the app</span></span>

<span data-ttu-id="f14bf-255">SpaServices šablony jsou předem nakonfigurované ke spuštění testů na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="f14bf-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="f14bf-256">Jasmine je Oblíbené jednotkových testů pro jazyk JavaScript, zatímco Karma je nástroj test runner pro tyto testy.</span><span class="sxs-lookup"><span data-stu-id="f14bf-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="f14bf-257">Karma je nakonfigurováno pro práci s [Webpacku Dev Middleware](#webpack-dev-middleware) tak, že vývojář není potřeba zastavit a spustit test pokaždé, když dojde ke změně.</span><span class="sxs-lookup"><span data-stu-id="f14bf-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="f14bf-258">Ať už běží s testovacím případem nebo testový případ samotný kód, test se spustí automaticky.</span><span class="sxs-lookup"><span data-stu-id="f14bf-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="f14bf-259">Jako příklad použijeme aplikaci Angular, dva Jasmine testovací případy jsou už k dispozici pro `CounterComponent` v *counter.component.spec.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="f14bf-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="f14bf-260">Otevřete příkazový řádek v *ClientApp* adresáře.</span><span class="sxs-lookup"><span data-stu-id="f14bf-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="f14bf-261">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f14bf-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="f14bf-262">Tento skript spustí nástroj test runner Karma, který čte definované v nastavení *karma.conf.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="f14bf-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="f14bf-263">Kromě jiných nastavení *karma.conf.js* identifikuje testovací soubory, které chcete provést prostřednictvím jeho `files` pole:</span><span class="sxs-lookup"><span data-stu-id="f14bf-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="f14bf-264">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="f14bf-264">Publish the app</span></span>

<span data-ttu-id="f14bf-265">Kombinování vygenerovaných prostředků na straně klienta a publikované artefakty ASP.NET Core do balíčku připravených k nasazení může být náročná.</span><span class="sxs-lookup"><span data-stu-id="f14bf-265">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="f14bf-266">Naštěstí SpaServices orchestruje tento proces celé publikace s vlastní cíl nástroje MSBuild s názvem `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="f14bf-266">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="f14bf-267">Cíl nástroje MSBuild má následující zodpovědnosti:</span><span class="sxs-lookup"><span data-stu-id="f14bf-267">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="f14bf-268">Obnovte balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="f14bf-268">Restore the npm packages.</span></span>
1. <span data-ttu-id="f14bf-269">Vytvoření výrobního sestavení prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f14bf-269">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="f14bf-270">Vytvořte výrobní sestavení vlastních prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f14bf-270">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="f14bf-271">Zkopírujte prostředky vygenerované v sadě Webpack do složky pro publikování.</span><span class="sxs-lookup"><span data-stu-id="f14bf-271">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="f14bf-272">Cíl nástroje MSBuild je vyvolána při spuštění:</span><span class="sxs-lookup"><span data-stu-id="f14bf-272">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="f14bf-273">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f14bf-273">Additional resources</span></span>

* [<span data-ttu-id="f14bf-274">Dokumentace angular</span><span class="sxs-lookup"><span data-stu-id="f14bf-274">Angular Docs</span></span>](https://angular.io/docs)
