---
title: Migrace obslužných rutin a modulů HTTP do ASP.NET Core middlewaru
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: bdf27ccb742d4bc05bac71e6c96d71c38dcb4b62
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975494"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="af072-102">Migrace obslužných rutin a modulů HTTP do ASP.NET Core middlewaru</span><span class="sxs-lookup"><span data-stu-id="af072-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="af072-103">Tento článek ukazuje, jak migrovat existující [moduly a obslužné rutiny ASP.NET HTTP ze serveru System. webServer](/iis/configuration/system.webserver/) do ASP.NET Core [middlewaru](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="af072-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="af072-104">Přenavštívené moduly a obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="af072-104">Modules and handlers revisited</span></span>

<span data-ttu-id="af072-105">Než začnete ASP.NET Core middlewaru, nejdřív si rekapitulace, jak fungují moduly HTTP a obslužné rutiny:</span><span class="sxs-lookup"><span data-stu-id="af072-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Obslužná rutina modulů](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="af072-107">**Obslužné rutiny:**</span><span class="sxs-lookup"><span data-stu-id="af072-107">**Handlers are:**</span></span>

* <span data-ttu-id="af072-108">Třídy, které implementují [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="af072-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="af072-109">Slouží ke zpracování požadavků s daným názvem nebo příponou souboru, například *. Report.*</span><span class="sxs-lookup"><span data-stu-id="af072-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="af072-110">[Nakonfigurováno](/iis/configuration/system.webserver/handlers/) v *souboru Web. config*</span><span class="sxs-lookup"><span data-stu-id="af072-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="af072-111">**Moduly jsou:**</span><span class="sxs-lookup"><span data-stu-id="af072-111">**Modules are:**</span></span>

* <span data-ttu-id="af072-112">Třídy, které implementují [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="af072-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="af072-113">Vyvoláno pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="af072-113">Invoked for every request</span></span>

* <span data-ttu-id="af072-114">Schopnost krátkodobého okruhu (zastavení dalšího zpracování žádosti)</span><span class="sxs-lookup"><span data-stu-id="af072-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="af072-115">Může přidat do odpovědi HTTP nebo vytvořit vlastní.</span><span class="sxs-lookup"><span data-stu-id="af072-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="af072-116">[Nakonfigurováno](/iis/configuration/system.webserver/modules/) v *souboru Web. config*</span><span class="sxs-lookup"><span data-stu-id="af072-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="af072-117">**Pořadí, ve kterém moduly zpracovávají příchozí požadavky, určuje:**</span><span class="sxs-lookup"><span data-stu-id="af072-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="af072-118">[Životní cyklus aplikace](https://msdn.microsoft.com/library/ms227673.aspx), což je události řady, které nástroj ASP.NET vyvolal: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)atd. Každý modul může vytvořit obslužnou rutinu pro jednu nebo více událostí.</span><span class="sxs-lookup"><span data-stu-id="af072-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="af072-119">Pro stejnou událost, pořadí, ve kterém jsou konfigurovány v *souboru Web. config*.</span><span class="sxs-lookup"><span data-stu-id="af072-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="af072-120">Kromě modulů můžete přidat obslužné rutiny pro události životního cyklu do souboru *Global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="af072-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="af072-121">Tyto obslužné rutiny jsou spouštěny po obslužných rutinách v konfigurovaných modulech.</span><span class="sxs-lookup"><span data-stu-id="af072-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="af072-122">Z obslužných rutin a modulů pro middleware</span><span class="sxs-lookup"><span data-stu-id="af072-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="af072-123">**Middleware jsou jednodušší než moduly HTTP a obslužné rutiny:**</span><span class="sxs-lookup"><span data-stu-id="af072-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="af072-124">Moduly, obslužné rutiny, *Global.asax.cs*, *Web. config* (s výjimkou konfigurace služby IIS) a životní cyklus aplikace se odešlou.</span><span class="sxs-lookup"><span data-stu-id="af072-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="af072-125">Služba middlewaru převzala role obou modulů i obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="af072-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="af072-126">Middleware jsou nakonfigurovány pomocí kódu, nikoli v *souboru Web. config.*</span><span class="sxs-lookup"><span data-stu-id="af072-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="af072-127">[Větvení kanálu](xref:fundamentals/middleware/index#use-run-and-map) vám umožňuje odesílat požadavky na konkrétní middleware na základě nejen adresy URL, ale také na hlavičkách žádostí, řetězcích dotazů atd.</span><span class="sxs-lookup"><span data-stu-id="af072-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="af072-128">**Middleware jsou velmi podobné modulům:**</span><span class="sxs-lookup"><span data-stu-id="af072-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="af072-129">Vyvoláno v principu pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="af072-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="af072-130">Může se stát, že žádost nebude v krátkém okruhu předávat [do dalšího middlewaru](#http-modules-shortcircuiting-middleware) .</span><span class="sxs-lookup"><span data-stu-id="af072-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="af072-131">Schopné vytvořit vlastní odpověď HTTP</span><span class="sxs-lookup"><span data-stu-id="af072-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="af072-132">**Middleware a moduly jsou zpracovávány v jiném pořadí:**</span><span class="sxs-lookup"><span data-stu-id="af072-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="af072-133">Pořadí middlewaru je založené na pořadí, ve kterém jsou vloženy do kanálu požadavků, zatímco pořadí modulů je založeno hlavně na událostech [životního cyklu aplikace](https://msdn.microsoft.com/library/ms227673.aspx) .</span><span class="sxs-lookup"><span data-stu-id="af072-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="af072-134">Pořadí middlewaru pro odpovědi je opačné než u požadavků, zatímco pořadí modulů je u požadavků a odpovědí stejné.</span><span class="sxs-lookup"><span data-stu-id="af072-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="af072-135">Viz [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="af072-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="af072-137">Všimněte si, jak je výše uvedený obrázek, ověřovací middleware krátce vyřídil požadavek.</span><span class="sxs-lookup"><span data-stu-id="af072-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="af072-138">Migrace kódu modulu do middlewaru</span><span class="sxs-lookup"><span data-stu-id="af072-138">Migrating module code to middleware</span></span>

<span data-ttu-id="af072-139">Existující modul HTTP bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="af072-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="af072-140">Jak je znázorněno [](xref:fundamentals/middleware/index) na stránce middleware, ASP.NET Core middleware je třída, která `Invoke` zpřístupňuje metodu `HttpContext` s návratem `Task`a.</span><span class="sxs-lookup"><span data-stu-id="af072-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="af072-141">Váš nový middleware bude vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="af072-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="af072-142">Předchozí šablona middlewaru byla pořízena z oddílu o [zápisu middlewaru](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="af072-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="af072-143">Pomocná třída *MyMiddlewareExtensions* usnadňuje konfiguraci middlewaru ve vaší `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="af072-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="af072-144">`UseMyMiddleware` Metoda přidá třídu middleware do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="af072-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="af072-145">Služby, které vyžaduje middleware, se vloží do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="af072-146">Váš modul může ukončit žádost, například pokud uživatel není autorizovaný:</span><span class="sxs-lookup"><span data-stu-id="af072-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="af072-147">Middleware to zpracovává tím, že `Invoke` nevolá na další middleware v kanálu.</span><span class="sxs-lookup"><span data-stu-id="af072-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="af072-148">Mějte na paměti, že to neukončí celý požadavek, protože předchozí middleware budou přesto vyvolány, když odpověď prochází způsobem kanálu.</span><span class="sxs-lookup"><span data-stu-id="af072-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="af072-149">Když migrujete funkci modulu na nový middleware, může se stát, že váš kód nebude zkompilován, protože se `HttpContext` v ASP.NET Core významně změnila třída.</span><span class="sxs-lookup"><span data-stu-id="af072-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="af072-150">[Později](#migrating-to-the-new-httpcontext)se dozvíte, jak migrovat do nové ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="af072-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="af072-151">Migruje se vložení modulu do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="af072-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="af072-152">Moduly HTTP se obvykle přidávají do kanálu požadavků pomocí *souboru Web. config*:</span><span class="sxs-lookup"><span data-stu-id="af072-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="af072-153">Převeďte to [přidáním nového middlewaru](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) do kanálu požadavků ve vaší `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="af072-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="af072-154">Přesné místo v kanálu, kam vložíte nový middleware, závisí na události, kterou zpracuje jako modul (`BeginRequest`, `EndRequest`atd.) a pořadí v seznamu modulů v *souboru Web. config*.</span><span class="sxs-lookup"><span data-stu-id="af072-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="af072-155">Jak je uvedeno výše, v ASP.NET Core není žádný životní cyklus aplikace a pořadí, ve kterém jsou odpovědi zpracovávány middlewarem, se liší od pořadí používaného moduly.</span><span class="sxs-lookup"><span data-stu-id="af072-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="af072-156">To může být náročnější na rozhodnutí o řazení.</span><span class="sxs-lookup"><span data-stu-id="af072-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="af072-157">Pokud se řazení stalo problémem, mohli byste svůj modul rozdělit do několika součástí middlewaru, které je možné seřadit nezávisle.</span><span class="sxs-lookup"><span data-stu-id="af072-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="af072-158">Migrace kódu obslužné rutiny do middlewaru</span><span class="sxs-lookup"><span data-stu-id="af072-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="af072-159">Obslužná rutina HTTP vypadá nějak takto:</span><span class="sxs-lookup"><span data-stu-id="af072-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="af072-160">V projektu ASP.NET Core byste ho přeložili na middleware podobnou této:</span><span class="sxs-lookup"><span data-stu-id="af072-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="af072-161">Tento middleware je velmi podobný middlewaru, který odpovídá modulům.</span><span class="sxs-lookup"><span data-stu-id="af072-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="af072-162">Jediným skutečným rozdílem je, že zde není žádné volání `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="af072-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="af072-163">To dává smysl, protože obslužná rutina je na konci kanálu požadavků, takže nebudete moct vyvolat žádné další middleware.</span><span class="sxs-lookup"><span data-stu-id="af072-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="af072-164">Migrace vložení obslužné rutiny do kanálu žádosti</span><span class="sxs-lookup"><span data-stu-id="af072-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="af072-165">Konfigurace obslužné rutiny HTTP se provádí v *souboru Web. config* a vypadá nějak takto:</span><span class="sxs-lookup"><span data-stu-id="af072-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="af072-166">To můžete převést přidáním nového middlewaru obslužné rutiny do kanálu požadavků ve vaší `Startup` třídě, podobně jako middleware převedené z modulů.</span><span class="sxs-lookup"><span data-stu-id="af072-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="af072-167">Problém s tímto přístupem je, že by poslal všechny žádosti do nového middlewaru obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="af072-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="af072-168">Nicméně budete chtít, aby žádosti s daným rozšířením dosáhly vašeho middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="af072-169">To vám poskytne stejné funkce jako u vaší obslužné rutiny HTTP.</span><span class="sxs-lookup"><span data-stu-id="af072-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="af072-170">Jedním z řešení je vytvořit větev kanálu pro žádosti s danou příponou pomocí `MapWhen` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="af072-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="af072-171">Provedete to stejným způsobem `Configure` , jakým přidáte další middleware:</span><span class="sxs-lookup"><span data-stu-id="af072-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="af072-172">`MapWhen`přebírá tyto parametry:</span><span class="sxs-lookup"><span data-stu-id="af072-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="af072-173">Výraz lambda, který přebírá `HttpContext` a vrátí `true` , zda by požadavek měl přejít mimo větev.</span><span class="sxs-lookup"><span data-stu-id="af072-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="af072-174">To znamená, že můžete požadavky větví nejenom na základě jejich rozšíření, ale také v hlavičkách žádostí, parametrech řetězce dotazu atd.</span><span class="sxs-lookup"><span data-stu-id="af072-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="af072-175">Lambda, který přebírá `IApplicationBuilder` a přidává všechny middleware pro větev.</span><span class="sxs-lookup"><span data-stu-id="af072-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="af072-176">To znamená, že můžete přidat další middleware do větve před middlewarem vaší obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="af072-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="af072-177">Middleware přidaný do kanálu před tím, než se větev vyvolá u všech požadavků; Tato větev nebude mít žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="af072-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="af072-178">Načítání možností middlewaru pomocí vzoru možností</span><span class="sxs-lookup"><span data-stu-id="af072-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="af072-179">Některé moduly a obslužné rutiny mají možnosti konfigurace, které jsou uloženy v *souboru Web. config*. V ASP.NET Core se však používá nový model konfigurace místo souboru *Web. config*.</span><span class="sxs-lookup"><span data-stu-id="af072-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="af072-180">Nový [konfigurační systém](xref:fundamentals/configuration/index) poskytuje tyto možnosti, jak tento problém vyřešit:</span><span class="sxs-lookup"><span data-stu-id="af072-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="af072-181">Přímo do middlewaru založit možnosti, jak je znázorněno v [Další části](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="af072-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="af072-182">Použijte [vzor možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="af072-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="af072-183">Vytvořte třídu pro uložení vašich možností middlewaru, například:</span><span class="sxs-lookup"><span data-stu-id="af072-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="af072-184">Uložení hodnot možností</span><span class="sxs-lookup"><span data-stu-id="af072-184">Store the option values</span></span>

   <span data-ttu-id="af072-185">Konfigurační systém umožňuje ukládat hodnoty možností kdekoli, kde chcete.</span><span class="sxs-lookup"><span data-stu-id="af072-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="af072-186">Většina lokalit ale používá *appSettings. JSON*, takže probereme tento přístup:</span><span class="sxs-lookup"><span data-stu-id="af072-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="af072-187">*MyMiddlewareOptionsSection* je tady název oddílu.</span><span class="sxs-lookup"><span data-stu-id="af072-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="af072-188">Nemusí být stejný jako název vaší třídy možností.</span><span class="sxs-lookup"><span data-stu-id="af072-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="af072-189">Přidružit hodnoty možnosti k třídě Options</span><span class="sxs-lookup"><span data-stu-id="af072-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="af072-190">Vzor možností používá ASP.NET Core rozhraní pro vkládání závislostí k přidružení typu možností (například `MyMiddlewareOptions`) `MyMiddlewareOptions` k objektu, který má skutečné možnosti.</span><span class="sxs-lookup"><span data-stu-id="af072-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="af072-191">Aktualizujte `Startup` svou třídu:</span><span class="sxs-lookup"><span data-stu-id="af072-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="af072-192">Pokud používáte *appSettings. JSON*, přidejte jej do Tvůrce konfigurace v `Startup` konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="af072-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="af072-193">Nakonfigurujte službu možností:</span><span class="sxs-lookup"><span data-stu-id="af072-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="af072-194">Přidružte možnosti k vaší třídě možností:</span><span class="sxs-lookup"><span data-stu-id="af072-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="af072-195">Vloží možnosti do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="af072-196">To je podobné jako vkládání možností do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="af072-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="af072-197">Rozšiřující metoda [UseMiddleware](#http-modules-usemiddleware) , která přidává váš middleware `IApplicationBuilder` , se stará o vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="af072-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="af072-198">To není omezeno `IOptions` na objekty.</span><span class="sxs-lookup"><span data-stu-id="af072-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="af072-199">Jakýkoli jiný objekt, který váš middleware vyžaduje, může být tímto způsobem vložen.</span><span class="sxs-lookup"><span data-stu-id="af072-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="af072-200">Načítání možností middlewaru prostřednictvím přímého vkládání</span><span class="sxs-lookup"><span data-stu-id="af072-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="af072-201">Vzor možností má výhodu, že vytvoří volné propojení mezi hodnotami možností a jejich příjemci.</span><span class="sxs-lookup"><span data-stu-id="af072-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="af072-202">Po přidružení třídy možností se skutečnými hodnotami možností může jakákoliv jiná třída získat přístup k možnostem prostřednictvím rozhraní injektáže pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="af072-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="af072-203">Nemusíte předávat hodnoty možností.</span><span class="sxs-lookup"><span data-stu-id="af072-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="af072-204">Tato možnost se ukončí, pokud chcete stejný middleware používat dvakrát a s různými možnostmi.</span><span class="sxs-lookup"><span data-stu-id="af072-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="af072-205">Například autorizační middleware používaný v různých větvích umožňuje různé role.</span><span class="sxs-lookup"><span data-stu-id="af072-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="af072-206">Nelze přidružit dva různé objekty možností s jednou třídou možností.</span><span class="sxs-lookup"><span data-stu-id="af072-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="af072-207">Řešením je získat objekty možností se skutečnými hodnotami možností ve vaší `Startup` třídě a předat je přímo do každé instance vašeho middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="af072-208">Přidat druhý klíč do souboru *appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="af072-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="af072-209">Chcete-li přidat druhou sadu možností do souboru *appSettings. JSON* , použijte k jedinečné identifikaci nový klíč:</span><span class="sxs-lookup"><span data-stu-id="af072-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="af072-210">Načtěte hodnoty možností a předejte je middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="af072-211">Metoda `Use...` rozšíření (která přidá váš middleware do kanálu) je logické místo, které se má předat v hodnotách možností:</span><span class="sxs-lookup"><span data-stu-id="af072-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="af072-212">Povolte middlewari, aby převzala parametr options.</span><span class="sxs-lookup"><span data-stu-id="af072-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="af072-213">Poskytněte přetížení `Use...` metody rozšíření (která převezme parametr options a předá ho do `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="af072-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="af072-214">Když `UseMiddleware` je volána s parametry, předá parametry konstruktoru middleware při vytváření instance objektu middleware.</span><span class="sxs-lookup"><span data-stu-id="af072-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="af072-215">Všimněte si, jak toto zabalí objekt Options v `OptionsWrapper` objektu.</span><span class="sxs-lookup"><span data-stu-id="af072-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="af072-216">To implementuje `IOptions`, podle očekávání konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="af072-217">Migrace na novou HttpContext</span><span class="sxs-lookup"><span data-stu-id="af072-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="af072-218">Dříve jste viděli, že `Invoke` metoda v middlewaru přebírá parametr typu: `HttpContext`</span><span class="sxs-lookup"><span data-stu-id="af072-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="af072-219">`HttpContext`významně se změnil v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af072-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="af072-220">V této části se dozvíte, jak přeložit nejčastěji používané vlastnosti [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) na nový `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="af072-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="af072-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="af072-221">HttpContext</span></span>

<span data-ttu-id="af072-222">**HttpContext. Items** se přeloží:</span><span class="sxs-lookup"><span data-stu-id="af072-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="af072-223">**Jedinečné ID žádosti (žádný System. Web. HttpContext protějšek)**</span><span class="sxs-lookup"><span data-stu-id="af072-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="af072-224">Poskytuje jedinečné ID pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="af072-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="af072-225">Je velmi užitečné zahrnout do protokolů.</span><span class="sxs-lookup"><span data-stu-id="af072-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="af072-226">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="af072-226">HttpContext.Request</span></span>

<span data-ttu-id="af072-227">**HttpContext. Request. HttpMethod** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="af072-228">**HttpContext. Request. QueryString** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="af072-229">**HttpContext. Request. URL** a **HttpContext. Request. RawUrl** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="af072-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="af072-230">**HttpContext. Request. IsSecureConnection** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="af072-231">**HttpContext.Request.UserHostAddress** translates to:</span><span class="sxs-lookup"><span data-stu-id="af072-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="af072-232">**HttpContext. Request. cookies** se převede na:</span><span class="sxs-lookup"><span data-stu-id="af072-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="af072-233">**HttpContext. Request. Třída requestContext. parametr RouteData** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="af072-234">**HttpContext. Request. Headers** transformuje:</span><span class="sxs-lookup"><span data-stu-id="af072-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="af072-235">**HttpContext. Request. UserAgent** se převádí na:</span><span class="sxs-lookup"><span data-stu-id="af072-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="af072-236">**HttpContext. Request. UrlReferrer** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="af072-237">**HttpContext. Request. ContentType** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="af072-238">**HttpContext. Request. Form** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="af072-239">Přečte hodnoty formuláře pouze v případě, že je podřízený typ obsahu *x-www-form-urlencoded* nebo *form-data*.</span><span class="sxs-lookup"><span data-stu-id="af072-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="af072-240">**HttpContext. Request. InputStream** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="af072-241">Tento kód používejte pouze v middlewari typu pro zpracování, na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="af072-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="af072-242">Nezpracovaný text si můžete přečíst, jak je znázorněno výše na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="af072-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="af072-243">Middleware, který se pokouší přečíst tělo po prvním čtení, načte prázdné tělo.</span><span class="sxs-lookup"><span data-stu-id="af072-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="af072-244">To se nevztahuje na čtení formuláře uvedeného výše, protože to je hotové z vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="af072-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="af072-245">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="af072-245">HttpContext.Response</span></span>

<span data-ttu-id="af072-246">**HttpContext. Response. status** a **HttpContext. Response. StatusDescription** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="af072-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="af072-247">**Vlastnost HttpContext. Response. ContentEncoding** a **HttpContext. Response. ContentType** se převede na:</span><span class="sxs-lookup"><span data-stu-id="af072-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="af072-248">**HttpContext. Response. ContentType** sám o sobě také přeloží:</span><span class="sxs-lookup"><span data-stu-id="af072-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="af072-249">**HttpContext. Response. Output** se překládá na:</span><span class="sxs-lookup"><span data-stu-id="af072-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="af072-250">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="af072-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="af072-251">Soubor se zabývá popsáním [](../fundamentals/request-features.md#middleware-and-request-features)souboru.</span><span class="sxs-lookup"><span data-stu-id="af072-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="af072-252">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="af072-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="af072-253">Posílání hlaviček odpovědí je složité, protože pokud jste je nastavili po zapsání nějakého textu odpovědi, nebudou odeslány.</span><span class="sxs-lookup"><span data-stu-id="af072-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="af072-254">Řešením je nastavit metodu zpětného volání, která bude volána přímo před zápisem do odpovědi.</span><span class="sxs-lookup"><span data-stu-id="af072-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="af072-255">To se nejlépe provede na začátku `Invoke` metody v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="af072-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="af072-256">Je to metoda zpětného volání, která nastavuje hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="af072-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="af072-257">Následující kód nastaví metodu zpětného volání s `SetHeaders`názvem:</span><span class="sxs-lookup"><span data-stu-id="af072-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="af072-258">Metoda `SetHeaders` zpětného volání by vypadala takto:</span><span class="sxs-lookup"><span data-stu-id="af072-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="af072-259">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="af072-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="af072-260">Soubory cookie se cestují do prohlížeče v hlavičce odpovědi *souborového souboru cookie* .</span><span class="sxs-lookup"><span data-stu-id="af072-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="af072-261">V důsledku toho odeslání souborů cookie vyžaduje stejné zpětné volání, které se používá k odesílání hlaviček odpovědí:</span><span class="sxs-lookup"><span data-stu-id="af072-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="af072-262">Metoda `SetCookies` zpětného volání by vypadala takto:</span><span class="sxs-lookup"><span data-stu-id="af072-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="af072-263">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="af072-263">Additional resources</span></span>

* [<span data-ttu-id="af072-264">Obslužné rutiny HTTP a moduly HTTP – přehled</span><span class="sxs-lookup"><span data-stu-id="af072-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="af072-265">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="af072-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="af072-266">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="af072-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="af072-267">Middleware</span><span class="sxs-lookup"><span data-stu-id="af072-267">Middleware</span></span>](xref:fundamentals/middleware/index)
