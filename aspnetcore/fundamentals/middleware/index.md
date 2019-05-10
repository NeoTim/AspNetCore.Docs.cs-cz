---
title: Middleware ASP.NET Core
author: rick-anderson
description: Další informace o ASP.NET Core middleware a kanál žádosti.
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: bac121441d6856ca79affe1a3130e5cbc76debd9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901239"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="fe72a-103">Middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe72a-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="fe72a-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fe72a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fe72a-105">Middleware je software, který je sestaven do kanálu služby aplikace pro zpracování požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="fe72a-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="fe72a-106">Jednotlivé komponenty:</span><span class="sxs-lookup"><span data-stu-id="fe72a-106">Each component:</span></span>

* <span data-ttu-id="fe72a-107">Zvolí, zda se má předat požadavky na další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="fe72a-108">Můžete provádět práci před a po další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="fe72a-109">Delegáti požadavku se používají k vytvoření kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="fe72a-110">Delegáti žádost o zpracování konkrétního požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe72a-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="fe72a-111">Požádat o Delegáti jsou nakonfigurováni pomocí <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="fe72a-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="fe72a-112">Delegát jednotlivých požadavků může být zadaný řádek jako anonymní metody (označované jako middlewaru vložených), nebo může být definován ve třídě opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="fe72a-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="fe72a-113">Tyto opakovaně použitelné třídy a v řádku anonymní metody jsou *middleware*, označované také jako *middlewarových komponent*.</span><span class="sxs-lookup"><span data-stu-id="fe72a-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="fe72a-114">Jednotlivé komponenty middleware v kanálu požadavku zodpovídá za vyvolání další komponenta v kanálu nebo zkrácenou kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="fe72a-115">Při zkratům middleware, je volána *terminálu middleware* protože zabraňuje další middleware v zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="fe72a-116"><xref:migration/http-modules> Vysvětluje rozdíl mezi požadavek kanály v ASP.NET Core a ASP.NET 4.x a poskytuje další ukázky middlewaru.</span><span class="sxs-lookup"><span data-stu-id="fe72a-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="fe72a-117">Vytvoření kanálu middlewaru s IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="fe72a-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="fe72a-118">Kanál žádosti ASP.NET Core se skládá z posloupnost požadavek delegáty, volá se jedna po druhé.</span><span class="sxs-lookup"><span data-stu-id="fe72a-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="fe72a-119">Následující diagram ukazuje koncept.</span><span class="sxs-lookup"><span data-stu-id="fe72a-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="fe72a-120">Vlákno provádění postupuje černé šipky.</span><span class="sxs-lookup"><span data-stu-id="fe72a-120">The thread of execution follows the black arrows.</span></span>

