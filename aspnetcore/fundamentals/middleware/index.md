---
title: Middleware ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core middleware a kanál žádosti.
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2018
uid: fundamentals/middleware/index
ms.openlocfilehash: 6daf201654d68de978141f3dd42d48732c1161f7
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570032"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="cf429-103">Middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf429-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="cf429-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cf429-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cf429-105">Middleware je software, který je sestaven do kanálu služby aplikace pro zpracování požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="cf429-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="cf429-106">Jednotlivé komponenty:</span><span class="sxs-lookup"><span data-stu-id="cf429-106">Each component:</span></span>

* <span data-ttu-id="cf429-107">Zvolí, zda se má předat požadavky na další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="cf429-108">Práci můžete provádět, před a po zavolání další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-108">Can perform work before and after the next component in the pipeline is invoked.</span></span>

<span data-ttu-id="cf429-109">Delegáti požadavku se používají k vytvoření kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="cf429-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="cf429-110">Delegáti žádost o zpracování konkrétního požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="cf429-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="cf429-111">Požádat o Delegáti jsou nakonfigurováni pomocí <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="cf429-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="cf429-112">Delegát jednotlivých požadavků může být zadaný řádek jako anonymní metody (označované jako middlewaru vložených), nebo může být definován ve třídě opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="cf429-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="cf429-113">Tyto opakovaně použitelné třídy a v řádku anonymní metody jsou *middleware*, označované také jako *middlewarových komponent*.</span><span class="sxs-lookup"><span data-stu-id="cf429-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="cf429-114">Jednotlivé komponenty middleware v kanálu požadavku zodpovídá za vyvolání další komponenta v kanálu nebo zkrácenou kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span>

<span data-ttu-id="cf429-115"><xref:migration/http-modules> Vysvětluje rozdíl mezi požadavek kanály v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cf429-115"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="cf429-116">Vytvoření kanálu middlewaru s IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="cf429-116">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="cf429-117">Kanál žádosti ASP.NET Core se skládá z posloupnost požadavek delegáty, volá se jedna po druhé.</span><span class="sxs-lookup"><span data-stu-id="cf429-117">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="cf429-118">Následující diagram ukazuje koncept.</span><span class="sxs-lookup"><span data-stu-id="cf429-118">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="cf429-119">Vlákno provádění postupuje černé šipky.</span><span class="sxs-lookup"><span data-stu-id="cf429-119">The thread of execution follows the black arrows.</span></span>

