---
title: Migrace obslužných rutin a modulů HTTP do ASP.NET Core middlewaru
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: 214e3fa86a1418f04a5e292cdc1b4baac8c75643
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074184"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="63879-102">Migrace obslužných rutin a modulů HTTP do ASP.NET Core middlewaru</span><span class="sxs-lookup"><span data-stu-id="63879-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="63879-103">Tento článek ukazuje, jak migrovat existující [moduly a obslužné rutiny ASP.NET HTTP ze serveru System. webServer](/iis/configuration/system.webserver/) do ASP.NET Core [middlewaru](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="63879-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="63879-104">Přenavštívené moduly a obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="63879-104">Modules and handlers revisited</span></span>

<span data-ttu-id="63879-105">Než začnete ASP.NET Core middlewaru, nejdřív si rekapitulace, jak fungují moduly HTTP a obslužné rutiny:</span><span class="sxs-lookup"><span data-stu-id="63879-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Obslužná rutina modulů](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="63879-107">**Obslužné rutiny:**</span><span class="sxs-lookup"><span data-stu-id="63879-107">**Handlers are:**</span></span>

* <span data-ttu-id="63879-108">Třídy, které implementují [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="63879-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="63879-109">Slouží ke zpracování požadavků s daným názvem nebo příponou souboru, například *. Report.*</span><span class="sxs-lookup"><span data-stu-id="63879-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="63879-110">[Nakonfigurováno](/iis/configuration/system.webserver/handlers/) v *Web.config*</span><span class="sxs-lookup"><span data-stu-id="63879-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="63879-111">**Moduly jsou:**</span><span class="sxs-lookup"><span data-stu-id="63879-111">**Modules are:**</span></span>

* <span data-ttu-id="63879-112">Třídy, které implementují [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="63879-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="63879-113">Vyvoláno pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="63879-113">Invoked for every request</span></span>

* <span data-ttu-id="63879-114">Schopnost krátkodobého okruhu (zastavení dalšího zpracování žádosti)</span><span class="sxs-lookup"><span data-stu-id="63879-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="63879-115">Může přidat do odpovědi HTTP nebo vytvořit vlastní.</span><span class="sxs-lookup"><span data-stu-id="63879-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="63879-116">[Nakonfigurováno](/iis/configuration/system.webserver/modules/) v *Web.config*</span><span class="sxs-lookup"><span data-stu-id="63879-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="63879-117">**Pořadí, ve kterém moduly zpracovávají příchozí požadavky, určuje:**</span><span class="sxs-lookup"><span data-stu-id="63879-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="63879-118">[Životní cyklus aplikace](https://msdn.microsoft.com/library/ms227673.aspx), což je události řady, které jsou aktivovány ASP.NET: [beginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)atd. Každý modul může vytvořit obslužnou rutinu pro jednu nebo více událostí.</span><span class="sxs-lookup"><span data-stu-id="63879-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="63879-119">Pro stejnou událost, pořadí, ve kterém jsou nakonfigurované v *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="63879-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="63879-120">Kromě modulů můžete přidat obslužné rutiny pro události životního cyklu do souboru *Global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="63879-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="63879-121">Tyto obslužné rutiny jsou spouštěny po obslužných rutinách v konfigurovaných modulech.</span><span class="sxs-lookup"><span data-stu-id="63879-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="63879-122">Z obslužných rutin a modulů pro middleware</span><span class="sxs-lookup"><span data-stu-id="63879-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="63879-123">**Middleware jsou jednodušší než moduly HTTP a obslužné rutiny:**</span><span class="sxs-lookup"><span data-stu-id="63879-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="63879-124">Moduly, obslužné rutiny, *Global.asax.cs*, *Web.config* (kromě konfigurace služby IIS) a životní cyklus aplikace se odešlou.</span><span class="sxs-lookup"><span data-stu-id="63879-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="63879-125">Služba middlewaru převzala role obou modulů i obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="63879-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="63879-126">Middleware jsou nakonfigurovány pomocí kódu, nikoli v *Web.config*</span><span class="sxs-lookup"><span data-stu-id="63879-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="63879-127">[Větvení kanálu](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) vám umožňuje odesílat požadavky na konkrétní middleware na základě nejen adresy URL, ale také na hlavičkách žádostí, řetězcích dotazů atd.</span><span class="sxs-lookup"><span data-stu-id="63879-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="63879-128">[Větvení kanálu](xref:fundamentals/middleware/index#use-run-and-map) vám umožňuje odesílat požadavky na konkrétní middleware na základě nejen adresy URL, ale také na hlavičkách žádostí, řetězcích dotazů atd.</span><span class="sxs-lookup"><span data-stu-id="63879-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="63879-129">**Middleware jsou velmi podobné modulům:**</span><span class="sxs-lookup"><span data-stu-id="63879-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="63879-130">Vyvoláno v principu pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="63879-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="63879-131">Může se stát, že žádost nebude v krátkém okruhu předávat [do dalšího middlewaru](#http-modules-shortcircuiting-middleware) .</span><span class="sxs-lookup"><span data-stu-id="63879-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="63879-132">Schopné vytvořit vlastní odpověď HTTP</span><span class="sxs-lookup"><span data-stu-id="63879-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="63879-133">**Middleware a moduly jsou zpracovávány v jiném pořadí:**</span><span class="sxs-lookup"><span data-stu-id="63879-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="63879-134">Pořadí middlewaru je založené na pořadí, ve kterém jsou vloženy do kanálu požadavků, zatímco pořadí modulů je založeno hlavně na událostech [životního cyklu aplikace](https://msdn.microsoft.com/library/ms227673.aspx) .</span><span class="sxs-lookup"><span data-stu-id="63879-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="63879-135">Pořadí middlewaru pro odpovědi je opačné než u požadavků, zatímco pořadí modulů je u požadavků a odpovědí stejné.</span><span class="sxs-lookup"><span data-stu-id="63879-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="63879-136">Viz [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="63879-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="63879-138">Všimněte si, jak je výše uvedený obrázek, ověřovací middleware krátce vyřídil požadavek.</span><span class="sxs-lookup"><span data-stu-id="63879-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="63879-139">Migrace kódu modulu do middlewaru</span><span class="sxs-lookup"><span data-stu-id="63879-139">Migrating module code to middleware</span></span>

<span data-ttu-id="63879-140">Existující modul HTTP bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="63879-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="63879-141">Jak je znázorněno na stránce [middleware](xref:fundamentals/middleware/index) , ASP.NET Core middleware je třída, která zpřístupňuje `Invoke` metodu s `HttpContext` návratem a `Task` .</span><span class="sxs-lookup"><span data-stu-id="63879-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="63879-142">Váš nový middleware bude vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="63879-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="63879-143">Předchozí šablona middlewaru byla pořízena z oddílu o [zápisu middlewaru](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="63879-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="63879-144">Pomocná třída *MyMiddlewareExtensions* usnadňuje konfiguraci middlewaru ve vaší `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="63879-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="63879-145">`UseMyMiddleware`Metoda přidá třídu middleware do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="63879-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="63879-146">Služby, které vyžaduje middleware, se vloží do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="63879-147">Váš modul může ukončit žádost, například pokud uživatel není autorizovaný:</span><span class="sxs-lookup"><span data-stu-id="63879-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="63879-148">Middleware to zpracovává tím, že nevolá `Invoke` na další middleware v kanálu.</span><span class="sxs-lookup"><span data-stu-id="63879-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="63879-149">Mějte na paměti, že to neukončí celý požadavek, protože předchozí middleware budou přesto vyvolány, když odpověď prochází způsobem kanálu.</span><span class="sxs-lookup"><span data-stu-id="63879-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="63879-150">Když migrujete funkci modulu na nový middleware, může se stát, že váš kód nebude zkompilován, protože se `HttpContext` v ASP.NET Core významně změnila třída.</span><span class="sxs-lookup"><span data-stu-id="63879-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="63879-151">[Později](#migrating-to-the-new-httpcontext)se dozvíte, jak migrovat do nové ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="63879-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="63879-152">Migruje se vložení modulu do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="63879-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="63879-153">Moduly HTTP se obvykle přidávají do kanálu požadavků pomocí *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="63879-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="63879-154">Převeďte to [přidáním nového middlewaru](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) do kanálu požadavků ve vaší `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="63879-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="63879-155">Přesné místo v kanálu, kam vložíte nový middleware, závisí na události, kterou zpracuje jako modul ( `BeginRequest` , `EndRequest` atd.) a pořadí v seznamu modulů v *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="63879-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="63879-156">Jak je uvedeno výše, v ASP.NET Core není žádný životní cyklus aplikace a pořadí, ve kterém jsou odpovědi zpracovávány middlewarem, se liší od pořadí používaného moduly.</span><span class="sxs-lookup"><span data-stu-id="63879-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="63879-157">To může být náročnější na rozhodnutí o řazení.</span><span class="sxs-lookup"><span data-stu-id="63879-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="63879-158">Pokud se řazení stalo problémem, mohli byste svůj modul rozdělit do několika součástí middlewaru, které je možné seřadit nezávisle.</span><span class="sxs-lookup"><span data-stu-id="63879-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="63879-159">Migrace kódu obslužné rutiny do middlewaru</span><span class="sxs-lookup"><span data-stu-id="63879-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="63879-160">Obslužná rutina HTTP vypadá nějak takto:</span><span class="sxs-lookup"><span data-stu-id="63879-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="63879-161">V projektu ASP.NET Core byste ho přeložili na middleware podobnou této:</span><span class="sxs-lookup"><span data-stu-id="63879-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="63879-162">Tento middleware je velmi podobný middlewaru, který odpovídá modulům.</span><span class="sxs-lookup"><span data-stu-id="63879-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="63879-163">Jediným skutečným rozdílem je, že zde není žádné volání `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="63879-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="63879-164">To dává smysl, protože obslužná rutina je na konci kanálu požadavků, takže nebudete moct vyvolat žádné další middleware.</span><span class="sxs-lookup"><span data-stu-id="63879-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="63879-165">Migrace vložení obslužné rutiny do kanálu žádosti</span><span class="sxs-lookup"><span data-stu-id="63879-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="63879-166">Konfigurace obslužné rutiny HTTP se provádí v *Web.config* a vypadá nějak takto:</span><span class="sxs-lookup"><span data-stu-id="63879-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="63879-167">To můžete převést přidáním nového middlewaru obslužné rutiny do kanálu požadavků ve vaší `Startup` třídě, podobně jako middleware převedené z modulů.</span><span class="sxs-lookup"><span data-stu-id="63879-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="63879-168">Problém s tímto přístupem je, že by poslal všechny žádosti do nového middlewaru obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="63879-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="63879-169">Nicméně budete chtít, aby žádosti s daným rozšířením dosáhly vašeho middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="63879-170">To vám poskytne stejné funkce jako u vaší obslužné rutiny HTTP.</span><span class="sxs-lookup"><span data-stu-id="63879-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="63879-171">Jedním z řešení je vytvořit větev kanálu pro žádosti s danou příponou pomocí `MapWhen` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="63879-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="63879-172">Provedete to stejným způsobem, jakým `Configure` přidáte další middleware:</span><span class="sxs-lookup"><span data-stu-id="63879-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="63879-173">`MapWhen`přebírá tyto parametry:</span><span class="sxs-lookup"><span data-stu-id="63879-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="63879-174">Výraz lambda, který přebírá `HttpContext` a vrátí, `true` zda by požadavek měl přejít mimo větev.</span><span class="sxs-lookup"><span data-stu-id="63879-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="63879-175">To znamená, že můžete požadavky větví nejenom na základě jejich rozšíření, ale také v hlavičkách žádostí, parametrech řetězce dotazu atd.</span><span class="sxs-lookup"><span data-stu-id="63879-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="63879-176">Lambda, který přebírá `IApplicationBuilder` a přidává všechny middleware pro větev.</span><span class="sxs-lookup"><span data-stu-id="63879-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="63879-177">To znamená, že můžete přidat další middleware do větve před middlewarem vaší obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="63879-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="63879-178">Middleware přidaný do kanálu před tím, než se větev vyvolá u všech požadavků; Tato větev nebude mít žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="63879-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="63879-179">Načítání možností middlewaru pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="63879-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="63879-180">Některé moduly a obslužné rutiny mají možnosti konfigurace, které jsou uloženy v *Web.config*. V ASP.NET Core se ale místo *Web.config*používá nový model konfigurace.</span><span class="sxs-lookup"><span data-stu-id="63879-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="63879-181">Nový [konfigurační systém](xref:fundamentals/configuration/index) poskytuje tyto možnosti, jak tento problém vyřešit:</span><span class="sxs-lookup"><span data-stu-id="63879-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="63879-182">Přímo do middlewaru založit možnosti, jak je znázorněno v [Další části](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="63879-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="63879-183">Použijte [vzor možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="63879-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="63879-184">Vytvořte třídu pro uložení vašich možností middlewaru, například:</span><span class="sxs-lookup"><span data-stu-id="63879-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="63879-185">Uložení hodnot možností</span><span class="sxs-lookup"><span data-stu-id="63879-185">Store the option values</span></span>

   <span data-ttu-id="63879-186">Konfigurační systém umožňuje ukládat hodnoty možností kdekoli, kde chcete.</span><span class="sxs-lookup"><span data-stu-id="63879-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="63879-187">Většina lokalit ale používá *appsettings.jsna*, takže provedeme tento přístup:</span><span class="sxs-lookup"><span data-stu-id="63879-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="63879-188">*MyMiddlewareOptionsSection* je tady název oddílu.</span><span class="sxs-lookup"><span data-stu-id="63879-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="63879-189">Nemusí být stejný jako název vaší třídy možností.</span><span class="sxs-lookup"><span data-stu-id="63879-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="63879-190">Přidružit hodnoty možnosti k třídě Options</span><span class="sxs-lookup"><span data-stu-id="63879-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="63879-191">Vzor možností používá ASP.NET Core rozhraní pro vkládání závislostí k přidružení typu možností (například) k `MyMiddlewareOptions` `MyMiddlewareOptions` objektu, který má skutečné možnosti.</span><span class="sxs-lookup"><span data-stu-id="63879-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="63879-192">Aktualizujte svou `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="63879-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="63879-193">Pokud používáte *appsettings.jsna*, přidejte ho do Tvůrce konfigurace v `Startup` konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="63879-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="63879-194">Nakonfigurujte službu možností:</span><span class="sxs-lookup"><span data-stu-id="63879-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="63879-195">Přidružte možnosti k vaší třídě možností:</span><span class="sxs-lookup"><span data-stu-id="63879-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="63879-196">Vloží možnosti do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="63879-197">To je podobné jako vkládání možností do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="63879-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="63879-198">Rozšiřující metoda [UseMiddleware](#http-modules-usemiddleware) , která přidává váš middleware, se `IApplicationBuilder` stará o vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="63879-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="63879-199">To není omezeno na `IOptions` objekty.</span><span class="sxs-lookup"><span data-stu-id="63879-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="63879-200">Jakýkoli jiný objekt, který váš middleware vyžaduje, může být tímto způsobem vložen.</span><span class="sxs-lookup"><span data-stu-id="63879-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="63879-201">Načítání možností middlewaru prostřednictvím přímého vkládání</span><span class="sxs-lookup"><span data-stu-id="63879-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="63879-202">Vzor možností má výhodu, že vytvoří volné propojení mezi hodnotami možností a jejich příjemci.</span><span class="sxs-lookup"><span data-stu-id="63879-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="63879-203">Po přidružení třídy možností se skutečnými hodnotami možností může jakákoliv jiná třída získat přístup k možnostem prostřednictvím rozhraní injektáže pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="63879-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="63879-204">Nemusíte předávat hodnoty možností.</span><span class="sxs-lookup"><span data-stu-id="63879-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="63879-205">Tato možnost se ukončí, pokud chcete stejný middleware používat dvakrát a s různými možnostmi.</span><span class="sxs-lookup"><span data-stu-id="63879-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="63879-206">Například autorizační middleware používaný v různých větvích umožňuje různé role.</span><span class="sxs-lookup"><span data-stu-id="63879-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="63879-207">Nelze přidružit dva různé objekty možností s jednou třídou možností.</span><span class="sxs-lookup"><span data-stu-id="63879-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="63879-208">Řešením je získat objekty možností se skutečnými hodnotami možností ve vaší `Startup` třídě a předat je přímo do každé instance vašeho middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="63879-209">Přidat druhý klíč k *appsettings.js*</span><span class="sxs-lookup"><span data-stu-id="63879-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="63879-210">Chcete-li přidat druhou sadu možností do *appsettings.jsv* souboru, použijte k jedinečné identifikaci nový klíč:</span><span class="sxs-lookup"><span data-stu-id="63879-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="63879-211">Načtěte hodnoty možností a předejte je middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="63879-212">`Use...`Metoda rozšíření (která přidá váš middleware do kanálu) je logické místo, které se má předat v hodnotách možností:</span><span class="sxs-lookup"><span data-stu-id="63879-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="63879-213">Povolte middlewari, aby převzala parametr options.</span><span class="sxs-lookup"><span data-stu-id="63879-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="63879-214">Poskytněte přetížení `Use...` metody rozšíření (která převezme parametr options a předá ho do `UseMiddleware` ).</span><span class="sxs-lookup"><span data-stu-id="63879-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="63879-215">Když `UseMiddleware` je volána s parametry, předá parametry konstruktoru middleware při vytváření instance objektu middleware.</span><span class="sxs-lookup"><span data-stu-id="63879-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="63879-216">Všimněte si, jak toto zabalí objekt Options v `OptionsWrapper` objektu.</span><span class="sxs-lookup"><span data-stu-id="63879-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="63879-217">To implementuje `IOptions` , podle očekávání konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="63879-218">Migrace na novou HttpContext</span><span class="sxs-lookup"><span data-stu-id="63879-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="63879-219">Dříve jste viděli, že `Invoke` metoda v middlewaru přebírá parametr typu `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="63879-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="63879-220">`HttpContext`významně se změnil v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63879-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="63879-221">V této části se dozvíte, jak přeložit nejčastěji používané vlastnosti [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) na nový `Microsoft.AspNetCore.Http.HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="63879-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="63879-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="63879-222">HttpContext</span></span>

<span data-ttu-id="63879-223">**HttpContext. Items** se přeloží:</span><span class="sxs-lookup"><span data-stu-id="63879-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="63879-224">**Jedinečné ID žádosti (žádný System. Web. HttpContext protějšek)**</span><span class="sxs-lookup"><span data-stu-id="63879-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="63879-225">Poskytuje jedinečné ID pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="63879-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="63879-226">Je velmi užitečné zahrnout do protokolů.</span><span class="sxs-lookup"><span data-stu-id="63879-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="63879-227">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="63879-227">HttpContext.Request</span></span>

<span data-ttu-id="63879-228">**HttpContext. Request. HttpMethod** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="63879-229">**HttpContext. Request. QueryString** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="63879-230">**HttpContext. Request. URL** a **HttpContext. Request. RawUrl** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="63879-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="63879-231">**HttpContext. Request. IsSecureConnection** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="63879-232">**HttpContext. Request. UserHostAddress** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="63879-233">**HttpContext. Request. cookies** se převede na:</span><span class="sxs-lookup"><span data-stu-id="63879-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="63879-234">**HttpContext. Request. Třída requestContext. parametr RouteData** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="63879-235">**HttpContext. Request. Headers** transformuje:</span><span class="sxs-lookup"><span data-stu-id="63879-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="63879-236">**HttpContext. Request. UserAgent** se převádí na:</span><span class="sxs-lookup"><span data-stu-id="63879-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="63879-237">**HttpContext. Request. UrlReferrer** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="63879-238">**HttpContext. Request. ContentType** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="63879-239">**HttpContext. Request. Form** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="63879-240">Přečte hodnoty formuláře pouze v případě, že je podřízený typ obsahu *x-www-form-urlencoded* nebo *form-data*.</span><span class="sxs-lookup"><span data-stu-id="63879-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="63879-241">**HttpContext. Request. InputStream** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="63879-242">Tento kód používejte pouze v middlewari typu pro zpracování, na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="63879-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="63879-243">Nezpracovaný text si můžete přečíst, jak je znázorněno výše na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="63879-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="63879-244">Middleware, který se pokouší přečíst tělo po prvním čtení, načte prázdné tělo.</span><span class="sxs-lookup"><span data-stu-id="63879-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="63879-245">To se nevztahuje na čtení formuláře uvedeného výše, protože to je hotové z vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="63879-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="63879-246">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="63879-246">HttpContext.Response</span></span>

<span data-ttu-id="63879-247">**HttpContext. Response. status** a **HttpContext. Response. StatusDescription** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="63879-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="63879-248">**Vlastnost HttpContext. Response. ContentEncoding** a **HttpContext. Response. ContentType** se převede na:</span><span class="sxs-lookup"><span data-stu-id="63879-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="63879-249">**HttpContext. Response. ContentType** sám o sobě také přeloží:</span><span class="sxs-lookup"><span data-stu-id="63879-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="63879-250">**HttpContext. Response. Output** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="63879-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="63879-251">**HttpContext. Response. zpracování funkce TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="63879-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="63879-252">Soubor [se zabývá](../fundamentals/request-features.md#middleware-and-request-features)popsáním souboru.</span><span class="sxs-lookup"><span data-stu-id="63879-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="63879-253">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="63879-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="63879-254">Posílání hlaviček odpovědí je složité, protože pokud jste je nastavili po zapsání nějakého textu odpovědi, nebudou odeslány.</span><span class="sxs-lookup"><span data-stu-id="63879-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="63879-255">Řešením je nastavit metodu zpětného volání, která bude volána přímo před zápisem do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="63879-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="63879-256">To se nejlépe provede na začátku `Invoke` metody v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="63879-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="63879-257">Je to metoda zpětného volání, která nastavuje hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="63879-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="63879-258">Následující kód nastaví metodu zpětného volání s názvem `SetHeaders` :</span><span class="sxs-lookup"><span data-stu-id="63879-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="63879-259">`SetHeaders`Metoda zpětného volání by vypadala takto:</span><span class="sxs-lookup"><span data-stu-id="63879-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="63879-260">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="63879-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="63879-261">Soubory cookie se cestují do prohlížeče v hlavičce odpovědi *souborového souboru cookie* .</span><span class="sxs-lookup"><span data-stu-id="63879-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="63879-262">V důsledku toho odeslání souborů cookie vyžaduje stejné zpětné volání, které se používá k odesílání hlaviček odpovědí:</span><span class="sxs-lookup"><span data-stu-id="63879-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="63879-263">`SetCookies`Metoda zpětného volání by vypadala takto:</span><span class="sxs-lookup"><span data-stu-id="63879-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="63879-264">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="63879-264">Additional resources</span></span>

* [<span data-ttu-id="63879-265">Obslužné rutiny HTTP a moduly HTTP – přehled</span><span class="sxs-lookup"><span data-stu-id="63879-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="63879-266">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="63879-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="63879-267">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="63879-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="63879-268">Middleware</span><span class="sxs-lookup"><span data-stu-id="63879-268">Middleware</span></span>](xref:fundamentals/middleware/index)
