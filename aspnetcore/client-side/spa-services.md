---
title: Použití služeb jazyka JavaScript vytvořte jednostránkové aplikace v ASP.NET Core
author: scottaddie
description: Přečtěte si o výhodách používání služby jazyka JavaScript vytvořte jedné stránce aplikace (SPA) se opírá o ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 05/28/2019
uid: client-side/spa-services
ms.openlocfilehash: 19710b58bca606d21feda9069ad00edd1e4f72e9
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813476"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="67636-103">Použití služeb jazyka JavaScript vytvořte jednostránkové aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67636-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="67636-104">Podle [Scott Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="67636-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="67636-105">Jednu stránku aplikace (SPA) je typ oblíbené webové aplikace z důvodu jeho přináší bohaté uživatelské prostředí.</span><span class="sxs-lookup"><span data-stu-id="67636-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="67636-106">Integrace rozhraní jednostránková aplikace na straně klienta nebo knihovny, například [Angular](https://angular.io/) nebo [React](https://facebook.github.io/react/), pomocí rozhraní na straně serveru, jako jsou ASP.NET Core může být obtížné.</span><span class="sxs-lookup"><span data-stu-id="67636-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="67636-107">Omezíte třecí plochy v procesu integrace byla vyvinuta služby jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="67636-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="67636-108">Umožňuje bezproblémové operace mezi různých klientských a serverových sad technologie.</span><span class="sxs-lookup"><span data-stu-id="67636-108">It enables seamless operation between the different client and server technology stacks.</span></span>

## <a name="what-is-javascript-services"></a><span data-ttu-id="67636-109">Co je služba jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="67636-109">What is JavaScript Services</span></span>

<span data-ttu-id="67636-110">Služby jazyka JavaScript je sada technologií na straně klienta pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67636-110">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="67636-111">Jeho cílem je na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="67636-111">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="67636-112">Služba jazyka JavaScript se skládá ze dvou různých balíčcích NuGet:</span><span class="sxs-lookup"><span data-stu-id="67636-112">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="67636-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="67636-113">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="67636-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="67636-114">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="67636-115">Tyto balíčky jsou užitečné v následujících scénářích:</span><span class="sxs-lookup"><span data-stu-id="67636-115">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="67636-116">Spuštění JavaScriptu na serveru</span><span class="sxs-lookup"><span data-stu-id="67636-116">Run JavaScript on the server</span></span>
* <span data-ttu-id="67636-117">Použít SPA architektury nebo knihovny</span><span class="sxs-lookup"><span data-stu-id="67636-117">Use a SPA framework or library</span></span>
* <span data-ttu-id="67636-118">Vytvoření prostředků na straně klienta s Webpacku</span><span class="sxs-lookup"><span data-stu-id="67636-118">Build client-side assets with Webpack</span></span>

<span data-ttu-id="67636-119">Mnoho pozornosti v tomto článku je umístěn na použití SpaServices balíčku.</span><span class="sxs-lookup"><span data-stu-id="67636-119">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="67636-120">Co je SpaServices</span><span class="sxs-lookup"><span data-stu-id="67636-120">What is SpaServices</span></span>