![Vzor zpracování požadavku zobrazující žádosti přicházející, zpracování až tři middlewares a odpovědi opuštění aplikace.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="cf429-123">Všem delegátům můžete provádět operace, před a po dalším delegáta.</span><span class="sxs-lookup"><span data-stu-id="cf429-123">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="cf429-124">Také můžete rozhodnout delegáta úspěšná žádost o další delegátem, která se nazývá *zkrácenou kanál žádosti*.</span><span class="sxs-lookup"><span data-stu-id="cf429-124">A delegate can also decide to not pass a request to the next delegate, which is called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="cf429-125">Krátký cyklus je často žádoucí, protože tím eliminujete zbytečné práce.</span><span class="sxs-lookup"><span data-stu-id="cf429-125">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="cf429-126">Middleware statické soubory můžete například vrátit žádost o statický soubor a zkrácenou rest z kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-126">For example, Static File Middleware can return a request for a static file and short-circuit the rest of the pipeline.</span></span> <span data-ttu-id="cf429-127">Delegáti zpracování výjimek se nazývají již v rané fázi v kanálu, takže se můžete zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-127">Exception-handling delegates are called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="cf429-128">Nejjednodušší možný aplikace ASP.NET Core nastaví delegáta jedné žádosti, která zpracovává všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="cf429-128">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="cf429-129">Tento případ neobsahuje kanál aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="cf429-129">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="cf429-130">Místo toho jednoho anonymní funkce je volána v reakci na každý požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="cf429-130">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="cf429-131">První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegáta ukončí kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-131">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="cf429-132">Zřetězit více požadavek delegátů spolu s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="cf429-132">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="cf429-133">`next` Parametr představuje další delegáta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-133">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="cf429-134">Můžete zkrácenou kanál pomocí *není* volání *Další* parametru.</span><span class="sxs-lookup"><span data-stu-id="cf429-134">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="cf429-135">Můžete obvykle provádět akce před i po další delegáta, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="cf429-135">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

> [!WARNING]
> <span data-ttu-id="cf429-136">Nevolejte `next.Invoke` po odeslání odpovědi klientovi.</span><span class="sxs-lookup"><span data-stu-id="cf429-136">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="cf429-137">Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po spuštění odpovědi vrátit výjimku.</span><span class="sxs-lookup"><span data-stu-id="cf429-137">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="cf429-138">Například změny, jako je nastavení hlaviček a stavovým kódem vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="cf429-138">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="cf429-139">Zápis do datové části odpovědi po volání `next`:</span><span class="sxs-lookup"><span data-stu-id="cf429-139">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="cf429-140">Může způsobit narušení protokolu.</span><span class="sxs-lookup"><span data-stu-id="cf429-140">May cause a protocol violation.</span></span> <span data-ttu-id="cf429-141">Například zápis informace než uvedené `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="cf429-141">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="cf429-142">Může dojít k poškození formát těla zprávy.</span><span class="sxs-lookup"><span data-stu-id="cf429-142">May corrupt the body format.</span></span> <span data-ttu-id="cf429-143">HTML zápatí například zápis do souboru šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="cf429-143">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="cf429-144"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečné nápovědu k označení, pokud byly odeslány hlavičky nebo text byl zapsán do.</span><span class="sxs-lookup"><span data-stu-id="cf429-144"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="cf429-145">Pořadí</span><span class="sxs-lookup"><span data-stu-id="cf429-145">Order</span></span>

<span data-ttu-id="cf429-146">Pořadí, ve kterém middlewarových komponent přidány `Startup.Configure` metoda definuje pořadí, ve kterém jsou vyvolány middlewarových komponent na požadavky a obrácenému pořadí pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="cf429-146">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="cf429-147">Pořadí je důležité pro zabezpečení, výkon a funkčnost.</span><span class="sxs-lookup"><span data-stu-id="cf429-147">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="cf429-148">Následující `Startup.Configure` metoda přidá middlewarových komponent pro běžné scénáře aplikací:</span><span class="sxs-lookup"><span data-stu-id="cf429-148">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="cf429-149">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="cf429-149">Exception/error handling</span></span>
1. <span data-ttu-id="cf429-150">Protokol zabezpečení striktní přenosu HTTP</span><span class="sxs-lookup"><span data-stu-id="cf429-150">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="cf429-151">Přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="cf429-151">HTTPS redirection</span></span>
1. <span data-ttu-id="cf429-152">Statický souborový server</span><span class="sxs-lookup"><span data-stu-id="cf429-152">Static file server</span></span>
1. <span data-ttu-id="cf429-153">Vynucení zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="cf429-153">Cookie policy enforcement</span></span>
1. <span data-ttu-id="cf429-154">Ověřování</span><span class="sxs-lookup"><span data-stu-id="cf429-154">Authentication</span></span>
1. <span data-ttu-id="cf429-155">Relace</span><span class="sxs-lookup"><span data-stu-id="cf429-155">Session</span></span>
1. <span data-ttu-id="cf429-156">MVC</span><span class="sxs-lookup"><span data-stu-id="cf429-156">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. <span data-ttu-id="cf429-157">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="cf429-157">Exception/error handling</span></span>
1. <span data-ttu-id="cf429-158">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="cf429-158">Static files</span></span>
1. <span data-ttu-id="cf429-159">Ověřování</span><span class="sxs-lookup"><span data-stu-id="cf429-159">Authentication</span></span>
1. <span data-ttu-id="cf429-160">Relace</span><span class="sxs-lookup"><span data-stu-id="cf429-160">Session</span></span>
1. <span data-ttu-id="cf429-161">MVC</span><span class="sxs-lookup"><span data-stu-id="cf429-161">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

<span data-ttu-id="cf429-162">V předchozím příkladu kódu, každá metoda rozšíření middlewaru je vystaven na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="cf429-162">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="cf429-163"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první komponenta middlewaru přidané do kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-163"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="cf429-164">Proto Middleware obslužné rutiny výjimek zachytává všechny výjimky, ke kterým dochází v pozdější volání.</span><span class="sxs-lookup"><span data-stu-id="cf429-164">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="cf429-165">Statický soubor middlewaru je volána v rané fázi kanálu tak, aby mohl zpracovávat požadavky a zkrácenou bez nutnosti kontaktovat zbývající součásti.</span><span class="sxs-lookup"><span data-stu-id="cf429-165">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="cf429-166">Poskytuje Middleware statické soubory **žádné** kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="cf429-166">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="cf429-167">Všechny soubory obsluhuje, včetně těch *wwwroot*, jsou veřejně dostupné.</span><span class="sxs-lookup"><span data-stu-id="cf429-167">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="cf429-168">Přístup k zabezpečení statické soubory, naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="cf429-168">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cf429-169">Pokud žádost není zpracovávaných middlewarem statický soubor, je předána Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="cf429-169">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="cf429-170">Ověřování není zkrácenou neověřené žádosti.</span><span class="sxs-lookup"><span data-stu-id="cf429-170">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="cf429-171">I když ověřovací Middleware ověřuje požadavky, autorizace (a odmítnutí) nastane pouze po MVC vybere konkrétní stránky Razor nebo MVC kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="cf429-171">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cf429-172">Pokud požadavek není zpracováván Middleware statické soubory, je předána Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="cf429-172">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="cf429-173">Identita není zkrácenou neověřené žádosti.</span><span class="sxs-lookup"><span data-stu-id="cf429-173">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="cf429-174">I když žádosti o ověření Identity autorizace (a odmítnutí) nastane pouze po MVC vybere konkrétní kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="cf429-174">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="cf429-175">Následující příklad ukazuje middleware pořadí, ve kterém jsou požadavky pro statické soubory zpracovávány middlewarem statický soubor před Middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="cf429-175">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="cf429-176">Statické soubory nejsou komprimovaný pomocí tohoto pořadí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cf429-176">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="cf429-177">Odpovědi MVC <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze komprimovat.</span><span class="sxs-lookup"><span data-stu-id="cf429-177">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="cf429-178">Použití, spuštění a mapy</span><span class="sxs-lookup"><span data-stu-id="cf429-178">Use, Run, and Map</span></span>

<span data-ttu-id="cf429-179">Konfigurace kanálu HTTP pomocí `Use`, `Run`, a `Map`.</span><span class="sxs-lookup"><span data-stu-id="cf429-179">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="cf429-180">`Use` Metoda můžete zkrácenou kanálu (tj. Pokud nebude volat `next` delegáta požadavku).</span><span class="sxs-lookup"><span data-stu-id="cf429-180">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="cf429-181">`Run` je konvence, a některé middlewarových komponent může vystavit `Run[Middleware]` metody, které běží na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-181">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="cf429-182"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> rozšíření jsou použity jako konvence pro větvení kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-182"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="cf429-183">`Map*` větve kanál požadavku na základě shody zadanou cestu požadavku.</span><span class="sxs-lookup"><span data-stu-id="cf429-183">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="cf429-184">Pokud cesta požadavku začíná dané cesty, větve je proveden.</span><span class="sxs-lookup"><span data-stu-id="cf429-184">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="cf429-185">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="cf429-185">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="cf429-186">Požadavek</span><span class="sxs-lookup"><span data-stu-id="cf429-186">Request</span></span>             | <span data-ttu-id="cf429-187">Odpověď</span><span class="sxs-lookup"><span data-stu-id="cf429-187">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="cf429-188">1234</span><span class="sxs-lookup"><span data-stu-id="cf429-188">localhost:1234</span></span>      | <span data-ttu-id="cf429-189">Dobrý den ze bez mapování delegáta.</span><span class="sxs-lookup"><span data-stu-id="cf429-189">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="cf429-190">1234 / map1</span><span class="sxs-lookup"><span data-stu-id="cf429-190">localhost:1234/map1</span></span> | <span data-ttu-id="cf429-191">Mapování Test 1</span><span class="sxs-lookup"><span data-stu-id="cf429-191">Map Test 1</span></span>                   |
| <span data-ttu-id="cf429-192">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="cf429-192">localhost:1234/map2</span></span> | <span data-ttu-id="cf429-193">Mapování testů 2</span><span class="sxs-lookup"><span data-stu-id="cf429-193">Map Test 2</span></span>                   |
| <span data-ttu-id="cf429-194">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="cf429-194">localhost:1234/map3</span></span> | <span data-ttu-id="cf429-195">Dobrý den ze bez mapování delegáta.</span><span class="sxs-lookup"><span data-stu-id="cf429-195">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="cf429-196">Když `Map` se používá, segmenty cesty odpovídající jsou odebrány z `HttpRequest.Path` a připojený k `HttpRequest.PathBase` pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="cf429-196">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="cf429-197">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) větví kanál požadavku na základě výsledku daného predikátu.</span><span class="sxs-lookup"><span data-stu-id="cf429-197">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="cf429-198">Žádné predikátu typu `Func<HttpContext, bool>` je možné mapovat požadavky na novou větev kanálu.</span><span class="sxs-lookup"><span data-stu-id="cf429-198">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="cf429-199">V následujícím příkladu, predikát slouží k detekci přítomnosti proměnnou s řetězcem dotazu `branch`:</span><span class="sxs-lookup"><span data-stu-id="cf429-199">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="cf429-200">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="cf429-200">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="cf429-201">Požadavek</span><span class="sxs-lookup"><span data-stu-id="cf429-201">Request</span></span>                       | <span data-ttu-id="cf429-202">Odpověď</span><span class="sxs-lookup"><span data-stu-id="cf429-202">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="cf429-203">1234</span><span class="sxs-lookup"><span data-stu-id="cf429-203">localhost:1234</span></span>                | <span data-ttu-id="cf429-204">Dobrý den ze bez mapování delegáta.</span><span class="sxs-lookup"><span data-stu-id="cf429-204">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="cf429-205">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="cf429-205">localhost:1234/?branch=master</span></span> | <span data-ttu-id="cf429-206">Větev se použije hlavní =</span><span class="sxs-lookup"><span data-stu-id="cf429-206">Branch used = master</span></span>         |

<span data-ttu-id="cf429-207">`Map` podporuje vnořené, například:</span><span class="sxs-lookup"><span data-stu-id="cf429-207">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

<span data-ttu-id="cf429-208">`Map` Můžete také porovnat více segmentů najednou:</span><span class="sxs-lookup"><span data-stu-id="cf429-208">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="cf429-209">Integrované middlewaru</span><span class="sxs-lookup"><span data-stu-id="cf429-209">Built-in middleware</span></span>

<span data-ttu-id="cf429-210">ASP.NET Core se dodává s následujícími součástmi middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cf429-210">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="cf429-211">*Pořadí* sloupec obsahuje poznámky k umístění middleware v kanálu požadavku a za jakých podmínek middleware může ukončit žádosti a zabránit dalším middlewarem zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="cf429-211">The *Order* column provides notes on the middleware's placement in the request pipeline and under what conditions the middleware may terminate the request and prevent other middleware from processing a request.</span></span>

| <span data-ttu-id="cf429-212">Middleware</span><span class="sxs-lookup"><span data-stu-id="cf429-212">Middleware</span></span> | <span data-ttu-id="cf429-213">Popis</span><span class="sxs-lookup"><span data-stu-id="cf429-213">Description</span></span> | <span data-ttu-id="cf429-214">Pořadí</span><span class="sxs-lookup"><span data-stu-id="cf429-214">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="cf429-215">Ověřování</span><span class="sxs-lookup"><span data-stu-id="cf429-215">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="cf429-216">Poskytuje podporu ověřování.</span><span class="sxs-lookup"><span data-stu-id="cf429-216">Provides authentication support.</span></span> | <span data-ttu-id="cf429-217">Před `HttpContext.User` je potřeba.</span><span class="sxs-lookup"><span data-stu-id="cf429-217">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="cf429-218">Terminál pro zpětná volání OAuth.</span><span class="sxs-lookup"><span data-stu-id="cf429-218">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="cf429-219">Zásady souborů cookie</span><span class="sxs-lookup"><span data-stu-id="cf429-219">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="cf429-220">Sleduje svolení od uživatelů pro ukládání osobních údajů a vynucuje minimální standardy pro soubor cookie pole, jako například `secure` a `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="cf429-220">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="cf429-221">Před middlewaru, který vydá soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="cf429-221">Before middleware that issues cookies.</span></span> <span data-ttu-id="cf429-222">Příklady: Ověřování, relace, MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="cf429-222">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="cf429-223">CORS</span><span class="sxs-lookup"><span data-stu-id="cf429-223">CORS</span></span>](xref:security/cors) | <span data-ttu-id="cf429-224">Konfiguruje sdílení prostředků různého původu.</span><span class="sxs-lookup"><span data-stu-id="cf429-224">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="cf429-225">Před provedením komponent, které používají CORS.</span><span class="sxs-lookup"><span data-stu-id="cf429-225">Before components that use CORS.</span></span> |
| [<span data-ttu-id="cf429-226">Diagnostika</span><span class="sxs-lookup"><span data-stu-id="cf429-226">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="cf429-227">Konfiguruje se Diagnostika.</span><span class="sxs-lookup"><span data-stu-id="cf429-227">Configures diagnostics.</span></span> | <span data-ttu-id="cf429-228">Před provedením komponent, které generují chyby.</span><span class="sxs-lookup"><span data-stu-id="cf429-228">Before components that generate errors.</span></span> |
| [<span data-ttu-id="cf429-229">Přesměrovaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="cf429-229">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="cf429-230">Předává záhlaví směrovány přes proxy server na aktuální žádost.</span><span class="sxs-lookup"><span data-stu-id="cf429-230">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="cf429-231">Před provedením komponent, které využívají aktualizovanými poli.</span><span class="sxs-lookup"><span data-stu-id="cf429-231">Before components that consume the updated fields.</span></span> <span data-ttu-id="cf429-232">Příklady: schéma, hostitele, IP adresu klienta, metoda.</span><span class="sxs-lookup"><span data-stu-id="cf429-232">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="cf429-233">Přepsání metody HTTP</span><span class="sxs-lookup"><span data-stu-id="cf429-233">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="cf429-234">Umožňuje příchozí požadavek POST k přepsání metody.</span><span class="sxs-lookup"><span data-stu-id="cf429-234">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="cf429-235">Před provedením komponent, které využívají aktualizované metody.</span><span class="sxs-lookup"><span data-stu-id="cf429-235">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="cf429-236">Přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="cf429-236">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="cf429-237">Přesměrujte všechny požadavky HTTP na HTTPS (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="cf429-237">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="cf429-238">Před provedením komponent, které využívají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="cf429-238">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="cf429-239">Zabezpečení striktní přenosu HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="cf429-239">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="cf429-240">Zabezpečení vylepšení middleware, který přidá hlavičku odpovědi speciální (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="cf429-240">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="cf429-241">Předtím, než se posílají žádosti a po součásti, které mění žádosti.</span><span class="sxs-lookup"><span data-stu-id="cf429-241">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="cf429-242">Příklady: Předané záhlaví, přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="cf429-242">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="cf429-243">MVC</span><span class="sxs-lookup"><span data-stu-id="cf429-243">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="cf429-244">Zpracuje žádosti s MVC/Razor Pages (ASP.NET Core 2.0 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="cf429-244">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="cf429-245">Terminál, pokud požadavek odpovídá trase.</span><span class="sxs-lookup"><span data-stu-id="cf429-245">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="cf429-246">OWIN</span><span class="sxs-lookup"><span data-stu-id="cf429-246">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="cf429-247">Interoperabilita s aplikací OWIN, servery a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cf429-247">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="cf429-248">Terminál, pokud middlewaru OWIN, který plně zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="cf429-248">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="cf429-249">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="cf429-249">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="cf429-250">Poskytuje podporu pro ukládání do mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="cf429-250">Provides support for caching responses.</span></span> | <span data-ttu-id="cf429-251">Před provedením komponent, které vyžadují ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="cf429-251">Before components that require caching.</span></span> |
| [<span data-ttu-id="cf429-252">Kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="cf429-252">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="cf429-253">Poskytuje podporu pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="cf429-253">Provides support for compressing responses.</span></span> | <span data-ttu-id="cf429-254">Před provedením komponent, které vyžadují komprese.</span><span class="sxs-lookup"><span data-stu-id="cf429-254">Before components that require compression.</span></span> |
| [<span data-ttu-id="cf429-255">Žádost o lokalizaci</span><span class="sxs-lookup"><span data-stu-id="cf429-255">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="cf429-256">Poskytuje podporu lokalizace.</span><span class="sxs-lookup"><span data-stu-id="cf429-256">Provides localization support.</span></span> | <span data-ttu-id="cf429-257">Před provedením komponent citlivé lokalizace.</span><span class="sxs-lookup"><span data-stu-id="cf429-257">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="cf429-258">Směrování</span><span class="sxs-lookup"><span data-stu-id="cf429-258">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="cf429-259">Definuje a omezuje žádosti trasy.</span><span class="sxs-lookup"><span data-stu-id="cf429-259">Defines and constrains request routes.</span></span> | <span data-ttu-id="cf429-260">Terminál odpovídající trasy.</span><span class="sxs-lookup"><span data-stu-id="cf429-260">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="cf429-261">Relace</span><span class="sxs-lookup"><span data-stu-id="cf429-261">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="cf429-262">Poskytuje podporu pro správu uživatelských relací.</span><span class="sxs-lookup"><span data-stu-id="cf429-262">Provides support for managing user sessions.</span></span> | <span data-ttu-id="cf429-263">Před provedením komponent, které vyžadují relace.</span><span class="sxs-lookup"><span data-stu-id="cf429-263">Before components that require Session.</span></span> |
| [<span data-ttu-id="cf429-264">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="cf429-264">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="cf429-265">Poskytuje podporu pro poskytování statických souborů a procházení adresářů.</span><span class="sxs-lookup"><span data-stu-id="cf429-265">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="cf429-266">Terminál, pokud požadavek odpovídá souboru.</span><span class="sxs-lookup"><span data-stu-id="cf429-266">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="cf429-267">Přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="cf429-267">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="cf429-268">Poskytuje podporu pro přepis adres URL a přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="cf429-268">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="cf429-269">Před provedením komponent, které využívají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="cf429-269">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="cf429-270">Webové sokety</span><span class="sxs-lookup"><span data-stu-id="cf429-270">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="cf429-271">Povolí protokol Websocket.</span><span class="sxs-lookup"><span data-stu-id="cf429-271">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="cf429-272">Před provedením komponent, které jsou nutné, aby přijímal požadavky protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="cf429-272">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="write-middleware"></a><span data-ttu-id="cf429-273">Zápis middlewaru</span><span class="sxs-lookup"><span data-stu-id="cf429-273">Write middleware</span></span>

<span data-ttu-id="cf429-274">Middleware je obecně zapouzdřené v třídě a vystavený s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="cf429-274">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="cf429-275">Vezměte v úvahu následující middlewaru, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="cf429-275">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="cf429-276">Předchozí kód ukázkové slouží k předvedení vytváření komponenta middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cf429-276">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="cf429-277">ASP.NET Core lokalizace integrovanou podporu najdete na webu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="cf429-277">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="cf429-278">Middleware můžete otestovat tím, že předáte v jazykové verzi, například `http://localhost:7997/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="cf429-278">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="cf429-279">Následující kód přesune delegáta middleware pro třídu:</span><span class="sxs-lookup"><span data-stu-id="cf429-279">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cf429-280">Middleware `Task` název metody musí být `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="cf429-280">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="cf429-281">V technologii ASP.NET Core 2.0 nebo novější, název může být buď `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="cf429-281">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

<span data-ttu-id="cf429-282">Poskytuje následující metody rozšíření middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="cf429-282">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="cf429-283">Následující kód volá middleware z `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="cf429-283">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="cf429-284">Postupujte podle middleware [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) zveřejněním závislých 've svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cf429-284">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="cf429-285">Middleware je vytvořen jednou za *dobu životnosti aplikace*.</span><span class="sxs-lookup"><span data-stu-id="cf429-285">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="cf429-286">Najdete v článku [závislosti na požadavku](#per-request-dependencies) části, pokud je potřeba sdílet s middlewaru v rámci žádost o služby.</span><span class="sxs-lookup"><span data-stu-id="cf429-286">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="cf429-287">Middlewarových komponent lze vyřešit jejich závislosti z [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cf429-287">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="cf429-288">[UseMiddleware&lt;T&gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) můžete také přijmout přímo další parametry.</span><span class="sxs-lookup"><span data-stu-id="cf429-288">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="cf429-289">Závislosti na základě žádosti</span><span class="sxs-lookup"><span data-stu-id="cf429-289">Per-request dependencies</span></span>

<span data-ttu-id="cf429-290">Protože middlewaru je vytvořen při spuštění aplikace, nikoli jednotlivých žádostí, *obor* služby životního cyklu používat middleware konstruktory nejsou sdíleny s jinými typy vložený závislostí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="cf429-290">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="cf429-291">Pokud musíte sdílet *obor* služby mezi middlewaru a ostatními typy, přidejte tyto služby `Invoke` podpis metody.</span><span class="sxs-lookup"><span data-stu-id="cf429-291">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="cf429-292">`Invoke` Metoda může přijímat další parametry, které se vyplní podle DI:</span><span class="sxs-lookup"><span data-stu-id="cf429-292">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="cf429-293">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cf429-293">Additional resources</span></span>

* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
