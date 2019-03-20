---
title: Migrace moduly a obslužné rutiny HTTP do middlewaru ASP.NET Core
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 516230a66ee3edba986c91d79684256aa8e4c994
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209843"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="be2e0-102">Migrace moduly a obslužné rutiny HTTP do middlewaru ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be2e0-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="be2e0-103">Podle [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span><span class="sxs-lookup"><span data-stu-id="be2e0-103">By [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)</span></span>

<span data-ttu-id="be2e0-104">Tento článek popisuje, jak migrovat existující ASP.NET [z modulů HTTP a obslužných rutin system.webserver](/iis/configuration/system.webserver/) k ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="be2e0-104">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="be2e0-105">Moduly a obslužné rutiny kdykoli znovu spustit.</span><span class="sxs-lookup"><span data-stu-id="be2e0-105">Modules and handlers revisited</span></span>

<span data-ttu-id="be2e0-106">Než budete pokračovat s middlewarem ASP.NET Core, Pojďme nejdříve rekapitulace jak fungují moduly protokolu HTTP a obslužné rutiny:</span><span class="sxs-lookup"><span data-stu-id="be2e0-106">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Obslužná rutina moduly](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="be2e0-108">**Obslužné rutiny jsou:**</span><span class="sxs-lookup"><span data-stu-id="be2e0-108">**Handlers are:**</span></span>

* <span data-ttu-id="be2e0-109">Třídy, které implementují [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="be2e0-109">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="be2e0-110">Používá ke zpracování požadavků pomocí daného názvu souboru nebo příponu, například *.report*</span><span class="sxs-lookup"><span data-stu-id="be2e0-110">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="be2e0-111">[Nakonfigurované](/iis/configuration/system.webserver/handlers/) v *Web.config*</span><span class="sxs-lookup"><span data-stu-id="be2e0-111">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="be2e0-112">**Moduly jsou:**</span><span class="sxs-lookup"><span data-stu-id="be2e0-112">**Modules are:**</span></span>

* <span data-ttu-id="be2e0-113">Třídy, které implementují [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="be2e0-113">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="be2e0-114">Vyvolat pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="be2e0-114">Invoked for every request</span></span>

* <span data-ttu-id="be2e0-115">Možnost zkrácenou (zastaví další zpracování požadavku)</span><span class="sxs-lookup"><span data-stu-id="be2e0-115">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="be2e0-116">Možnost přidat do odpovědi HTTP, nebo vytvořte svoje vlastní</span><span class="sxs-lookup"><span data-stu-id="be2e0-116">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="be2e0-117">[Nakonfigurované](/iis/configuration/system.webserver/modules/) v *Web.config*</span><span class="sxs-lookup"><span data-stu-id="be2e0-117">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="be2e0-118">**Pořadí modulů zpracovat příchozí žádosti, ve které je určeno:**</span><span class="sxs-lookup"><span data-stu-id="be2e0-118">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="be2e0-119">[Životního cyklu aplikace](https://msdn.microsoft.com/library/ms227673.aspx), což je řada události vyvolané ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)atd. Každý modul vytvořit obslužnou rutinu pro jeden nebo více událostí.</span><span class="sxs-lookup"><span data-stu-id="be2e0-119">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="be2e0-120">Pro stejnou událost, pořadí, ve kterém jsou nakonfigurované v *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="be2e0-120">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="be2e0-121">Kromě modulů, můžete přidat obslužné rutiny pro události životního cyklu, které mají vaše *Global.asax.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-121">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="be2e0-122">Tyto rutiny spustit po obslužných rutin v nakonfigurovaných moduly.</span><span class="sxs-lookup"><span data-stu-id="be2e0-122">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="be2e0-123">Z obslužné rutiny a moduly, které middleware</span><span class="sxs-lookup"><span data-stu-id="be2e0-123">From handlers and modules to middleware</span></span>

<span data-ttu-id="be2e0-124">**Middleware je jednodušší než HTTP moduly a obslužné rutiny:**</span><span class="sxs-lookup"><span data-stu-id="be2e0-124">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="be2e0-125">Moduly, obslužné rutiny, *Global.asax.cs*, *Web.config* (s výjimkou konfigurace služby IIS) a životního cyklu aplikace jsou pryč</span><span class="sxs-lookup"><span data-stu-id="be2e0-125">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="be2e0-126">Role moduly a obslužné rutiny byly převzaty middlewaru</span><span class="sxs-lookup"><span data-stu-id="be2e0-126">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="be2e0-127">Middleware budou nakonfigurováni s použitím kódu spíše než v *Web.config*</span><span class="sxs-lookup"><span data-stu-id="be2e0-127">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="be2e0-128">[Větvení v kanálu](xref:fundamentals/middleware/index#use-run-and-map) umožňuje odesílat požadavky pro konkrétní middleware, na základě nejenom adresy URL ale také na hlavičky žádosti, řetězce dotazů, atd.</span><span class="sxs-lookup"><span data-stu-id="be2e0-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="be2e0-129">**Middleware jsou velmi podobné moduly:**</span><span class="sxs-lookup"><span data-stu-id="be2e0-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="be2e0-130">Vyvolá se v zásadě pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="be2e0-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="be2e0-131">Možnost zkrácenou žádost, pomocí [není předání požadavku do dalšího middlewaru](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="be2e0-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="be2e0-132">Možnost vytvářet své vlastní odpovědi HTTP</span><span class="sxs-lookup"><span data-stu-id="be2e0-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="be2e0-133">**Middleware a moduly jsou zpracovány v jiném pořadí:**</span><span class="sxs-lookup"><span data-stu-id="be2e0-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="be2e0-134">Pořadí middlewaru je založena na pořadí, ve kterém jste vložili do kanálu požadavku, zatímco je především podle pořadí modulů [životního cyklu aplikace](https://msdn.microsoft.com/library/ms227673.aspx) události</span><span class="sxs-lookup"><span data-stu-id="be2e0-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="be2e0-135">Middlewaru odpovědi probíhá v pořadí reverzní od požadavkům, přestože pořadí modulů je stejný pro požadavky a odpovědi</span><span class="sxs-lookup"><span data-stu-id="be2e0-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="be2e0-136">Zobrazit [vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="be2e0-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="be2e0-138">Všimněte si, jak na obrázku výše, že ověřovací middleware short-circuited požadavku.</span><span class="sxs-lookup"><span data-stu-id="be2e0-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="be2e0-139">Migrace kódu modulu do middlewaru</span><span class="sxs-lookup"><span data-stu-id="be2e0-139">Migrating module code to middleware</span></span>

<span data-ttu-id="be2e0-140">Existující modul HTTP bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="be2e0-141">Jak je znázorněno v [Middleware](xref:fundamentals/middleware/index) stránky, middleware ASP.NET Core je třída, která poskytuje `Invoke` pořízení – metoda `HttpContext` a vrácení `Task`.</span><span class="sxs-lookup"><span data-stu-id="be2e0-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="be2e0-142">Nové middleware bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="be2e0-143">Předchozí šablonu postupem middleware byla získána z část [zápis middlewaru](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="be2e0-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="be2e0-144">*MyMiddlewareExtensions* pomocná třída usnadňuje konfiguraci middlewaru v vaše `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="be2e0-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="be2e0-145">`UseMyMiddleware` Metoda přidá do kanálu požadavku vaše třída middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="be2e0-146">Získání služby nezbytné middlewarem vložený v konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="be2e0-147">Modul může ukončit požadavek, například pokud uživatel nemá oprávnění:</span><span class="sxs-lookup"><span data-stu-id="be2e0-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="be2e0-148">Tento middleware zpracovává není voláním `Invoke` na další middleware v kanálu.</span><span class="sxs-lookup"><span data-stu-id="be2e0-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="be2e0-149">Mějte na paměti, že to nebude ukončit plně požadavek, protože předchozí middlewares bude stále vyvolán při odpovědi díky cestě zpět prostřednictvím kanálu.</span><span class="sxs-lookup"><span data-stu-id="be2e0-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="be2e0-150">Při migraci vašeho modulu funkce do nové middleware možná zjistíte, že váš kód nebude kompilovat, protože `HttpContext` třídy významně změnil v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be2e0-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="be2e0-151">[Později](#migrating-to-the-new-httpcontext), uvidíte, jak migrovat do nového objektu HttpContext ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be2e0-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="be2e0-152">Migrace modulu vložení do kanálu požadavku</span><span class="sxs-lookup"><span data-stu-id="be2e0-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="be2e0-153">Z modulů HTTP jsou obvykle přidány do požadavku kanálu pomocí *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="be2e0-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="be2e0-154">Tím, že převod [přidání nové middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) požadavku kanálu v vaše `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="be2e0-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="be2e0-155">Přesné místě v kanálu, ve kterém vložíte nový middleware závisí na událost, která je zpracována jako modul (`BeginRequest`, `EndRequest`atd) a jeho pořadí v seznamu modulů v *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="be2e0-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="be2e0-156">Jak už jsme uvedli, neexistuje žádný životního cyklu aplikací v ASP.NET Core a pořadí, ve kterém jsou zpracovány odpovědi middlewarem se liší od pořadí, použít moduly.</span><span class="sxs-lookup"><span data-stu-id="be2e0-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="be2e0-157">To může se rozhodnout pořadí ještě náročnější.</span><span class="sxs-lookup"><span data-stu-id="be2e0-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="be2e0-158">Pokud řazení stane problém, můžete modul rozdělit do několika middlewarových komponent, které lze provést řazení nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="be2e0-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="be2e0-159">Migrace kódu obslužné rutiny pro middleware</span><span class="sxs-lookup"><span data-stu-id="be2e0-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="be2e0-160">Obslužné rutiny HTTP vypadá přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="be2e0-161">V projektu ASP.NET Core převedla to pro middleware nějak takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="be2e0-162">Tento middleware je velmi podobný middleware odpovídající moduly.</span><span class="sxs-lookup"><span data-stu-id="be2e0-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="be2e0-163">Pouze skutečné rozdílem je, že zde není žádná volání `_next.Invoke(context)`.</span><span class="sxs-lookup"><span data-stu-id="be2e0-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="be2e0-164">To dává smysl, protože rutina je na konci požadavku kanálu, takže neexistují žádné další middleware, který má být vyvolán.</span><span class="sxs-lookup"><span data-stu-id="be2e0-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="be2e0-165">Migrace obslužná rutina vložení do kanálu požadavku</span><span class="sxs-lookup"><span data-stu-id="be2e0-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="be2e0-166">Konfiguraci obslužné rutiny HTTP se provádí *Web.config* a vypadá přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="be2e0-167">To může převést tak, že přidáte nový middlewaru obslužná rutina požadavku kanálu v vaše `Startup` třídy, podobně jako s middlewarem převést z modulů.</span><span class="sxs-lookup"><span data-stu-id="be2e0-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="be2e0-168">Problém s tohoto přístupu je, že ho bude posílat všechny žádosti o novou obslužnou rutinu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="be2e0-169">Ale chcete jenom s danou příponou přenosu žádostí do vlastního middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="be2e0-170">Který by vám poskytlo stejné funkce, kterou jste používali s obslužnou rutinu HTTP.</span><span class="sxs-lookup"><span data-stu-id="be2e0-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="be2e0-171">Jedním z řešení je větvení kanálu pro požadavky s danou příponou pomocí `MapWhen` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="be2e0-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="be2e0-172">Můžete to provést ve stejném `Configure` metody, kde můžete přidávat další middleware:</span><span class="sxs-lookup"><span data-stu-id="be2e0-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="be2e0-173">`MapWhen` mají tyto parametry:</span><span class="sxs-lookup"><span data-stu-id="be2e0-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="be2e0-174">Výraz lambda, který přijímá `HttpContext` a vrátí `true` Pokud požadavek by měl přejděte dolů větve.</span><span class="sxs-lookup"><span data-stu-id="be2e0-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="be2e0-175">To znamená, že můžete rozvětvit požadavky nejen založené na jejich rozšíření, ale také na hlavičky žádosti, parametry řetězce dotazu, atd.</span><span class="sxs-lookup"><span data-stu-id="be2e0-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="be2e0-176">Výraz lambda, který přebírá `IApplicationBuilder` a přidá veškerý middleware pro větev.</span><span class="sxs-lookup"><span data-stu-id="be2e0-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="be2e0-177">To znamená, že přidáte další middleware do větve před middlewaru obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="be2e0-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="be2e0-178">Middleware přidané do kanálu než větev se vyvolá u všech požadavků. větev nebude mít vliv na ně.</span><span class="sxs-lookup"><span data-stu-id="be2e0-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="be2e0-179">Možnosti middlewaru pomocí vzoru možnosti načítání</span><span class="sxs-lookup"><span data-stu-id="be2e0-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="be2e0-180">Některé moduly a obslužné rutiny mají možnosti konfigurace, které jsou uloženy v *Web.config*. V ASP.NET Core, nový model konfigurace se používá místo *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="be2e0-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="be2e0-181">Nové [konfigurační systém](xref:fundamentals/configuration/index) vám tento problém vyřešit tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="be2e0-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="be2e0-182">Přímo vložit možnosti do middleware, jak je znázorněno [další části](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="be2e0-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="be2e0-183">Použití [možnosti vzor](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="be2e0-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="be2e0-184">Vytvoření třídy k umístění možnosti middlewaru, například:</span><span class="sxs-lookup"><span data-stu-id="be2e0-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="be2e0-185">Hodnoty možností Store</span><span class="sxs-lookup"><span data-stu-id="be2e0-185">Store the option values</span></span>

   <span data-ttu-id="be2e0-186">Konfigurace systému umožňuje ukládat hodnoty možností kamkoli chcete.</span><span class="sxs-lookup"><span data-stu-id="be2e0-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="be2e0-187">Ale nejvíce lokality použijte *appsettings.json*, takže provedeme tento přístup:</span><span class="sxs-lookup"><span data-stu-id="be2e0-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="be2e0-188">*MyMiddlewareOptionsSection* následuje název oddílu.</span><span class="sxs-lookup"><span data-stu-id="be2e0-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="be2e0-189">Nemusí být stejný jako název třídy možnosti.</span><span class="sxs-lookup"><span data-stu-id="be2e0-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="be2e0-190">Třída options přidružit hodnot možností</span><span class="sxs-lookup"><span data-stu-id="be2e0-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="be2e0-191">Možnosti vzor používá framework injektáž závislostí ASP.NET Core pro přiřazení možností typu (například `MyMiddlewareOptions`) s `MyMiddlewareOptions` objekt, který má skutečné možnosti.</span><span class="sxs-lookup"><span data-stu-id="be2e0-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="be2e0-192">Aktualizace vašeho `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="be2e0-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="be2e0-193">Pokud používáte *appsettings.json*, přidejte do konfigurace tvůrce v `Startup` konstruktor:</span><span class="sxs-lookup"><span data-stu-id="be2e0-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="be2e0-194">Nakonfigurujte možnosti služby:</span><span class="sxs-lookup"><span data-stu-id="be2e0-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="be2e0-195">Vaše možnosti přidružte vaše třída možnosti:</span><span class="sxs-lookup"><span data-stu-id="be2e0-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="be2e0-196">Vložit možnosti do vaší konstruktor middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="be2e0-197">To se podobá vkládá možnosti do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="be2e0-198">[UseMiddleware](#http-modules-usemiddleware) rozšiřující metoda, která přidá vaši middlewaru, který má `IApplicationBuilder` se postará o vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="be2e0-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="be2e0-199">To se neomezuje na `IOptions` objekty.</span><span class="sxs-lookup"><span data-stu-id="be2e0-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="be2e0-200">Tímto způsobem můžete vloženy jakýkoli jiný objekt, který vyžaduje middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="be2e0-201">Načítají se možnosti middlewaru prostřednictvím přímé vkládání</span><span class="sxs-lookup"><span data-stu-id="be2e0-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="be2e0-202">Možnosti vzor má výhodu, že vytvoří volné párování mezi hodnotami možnosti a jejich příjemce.</span><span class="sxs-lookup"><span data-stu-id="be2e0-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="be2e0-203">Po třídy jste přidružili k možnosti skutečné hodnoty, jiné třídy lze získat přístup k možnostem prostřednictvím rozhraní vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="be2e0-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="be2e0-204">Není třeba předávat hodnoty možnosti.</span><span class="sxs-lookup"><span data-stu-id="be2e0-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="be2e0-205">Tím je prolomen ale pokud chcete použít stejné middleware dvakrát, s použitím různých možností.</span><span class="sxs-lookup"><span data-stu-id="be2e0-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="be2e0-206">Například povolení middleware použitý v jiné větve, které umožňuje různé role.</span><span class="sxs-lookup"><span data-stu-id="be2e0-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="be2e0-207">Dva objekty různé možnosti nelze přidružit jeden možnosti třídy.</span><span class="sxs-lookup"><span data-stu-id="be2e0-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="be2e0-208">Řešením je získat možnosti objekty s hodnotami skutečné možnosti ve vaší `Startup` třídy a předat tyto přímo ke každé instanci middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="be2e0-209">Přidejte druhý klíč do *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="be2e0-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="be2e0-210">Chcete-li přidat druhou sadu možností *appsettings.json* souboru, aby byl jednoznačně identifikovaný použijte nový klíč:</span><span class="sxs-lookup"><span data-stu-id="be2e0-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="be2e0-211">Načíst hodnoty pro možnosti a předat je do middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="be2e0-212">`Use...` – Metoda rozšíření (která middlewaru přidá do kanálu) je logické místo a zajistěte tak předání hodnot možností:</span><span class="sxs-lookup"><span data-stu-id="be2e0-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="be2e0-213">Povolí middleware má provést parametr možnosti.</span><span class="sxs-lookup"><span data-stu-id="be2e0-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="be2e0-214">Poskytněte přetížení `Use...` – metoda rozšíření (, která přijímá parametr možnosti a předává jej do `UseMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="be2e0-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="be2e0-215">Když `UseMiddleware` se volá s parametry, předá parametry do konstruktoru middleware při vytvoření instance objektu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="be2e0-216">Všimněte si, jak to zabalí objekt možnosti v `OptionsWrapper` objektu.</span><span class="sxs-lookup"><span data-stu-id="be2e0-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="be2e0-217">To implementuje `IOptions`, jak očekává konstruktor middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="be2e0-218">Migrace do nového objektu HttpContext</span><span class="sxs-lookup"><span data-stu-id="be2e0-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="be2e0-219">Už dříve jste viděli, která `Invoke` metoda v middlewaru přebírá parametr typu `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="be2e0-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="be2e0-220">`HttpContext` má se významně změnil v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be2e0-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="be2e0-221">Tato část ukazuje, jak nejčastěji používané vlastnosti přeložit [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) k novému `Microsoft.AspNetCore.Http.HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="be2e0-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="be2e0-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="be2e0-222">HttpContext</span></span>

<span data-ttu-id="be2e0-223">**HttpContext.Items** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="be2e0-224">**ID požadavku jedinečné (nevyskytují System.Web.HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="be2e0-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="be2e0-225">Poskytuje jedinečný id pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="be2e0-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="be2e0-226">Velmi užitečné zahrnout do protokolů.</span><span class="sxs-lookup"><span data-stu-id="be2e0-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="be2e0-227">HttpContext.Request</span><span class="sxs-lookup"><span data-stu-id="be2e0-227">HttpContext.Request</span></span>

<span data-ttu-id="be2e0-228">**HttpContext.Request.HttpMethod** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="be2e0-229">**HttpContext.Request.QueryString** translates to:</span><span class="sxs-lookup"><span data-stu-id="be2e0-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="be2e0-230">**HttpContext.Request.Url** a **HttpContext.Request.RawUrl** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="be2e0-231">**HttpContext.Request.IsSecureConnection** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="be2e0-232">**HttpContext.Request.UserHostAddress** translates to:</span><span class="sxs-lookup"><span data-stu-id="be2e0-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="be2e0-233">**HttpContext.Request.Cookies** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="be2e0-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span><span class="sxs-lookup"><span data-stu-id="be2e0-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="be2e0-235">**HttpContext.Request.Headers** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="be2e0-236">**HttpContext.Request.UserAgent** translates to:</span><span class="sxs-lookup"><span data-stu-id="be2e0-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="be2e0-237">**HttpContext.Request.UrlReferrer** translates to:</span><span class="sxs-lookup"><span data-stu-id="be2e0-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="be2e0-238">**HttpContext.Request.ContentType** translates to:</span><span class="sxs-lookup"><span data-stu-id="be2e0-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="be2e0-239">**HttpContext.Request.Form** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="be2e0-240">Čtení hodnot formuláře pouze v případě, že je typem obsahu sub *x--www-form-urlencoded* nebo *data formuláře*.</span><span class="sxs-lookup"><span data-stu-id="be2e0-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="be2e0-241">**HttpContext.Request.InputStream** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="be2e0-242">Tento kód používejte pouze ve obslužné rutiny typu middleware, na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="be2e0-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="be2e0-243">Nezpracované tělo si můžete přečíst, jak je znázorněno výše jen jednou pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="be2e0-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="be2e0-244">Middleware pokusu o čtení textu po první čtení budou číst prázdným textem zprávy.</span><span class="sxs-lookup"><span data-stu-id="be2e0-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="be2e0-245">To neplatí pro čtení formuláře, jak je uvedeno výše, protože, který se provádí z vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="be2e0-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="be2e0-246">HttpContext.Response</span><span class="sxs-lookup"><span data-stu-id="be2e0-246">HttpContext.Response</span></span>

<span data-ttu-id="be2e0-247">**HttpContext.Response.Status** a **HttpContext.Response.StatusDescription** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="be2e0-248">**HttpContext.Response.ContentEncoding** a **HttpContext.Response.ContentType** přeložit na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="be2e0-249">**HttpContext.Response.ContentType** na svůj vlastní také převádí na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="be2e0-250">**HttpContext.Response.Output** se přeloží na:</span><span class="sxs-lookup"><span data-stu-id="be2e0-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="be2e0-251">**HttpContext.Response.TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="be2e0-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="be2e0-252">Poskytovat souboru je popsána [tady](../fundamentals/request-features.md#middleware-and-request-features).</span><span class="sxs-lookup"><span data-stu-id="be2e0-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="be2e0-253">**HttpContext.Response.Headers**</span><span class="sxs-lookup"><span data-stu-id="be2e0-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="be2e0-254">Odesílání hlaviček odpovědí je složité skutečnost, že pokud jste nastavili, je poté, co všechno se zapsala do těla odpovědi, že nebude odeslána.</span><span class="sxs-lookup"><span data-stu-id="be2e0-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="be2e0-255">Řešením je nastavení, která bude volána vpravo před zápisem do odpovědi spustí metodu zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="be2e0-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="be2e0-256">To se nejlépe provádí na začátku `Invoke` metoda v middlewaru.</span><span class="sxs-lookup"><span data-stu-id="be2e0-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="be2e0-257">Je tato metoda zpětného volání, který nastavuje vaše hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="be2e0-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="be2e0-258">Následující kód nastaví volá metodu zpětného volání `SetHeaders`:</span><span class="sxs-lookup"><span data-stu-id="be2e0-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="be2e0-259">`SetHeaders` Metoda zpětného volání bude vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="be2e0-260">**HttpContext.Response.Cookies**</span><span class="sxs-lookup"><span data-stu-id="be2e0-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="be2e0-261">Soubory cookie cestovat do prohlížeče v *Set-Cookie* hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="be2e0-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="be2e0-262">V důsledku toho odesílání souborů cookie vyžaduje stejné zpětného volání jako používané pro odesílání hlaviček odpovědí:</span><span class="sxs-lookup"><span data-stu-id="be2e0-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="be2e0-263">`SetCookies` Metoda zpětného volání by vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="be2e0-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="be2e0-264">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="be2e0-264">Additional resources</span></span>

* [<span data-ttu-id="be2e0-265">Přehled protokolu HTTP moduly a obslužné rutiny HTTP</span><span class="sxs-lookup"><span data-stu-id="be2e0-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="be2e0-266">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="be2e0-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="be2e0-267">Spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="be2e0-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="be2e0-268">Middleware</span><span class="sxs-lookup"><span data-stu-id="be2e0-268">Middleware</span></span>](xref:fundamentals/middleware/index)