<span data-ttu-id="67636-121">SpaServices byl vytvořen na pozici ASP.NET Core, jako preferovanou platformu na straně serveru vývojářů pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="67636-121">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="67636-122">SpaServices není vyžadována k vývoji SPA s ASP.NET Core a nezamyká vývojáře do rozhraní konkrétního klienta.</span><span class="sxs-lookup"><span data-stu-id="67636-122">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="67636-123">SpaServices infrastrukturu užitečné jako:</span><span class="sxs-lookup"><span data-stu-id="67636-123">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="67636-124">Dokončení fáze před vykreslením na straně serveru</span><span class="sxs-lookup"><span data-stu-id="67636-124">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="67636-125">Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="67636-125">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="67636-126">Nahrazení horké modulu</span><span class="sxs-lookup"><span data-stu-id="67636-126">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="67636-127">Směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="67636-127">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="67636-128">Společně tyto součástí infrastruktury vylepšení pracovního postupu vývoje i prostředí runtime.</span><span class="sxs-lookup"><span data-stu-id="67636-128">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="67636-129">Součásti může být přijata jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="67636-129">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="67636-130">Předpoklady pro použití SpaServices</span><span class="sxs-lookup"><span data-stu-id="67636-130">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="67636-131">Pro práci s SpaServices, nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="67636-131">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="67636-132">[Node.js](https://nodejs.org/) (verze 6 nebo novější) pomocí npm</span><span class="sxs-lookup"><span data-stu-id="67636-132">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="67636-133">Ověření tyto součásti jsou nainstalovány a najdete, spusťte z příkazového řádku následující:</span><span class="sxs-lookup"><span data-stu-id="67636-133">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="67636-134">Pokud nasazení na web Azure, nemusíte nic dělat&mdash;Node.js je nainstalováný a dostupný v serverových prostředích.</span><span class="sxs-lookup"><span data-stu-id="67636-134">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="67636-135">Ve Windows pomocí sady Visual Studio 2017, je nainstalován sadu SDK tak, že vyberete **vývoj pro různé platformy .NET Core** pracovního vytížení.</span><span class="sxs-lookup"><span data-stu-id="67636-135">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="67636-136">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) balíček NuGet</span><span class="sxs-lookup"><span data-stu-id="67636-136">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="67636-137">Dokončení fáze před vykreslením na straně serveru</span><span class="sxs-lookup"><span data-stu-id="67636-137">Server-side prerendering</span></span>

<span data-ttu-id="67636-138">Univerzální aplikace (označované také jako isomorphic) je schopný běžet současně na serveru a klienta aplikace v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="67636-138">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="67636-139">Angular, React a dalších oblíbených architektur poskytují univerzální platformu pro tento styl vývoje aplikace.</span><span class="sxs-lookup"><span data-stu-id="67636-139">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="67636-140">Cílem je nejprve vykreslit framework komponent na serveru pomocí Node.js a následně delegovat další spuštění klientovi.</span><span class="sxs-lookup"><span data-stu-id="67636-140">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="67636-141">ASP.NET Core [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) poskytované SpaServices zjednodušení implementace modulů na straně serveru dokončení fáze před vykreslením vyvoláním funkce jazyka JavaScript na serveru.</span><span class="sxs-lookup"><span data-stu-id="67636-141">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="67636-142">Před vykreslením požadavky na straně serveru</span><span class="sxs-lookup"><span data-stu-id="67636-142">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="67636-143">Nainstalujte [aspnet – dokončení fáze před vykreslením](https://www.npmjs.com/package/aspnet-prerendering) balíčku npm:</span><span class="sxs-lookup"><span data-stu-id="67636-143">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="67636-144">Před vykreslením konfigurace na straně serveru</span><span class="sxs-lookup"><span data-stu-id="67636-144">Server-side prerendering configuration</span></span>

<span data-ttu-id="67636-145">Pomocné rutiny značek se provádí objevitelný prostřednictvím registrace oboru názvů v projektu *_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="67636-145">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="67636-146">Tyto pomocné rutiny značek abstrakci složitými rozhraními komunikovat přímo s nižší úrovně rozhraní API s využitím syntaxe jazyka HTML v zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="67636-146">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="67636-147">Pomocná rutina značky prostředí ASP. provedením operace prerender modulu</span><span class="sxs-lookup"><span data-stu-id="67636-147">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="67636-148">`asp-prerender-module` Pomocné rutiny značky použité v předchozím příkladu kód provede *ClientApp/dist/main-server.js* na serveru pomocí Node.js.</span><span class="sxs-lookup"><span data-stu-id="67636-148">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="67636-149">Pro přehlednost společnosti saké *hlavní server.js* je artefakt úlohy transpilation TypeScript pro JavaScript v souboru [Webpacku](https://webpack.github.io/) procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="67636-149">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="67636-150">Webpacku definuje vstupní bod aliasu `main-server`; a procházení grafu závislostí pro tento alias začíná *ClientApp/spouštěcí server.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="67636-150">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="67636-151">V následujícím příkladu Angular *ClientApp/spouštěcí server.ts* využívá soubor `createServerRenderer` funkce a `RenderResult` typ `aspnet-prerendering` balíček npm ke konfiguraci vykreslování na serveru pomocí Node.js.</span><span class="sxs-lookup"><span data-stu-id="67636-151">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="67636-152">Značka jazyka HTML, který je určený pro vykreslování na straně serveru je předána volání funkce řešení, která je zabalena v JavaScriptu se silnými typy `Promise` objektu.</span><span class="sxs-lookup"><span data-stu-id="67636-152">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="67636-153">`Promise` Násobek objektu je, že asynchronně poskytuje kód HTML na stránce pro vkládání v elementu modelu DOM zástupný symbol.</span><span class="sxs-lookup"><span data-stu-id="67636-153">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="67636-154">Pomocná rutina značky prostředí ASP. provedením operace prerender dat</span><span class="sxs-lookup"><span data-stu-id="67636-154">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="67636-155">Pokud spolu s `asp-prerender-module` pomocné rutiny značky `asp-prerender-data` pomocné rutiny značky lze použít k předání kontextové informace ze zobrazení syntaxe Razor pro jazyk JavaScript na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="67636-155">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="67636-156">Například následující kód předá data uživatele `main-server` modul:</span><span class="sxs-lookup"><span data-stu-id="67636-156">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="67636-157">Přijatého `UserName` argument serializován pomocí předdefinovaných serializátor JSON a je uložen v `params.data` objektu.</span><span class="sxs-lookup"><span data-stu-id="67636-157">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="67636-158">V následujícím příkladu Angular, data se používají k vytvoření přizpůsobené pozdrav v rámci `h1` element:</span><span class="sxs-lookup"><span data-stu-id="67636-158">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="67636-159">Názvy vlastností, které jsou předány v pomocných rutin značek jsou reprezentovány s **PascalCase** zápis.</span><span class="sxs-lookup"><span data-stu-id="67636-159">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="67636-160">Kontrast, JavaScript, kde jsou reprezentovány stejné názvy vlastností **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="67636-160">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="67636-161">Výchozí konfigurace serializace JSON je zodpovědná za tento rozdíl.</span><span class="sxs-lookup"><span data-stu-id="67636-161">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="67636-162">Rozbalte na předchozí příklad kódu, data mohou být předána ze serveru do zobrazení podle hydrating `globals` zadanou pro vlastnost `resolve` funkce:</span><span class="sxs-lookup"><span data-stu-id="67636-162">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="67636-163">`postList` Pole definované uvnitř `globals` objekt je připojen k prohlížeči globální `window` objektu.</span><span class="sxs-lookup"><span data-stu-id="67636-163">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="67636-164">Tato proměnná zvedání globálním rozsahem eliminuje duplikace úsilí, zvlášť, protože se týkají načítají stejná data jednou na serveru a znovu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="67636-164">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globální proměnná postList připojen k objektu window](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="67636-166">Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="67636-166">Webpack Dev Middleware</span></span>

<span data-ttu-id="67636-167">[Dev Middleware Webpacku](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) zavádí zjednodušit vývoj pracovní postup, kterým Webpacku sestavení prostředků na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="67636-167">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="67636-168">Middleware automaticky zkompiluje a slouží prostředky na straně klienta, pokud stránka opětovném načtení nástroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="67636-168">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="67636-169">Alternativním přístupem je vyvolat ručně Webpacku prostřednictvím skriptu buildu projektu npm při změně závislostí třetích stran nebo vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="67636-169">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="67636-170">Npm sestavení skriptu *package.json* souboru je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="67636-170">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="67636-171">Middleware Webpacku vývoj požadavků</span><span class="sxs-lookup"><span data-stu-id="67636-171">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="67636-172">Nainstalujte [aspnet webpacku](https://www.npmjs.com/package/aspnet-webpack) balíčku npm:</span><span class="sxs-lookup"><span data-stu-id="67636-172">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="67636-173">Konfigurace Webpacku Dev middlewaru</span><span class="sxs-lookup"><span data-stu-id="67636-173">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="67636-174">Webpacku Dev middlewaru je zaregistrovat do kanál požadavků HTTP prostřednictvím následující kód *Startup.cs* souboru `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="67636-174">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="67636-175">`UseWebpackDevMiddleware` – Metoda rozšíření musí být volána před [registrace hostování statického souboru](xref:fundamentals/static-files) prostřednictvím `UseStaticFiles` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="67636-175">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="67636-176">Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="67636-176">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="67636-177">*Webpack.config.js* souboru `output.publicPath` vlastnost informuje middleware a sledujte `dist` složku pro změny:</span><span class="sxs-lookup"><span data-stu-id="67636-177">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="67636-178">Nahrazení horké modulu</span><span class="sxs-lookup"><span data-stu-id="67636-178">Hot Module Replacement</span></span>

<span data-ttu-id="67636-179">Představte si, že na Webpacku [horké nahrazení modulu](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) funkce jako vývojem představ o [Webpacku Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="67636-179">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="67636-180">HMR představuje stejné výhody, ale další zjednodušuje pracovní postup vývoje pomocí automaticky aktualizuje obsah stránky po kompilaci změn.</span><span class="sxs-lookup"><span data-stu-id="67636-180">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="67636-181">Tento nástroj nezaměnili při aktualizaci prohlížeče, které by mohly narušit aktuálního stavu v paměti a ladicí relace tato jednostránková aplikace.</span><span class="sxs-lookup"><span data-stu-id="67636-181">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="67636-182">Je aktivní propojení mezi službou Webpacku Dev Middleware a prohlížeče, což znamená, že změny jsou vloženy do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="67636-182">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="67636-183">Aktivní požadavky modulu nahrazení</span><span class="sxs-lookup"><span data-stu-id="67636-183">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="67636-184">Nainstalujte [webpacku horkou middleware](https://www.npmjs.com/package/webpack-hot-middleware) balíčku npm:</span><span class="sxs-lookup"><span data-stu-id="67636-184">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="67636-185">Aktivní konfigurace modulu nahrazení</span><span class="sxs-lookup"><span data-stu-id="67636-185">Hot Module Replacement configuration</span></span>

<span data-ttu-id="67636-186">Komponenta HMR musí být zaregistrovaný do kanál požadavků HTTP pro MVC v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="67636-186">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="67636-187">Stejně jako dřív platilo s [Webpacku Dev Middleware](#webpack-dev-middleware), `UseWebpackDevMiddleware` – metoda rozšíření musí být volána před `UseStaticFiles` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="67636-187">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="67636-188">Z bezpečnostních důvodů se zaregistrujte middleware pouze v případě, že aplikace běží v režimu pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="67636-188">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="67636-189">*Webpack.config.js* soubor musí definovat `plugins` i v případě, že je ponecháno prázdné pole:</span><span class="sxs-lookup"><span data-stu-id="67636-189">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="67636-190">Po načtení aplikace v prohlížeči, poskytuje nástroje pro vývojáře kartu konzoly pro potvrzení HMR aktivace:</span><span class="sxs-lookup"><span data-stu-id="67636-190">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Horké modulu nahrazení připojené zprávy](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="67636-192">Směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="67636-192">Routing helpers</span></span>

<span data-ttu-id="67636-193">Ve většině SPA založené na ASP.NET Core směrování na straně klienta se často vyžaduje kromě směrování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="67636-193">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="67636-194">Jednostránková aplikace a MVC směrovací systémy může pracovat nezávisle na bez rušení.</span><span class="sxs-lookup"><span data-stu-id="67636-194">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="67636-195">Existuje, ale jeden okraj případu představující vyzve: identifikace obdržíte kód odpovědi HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="67636-195">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="67636-196">Vezměte v úvahu scénář, ve kterém bez přípony trasu `/some/page` se používá.</span><span class="sxs-lookup"><span data-stu-id="67636-196">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="67636-197">Předpokládejme, požadavek nebude – porovnávací trasu na straně serveru, ale jeho vzor neshoduje trasu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="67636-197">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="67636-198">Teď se podíváme příchozí žádosti pro `/images/user-512.png`, což obecně očekává, že se najít soubor obrázku na serveru.</span><span class="sxs-lookup"><span data-stu-id="67636-198">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="67636-199">Pokud tuto cestu požadovaného prostředku neodpovídá žádné trasu na straně serveru nebo statický soubor, je pravděpodobné, že aplikace na straně klienta by ji zpracovat&mdash;obvykle vrátí stavový kód HTTP 404 je žádoucí.</span><span class="sxs-lookup"><span data-stu-id="67636-199">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="67636-200">Požadavky směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="67636-200">Routing helpers prerequisites</span></span>

<span data-ttu-id="67636-201">Nainstalujte balíček npm směrování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="67636-201">Install the client-side routing npm package.</span></span> <span data-ttu-id="67636-202">Jako příklad použijeme Angular:</span><span class="sxs-lookup"><span data-stu-id="67636-202">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="67636-203">Konfigurace směrování pomocné rutiny</span><span class="sxs-lookup"><span data-stu-id="67636-203">Routing helpers configuration</span></span>

<span data-ttu-id="67636-204">Rozšiřující metoda s názvem `MapSpaFallbackRoute` je používán `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="67636-204">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="67636-205">Trasy se vyhodnocují v pořadí, ve kterém jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="67636-205">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="67636-206">V důsledku toho `default` trasy v předchozím příkladu kódu je nejdříve nepoužije pro porovnávání vzorů.</span><span class="sxs-lookup"><span data-stu-id="67636-206">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="67636-207">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="67636-207">Create a new project</span></span>

<span data-ttu-id="67636-208">JavaScript služby poskytují předem nakonfigurované aplikace šablony.</span><span class="sxs-lookup"><span data-stu-id="67636-208">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="67636-209">Tyto šablony ve spojení s jinou architektury a knihovny, například Angular, React nebo Reduxem SpaServices používá.</span><span class="sxs-lookup"><span data-stu-id="67636-209">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="67636-210">Tyto šablony lze ji nainstalovat prostřednictvím rozhraní příkazového řádku .NET Core, spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="67636-210">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```console
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="67636-211">Zobrazí se seznam dostupných šablon SPA:</span><span class="sxs-lookup"><span data-stu-id="67636-211">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="67636-212">Šablony</span><span class="sxs-lookup"><span data-stu-id="67636-212">Templates</span></span>                                 | <span data-ttu-id="67636-213">Krátký název</span><span class="sxs-lookup"><span data-stu-id="67636-213">Short Name</span></span> | <span data-ttu-id="67636-214">Jazyk</span><span class="sxs-lookup"><span data-stu-id="67636-214">Language</span></span> | <span data-ttu-id="67636-215">Značky</span><span class="sxs-lookup"><span data-stu-id="67636-215">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="67636-216">ASP.NET Core MVC pomocí Angular</span><span class="sxs-lookup"><span data-stu-id="67636-216">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="67636-217">Angular</span><span class="sxs-lookup"><span data-stu-id="67636-217">angular</span></span>    | <span data-ttu-id="67636-218">[C#]</span><span class="sxs-lookup"><span data-stu-id="67636-218">[C#]</span></span>     | <span data-ttu-id="67636-219">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="67636-219">Web/MVC/SPA</span></span> |
| <span data-ttu-id="67636-220">MVC ASP.NET Core pomocí React.js</span><span class="sxs-lookup"><span data-stu-id="67636-220">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="67636-221">react</span><span class="sxs-lookup"><span data-stu-id="67636-221">react</span></span>      | <span data-ttu-id="67636-222">[C#]</span><span class="sxs-lookup"><span data-stu-id="67636-222">[C#]</span></span>     | <span data-ttu-id="67636-223">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="67636-223">Web/MVC/SPA</span></span> |
| <span data-ttu-id="67636-224">MVC ASP.NET Core pomocí React.js a Redux</span><span class="sxs-lookup"><span data-stu-id="67636-224">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="67636-225">reactredux</span><span class="sxs-lookup"><span data-stu-id="67636-225">reactredux</span></span> | <span data-ttu-id="67636-226">[C#]</span><span class="sxs-lookup"><span data-stu-id="67636-226">[C#]</span></span>     | <span data-ttu-id="67636-227">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="67636-227">Web/MVC/SPA</span></span> |

<span data-ttu-id="67636-228">K vytvoření nového projektu pomocí jedné z šablon SPA, zahrnout **krátký název** šablony v [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz.</span><span class="sxs-lookup"><span data-stu-id="67636-228">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="67636-229">Následující příkaz vytvoří aplikaci Angular s ASP.NET Core MVC, která je nakonfigurovaná na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="67636-229">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```console
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="67636-230">Nastavit režim konfigurace modulu runtime</span><span class="sxs-lookup"><span data-stu-id="67636-230">Set the runtime configuration mode</span></span>

<span data-ttu-id="67636-231">Existují dva způsoby konfigurace primární runtime:</span><span class="sxs-lookup"><span data-stu-id="67636-231">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="67636-232">**Vývoj**:</span><span class="sxs-lookup"><span data-stu-id="67636-232">**Development**:</span></span>
  * <span data-ttu-id="67636-233">Zahrnuje zdrojových mapování pro usnadnění ladění.</span><span class="sxs-lookup"><span data-stu-id="67636-233">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="67636-234">Neoptimalizuje kód na straně klienta pro výkon.</span><span class="sxs-lookup"><span data-stu-id="67636-234">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="67636-235">**Produkční**:</span><span class="sxs-lookup"><span data-stu-id="67636-235">**Production**:</span></span>
  * <span data-ttu-id="67636-236">Vyloučí zdrojových mapování.</span><span class="sxs-lookup"><span data-stu-id="67636-236">Excludes source maps.</span></span>
  * <span data-ttu-id="67636-237">Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.</span><span class="sxs-lookup"><span data-stu-id="67636-237">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="67636-238">ASP.NET Core, používá proměnnou prostředí s názvem `ASPNETCORE_ENVIRONMENT` k uložení režim konfigurace arm.</span><span class="sxs-lookup"><span data-stu-id="67636-238">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="67636-239">Další informace najdete v tématu [nastavte prostředí](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="67636-239">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="67636-240">Spuštění pomocí .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="67636-240">Run with .NET Core CLI</span></span>

<span data-ttu-id="67636-241">Obnovte spuštěním následujícího příkazu v kořenovém adresáři projektu vyžaduje NuGet a npm, balíčků:</span><span class="sxs-lookup"><span data-stu-id="67636-241">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```console
dotnet restore && npm i
```

<span data-ttu-id="67636-242">Sestavte a spusťte aplikaci:</span><span class="sxs-lookup"><span data-stu-id="67636-242">Build and run the application:</span></span>

```console
dotnet run
```

<span data-ttu-id="67636-243">Spuštění aplikace v místním hostiteli podle [režim konfigurace modulu runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="67636-243">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="67636-244">Přejděte na `http://localhost:5000` v prohlížeči se zobrazí na úvodní stránku.</span><span class="sxs-lookup"><span data-stu-id="67636-244">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="67636-245">Spuštění sady Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="67636-245">Run with Visual Studio 2017</span></span>

<span data-ttu-id="67636-246">Otevřít *.csproj* soubor generovaný nástrojem [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu.</span><span class="sxs-lookup"><span data-stu-id="67636-246">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="67636-247">Obnoví požadované balíčky NuGet a npm se automaticky při otevření projektu.</span><span class="sxs-lookup"><span data-stu-id="67636-247">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="67636-248">Tento proces obnovení může trvat několik minut a aplikace je připraven ke spuštění až po dokončení.</span><span class="sxs-lookup"><span data-stu-id="67636-248">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="67636-249">Klikněte na zelené tlačítko pro spuštění nebo stisknutím klávesy `Ctrl + F5`, a v prohlížeči otevře úvodní stránku aplikace.</span><span class="sxs-lookup"><span data-stu-id="67636-249">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="67636-250">Aplikace bude spuštěna na místním hostiteli podle [režim konfigurace modulu runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="67636-250">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="67636-251">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="67636-251">Test the app</span></span>

<span data-ttu-id="67636-252">SpaServices šablony jsou předem nakonfigurované ke spuštění testů na straně klienta pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="67636-252">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="67636-253">Jasmine je Oblíbené jednotkových testů pro jazyk JavaScript, zatímco Karma je nástroj test runner pro tyto testy.</span><span class="sxs-lookup"><span data-stu-id="67636-253">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="67636-254">Karma je nakonfigurováno pro práci s [Webpacku Dev Middleware](#webpack-dev-middleware) tak, že vývojář není potřeba zastavit a spustit test pokaždé, když dojde ke změně.</span><span class="sxs-lookup"><span data-stu-id="67636-254">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="67636-255">Ať už běží s testovacím případem nebo testový případ samotný kód, test se spustí automaticky.</span><span class="sxs-lookup"><span data-stu-id="67636-255">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="67636-256">Jako příklad použijeme aplikaci Angular, dva Jasmine testovací případy jsou už k dispozici pro `CounterComponent` v *counter.component.spec.ts* souboru:</span><span class="sxs-lookup"><span data-stu-id="67636-256">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="67636-257">Otevřete příkazový řádek v *ClientApp* adresáře.</span><span class="sxs-lookup"><span data-stu-id="67636-257">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="67636-258">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="67636-258">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="67636-259">Tento skript spustí nástroj test runner Karma, který čte definované v nastavení *karma.conf.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="67636-259">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="67636-260">Kromě jiných nastavení *karma.conf.js* identifikuje testovací soubory, které chcete provést prostřednictvím jeho `files` pole:</span><span class="sxs-lookup"><span data-stu-id="67636-260">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="67636-261">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="67636-261">Publish the app</span></span>

<span data-ttu-id="67636-262">Kombinování vygenerovaných prostředků na straně klienta a publikované artefakty ASP.NET Core do balíčku připravených k nasazení může být náročná.</span><span class="sxs-lookup"><span data-stu-id="67636-262">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="67636-263">Naštěstí SpaServices orchestruje tento proces celé publikace s vlastní cíl nástroje MSBuild s názvem `RunWebpack`:</span><span class="sxs-lookup"><span data-stu-id="67636-263">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="67636-264">Cíl nástroje MSBuild má následující zodpovědnosti:</span><span class="sxs-lookup"><span data-stu-id="67636-264">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="67636-265">Obnovení balíčků npm.</span><span class="sxs-lookup"><span data-stu-id="67636-265">Restore the npm packages.</span></span>
1. <span data-ttu-id="67636-266">Vytvoření produkčních sestavení prostředků třetích stran, na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="67636-266">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="67636-267">Vytvoření produkčních sestavení vlastních prostředků na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="67636-267">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="67636-268">Kopírovat materiály generované Webpacku do složky publikovat.</span><span class="sxs-lookup"><span data-stu-id="67636-268">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="67636-269">Cíl nástroje MSBuild je vyvolána při spuštění:</span><span class="sxs-lookup"><span data-stu-id="67636-269">The MSBuild target is invoked when running:</span></span>

```console
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="67636-270">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="67636-270">Additional resources</span></span>

* [<span data-ttu-id="67636-271">Dokumentace angular</span><span class="sxs-lookup"><span data-stu-id="67636-271">Angular Docs</span></span>](https://angular.io/docs)
