---
title: Použití služeb JavaScriptServices vytvářet jednostránkové aplikace ASP.NET Core
author: scottaddie
description: Informace o výhodách použití služeb JavaScriptServices pro vytvoření jedné stránce aplikace (SPA) se opírá o ASP.NET Core.
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 08/02/2017
uid: client-side/spa-services
ms.openlocfilehash: ee772e67ef14608bcc6e3498ade00424ff6090e5
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121373"
---
# <a name="use-javascriptservices-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="d16ba-103">Použití služeb JavaScriptServices vytvářet jednostránkové aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d16ba-103">Use JavaScriptServices to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="d16ba-104">Podle [Scott Addie](https://github.com/scottaddie) a [Fiyaz Hasan](http://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="d16ba-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](http://fiyazhasan.me/)</span></span>

<span data-ttu-id="d16ba-105">Jednu stránku aplikace (SPA) je typ oblíbené webové aplikace z důvodu jeho přináší bohaté uživatelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="d16ba-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="d16ba-106">Integrace rozhraní jednostránková aplikace na straně klienta nebo knihovny, například [Angular](https://angular.io/) nebo [React](https://facebook.github.io/react/), pomocí rozhraní na straně serveru, jako je ASP.NET Core může být obtížné.</span><span class="sxs-lookup"><span data-stu-id="d16ba-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks like ASP.NET Core can be difficult.</span></span> <span data-ttu-id="d16ba-107">[Využitím JavaScriptServices](https://github.com/aspnet/JavaScriptServices) byla vyvinuta omezíte třecí plochy v procesu integrace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-107">[JavaScriptServices](https://github.com/aspnet/JavaScriptServices) was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="d16ba-108">Umožňuje bezproblémové operace mezi různých klientských a serverových sad technologie.</span><span class="sxs-lookup"><span data-stu-id="d16ba-108">It enables seamless operation between the different client and server technology stacks.</span></span>

<a name="what-is-js-services"></a>

## <a name="what-is-javascriptservices"></a><span data-ttu-id="d16ba-109">Co je služeb JavaScriptServices</span><span class="sxs-lookup"><span data-stu-id="d16ba-109">What is JavaScriptServices</span></span>

<span data-ttu-id="d16ba-110">Využitím JavaScriptServices je sada technologií na straně klienta pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d16ba-110">JavaScriptServices is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="d16ba-111">Jeho cílem je na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="d16ba-111">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="d16ba-112">Využitím JavaScriptServices se skládá ze tří různých balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="d16ba-112">JavaScriptServices consists of three distinct NuGet packages:</span></span>

* <span data-ttu-id="d16ba-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="d16ba-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="d16ba-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="d16ba-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>
* <span data-ttu-id="d16ba-115">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span><span class="sxs-lookup"><span data-stu-id="d16ba-115">[Microsoft.AspNetCore.SpaTemplates](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaTemplates/) (SpaTemplates)</span></span>

<span data-ttu-id="d16ba-116">Tyto balíčky jsou užitečné, pokud jste:</span><span class="sxs-lookup"><span data-stu-id="d16ba-116">These packages are useful if you:</span></span>

* <span data-ttu-id="d16ba-117">Spuštění JavaScriptu na serveru</span><span class="sxs-lookup"><span data-stu-id="d16ba-117">Run JavaScript on the server</span></span>
* <span data-ttu-id="d16ba-118">Použít SPA architektury nebo knihovny</span><span class="sxs-lookup"><span data-stu-id="d16ba-118">Use a SPA framework or library</span></span>
* <span data-ttu-id="d16ba-119">Vytvoření prostředků na straně klienta s Webpacku</span><span class="sxs-lookup"><span data-stu-id="d16ba-119">Build client-side assets with Webpack</span></span>

<span data-ttu-id="d16ba-120">Mnoho pozornosti v tomto článku je umístěn na použití SpaServices balíčku.</span><span class="sxs-lookup"><span data-stu-id="d16ba-120">Much of the focus in this article is placed on using the SpaServices package.</span></span>

<a name="what-is-spa-services"></a>

## <a name="what-is-spaservices"></a><span data-ttu-id="d16ba-121">Co je SpaServices</span><span class="sxs-lookup"><span data-stu-id="d16ba-121">What is SpaServices</span></span>

<span data-ttu-id="d16ba-122">SpaServices byl vytvořen na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="d16ba-122">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="d16ba-123">SpaServices není vyžadována k vývoji SPA s ASP.NET Core a nebude vám nutit rozhraní konkrétního klienta.</span><span class="sxs-lookup"><span data-stu-id="d16ba-123">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock you into a particular client framework.</span></span>

<span data-ttu-id="d16ba-124">SpaServices infrastrukturu užitečné jako:</span><span class="sxs-lookup"><span data-stu-id="d16ba-124">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="d16ba-125">Dokončení fáze před vykreslením na straně serveru</span><span class="sxs-lookup"><span data-stu-id="d16ba-125">Server-side prerendering</span></span>](#server-prerendering)
* [<span data-ttu-id="d16ba-126">Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="d16ba-126">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="d16ba-127">Nahrazení horké modulu</span><span class="sxs-lookup"><span data-stu-id="d16ba-127">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="d16ba-128">Směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="d16ba-128">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="d16ba-129">Společně tyto součástí infrastruktury vylepšení pracovního postupu vývoje i prostředí runtime.</span><span class="sxs-lookup"><span data-stu-id="d16ba-129">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="d16ba-130">Součásti může být přijata jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="d16ba-130">The components can be adopted individually.</span></span>

<a name="spa-services-prereqs"></a>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="d16ba-131">Předpoklady pro použití SpaServices</span><span class="sxs-lookup"><span data-stu-id="d16ba-131">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="d16ba-132">Pro práci s SpaServices, nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="d16ba-132">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="d16ba-133">[Node.js](https://nodejs.org/) (verze 6 nebo novější) pomocí npm</span><span class="sxs-lookup"><span data-stu-id="d16ba-133">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>
  * <span data-ttu-id="d16ba-134">Ověření tyto součásti jsou nainstalovány a najdete, spusťte z příkazového řádku následující:</span><span class="sxs-lookup"><span data-stu-id="d16ba-134">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

<span data-ttu-id="d16ba-135">Poznámka: Pokud nasazujete na web Azure, nemusíte dělat nic tady &mdash; Node.js je nainstalováný a dostupný v serverových prostředích.</span><span class="sxs-lookup"><span data-stu-id="d16ba-135">Note: If you're deploying to an Azure web site, you don't need to do anything here &mdash; Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="d16ba-136">Pokud jste na Windows pomocí sady Visual Studio 2017, nainstaluje se sada SDK tak, že vyberete **vývoj pro různé platformy .NET Core** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="d16ba-136">If you're on Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="d16ba-137">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="d16ba-137">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

<a name="server-prerendering"></a>

## <a name="server-side-prerendering"></a><span data-ttu-id="d16ba-138">Dokončení fáze před vykreslením na straně serveru</span><span class="sxs-lookup"><span data-stu-id="d16ba-138">Server-side prerendering</span></span>

<span data-ttu-id="d16ba-139">Univerzální aplikace (označované také jako isomorphic) je schopný běžet současně na serveru a klienta aplikace v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d16ba-139">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="d16ba-140">Angular, React a dalších oblíbených architektur poskytují univerzální platformu pro tento styl vývoje aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-140">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="d16ba-141">Cílem je nejprve vykreslit framework komponent na serveru pomocí Node.js a následně delegovat další spuštění klientovi.</span><span class="sxs-lookup"><span data-stu-id="d16ba-141">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="d16ba-142">ASP.NET Core [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) poskytované SpaServices zjednodušení implementace modulů na straně serveru dokončení fáze před vykreslením vyvoláním funkce jazyka JavaScript na serveru.</span><span class="sxs-lookup"><span data-stu-id="d16ba-142">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d16ba-143">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d16ba-143">Prerequisites</span></span>

<span data-ttu-id="d16ba-144">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="d16ba-144">Install the following:</span></span>

* <span data-ttu-id="d16ba-145">[ASPNET – dokončení fáze před vykreslením](https://www.npmjs.com/package/aspnet-prerendering) balíčku npm:</span><span class="sxs-lookup"><span data-stu-id="d16ba-145">[aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

    ```console
    npm i -S aspnet-prerendering
    ```

### <a name="configuration"></a><span data-ttu-id="d16ba-146">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="d16ba-146">Configuration</span></span>

<span data-ttu-id="d16ba-147">Pomocné rutiny značek se provádí objevitelný prostřednictvím registrace oboru názvů v projektu *_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="d16ba-147">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="d16ba-148">Tyto pomocné rutiny značek abstrakci složitými rozhraními komunikovat přímo s nižší úrovně rozhraní API s využitím syntaxe jazyka HTML v zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="d16ba-148">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="the-asp-prerender-module-tag-helper"></a><span data-ttu-id="d16ba-149">`asp-prerender-module` Pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="d16ba-149">The `asp-prerender-module` Tag Helper</span></span>

<span data-ttu-id="d16ba-150">`asp-prerender-module` Pomocné rutiny značky použité v předchozím příkladu kód provede *ClientApp/dist/main-server.js* na serveru pomocí Node.js.</span><span class="sxs-lookup"><span data-stu-id="d16ba-150">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="d16ba-151">Pro přehlednost společnosti saké *hlavní server.js* je artefakt úlohy transpilation TypeScript pro JavaScript v souboru [Webpacku](http://webpack.github.io/) procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="d16ba-151">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](http://webpack.github.io/) build process.</span></span> <span data-ttu-id="d16ba-152">Webpacku definuje vstupní bod aliasu `main-server`; a procházení grafu závislostí pro tento alias začíná *ClientApp/spouštěcí server.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="d16ba-152">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="d16ba-153">V následujícím příkladu Angular *ClientApp/spouštěcí server.ts* využívá soubor `createServerRenderer` funkce a `RenderResult` typ `aspnet-prerendering` balíček npm ke konfiguraci vykreslování na serveru pomocí Node.js.</span><span class="sxs-lookup"><span data-stu-id="d16ba-153">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="d16ba-154">Značka jazyka HTML, který je určený pro vykreslování na straně serveru je předána volání funkce řešení, která je zabalena v JavaScriptu se silnými typy `Promise` objektu.</span><span class="sxs-lookup"><span data-stu-id="d16ba-154">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="d16ba-155">`Promise` Násobek objektu je, že asynchronně poskytuje kód HTML na stránce pro vkládání v elementu modelu DOM zástupný symbol.</span><span class="sxs-lookup"><span data-stu-id="d16ba-155">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="the-asp-prerender-data-tag-helper"></a><span data-ttu-id="d16ba-156">`asp-prerender-data` Pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="d16ba-156">The `asp-prerender-data` Tag Helper</span></span>

<span data-ttu-id="d16ba-157">Pokud spolu s `asp-prerender-module` pomocné rutiny značky `asp-prerender-data` pomocné rutiny značky lze použít k předání kontextové informace ze zobrazení syntaxe Razor pro jazyk JavaScript na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="d16ba-157">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="d16ba-158">Například následující kód předá data uživatele `main-server` modul:</span><span class="sxs-lookup"><span data-stu-id="d16ba-158">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="d16ba-159">Přijatého `UserName` argument serializován pomocí předdefinovaných serializátor JSON a je uložen v `params.data` objektu.</span><span class="sxs-lookup"><span data-stu-id="d16ba-159">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="d16ba-160">V následujícím příkladu Angular, data se používají k vytvoření přizpůsobené pozdrav v rámci `h1` element:</span><span class="sxs-lookup"><span data-stu-id="d16ba-160">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="d16ba-161">Poznámka: Názvy vlastností, které jsou předány v pomocných rutin značek jsou reprezentovány s **PascalCase** zápis.</span><span class="sxs-lookup"><span data-stu-id="d16ba-161">Note: Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="d16ba-162">Kontrast, JavaScript, kde jsou reprezentovány stejné názvy vlastností **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="d16ba-162">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="d16ba-163">Výchozí konfigurace serializace JSON je zodpovědná za tento rozdíl.</span><span class="sxs-lookup"><span data-stu-id="d16ba-163">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="d16ba-164">Rozbalte na předchozí příklad kódu, data mohou být předána ze serveru do zobrazení podle hydrating `globals` zadanou pro vlastnost `resolve` funkce:</span><span class="sxs-lookup"><span data-stu-id="d16ba-164">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="d16ba-165">`postList` Pole definované uvnitř `globals` objekt je připojen k prohlížeči globální `window` objektu.</span><span class="sxs-lookup"><span data-stu-id="d16ba-165">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="d16ba-166">Tato proměnná zvedání globálním rozsahem eliminuje duplikace úsilí, zvlášť, protože se týkají načítají stejná data jednou na serveru a znovu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d16ba-166">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globální proměnná postList připojen k objektu window](spa-services/_static/global_variable.png)

<a name="webpack-dev-middleware"></a>

## <a name="webpack-dev-middleware"></a><span data-ttu-id="d16ba-168">Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="d16ba-168">Webpack Dev Middleware</span></span>

<span data-ttu-id="d16ba-169">[Dev Middleware Webpacku](https://webpack.github.io/docs/webpack-dev-middleware.html) zavádí zjednodušit vývoj pracovní postup, kterým Webpacku sestavení prostředků na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="d16ba-169">[Webpack Dev Middleware](https://webpack.github.io/docs/webpack-dev-middleware.html) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="d16ba-170">Middleware automaticky zkompiluje a slouží prostředky na straně klienta, pokud stránka opětovném načtení nástroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d16ba-170">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="d16ba-171">Alternativním přístupem je vyvolat ručně Webpacku prostřednictvím skriptu buildu projektu npm při změně závislostí třetích stran nebo vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="d16ba-171">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="d16ba-172">Npm sestavení skriptu *package.json* souboru je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d16ba-172">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="prerequisites"></a><span data-ttu-id="d16ba-173">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d16ba-173">Prerequisites</span></span>

<span data-ttu-id="d16ba-174">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="d16ba-174">Install the following:</span></span>

* <span data-ttu-id="d16ba-175">[ASPNET webpacku](https://www.npmjs.com/package/aspnet-webpack) balíčku npm:</span><span class="sxs-lookup"><span data-stu-id="d16ba-175">[aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

    ```console
    npm i -D aspnet-webpack
    ```

### <a name="configuration"></a><span data-ttu-id="d16ba-176">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="d16ba-176">Configuration</span></span>

<span data-ttu-id="d16ba-177">Webpacku Dev middlewaru je zaregistrovat do kanál požadavků HTTP prostřednictvím následující kód *Startup.cs* souboru `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="d16ba-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="d16ba-178">`UseWebpackDevMiddleware` – Metoda rozšíření musí být volána před [registrace hostování statického souboru](xref:fundamentals/static-files) prostřednictvím `UseStaticFiles` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d16ba-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="d16ba-179">Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="d16ba-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="d16ba-180">*Webpack.config.js* souboru `output.publicPath` vlastnost informuje middleware a sledujte `dist` složku pro změny:</span><span class="sxs-lookup"><span data-stu-id="d16ba-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

<a name="hot-module-replacement"></a>

## <a name="hot-module-replacement"></a><span data-ttu-id="d16ba-181">Nahrazení horké modulu</span><span class="sxs-lookup"><span data-stu-id="d16ba-181">Hot Module Replacement</span></span>

<span data-ttu-id="d16ba-182">Představte si, že na Webpacku [horké nahrazení modulu](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) funkce jako vývojem představ o [Webpacku Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="d16ba-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="d16ba-183">HMR představuje stejné výhody, ale další zjednodušuje pracovní postup vývoje pomocí automaticky aktualizuje obsah stránky po kompilaci změn.</span><span class="sxs-lookup"><span data-stu-id="d16ba-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="d16ba-184">Tento nástroj nezaměnili při aktualizaci prohlížeče, které by mohly narušit aktuálního stavu v paměti a ladicí relace tato jednostránková aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="d16ba-185">Je aktivní propojení mezi službou Webpacku Dev Middleware a prohlížeče, což znamená, že změny jsou vloženy do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d16ba-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d16ba-186">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d16ba-186">Prerequisites</span></span>

<span data-ttu-id="d16ba-187">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="d16ba-187">Install the following:</span></span>

* <span data-ttu-id="d16ba-188">[webpacku horkou middleware](https://www.npmjs.com/package/webpack-hot-middleware) balíčku npm:</span><span class="sxs-lookup"><span data-stu-id="d16ba-188">[webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

    ```console
    npm i -D webpack-hot-middleware
    ```

### <a name="configuration"></a><span data-ttu-id="d16ba-189">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="d16ba-189">Configuration</span></span>

<span data-ttu-id="d16ba-190">Komponenta HMR musí být zaregistrovaný do kanál požadavků HTTP pro MVC v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="d16ba-190">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="d16ba-191">Stejně jako dřív platilo s [Webpacku Dev Middleware](#webpack-dev-middleware), `UseWebpackDevMiddleware` – metoda rozšíření musí být volána před `UseStaticFiles` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d16ba-191">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="d16ba-192">Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="d16ba-192">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="d16ba-193">*Webpack.config.js* soubor musí definovat `plugins` i v případě, že je ponecháno prázdné pole:</span><span class="sxs-lookup"><span data-stu-id="d16ba-193">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="d16ba-194">Po načtení aplikace v prohlížeči, poskytuje nástroje pro vývojáře kartu konzoly pro potvrzení HMR aktivace:</span><span class="sxs-lookup"><span data-stu-id="d16ba-194">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Horké modulu nahrazení připojené zprávy](spa-services/_static/hmr_connected.png)

<a name="routing-helpers"></a>

## <a name="routing-helpers"></a><span data-ttu-id="d16ba-196">Směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="d16ba-196">Routing helpers</span></span>

<span data-ttu-id="d16ba-197">Ve většině SPA založené na ASP.NET Core měli byste na straně klienta směrování kromě směrování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="d16ba-197">In most ASP.NET Core-based SPAs, you'll want client-side routing in addition to server-side routing.</span></span> <span data-ttu-id="d16ba-198">Jednostránková aplikace a MVC směrovací systémy může pracovat nezávisle na bez rušení.</span><span class="sxs-lookup"><span data-stu-id="d16ba-198">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="d16ba-199">Existuje, ale jeden okraj případu představující vyzve: identifikace obdržíte kód odpovědi HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="d16ba-199">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="d16ba-200">Vezměte v úvahu scénář, ve kterém bez přípony trasu `/some/page` se používá.</span><span class="sxs-lookup"><span data-stu-id="d16ba-200">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="d16ba-201">Předpokládejme, požadavek nebude – porovnávací trasu na straně serveru, ale jeho vzor neshoduje trasu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d16ba-201">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="d16ba-202">Teď se podíváme příchozí žádosti pro `/images/user-512.png`, což obecně očekává, že se najít soubor obrázku na serveru.</span><span class="sxs-lookup"><span data-stu-id="d16ba-202">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="d16ba-203">Pokud tuto cestu požadovaného prostředku neodpovídá žádné trasu na straně serveru nebo statický soubor, je pravděpodobné, že aplikace na straně klienta by ji zpracovat – obecně chcete vrátit stavový kód HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="d16ba-203">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it — you generally want to return a 404 HTTP status code.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="d16ba-204">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d16ba-204">Prerequisites</span></span>

<span data-ttu-id="d16ba-205">Nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="d16ba-205">Install the following:</span></span>

* <span data-ttu-id="d16ba-206">Npm package směrování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d16ba-206">The client-side routing npm package.</span></span> <span data-ttu-id="d16ba-207">Jako příklad použijeme Angular:</span><span class="sxs-lookup"><span data-stu-id="d16ba-207">Using Angular as an example:</span></span>

    ```console
    npm i -S @angular/router
    ```

### <a name="configuration"></a><span data-ttu-id="d16ba-208">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="d16ba-208">Configuration</span></span>

<span data-ttu-id="d16ba-209">Rozšiřující metoda s názvem `MapSpaFallbackRoute` je používán `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="d16ba-209">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="d16ba-210">Tip: Trasy se vyhodnocují v pořadí, ve kterém jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="d16ba-210">Tip: Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="d16ba-211">V důsledku toho `default` trasy v předchozím příkladu kódu je nejdříve nepoužije pro porovnávání vzorů.</span><span class="sxs-lookup"><span data-stu-id="d16ba-211">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

<a name="new-project-creation"></a>

## <a name="creating-a-new-project"></a><span data-ttu-id="d16ba-212">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="d16ba-212">Creating a new project</span></span>

<span data-ttu-id="d16ba-213">Využitím JavaScriptServices poskytuje šablony předem nakonfigurované aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-213">JavaScriptServices provides pre-configured application templates.</span></span> <span data-ttu-id="d16ba-214">Tyto šablony ve spojení s jinou architektury a knihovny, například Angular, React nebo Reduxem SpaServices používá.</span><span class="sxs-lookup"><span data-stu-id="d16ba-214">SpaServices is used in these templates, in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="d16ba-215">Tyto šablony lze ji nainstalovat prostřednictvím rozhraní příkazového řádku .NET Core, spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d16ba-215">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="d16ba-216">Zobrazí se seznam dostupných šablon SPA:</span><span class="sxs-lookup"><span data-stu-id="d16ba-216">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="d16ba-217">Šablony</span><span class="sxs-lookup"><span data-stu-id="d16ba-217">Templates</span></span>                                 | <span data-ttu-id="d16ba-218">Krátký název</span><span class="sxs-lookup"><span data-stu-id="d16ba-218">Short Name</span></span> | <span data-ttu-id="d16ba-219">Jazyk</span><span class="sxs-lookup"><span data-stu-id="d16ba-219">Language</span></span> | <span data-ttu-id="d16ba-220">Značky</span><span class="sxs-lookup"><span data-stu-id="d16ba-220">Tags</span></span>        |
|:------------------------------------------|:-----------|:---------|:------------|
| <span data-ttu-id="d16ba-221">ASP.NET Core MVC pomocí Angular</span><span class="sxs-lookup"><span data-stu-id="d16ba-221">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="d16ba-222">Angular</span><span class="sxs-lookup"><span data-stu-id="d16ba-222">angular</span></span>    | <span data-ttu-id="d16ba-223">[C#]</span><span class="sxs-lookup"><span data-stu-id="d16ba-223">[C#]</span></span>     | <span data-ttu-id="d16ba-224">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="d16ba-224">Web/MVC/SPA</span></span> |
| <span data-ttu-id="d16ba-225">MVC ASP.NET Core pomocí React.js</span><span class="sxs-lookup"><span data-stu-id="d16ba-225">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="d16ba-226">react</span><span class="sxs-lookup"><span data-stu-id="d16ba-226">react</span></span>      | <span data-ttu-id="d16ba-227">[C#]</span><span class="sxs-lookup"><span data-stu-id="d16ba-227">[C#]</span></span>     | <span data-ttu-id="d16ba-228">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="d16ba-228">Web/MVC/SPA</span></span> |
| <span data-ttu-id="d16ba-229">MVC ASP.NET Core pomocí React.js a Redux</span><span class="sxs-lookup"><span data-stu-id="d16ba-229">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="d16ba-230">reactredux</span><span class="sxs-lookup"><span data-stu-id="d16ba-230">reactredux</span></span> | <span data-ttu-id="d16ba-231">[C#]</span><span class="sxs-lookup"><span data-stu-id="d16ba-231">[C#]</span></span>     | <span data-ttu-id="d16ba-232">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="d16ba-232">Web/MVC/SPA</span></span> |

<span data-ttu-id="d16ba-233">K vytvoření nového projektu pomocí jedné z šablon SPA, zahrnout **krátký název** šablony v [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz.</span><span class="sxs-lookup"><span data-stu-id="d16ba-233">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="d16ba-234">Následující příkaz vytvoří aplikaci Angular s ASP.NET Core MVC, která je nakonfigurovaná na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="d16ba-234">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```console
dotnet new angular
```

<a name="runtime-config-mode"></a>

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="d16ba-235">Nastavit režim konfigurace modulu runtime</span><span class="sxs-lookup"><span data-stu-id="d16ba-235">Set the runtime configuration mode</span></span>

<span data-ttu-id="d16ba-236">Existují dva způsoby konfigurace primární runtime:</span><span class="sxs-lookup"><span data-stu-id="d16ba-236">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="d16ba-237">**Vývoj**:</span><span class="sxs-lookup"><span data-stu-id="d16ba-237">**Development**:</span></span>
  * <span data-ttu-id="d16ba-238">Zahrnuje zdrojových mapování pro usnadnění ladění.</span><span class="sxs-lookup"><span data-stu-id="d16ba-238">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="d16ba-239">Neoptimalizuje kód na straně klienta pro výkon.</span><span class="sxs-lookup"><span data-stu-id="d16ba-239">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="d16ba-240">**Produkční**:</span><span class="sxs-lookup"><span data-stu-id="d16ba-240">**Production**:</span></span>
  * <span data-ttu-id="d16ba-241">Vyloučí zdrojových mapování.</span><span class="sxs-lookup"><span data-stu-id="d16ba-241">Excludes source maps.</span></span>
  * <span data-ttu-id="d16ba-242">Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-242">Optimizes the client-side code via bundling & minification.</span></span>

<span data-ttu-id="d16ba-243">ASP.NET Core, používá proměnnou prostředí s názvem `ASPNETCORE_ENVIRONMENT` k uložení režim konfigurace arm.</span><span class="sxs-lookup"><span data-stu-id="d16ba-243">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="d16ba-244">Zobrazit **[nastavte prostředí](xref:fundamentals/environments#set-the-environment)** Další informace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-244">See **[Set the environment](xref:fundamentals/environments#set-the-environment)** for more information.</span></span>

### <a name="running-with-net-core-cli"></a><span data-ttu-id="d16ba-245">Pomocí rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d16ba-245">Running with .NET Core CLI</span></span>

<span data-ttu-id="d16ba-246">Obnovte spuštěním následujícího příkazu v kořenovém adresáři projektu vyžaduje NuGet a npm, balíčků:</span><span class="sxs-lookup"><span data-stu-id="d16ba-246">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```console
dotnet restore && npm i
```

<span data-ttu-id="d16ba-247">Sestavte a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d16ba-247">Build and run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="d16ba-248">Spuštění aplikace v místním hostiteli podle [režim konfigurace modulu runtime](#runtime-config-mode).</span><span class="sxs-lookup"><span data-stu-id="d16ba-248">The application starts on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span> <span data-ttu-id="d16ba-249">Přejděte na `http://localhost:5000` v prohlížeči se zobrazí na úvodní stránku.</span><span class="sxs-lookup"><span data-stu-id="d16ba-249">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="running-with-visual-studio-2017"></a><span data-ttu-id="d16ba-250">Pomocí sady Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="d16ba-250">Running with Visual Studio 2017</span></span>

<span data-ttu-id="d16ba-251">Otevřít *.csproj* soubor generovaný nástrojem [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu.</span><span class="sxs-lookup"><span data-stu-id="d16ba-251">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="d16ba-252">Obnoví požadované balíčky NuGet a npm se automaticky při otevření projektu.</span><span class="sxs-lookup"><span data-stu-id="d16ba-252">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="d16ba-253">Tento proces obnovení může trvat několik minut a aplikace je připraven ke spuštění až po dokončení.</span><span class="sxs-lookup"><span data-stu-id="d16ba-253">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="d16ba-254">Klikněte na zelené tlačítko pro spuštění nebo stisknutím klávesy `Ctrl + F5`, a v prohlížeči otevře úvodní stránku aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16ba-254">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="d16ba-255">Aplikace bude spuštěna na místním hostiteli podle [režim konfigurace modulu runtime](#runtime-config-mode).</span><span class="sxs-lookup"><span data-stu-id="d16ba-255">The application runs on localhost according to the [runtime configuration mode](#runtime-config-mode).</span></span>

<a name="app-testing"></a>

## <a name="testing-the-app"></a><span data-ttu-id="d16ba-256">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="d16ba-256">Testing the app</span></span>

<span data-ttu-id="d16ba-257">SpaServices šablony jsou předem nakonfigurované ke spuštění testů na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="d16ba-257">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="d16ba-258">Jasmine je Oblíbené jednotkových testů pro jazyk JavaScript, zatímco Karma je nástroj test runner pro tyto testy.</span><span class="sxs-lookup"><span data-stu-id="d16ba-258">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="d16ba-259">Karma je nakonfigurováno pro práci s [Webpacku Dev Middleware](#webpack-dev-middleware) tak, že vývojář není potřeba zastavit a spustit test pokaždé, když dojde ke změně.</span><span class="sxs-lookup"><span data-stu-id="d16ba-259">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="d16ba-260">Ať už běží s testovacím případem nebo testový případ samotný kód, test se spustí automaticky.</span><span class="sxs-lookup"><span data-stu-id="d16ba-260">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="d16ba-261">Jako příklad použijeme aplikaci Angular, dva Jasmine testovací případy jsou už k dispozici pro `CounterComponent` v *counter.component.spec.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="d16ba-261">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="d16ba-262">Otevřete příkazový řádek v *ClientApp* adresáře.</span><span class="sxs-lookup"><span data-stu-id="d16ba-262">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="d16ba-263">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d16ba-263">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="d16ba-264">Tento skript spustí nástroj test runner Karma, který čte definované v nastavení *karma.conf.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="d16ba-264">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="d16ba-265">Kromě jiných nastavení *karma.conf.js* identifikuje testovací soubory, které chcete provést prostřednictvím jeho `files` pole:</span><span class="sxs-lookup"><span data-stu-id="d16ba-265">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

<a name="app-publishing"></a>

## <a name="publishing-the-application"></a><span data-ttu-id="d16ba-266">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="d16ba-266">Publishing the application</span></span>

<span data-ttu-id="d16ba-267">Kombinování vygenerovaných prostředků na straně klienta a publikované artefakty ASP.NET Core do balíčku připravených k nasazení může být náročná.</span><span class="sxs-lookup"><span data-stu-id="d16ba-267">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="d16ba-268">Naštěstí SpaServices orchestruje tento proces celé publikace s vlastní cíl nástroje MSBuild s názvem `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="d16ba-268">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="d16ba-269">Cíl nástroje MSBuild má následující zodpovědnosti:</span><span class="sxs-lookup"><span data-stu-id="d16ba-269">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="d16ba-270">Obnovení balíčků npm</span><span class="sxs-lookup"><span data-stu-id="d16ba-270">Restore the npm packages</span></span>
1. <span data-ttu-id="d16ba-271">Vytvoření produkčních sestavení prostředků třetích stran, na straně klienta</span><span class="sxs-lookup"><span data-stu-id="d16ba-271">Create a production-grade build of the third-party, client-side assets</span></span>
1. <span data-ttu-id="d16ba-272">Vytvoření produkčních sestavení vlastních prostředků na straně klienta</span><span class="sxs-lookup"><span data-stu-id="d16ba-272">Create a production-grade build of the custom client-side assets</span></span>
1. <span data-ttu-id="d16ba-273">Kopírování generované Webpacku prostředky do složky publikovat</span><span class="sxs-lookup"><span data-stu-id="d16ba-273">Copy the Webpack-generated assets to the publish folder</span></span>

<span data-ttu-id="d16ba-274">Cíl nástroje MSBuild je vyvolána při spuštění:</span><span class="sxs-lookup"><span data-stu-id="d16ba-274">The MSBuild target is invoked when running:</span></span>

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="d16ba-275">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d16ba-275">Additional resources</span></span>

* [<span data-ttu-id="d16ba-276">Dokumentace angular</span><span class="sxs-lookup"><span data-stu-id="d16ba-276">Angular Docs</span></span>](https://angular.io/docs)
