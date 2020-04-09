---
title: Použití služeb JavaScript u vytváření jednostránkových aplikací v ASP.NET jádru
author: scottaddie
description: Seznamte se s výhodami používání služeb JavaScript services k vytvoření jednostránkové aplikace (SPA) podporované ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663777"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="f6e43-103">Použití služeb JavaScript u vytváření jednostránkových aplikací v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="f6e43-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="f6e43-104">[Scott Addie](https://github.com/scottaddie) a [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="f6e43-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="f6e43-105">Jednostránková aplikace (SPA) je populární typ webové aplikace díky své vlastní bohaté uživatelské zkušenosti.</span><span class="sxs-lookup"><span data-stu-id="f6e43-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="f6e43-106">Integrace architektury SPA na straně klienta nebo knihovny, jako je [například Angular](https://angular.io/) nebo [React](https://facebook.github.io/react/), s rozhraními na straně serveru, jako je ASP.NET Core může být obtížné.</span><span class="sxs-lookup"><span data-stu-id="f6e43-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="f6e43-107">JavaScript Services byl vyvinut pro snížení tření v integračním procesu.</span><span class="sxs-lookup"><span data-stu-id="f6e43-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="f6e43-108">Umožňuje bezproblémový provoz mezi různými zásobníky klientské a serverové technologie.</span><span class="sxs-lookup"><span data-stu-id="f6e43-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="f6e43-109">Funkce popsané v tomto článku jsou zastaralé od ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f6e43-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="f6e43-110">Jednodušší mechanismus integrace rozhraní SPA je k dispozici v balíčku [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet.</span><span class="sxs-lookup"><span data-stu-id="f6e43-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="f6e43-111">Další informace naleznete [v tématu [Announcement] Obsoleting Microsoft.AspNetCore.SpaServices a Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="f6e43-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="f6e43-112">Co jsou služby JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="f6e43-112">What is JavaScript Services</span></span>

<span data-ttu-id="f6e43-113">JavaScript Services je kolekce technologií na straně klienta pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6e43-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="f6e43-114">Jeho cílem je umístit ASP.NET Core jako preferovanou platformu vývojářů na straně serveru pro vytváření certifikátů.</span><span class="sxs-lookup"><span data-stu-id="f6e43-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="f6e43-115">Služby JavaScript se skládá ze dvou odlišných balíčků NuGet:</span><span class="sxs-lookup"><span data-stu-id="f6e43-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="f6e43-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="f6e43-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="f6e43-117">[Služby Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="f6e43-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="f6e43-118">Tyto balíčky jsou užitečné v následujících scénářích:</span><span class="sxs-lookup"><span data-stu-id="f6e43-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="f6e43-119">Spuštění JavaScriptu na serveru</span><span class="sxs-lookup"><span data-stu-id="f6e43-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="f6e43-120">Použití spa frameworku nebo knihovny</span><span class="sxs-lookup"><span data-stu-id="f6e43-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="f6e43-121">Vytváření prostředků na straně klienta pomocí webového balíčku</span><span class="sxs-lookup"><span data-stu-id="f6e43-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="f6e43-122">Velká část zaměření v tomto článku je umístěn na použití spaservices balíček.</span><span class="sxs-lookup"><span data-stu-id="f6e43-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="f6e43-123">Co je SpaServices</span><span class="sxs-lookup"><span data-stu-id="f6e43-123">What is SpaServices</span></span>

<span data-ttu-id="f6e43-124">SpaServices byl vytvořen pro pozici ASP.NET Core jako upřednostňovanou platformu vývojářů na straně serveru pro vytváření certifikátů.</span><span class="sxs-lookup"><span data-stu-id="f6e43-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="f6e43-125">SpaServices není nutné vyvíjet SPA s ASP.NET Core a nezamkne vývojáře do konkrétního klientského rámce.</span><span class="sxs-lookup"><span data-stu-id="f6e43-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="f6e43-126">SpaServices poskytuje užitečnou infrastrukturu, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="f6e43-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="f6e43-127">Předběžné vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f6e43-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="f6e43-128">Webový balíček Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="f6e43-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="f6e43-129">Výměna horkého modulu</span><span class="sxs-lookup"><span data-stu-id="f6e43-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="f6e43-130">Pomocníci směrování</span><span class="sxs-lookup"><span data-stu-id="f6e43-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="f6e43-131">Tyto součásti infrastruktury společně vylepšují pracovní postup vývoje i prostředí runtime.</span><span class="sxs-lookup"><span data-stu-id="f6e43-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="f6e43-132">Komponenty mohou být přijaty individuálně.</span><span class="sxs-lookup"><span data-stu-id="f6e43-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="f6e43-133">Předpoklady pro používání SpaServices</span><span class="sxs-lookup"><span data-stu-id="f6e43-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="f6e43-134">Chcete-li pracovat se službami SpaServices, nainstalujte následující:</span><span class="sxs-lookup"><span data-stu-id="f6e43-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="f6e43-135">[Node.js](https://nodejs.org/) (verze 6 nebo novější) s npm</span><span class="sxs-lookup"><span data-stu-id="f6e43-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="f6e43-136">Chcete-li ověřit, zda jsou tyto součásti nainstalovány a lze je najít, spusťte z příkazového řádku následující:</span><span class="sxs-lookup"><span data-stu-id="f6e43-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="f6e43-137">Pokud se nasazujete na web&mdash;Azure, není vyžadována žádná akce Node.js, která je nainstalovaná a dostupná v serverových prostředích.</span><span class="sxs-lookup"><span data-stu-id="f6e43-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="f6e43-138">Ve Windows pomocí Visual Studia 2017 je sada SDK nainstalována výběrem **vývojovéúlohy napříč platformami .NET Core.**</span><span class="sxs-lookup"><span data-stu-id="f6e43-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="f6e43-139">Balíček [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet</span><span class="sxs-lookup"><span data-stu-id="f6e43-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="f6e43-140">Předběžné vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f6e43-140">Server-side prerendering</span></span>

<span data-ttu-id="f6e43-141">Univerzální (známá také jako izomorfní) aplikace je javascriptová aplikace schopná spouštět jak na serveru, tak na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f6e43-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="f6e43-142">Úhlové, Reagovat a další populární architektury poskytují univerzální platformu pro tento styl vývoje aplikací.</span><span class="sxs-lookup"><span data-stu-id="f6e43-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="f6e43-143">Cílem je nejprve vykreslit součásti architektury na serveru prostřednictvím Node.js a potom delegovat další provádění klientovi.</span><span class="sxs-lookup"><span data-stu-id="f6e43-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="f6e43-144">ASP.NET [Pomocníky](xref:mvc/views/tag-helpers/intro) core tagů poskytované společností SpaServices zjednodušují implementaci předběžného vykreslování na straně serveru vyvoláním funkcí JavaScriptu na serveru.</span><span class="sxs-lookup"><span data-stu-id="f6e43-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="f6e43-145">Požadavky předběžného vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f6e43-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="f6e43-146">Nainstalujte balíček npm [aspnet-prerendering:](https://www.npmjs.com/package/aspnet-prerendering)</span><span class="sxs-lookup"><span data-stu-id="f6e43-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="f6e43-147">Konfigurace předběžného vykreslování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="f6e43-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="f6e43-148">Tag Helpers jsou zjistitelné prostřednictvím registrace oboru názvů v souboru *_ViewImports.cshtml* projektu:</span><span class="sxs-lookup"><span data-stu-id="f6e43-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="f6e43-149">Tyto tag pomocníci abstraktní pryč složitosti komunikace přímo s nízkoúrovňové rozhraní API využitím html-jako syntaxe uvnitř zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="f6e43-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="f6e43-150">pomocník tagů asp-prerender-module</span><span class="sxs-lookup"><span data-stu-id="f6e43-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="f6e43-151">Pomocník `asp-prerender-module` značek použitý v předchozím příkladu kódu provede *clientapp/dist/main-server.js* na serveru prostřednictvím souboru Node.js.</span><span class="sxs-lookup"><span data-stu-id="f6e43-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="f6e43-152">Z důvodu jasnosti je soubor *main-server.js* artefaktem úlohy transpichování typu Script-to-JavaScript v procesu sestavení [webpacku.](https://webpack.github.io/)</span><span class="sxs-lookup"><span data-stu-id="f6e43-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="f6e43-153">Webpack definuje alias vstupního `main-server`bodu ; a procházení grafu závislostí pro tento alias začíná v souboru *ClientApp/boot-server.ts:*</span><span class="sxs-lookup"><span data-stu-id="f6e43-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="f6e43-154">V následujícím příkladu Úhlové používá soubor *ClientApp/boot-server.ts* `createServerRenderer` funkci a `RenderResult` typ balíčku `aspnet-prerendering` npm ke konfiguraci vykreslování serveru pomocí souboru Node.js.</span><span class="sxs-lookup"><span data-stu-id="f6e43-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="f6e43-155">Značky HTML určené pro vykreslování na straně serveru jsou předány volání funkce `Promise` resolve, které je zabaleno do silně psaného objektu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f6e43-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="f6e43-156">Význam `Promise` objektu spoáčuje s tím, že asynchronně poskytuje značky HTML na stránku pro vkládání v zástupném prvku dom.</span><span class="sxs-lookup"><span data-stu-id="f6e43-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="f6e43-157">pomocník s tagy asp-prerender-data</span><span class="sxs-lookup"><span data-stu-id="f6e43-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="f6e43-158">Ve spojení s `asp-prerender-module` pomocníkem tagů lze `asp-prerender-data` pomocníka tagů použít k předání kontextových informací ze zobrazení Razor do JavaScriptu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f6e43-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="f6e43-159">Například následující značky předá uživatelská data modulu: `main-server`</span><span class="sxs-lookup"><span data-stu-id="f6e43-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="f6e43-160">Přijatý `UserName` argument je serializován pomocí předdefinovaného serializátoru `params.data` JSON a je uložen v objektu.</span><span class="sxs-lookup"><span data-stu-id="f6e43-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="f6e43-161">V následujícím příkladu Úhlové se data používají k vytvoření `h1` přizpůsobeného pozdravu v rámci prvku:</span><span class="sxs-lookup"><span data-stu-id="f6e43-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="f6e43-162">Názvy vlastností předané v pomocné spoje tagů jsou reprezentovány zápisem **PascalCase.**</span><span class="sxs-lookup"><span data-stu-id="f6e43-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="f6e43-163">Kontrast, že na JavaScript, kde jsou stejné názvy vlastností reprezentovány **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="f6e43-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="f6e43-164">Výchozí konfigurace serializace JSON je zodpovědný za tento rozdíl.</span><span class="sxs-lookup"><span data-stu-id="f6e43-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="f6e43-165">Chcete-li rozbalit v předchozím příkladu kódu, data mohou být `globals` předána ze `resolve` serveru do zobrazení hydratací vlastnosti poskytované funkci:</span><span class="sxs-lookup"><span data-stu-id="f6e43-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="f6e43-166">Pole `postList` definované uvnitř `globals` objektu je připojeno ke `window` globálnímu objektu prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f6e43-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="f6e43-167">Tato proměnná zvedání do globálního oboru eliminuje duplicitu úsilí, zejména pokud jde o načítání stejných dat jednou na serveru a znovu na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f6e43-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![globální proměnná postList připojená k objektu okna](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="f6e43-169">Webový balíček Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="f6e43-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="f6e43-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) představuje zjednodušený vývojový pracovní postup, ve kterém Webpack vytváří prostředky na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="f6e43-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="f6e43-171">Middleware automaticky zkompiluje a slouží prostředky na straně klienta při opětovném načtení stránky v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="f6e43-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="f6e43-172">Alternativní přístup je ručně vyvolat webpack prostřednictvím projektu npm sestavení skriptu při změně závislosti třetí strany nebo vlastní kód.</span><span class="sxs-lookup"><span data-stu-id="f6e43-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="f6e43-173">Skript sestavení npm v souboru *package.json* je uveden v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f6e43-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="f6e43-174">Požadavky webového balíčku Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="f6e43-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="f6e43-175">Nainstalujte balíček [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm:</span><span class="sxs-lookup"><span data-stu-id="f6e43-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="f6e43-176">Konfigurace webového balíčku Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="f6e43-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="f6e43-177">Webpack Dev Middleware je zaregistrován do kanálu požadavků *Startup.cs* HTTP pomocí `Configure` následujícího kódu v metodě Startup.cs souboru:</span><span class="sxs-lookup"><span data-stu-id="f6e43-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="f6e43-178">Metoda `UseWebpackDevMiddleware` rozšíření musí být volána před [registrací statického souboru hosting](xref:fundamentals/static-files) prostřednictvím `UseStaticFiles` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f6e43-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="f6e43-179">Z bezpečnostních důvodů zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="f6e43-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="f6e43-180">Vlastnost souboru `output.publicPath` *webpack.config.js* říká middlewaru, `dist` aby sledoval změnu ve složce:</span><span class="sxs-lookup"><span data-stu-id="f6e43-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="f6e43-181">Výměna horkého modulu</span><span class="sxs-lookup"><span data-stu-id="f6e43-181">Hot Module Replacement</span></span>

<span data-ttu-id="f6e43-182">Představte si funkci [Webpack Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) jako evoluci [Webpack Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="f6e43-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="f6e43-183">HMR zavádí všechny stejné výhody, ale dále zjednodušuje pracovní postup vývoje automatickou aktualizací obsahu stránky po kompilaci změn.</span><span class="sxs-lookup"><span data-stu-id="f6e43-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="f6e43-184">Nepleťte si to s aktualizací prohlížeče, která by narušila aktuální stav v paměti a ladění relace SPA.</span><span class="sxs-lookup"><span data-stu-id="f6e43-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="f6e43-185">Existuje živé spojení mezi službou Webpack Dev Middleware a prohlížečem, což znamená, že změny jsou tlačeny do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f6e43-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="f6e43-186">Předpoklady pro výměnu aktivního modulu</span><span class="sxs-lookup"><span data-stu-id="f6e43-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="f6e43-187">Nainstalujte balíček [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm:</span><span class="sxs-lookup"><span data-stu-id="f6e43-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="f6e43-188">Konfigurace výměny aktivního modulu</span><span class="sxs-lookup"><span data-stu-id="f6e43-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="f6e43-189">HMR komponenta musí být registrována do kanálu požadavků `Configure` HTTP společnosti MVC v metodě:</span><span class="sxs-lookup"><span data-stu-id="f6e43-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="f6e43-190">Jak tomu bylo u [Webpack Dev Middleware](#webpack-dev-middleware), `UseStaticFiles` musí být před metodou rozšíření volána metoda `UseWebpackDevMiddleware` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f6e43-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="f6e43-191">Z bezpečnostních důvodů zaregistrujte middleware pouze v případě, že aplikace běží v režimu vývoje.</span><span class="sxs-lookup"><span data-stu-id="f6e43-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="f6e43-192">Soubor *webpack.config.js* musí `plugins` definovat pole, i když je prázdné:</span><span class="sxs-lookup"><span data-stu-id="f6e43-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="f6e43-193">Po načtení aplikace v prohlížeči poskytuje karta Konzola vývojářských nástrojů potvrzení aktivace HMR:</span><span class="sxs-lookup"><span data-stu-id="f6e43-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Zpráva o výměně připojeného modulu hot module](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="f6e43-195">Pomocníci směrování</span><span class="sxs-lookup"><span data-stu-id="f6e43-195">Routing helpers</span></span>

<span data-ttu-id="f6e43-196">Ve většině ASP.NET virtuálních microsofty na straně klienta směrování je často žádoucí kromě směrování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="f6e43-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="f6e43-197">Směrovací systémy SPA a MVC mohou pracovat nezávisle bez rušení.</span><span class="sxs-lookup"><span data-stu-id="f6e43-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="f6e43-198">Existuje však jeden okraj případu, který představuje výzvy: identifikace 404 odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6e43-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="f6e43-199">Zvažte scénář, ve kterém `/some/page` se používá trasa bez rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f6e43-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="f6e43-200">Předpokládejme, že požadavek neodpovídá směru, ale jeho vzor odpovídá trase na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f6e43-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="f6e43-201">Nyní zvažte příchozí `/images/user-512.png`požadavek pro , který obecně očekává, že najde soubor obrázku na serveru.</span><span class="sxs-lookup"><span data-stu-id="f6e43-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="f6e43-202">Pokud tato požadovaná cesta k prostředku neodpovídá žádné cestě na straně serveru nebo statickému&mdash;souboru, je nepravděpodobné, že by aplikace na straně klienta zpracovat obecně vrácení 404 HTTP stavový kód je žádoucí.</span><span class="sxs-lookup"><span data-stu-id="f6e43-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="f6e43-203">Požadavky pomocníků směrování</span><span class="sxs-lookup"><span data-stu-id="f6e43-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="f6e43-204">Nainstalujte balíček npm směrování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f6e43-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="f6e43-205">Použití úhlu jako příklad:</span><span class="sxs-lookup"><span data-stu-id="f6e43-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="f6e43-206">Konfigurace pomocných postupů směrování</span><span class="sxs-lookup"><span data-stu-id="f6e43-206">Routing helpers configuration</span></span>

<span data-ttu-id="f6e43-207">Metoda rozšíření `MapSpaFallbackRoute` s názvem `Configure` se používá v metodě:</span><span class="sxs-lookup"><span data-stu-id="f6e43-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="f6e43-208">Trasy jsou vyhodnocovány v pořadí, ve kterém jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="f6e43-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="f6e43-209">V důsledku `default` toho se trasa v předchozím příkladu kódu používá nejprve pro porovnávání vzorů.</span><span class="sxs-lookup"><span data-stu-id="f6e43-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="f6e43-210">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="f6e43-210">Create a new project</span></span>

<span data-ttu-id="f6e43-211">Služby JavaScript poskytují předem nakonfigurované šablony aplikací.</span><span class="sxs-lookup"><span data-stu-id="f6e43-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="f6e43-212">SpaServices se používá v těchto šablonách ve spojení s různými rámci a knihovny, jako je například Angular, React a Redux.</span><span class="sxs-lookup"><span data-stu-id="f6e43-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="f6e43-213">Tyto šablony lze nainstalovat pomocí rozhraní PŘÍKAZU .NET Core cli spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="f6e43-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="f6e43-214">Zobrazí se seznam dostupných šablon SPA:</span><span class="sxs-lookup"><span data-stu-id="f6e43-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="f6e43-215">Šablony</span><span class="sxs-lookup"><span data-stu-id="f6e43-215">Templates</span></span>                                 | <span data-ttu-id="f6e43-216">Krátký název</span><span class="sxs-lookup"><span data-stu-id="f6e43-216">Short Name</span></span> | <span data-ttu-id="f6e43-217">Jazyk</span><span class="sxs-lookup"><span data-stu-id="f6e43-217">Language</span></span> | <span data-ttu-id="f6e43-218">Značky</span><span class="sxs-lookup"><span data-stu-id="f6e43-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="f6e43-219">MVC ASP.NET jádro s úhlovým</span><span class="sxs-lookup"><span data-stu-id="f6e43-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="f6e43-220">Úhlové</span><span class="sxs-lookup"><span data-stu-id="f6e43-220">angular</span></span>    | <span data-ttu-id="f6e43-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="f6e43-221">[C#]</span></span>     | <span data-ttu-id="f6e43-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="f6e43-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="f6e43-223">MVC ASP.NET jádro s React.js</span><span class="sxs-lookup"><span data-stu-id="f6e43-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="f6e43-224">react</span><span class="sxs-lookup"><span data-stu-id="f6e43-224">react</span></span>      | <span data-ttu-id="f6e43-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="f6e43-225">[C#]</span></span>     | <span data-ttu-id="f6e43-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="f6e43-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="f6e43-227">MVC ASP.NET Core s React.js a Redux</span><span class="sxs-lookup"><span data-stu-id="f6e43-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="f6e43-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="f6e43-228">reactredux</span></span> | <span data-ttu-id="f6e43-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="f6e43-229">[C#]</span></span>     | <span data-ttu-id="f6e43-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="f6e43-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="f6e43-231">Chcete-li vytvořit nový projekt pomocí jedné ze šablon SPA, zadejte **krátký název** šablony do nového příkazu [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="f6e43-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="f6e43-232">Následující příkaz vytvoří úhlovou aplikaci s ASP.NET Core MVC nakonfigurovaným pro stranu serveru:</span><span class="sxs-lookup"><span data-stu-id="f6e43-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="f6e43-233">Nastavení režimu konfigurace za běhu</span><span class="sxs-lookup"><span data-stu-id="f6e43-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="f6e43-234">Existují dva primární režimy konfigurace za běhu:</span><span class="sxs-lookup"><span data-stu-id="f6e43-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="f6e43-235">**Vývoj**:</span><span class="sxs-lookup"><span data-stu-id="f6e43-235">**Development**:</span></span>
  * <span data-ttu-id="f6e43-236">Obsahuje zdrojové mapy pro usnadnění ladění.</span><span class="sxs-lookup"><span data-stu-id="f6e43-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="f6e43-237">Neoptimalizuje kód na straně klienta pro výkon.</span><span class="sxs-lookup"><span data-stu-id="f6e43-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="f6e43-238">**Výroba**:</span><span class="sxs-lookup"><span data-stu-id="f6e43-238">**Production**:</span></span>
  * <span data-ttu-id="f6e43-239">Nezahrnuje zdrojové mapy.</span><span class="sxs-lookup"><span data-stu-id="f6e43-239">Excludes source maps.</span></span>
  * <span data-ttu-id="f6e43-240">Optimalizuje kód na straně klienta prostřednictvím sdružování a minifikace.</span><span class="sxs-lookup"><span data-stu-id="f6e43-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="f6e43-241">ASP.NET Core používá k `ASPNETCORE_ENVIRONMENT` uložení režimu konfigurace proměnnou prostředí s názvem.</span><span class="sxs-lookup"><span data-stu-id="f6e43-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="f6e43-242">Další informace naleznete [v tématu Nastavení prostředí](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="f6e43-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="f6e43-243">Spustit pomocí rozhraní CLI jádra .NET</span><span class="sxs-lookup"><span data-stu-id="f6e43-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="f6e43-244">Obnovení požadovaných balíčků NuGet a npm spuštěním následujícího příkazu v kořenovém adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="f6e43-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="f6e43-245">Sestavení a spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="f6e43-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="f6e43-246">Aplikace se spustí na localhost podle [režimu konfigurace runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="f6e43-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="f6e43-247">Přechod na `http://localhost:5000` v prohlížeči zobrazí vstupní stránku.</span><span class="sxs-lookup"><span data-stu-id="f6e43-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="f6e43-248">Spuštění s Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="f6e43-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="f6e43-249">Otevřete soubor *.csproj* generovaný novým příkazem [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="f6e43-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="f6e43-250">Požadované balíčky NuGet a npm jsou obnoveny automaticky při otevření projektu.</span><span class="sxs-lookup"><span data-stu-id="f6e43-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="f6e43-251">Tento proces obnovení může trvat až několik minut a aplikace je připravena ke spuštění po dokončení.</span><span class="sxs-lookup"><span data-stu-id="f6e43-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="f6e43-252">Klikněte na zelené `Ctrl + F5`tlačítko spuštění nebo stiskněte tlačítko a prohlížeč se otevře na vstupní stránce aplikace.</span><span class="sxs-lookup"><span data-stu-id="f6e43-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="f6e43-253">Aplikace běží na localhost podle [režimu konfigurace runtime](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="f6e43-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="f6e43-254">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="f6e43-254">Test the app</span></span>

<span data-ttu-id="f6e43-255">SpaServices šablony jsou pre-nakonfigurován tak, aby spustit klient-side testy pomocí [Karma](https://karma-runner.github.io/1.0/index.html) a [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="f6e43-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="f6e43-256">Jasmine je populární jednotka testování rámec pro JavaScript, zatímco Karma je test běžec pro tyto testy.</span><span class="sxs-lookup"><span data-stu-id="f6e43-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="f6e43-257">Karma je nakonfigurován pro práci s [Webpack Dev Middleware](#webpack-dev-middleware) tak, že vývojář není nutné zastavit a spustit test pokaždé, když jsou provedeny změny.</span><span class="sxs-lookup"><span data-stu-id="f6e43-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="f6e43-258">Ať už je kód spuštěn proti testovacího případu nebo samotného testovacího případu, test se spustí automaticky.</span><span class="sxs-lookup"><span data-stu-id="f6e43-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="f6e43-259">Použití angular aplikace jako příklad, dva jasmine testovacích `CounterComponent` případů jsou již k dispozici v *souboru counter.component.spec.ts:*</span><span class="sxs-lookup"><span data-stu-id="f6e43-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="f6e43-260">Otevřete příkazový řádek v adresáři *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="f6e43-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="f6e43-261">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f6e43-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="f6e43-262">Skript spustí test Karma běžec, který čte nastavení definované v *karma.conf.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="f6e43-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="f6e43-263">Mimo jiné nastavení *karma.conf.js* identifikuje testovací soubory, které `files` mají být provedeny prostřednictvím svého pole:</span><span class="sxs-lookup"><span data-stu-id="f6e43-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="f6e43-264">Publikování aplikace</span><span class="sxs-lookup"><span data-stu-id="f6e43-264">Publish the app</span></span>

<span data-ttu-id="f6e43-265">Další informace o publikování v Azure najdete v [tomto problému s GitHubem.](https://github.com/dotnet/AspNetCore.Docs/issues/12474)</span><span class="sxs-lookup"><span data-stu-id="f6e43-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="f6e43-266">Kombinace generovaných prostředků na straně klienta a publikovaných artefaktů ASP.NET Core do balíčku připraveného k nasazení může být těžkopádné.</span><span class="sxs-lookup"><span data-stu-id="f6e43-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="f6e43-267">Naštěstí SpaServices orchestruje celý proces publikování s vlastním cílem `RunWebpack`MSBuild s názvem :</span><span class="sxs-lookup"><span data-stu-id="f6e43-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="f6e43-268">Cíl MSBuild má následující povinnosti:</span><span class="sxs-lookup"><span data-stu-id="f6e43-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="f6e43-269">Obnovte balíčky npm.</span><span class="sxs-lookup"><span data-stu-id="f6e43-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="f6e43-270">Vytvořte sestavení na úrovni výroby aktiv třetích stran na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f6e43-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="f6e43-271">Vytvořte sestavení vlastních prostředků na straně klienta na úrovni výroby.</span><span class="sxs-lookup"><span data-stu-id="f6e43-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="f6e43-272">Zkopírujte datové zdroje generované webpackem do složky publikování.</span><span class="sxs-lookup"><span data-stu-id="f6e43-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="f6e43-273">Cíl MSBuild je vyvolán při spuštění:</span><span class="sxs-lookup"><span data-stu-id="f6e43-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="f6e43-274">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f6e43-274">Additional resources</span></span>

* [<span data-ttu-id="f6e43-275">Úhlové dokumenty</span><span class="sxs-lookup"><span data-stu-id="f6e43-275">Angular Docs</span></span>](https://angular.io/docs)