![Vzor zpracování požadavku zobrazující žádosti přicházející, zpracování až tři middlewares a odpovědi opuštění aplikace.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="fe72a-124">Všem delegátům můžete provádět operace, před a po dalším delegáta.</span><span class="sxs-lookup"><span data-stu-id="fe72a-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="fe72a-125">Zpracování výjimek delegáty by měla být volána již v rané fázi v kanálu, takže se můžete zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="fe72a-126">Nejjednodušší možný aplikace ASP.NET Core nastaví delegáta jedné žádosti, která zpracovává všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="fe72a-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="fe72a-127">Tento případ neobsahuje kanál aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="fe72a-128">Místo toho jednoho anonymní funkce je volána v reakci na každý požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe72a-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="fe72a-129">První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegáta ukončí kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="fe72a-130">Zřetězit více požadavek delegátů spolu s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="fe72a-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="fe72a-131">`next` Parametr představuje další delegáta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="fe72a-132">Můžete zkrácenou kanál pomocí *není* volání *Další* parametru.</span><span class="sxs-lookup"><span data-stu-id="fe72a-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="fe72a-133">Můžete obvykle provádět akce před i po další delegáta, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="fe72a-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

<span data-ttu-id="fe72a-134">Je-li delegáta není úspěšný žádost o další delegátu, nazývá *zkrácenou kanál žádosti*.</span><span class="sxs-lookup"><span data-stu-id="fe72a-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="fe72a-135">Krátký cyklus je často žádoucí, protože tím eliminujete zbytečné práce.</span><span class="sxs-lookup"><span data-stu-id="fe72a-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="fe72a-136">Například [Middleware statické soubory](xref:fundamentals/static-files) může fungovat jako *terminálu middleware* tak, že zpracování žádosti o statický soubor a krátký cyklus rest z kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="fe72a-137">Middleware přidaný do kanálu middleware, který ukončí dalšímu zpracování stále zpracovává kódu po jejich `next.Invoke` příkazy.</span><span class="sxs-lookup"><span data-stu-id="fe72a-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="fe72a-138">Nicméně zobrazí následující upozornění o pokusu o zápis, která již byla odeslána odpověď.</span><span class="sxs-lookup"><span data-stu-id="fe72a-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="fe72a-139">Nevolejte `next.Invoke` po odeslání odpovědi klientovi.</span><span class="sxs-lookup"><span data-stu-id="fe72a-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="fe72a-140">Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po spuštění odpovědi vrátit výjimku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="fe72a-141">Například změny, jako je nastavení hlaviček a stavovým kódem vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="fe72a-142">Zápis do datové části odpovědi po volání `next`:</span><span class="sxs-lookup"><span data-stu-id="fe72a-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="fe72a-143">Může způsobit narušení protokolu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-143">May cause a protocol violation.</span></span> <span data-ttu-id="fe72a-144">Například zápis informace než uvedené `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="fe72a-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="fe72a-145">Může dojít k poškození formát těla zprávy.</span><span class="sxs-lookup"><span data-stu-id="fe72a-145">May corrupt the body format.</span></span> <span data-ttu-id="fe72a-146">HTML zápatí například zápis do souboru šablony stylů CSS.</span><span class="sxs-lookup"><span data-stu-id="fe72a-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="fe72a-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečné nápovědu k označení, pokud byly odeslány hlavičky nebo text byl zapsán do.</span><span class="sxs-lookup"><span data-stu-id="fe72a-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="fe72a-148">Objednání</span><span class="sxs-lookup"><span data-stu-id="fe72a-148">Order</span></span>

<span data-ttu-id="fe72a-149">Pořadí, ve kterém middlewarových komponent přidány `Startup.Configure` metoda definuje pořadí, ve kterém jsou vyvolány middlewarových komponent na požadavky a obrácenému pořadí pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="fe72a-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="fe72a-150">Pořadí je důležité pro zabezpečení, výkon a funkčnost.</span><span class="sxs-lookup"><span data-stu-id="fe72a-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="fe72a-151">Následující `Startup.Configure` metoda přidá middlewarových komponent pro běžné scénáře aplikací:</span><span class="sxs-lookup"><span data-stu-id="fe72a-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="fe72a-152">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="fe72a-152">Exception/error handling</span></span>
1. <span data-ttu-id="fe72a-153">Protokol zabezpečení striktní přenosu HTTP</span><span class="sxs-lookup"><span data-stu-id="fe72a-153">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="fe72a-154">Přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="fe72a-154">HTTPS redirection</span></span>
1. <span data-ttu-id="fe72a-155">Statický souborový server</span><span class="sxs-lookup"><span data-stu-id="fe72a-155">Static file server</span></span>
1. <span data-ttu-id="fe72a-156">Vynucení zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="fe72a-156">Cookie policy enforcement</span></span>
1. <span data-ttu-id="fe72a-157">Ověřování</span><span class="sxs-lookup"><span data-stu-id="fe72a-157">Authentication</span></span>
1. <span data-ttu-id="fe72a-158">Relace</span><span class="sxs-lookup"><span data-stu-id="fe72a-158">Session</span></span>
1. <span data-ttu-id="fe72a-159">MVC</span><span class="sxs-lookup"><span data-stu-id="fe72a-159">MVC</span></span>

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

1. <span data-ttu-id="fe72a-160">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="fe72a-160">Exception/error handling</span></span>
1. <span data-ttu-id="fe72a-161">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="fe72a-161">Static files</span></span>
1. <span data-ttu-id="fe72a-162">Ověřování</span><span class="sxs-lookup"><span data-stu-id="fe72a-162">Authentication</span></span>
1. <span data-ttu-id="fe72a-163">Relace</span><span class="sxs-lookup"><span data-stu-id="fe72a-163">Session</span></span>
1. <span data-ttu-id="fe72a-164">MVC</span><span class="sxs-lookup"><span data-stu-id="fe72a-164">MVC</span></span>

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

<span data-ttu-id="fe72a-165">V předchozím příkladu kódu, každá metoda rozšíření middlewaru je vystaven na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="fe72a-165">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="fe72a-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první komponenta middlewaru přidané do kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="fe72a-167">Proto Middleware obslužné rutiny výjimek zachytává všechny výjimky, ke kterým dochází v pozdější volání.</span><span class="sxs-lookup"><span data-stu-id="fe72a-167">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="fe72a-168">Statický soubor middlewaru je volána v rané fázi kanálu tak, aby mohl zpracovávat požadavky a zkrácenou bez nutnosti kontaktovat zbývající součásti.</span><span class="sxs-lookup"><span data-stu-id="fe72a-168">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="fe72a-169">Poskytuje Middleware statické soubory **žádné** kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="fe72a-169">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="fe72a-170">Všechny soubory obsluhuje, včetně těch *wwwroot*, jsou veřejně dostupné.</span><span class="sxs-lookup"><span data-stu-id="fe72a-170">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="fe72a-171">Přístup k zabezpečení statické soubory, naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="fe72a-171">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fe72a-172">Pokud žádost není zpracovávaných middlewarem statický soubor, je předána Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe72a-172">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="fe72a-173">Ověřování není zkrácenou neověřené žádosti.</span><span class="sxs-lookup"><span data-stu-id="fe72a-173">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="fe72a-174">I když ověřovací Middleware ověřuje požadavky, autorizace (a odmítnutí) nastane pouze po MVC vybere konkrétní stránky Razor nebo MVC kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="fe72a-174">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fe72a-175">Pokud požadavek není zpracováván Middleware statické soubory, je předána Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe72a-175">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="fe72a-176">Identita není zkrácenou neověřené žádosti.</span><span class="sxs-lookup"><span data-stu-id="fe72a-176">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="fe72a-177">I když žádosti o ověření Identity autorizace (a odmítnutí) nastane pouze po MVC vybere konkrétní kontroleru a akce.</span><span class="sxs-lookup"><span data-stu-id="fe72a-177">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="fe72a-178">Následující příklad ukazuje middleware pořadí, ve kterém jsou požadavky pro statické soubory zpracovávány middlewarem statický soubor před Middleware pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="fe72a-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="fe72a-179">Statické soubory nejsou komprimovaný pomocí tohoto pořadí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="fe72a-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="fe72a-180">Odpovědi MVC <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze komprimovat.</span><span class="sxs-lookup"><span data-stu-id="fe72a-180">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="fe72a-181">Použití, spuštění a mapy</span><span class="sxs-lookup"><span data-stu-id="fe72a-181">Use, Run, and Map</span></span>

<span data-ttu-id="fe72a-182">Konfigurace kanálu HTTP pomocí `Use`, `Run`, a `Map`.</span><span class="sxs-lookup"><span data-stu-id="fe72a-182">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="fe72a-183">`Use` Metoda můžete zkrácenou kanálu (tj. Pokud nebude volat `next` delegáta požadavku).</span><span class="sxs-lookup"><span data-stu-id="fe72a-183">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="fe72a-184">`Run` je konvence, a některé middlewarových komponent může vystavit `Run[Middleware]` metody, které běží na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-184">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="fe72a-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> rozšíření jsou použity jako konvence pro větvení kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="fe72a-186">`Map*` větve kanál požadavku na základě shody zadanou cestu požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-186">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="fe72a-187">Pokud cesta požadavku začíná dané cesty, větve je proveden.</span><span class="sxs-lookup"><span data-stu-id="fe72a-187">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="fe72a-188">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-188">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="fe72a-189">Request</span><span class="sxs-lookup"><span data-stu-id="fe72a-189">Request</span></span>             | <span data-ttu-id="fe72a-190">Odpověď</span><span class="sxs-lookup"><span data-stu-id="fe72a-190">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="fe72a-191">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="fe72a-191">localhost:1234</span></span>      | <span data-ttu-id="fe72a-192">Dobrý den ze bez mapování delegáta.</span><span class="sxs-lookup"><span data-stu-id="fe72a-192">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="fe72a-193">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="fe72a-193">localhost:1234/map1</span></span> | <span data-ttu-id="fe72a-194">Mapování Test 1</span><span class="sxs-lookup"><span data-stu-id="fe72a-194">Map Test 1</span></span>                   |
| <span data-ttu-id="fe72a-195">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="fe72a-195">localhost:1234/map2</span></span> | <span data-ttu-id="fe72a-196">Mapování testů 2</span><span class="sxs-lookup"><span data-stu-id="fe72a-196">Map Test 2</span></span>                   |
| <span data-ttu-id="fe72a-197">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="fe72a-197">localhost:1234/map3</span></span> | <span data-ttu-id="fe72a-198">Dobrý den ze bez mapování delegáta.</span><span class="sxs-lookup"><span data-stu-id="fe72a-198">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="fe72a-199">Když `Map` se používá, segmenty cesty odpovídající jsou odebrány z `HttpRequest.Path` a připojený k `HttpRequest.PathBase` pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="fe72a-199">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="fe72a-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) větví kanál požadavku na základě výsledku daného predikátu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="fe72a-201">Žádné predikátu typu `Func<HttpContext, bool>` je možné mapovat požadavky na novou větev kanálu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-201">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="fe72a-202">V následujícím příkladu, predikát slouží k detekci přítomnosti proměnnou s řetězcem dotazu `branch`:</span><span class="sxs-lookup"><span data-stu-id="fe72a-202">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="fe72a-203">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-203">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="fe72a-204">Request</span><span class="sxs-lookup"><span data-stu-id="fe72a-204">Request</span></span>                       | <span data-ttu-id="fe72a-205">Odpověď</span><span class="sxs-lookup"><span data-stu-id="fe72a-205">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="fe72a-206">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="fe72a-206">localhost:1234</span></span>                | <span data-ttu-id="fe72a-207">Dobrý den ze bez mapování delegáta.</span><span class="sxs-lookup"><span data-stu-id="fe72a-207">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="fe72a-208">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="fe72a-208">localhost:1234/?branch=master</span></span> | <span data-ttu-id="fe72a-209">Větev se použije hlavní =</span><span class="sxs-lookup"><span data-stu-id="fe72a-209">Branch used = master</span></span>         |

<span data-ttu-id="fe72a-210">`Map` podporuje vnořené, například:</span><span class="sxs-lookup"><span data-stu-id="fe72a-210">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="fe72a-211">`Map` Můžete také porovnat více segmentů najednou:</span><span class="sxs-lookup"><span data-stu-id="fe72a-211">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="fe72a-212">Integrované middlewaru</span><span class="sxs-lookup"><span data-stu-id="fe72a-212">Built-in middleware</span></span>

<span data-ttu-id="fe72a-213">ASP.NET Core se dodává s následujícími součástmi middlewaru.</span><span class="sxs-lookup"><span data-stu-id="fe72a-213">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="fe72a-214">*Pořadí* sloupec obsahuje poznámky k umístění middleware v kanálu zpracování žádostí a za jakých podmínek middleware může ukončit zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe72a-214">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="fe72a-215">Je-li middleware zkratům kanál pro zpracování požadavku a zabrání další podřízené middleware zpracování požadavku, nazývá *terminálu middleware*.</span><span class="sxs-lookup"><span data-stu-id="fe72a-215">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="fe72a-216">Další informace o krátký cyklus, najdete v článku [vytvoření kanálu middlewaru s IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) oddílu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-216">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="fe72a-217">Middleware</span><span class="sxs-lookup"><span data-stu-id="fe72a-217">Middleware</span></span> | <span data-ttu-id="fe72a-218">Popis</span><span class="sxs-lookup"><span data-stu-id="fe72a-218">Description</span></span> | <span data-ttu-id="fe72a-219">Objednání</span><span class="sxs-lookup"><span data-stu-id="fe72a-219">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="fe72a-220">Ověřování</span><span class="sxs-lookup"><span data-stu-id="fe72a-220">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="fe72a-221">Poskytuje podporu ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe72a-221">Provides authentication support.</span></span> | <span data-ttu-id="fe72a-222">Před `HttpContext.User` je potřeba.</span><span class="sxs-lookup"><span data-stu-id="fe72a-222">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="fe72a-223">Terminál pro zpětná volání OAuth.</span><span class="sxs-lookup"><span data-stu-id="fe72a-223">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="fe72a-224">Zásady souborů cookie</span><span class="sxs-lookup"><span data-stu-id="fe72a-224">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="fe72a-225">Sleduje svolení od uživatelů pro ukládání osobních údajů a vynucuje minimální standardy pro soubor cookie pole, jako například `secure` a `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="fe72a-225">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="fe72a-226">Před middlewaru, který vydá soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="fe72a-226">Before middleware that issues cookies.</span></span> <span data-ttu-id="fe72a-227">Příklady: Ověřování, relace, MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="fe72a-227">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="fe72a-228">CORS</span><span class="sxs-lookup"><span data-stu-id="fe72a-228">CORS</span></span>](xref:security/cors) | <span data-ttu-id="fe72a-229">Konfiguruje sdílení prostředků různého původu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-229">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="fe72a-230">Před provedením komponent, které používají CORS.</span><span class="sxs-lookup"><span data-stu-id="fe72a-230">Before components that use CORS.</span></span> |
| [<span data-ttu-id="fe72a-231">Zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="fe72a-231">Exception Handling</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="fe72a-232">Zpracovává výjimky.</span><span class="sxs-lookup"><span data-stu-id="fe72a-232">Handles exceptions.</span></span> | <span data-ttu-id="fe72a-233">Před provedením komponent, které generují chyby.</span><span class="sxs-lookup"><span data-stu-id="fe72a-233">Before components that generate errors.</span></span> |
| [<span data-ttu-id="fe72a-234">Přesměrovaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="fe72a-234">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="fe72a-235">Předává záhlaví směrovány přes proxy server na aktuální žádost.</span><span class="sxs-lookup"><span data-stu-id="fe72a-235">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="fe72a-236">Před provedením komponent, které využívají aktualizovanými poli.</span><span class="sxs-lookup"><span data-stu-id="fe72a-236">Before components that consume the updated fields.</span></span> <span data-ttu-id="fe72a-237">Příklady: schéma, hostitele, IP adresu klienta, metoda.</span><span class="sxs-lookup"><span data-stu-id="fe72a-237">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="fe72a-238">Kontrola stavu</span><span class="sxs-lookup"><span data-stu-id="fe72a-238">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="fe72a-239">Kontroluje stav aplikace ASP.NET Core a jeho závislosti, jako je například kontrola dostupnosti databáze.</span><span class="sxs-lookup"><span data-stu-id="fe72a-239">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="fe72a-240">Terminál, pokud požadavek odpovídá stav vrácení koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="fe72a-240">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="fe72a-241">Přepsání metody HTTP</span><span class="sxs-lookup"><span data-stu-id="fe72a-241">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="fe72a-242">Umožňuje příchozí požadavek POST k přepsání metody.</span><span class="sxs-lookup"><span data-stu-id="fe72a-242">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="fe72a-243">Před provedením komponent, které využívají aktualizované metody.</span><span class="sxs-lookup"><span data-stu-id="fe72a-243">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="fe72a-244">Přesměrování protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="fe72a-244">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="fe72a-245">Přesměrujte všechny požadavky HTTP na HTTPS (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="fe72a-245">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="fe72a-246">Před provedením komponent, které využívají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="fe72a-246">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="fe72a-247">Zabezpečení striktní přenosu HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="fe72a-247">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="fe72a-248">Zabezpečení vylepšení middleware, který přidá hlavičku odpovědi speciální (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="fe72a-248">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="fe72a-249">Předtím, než se posílají žádosti a po součásti, které mění žádosti.</span><span class="sxs-lookup"><span data-stu-id="fe72a-249">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="fe72a-250">Příklady: Předané záhlaví, přepisování adres URL.</span><span class="sxs-lookup"><span data-stu-id="fe72a-250">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="fe72a-251">MVC</span><span class="sxs-lookup"><span data-stu-id="fe72a-251">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="fe72a-252">Zpracuje žádosti s MVC/Razor Pages (ASP.NET Core 2.0 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="fe72a-252">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="fe72a-253">Terminál, pokud požadavek odpovídá trase.</span><span class="sxs-lookup"><span data-stu-id="fe72a-253">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="fe72a-254">OWIN</span><span class="sxs-lookup"><span data-stu-id="fe72a-254">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="fe72a-255">Interoperabilita s aplikací OWIN, servery a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="fe72a-255">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="fe72a-256">Terminál, pokud middlewaru OWIN, který plně zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="fe72a-256">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="fe72a-257">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="fe72a-257">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="fe72a-258">Poskytuje podporu pro ukládání do mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="fe72a-258">Provides support for caching responses.</span></span> | <span data-ttu-id="fe72a-259">Před provedením komponent, které vyžadují ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="fe72a-259">Before components that require caching.</span></span> |
| [<span data-ttu-id="fe72a-260">Kompresi odpovědí</span><span class="sxs-lookup"><span data-stu-id="fe72a-260">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="fe72a-261">Poskytuje podporu pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="fe72a-261">Provides support for compressing responses.</span></span> | <span data-ttu-id="fe72a-262">Před provedením komponent, které vyžadují komprese.</span><span class="sxs-lookup"><span data-stu-id="fe72a-262">Before components that require compression.</span></span> |
| [<span data-ttu-id="fe72a-263">Žádost o lokalizaci</span><span class="sxs-lookup"><span data-stu-id="fe72a-263">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="fe72a-264">Poskytuje podporu lokalizace.</span><span class="sxs-lookup"><span data-stu-id="fe72a-264">Provides localization support.</span></span> | <span data-ttu-id="fe72a-265">Před provedením komponent citlivé lokalizace.</span><span class="sxs-lookup"><span data-stu-id="fe72a-265">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="fe72a-266">Směrování</span><span class="sxs-lookup"><span data-stu-id="fe72a-266">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="fe72a-267">Definuje a omezuje žádosti trasy.</span><span class="sxs-lookup"><span data-stu-id="fe72a-267">Defines and constrains request routes.</span></span> | <span data-ttu-id="fe72a-268">Terminál odpovídající trasy.</span><span class="sxs-lookup"><span data-stu-id="fe72a-268">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="fe72a-269">Relace</span><span class="sxs-lookup"><span data-stu-id="fe72a-269">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="fe72a-270">Poskytuje podporu pro správu uživatelských relací.</span><span class="sxs-lookup"><span data-stu-id="fe72a-270">Provides support for managing user sessions.</span></span> | <span data-ttu-id="fe72a-271">Před provedením komponent, které vyžadují relace.</span><span class="sxs-lookup"><span data-stu-id="fe72a-271">Before components that require Session.</span></span> |
| [<span data-ttu-id="fe72a-272">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="fe72a-272">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="fe72a-273">Poskytuje podporu pro poskytování statických souborů a procházení adresářů.</span><span class="sxs-lookup"><span data-stu-id="fe72a-273">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="fe72a-274">Terminál, pokud požadavek odpovídá souboru.</span><span class="sxs-lookup"><span data-stu-id="fe72a-274">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="fe72a-275">Přepisování adres URL</span><span class="sxs-lookup"><span data-stu-id="fe72a-275">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="fe72a-276">Poskytuje podporu pro přepis adres URL a přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="fe72a-276">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="fe72a-277">Před provedením komponent, které využívají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="fe72a-277">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="fe72a-278">Webové sokety</span><span class="sxs-lookup"><span data-stu-id="fe72a-278">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="fe72a-279">Povolí protokol Websocket.</span><span class="sxs-lookup"><span data-stu-id="fe72a-279">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="fe72a-280">Před provedením komponent, které jsou nutné, aby přijímal požadavky protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fe72a-280">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="fe72a-281">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fe72a-281">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
