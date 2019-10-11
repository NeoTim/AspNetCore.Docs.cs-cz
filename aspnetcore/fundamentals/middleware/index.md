---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 8f5c3aabf17e78ae9675048602317c54f08e82a7
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259810"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="c9889-103">Middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9889-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="c9889-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c9889-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c9889-105">Middleware je software, který se sestaví do kanálu aplikace za účelem zpracování požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="c9889-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="c9889-106">Jednotlivé komponenty:</span><span class="sxs-lookup"><span data-stu-id="c9889-106">Each component:</span></span>

* <span data-ttu-id="c9889-107">Zvolí, jestli se má žádost předat další komponentě v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="c9889-108">Může provádět práci před a za další komponentou v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="c9889-109">Delegáti žádostí se používají k sestavení kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="c9889-110">Delegát žádosti zpracuje jednotlivé požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9889-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="c9889-111">Delegáti žádostí jsou nakonfigurováni pomocí metod rozšíření <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="c9889-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="c9889-112">Jednotlivé delegáty žádostí je možné zadat jako anonymní metodu (označovanou v rámci middlewarového middlewaru) nebo ji lze definovat v opakovaně použitelné třídě.</span><span class="sxs-lookup"><span data-stu-id="c9889-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="c9889-113">Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*.</span><span class="sxs-lookup"><span data-stu-id="c9889-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="c9889-114">Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="c9889-115">Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.</span><span class="sxs-lookup"><span data-stu-id="c9889-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="c9889-116"><xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c9889-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="c9889-117">Vytvoření kanálu middlewaru pomocí IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="c9889-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="c9889-118">Kanál žádostí o ASP.NET Core se skládá z posloupnosti delegátů požadavků, který se nazývá jedna po druhé.</span><span class="sxs-lookup"><span data-stu-id="c9889-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="c9889-119">Následující diagram znázorňuje koncept.</span><span class="sxs-lookup"><span data-stu-id="c9889-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="c9889-120">Vlákno provádění následuje za černými šipkami.</span><span class="sxs-lookup"><span data-stu-id="c9889-120">The thread of execution follows the black arrows.</span></span>

![Požadavek zpracování, který ukazuje, že se dorazí na požadavek, zpracovává se prostřednictvím tří middlewarů a odpověď opouští aplikaci](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="c9889-124">Každý delegát může provádět operace před a po dalším delegátu.</span><span class="sxs-lookup"><span data-stu-id="c9889-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="c9889-125">Delegáty zpracování výjimek by se měly volat v rámci kanálu na začátku, takže můžou zachytit výjimky, ke kterým dochází v pozdějších fázích kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="c9889-126">Nejjednodušší možná ASP.NET Core aplikace nastaví jediného delegáta žádosti, který zpracovává všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="c9889-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="c9889-127">Tento případ nezahrnuje skutečný kanál žádostí.</span><span class="sxs-lookup"><span data-stu-id="c9889-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="c9889-128">Místo toho je volána jedna anonymní funkce v reakci na každý požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9889-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="c9889-129">První delegát <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> ukončí kanál.</span><span class="sxs-lookup"><span data-stu-id="c9889-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="c9889-130">Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*></span><span class="sxs-lookup"><span data-stu-id="c9889-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="c9889-131">Parametr `next` představuje dalšího delegáta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="c9889-132">Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr.</span><span class="sxs-lookup"><span data-stu-id="c9889-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="c9889-133">Obvykle můžete provádět akce před i po dalším delegátu, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="c9889-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="c9889-134">Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*.</span><span class="sxs-lookup"><span data-stu-id="c9889-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="c9889-135">Krátkodobé okruhy jsou často žádoucí, protože brání zbytečné práci.</span><span class="sxs-lookup"><span data-stu-id="c9889-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="c9889-136">Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="c9889-137">Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po jejich příkazech `next.Invoke`.</span><span class="sxs-lookup"><span data-stu-id="c9889-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="c9889-138">Přečtěte si ale následující upozornění týkající se pokusu o zápis do odpovědi, která již byla odeslána.</span><span class="sxs-lookup"><span data-stu-id="c9889-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="c9889-139">Po odeslání odpovědi klientovi Nevolejte `next.Invoke`.</span><span class="sxs-lookup"><span data-stu-id="c9889-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="c9889-140">Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po zahájení odezvy vyvolávají výjimku.</span><span class="sxs-lookup"><span data-stu-id="c9889-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="c9889-141">Například změny jako hlavičky nastavení a stavový kód vyvolávají výjimku.</span><span class="sxs-lookup"><span data-stu-id="c9889-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="c9889-142">Zápis do těla odpovědi po volání `next`:</span><span class="sxs-lookup"><span data-stu-id="c9889-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="c9889-143">Může způsobit narušení protokolu.</span><span class="sxs-lookup"><span data-stu-id="c9889-143">May cause a protocol violation.</span></span> <span data-ttu-id="c9889-144">Například zápis více než uvedené `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="c9889-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="c9889-145">Může poškodit formát textu.</span><span class="sxs-lookup"><span data-stu-id="c9889-145">May corrupt the body format.</span></span> <span data-ttu-id="c9889-146">Například zápis zápatí HTML do souboru CSS.</span><span class="sxs-lookup"><span data-stu-id="c9889-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="c9889-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo zda byl text napsán do.</span><span class="sxs-lookup"><span data-stu-id="c9889-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="c9889-148">Pořadí</span><span class="sxs-lookup"><span data-stu-id="c9889-148">Order</span></span>

<span data-ttu-id="c9889-149">Pořadí, v jakém jsou komponenty middlewaru přidány v metodě `Startup.Configure`, definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="c9889-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="c9889-150">Pořadí je důležité pro zabezpečení, výkon a funkčnost.</span><span class="sxs-lookup"><span data-stu-id="c9889-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="c9889-151">Následující metoda `Startup.Configure` přidává komponenty middlewaru pro běžné scénáře aplikací:</span><span class="sxs-lookup"><span data-stu-id="c9889-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-3.0"

1. <span data-ttu-id="c9889-152">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="c9889-152">Exception/error handling</span></span>
   * <span data-ttu-id="c9889-153">Když aplikace běží ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="c9889-153">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="c9889-154">Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9889-154">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="c9889-155">Zpráva middlewaru chybové stránky databáze oznamuje chyby běhového běhu databáze.</span><span class="sxs-lookup"><span data-stu-id="c9889-155">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="c9889-156">Když aplikace běží v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="c9889-156">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="c9889-157">Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.</span><span class="sxs-lookup"><span data-stu-id="c9889-157">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="c9889-158">Middleware protokolu HTTP Strict Transport Security (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá hlavičku `Strict-Transport-Security`.</span><span class="sxs-lookup"><span data-stu-id="c9889-158">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="c9889-159">Middleware přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c9889-159">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="c9889-160">Middleware statických souborů (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.</span><span class="sxs-lookup"><span data-stu-id="c9889-160">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="c9889-161">Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) v aplikaci vyhovuje nařízením EU Obecné nařízení o ochraně osobních údajů (GDPR).</span><span class="sxs-lookup"><span data-stu-id="c9889-161">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="c9889-162">Směrování middlewaru (`UseRouting`) pro směrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-162">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="c9889-163">Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="c9889-163">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="c9889-164">Middleware autorizace (`UseAuthorization`) opravňuje uživatele k přístupu k zabezpečeným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="c9889-164">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="c9889-165">Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace.</span><span class="sxs-lookup"><span data-stu-id="c9889-165">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="c9889-166">Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.</span><span class="sxs-lookup"><span data-stu-id="c9889-166">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="c9889-167">Middleware směrování koncového bodu (`UseEndpoints` s `MapRazorPages`) pro přidání Razor Pages koncových bodů do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-167">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="c9889-168">V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím oboru názvů <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c9889-168">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="c9889-169"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-169"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="c9889-170">Proto middleware obslužné rutiny výjimek zachycuje všechny výjimky, ke kterým dojde v pozdějších voláních.</span><span class="sxs-lookup"><span data-stu-id="c9889-170">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="c9889-171">Middleware statických souborů se zavolá v průběhu kanálu, aby mohl zpracovávat požadavky a krátké okruhy bez toho, aby procházely zbývajícími součástmi.</span><span class="sxs-lookup"><span data-stu-id="c9889-171">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="c9889-172">Middleware statických souborů neposkytuje **žádné** autorizační kontroly.</span><span class="sxs-lookup"><span data-stu-id="c9889-172">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="c9889-173">Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné.</span><span class="sxs-lookup"><span data-stu-id="c9889-173">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="c9889-174">Přístup k zabezpečení statických souborů najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c9889-174">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="c9889-175">Pokud požadavek nezpracovává middleware pro statický soubor, je předán ověřovacímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="c9889-175">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="c9889-176">Ověřování neověřuje neověřené požadavky v krátkém okruhu.</span><span class="sxs-lookup"><span data-stu-id="c9889-176">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="c9889-177">I když middleware ověřování ověřuje požadavky, autorizaci (a odmítnutí) proběhne pouze poté, co MVC vybere konkrétní stránku Razor nebo kontroler MVC a akci.</span><span class="sxs-lookup"><span data-stu-id="c9889-177">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="c9889-178">Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy.</span><span class="sxs-lookup"><span data-stu-id="c9889-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="c9889-179">Statické soubory nejsou s tímto pořadím middlewaru komprimovány.</span><span class="sxs-lookup"><span data-stu-id="c9889-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="c9889-180">Odezvy Razor Pages lze komprimovat.</span><span class="sxs-lookup"><span data-stu-id="c9889-180">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. <span data-ttu-id="c9889-181">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="c9889-181">Exception/error handling</span></span>
   * <span data-ttu-id="c9889-182">Když aplikace běží ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="c9889-182">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="c9889-183">Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9889-183">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="c9889-184">Middleware chybových stránek databáze (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) oznamuje chyby běhového běhu databáze.</span><span class="sxs-lookup"><span data-stu-id="c9889-184">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="c9889-185">Když aplikace běží v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="c9889-185">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="c9889-186">Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.</span><span class="sxs-lookup"><span data-stu-id="c9889-186">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="c9889-187">Middleware protokolu HTTP Strict Transport Security (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidá hlavičku `Strict-Transport-Security`.</span><span class="sxs-lookup"><span data-stu-id="c9889-187">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="c9889-188">Middleware přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c9889-188">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="c9889-189">Middleware statických souborů (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.</span><span class="sxs-lookup"><span data-stu-id="c9889-189">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="c9889-190">Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) v aplikaci vyhovuje nařízením EU Obecné nařízení o ochraně osobních údajů (GDPR).</span><span class="sxs-lookup"><span data-stu-id="c9889-190">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="c9889-191">Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="c9889-191">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="c9889-192">Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace.</span><span class="sxs-lookup"><span data-stu-id="c9889-192">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="c9889-193">Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.</span><span class="sxs-lookup"><span data-stu-id="c9889-193">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="c9889-194">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) Chcete-li přidat MVC do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-194">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="c9889-195">V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena na <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím oboru názvů <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c9889-195">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="c9889-196"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-196"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="c9889-197">Proto middleware obslužné rutiny výjimek zachycuje všechny výjimky, ke kterým dojde v pozdějších voláních.</span><span class="sxs-lookup"><span data-stu-id="c9889-197">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="c9889-198">Middleware statických souborů se zavolá v průběhu kanálu, aby mohl zpracovávat požadavky a krátké okruhy bez toho, aby procházely zbývajícími součástmi.</span><span class="sxs-lookup"><span data-stu-id="c9889-198">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="c9889-199">Middleware statických souborů neposkytuje **žádné** autorizační kontroly.</span><span class="sxs-lookup"><span data-stu-id="c9889-199">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="c9889-200">Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné.</span><span class="sxs-lookup"><span data-stu-id="c9889-200">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="c9889-201">Přístup k zabezpečení statických souborů najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="c9889-201">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="c9889-202">Pokud požadavek nezpracovává middleware pro statický soubor, je předán ověřovacímu middlewaru (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="c9889-202">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="c9889-203">Ověřování neověřuje neověřené požadavky v krátkém okruhu.</span><span class="sxs-lookup"><span data-stu-id="c9889-203">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="c9889-204">I když middleware ověřování ověřuje požadavky, autorizaci (a odmítnutí) proběhne pouze poté, co MVC vybere konkrétní stránku Razor nebo kontroler MVC a akci.</span><span class="sxs-lookup"><span data-stu-id="c9889-204">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="c9889-205">Následující příklad ukazuje pořadí middlewaru, kde požadavky na statické soubory jsou zpracovávány pomocí middlewaru statického souboru před použitím middlewaru pro komprimaci odezvy.</span><span class="sxs-lookup"><span data-stu-id="c9889-205">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="c9889-206">Statické soubory nejsou s tímto pořadím middlewaru komprimovány.</span><span class="sxs-lookup"><span data-stu-id="c9889-206">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="c9889-207">Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze zkomprimovat.</span><span class="sxs-lookup"><span data-stu-id="c9889-207">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

## <a name="use-run-and-map"></a><span data-ttu-id="c9889-208">Použití, spuštění a mapování</span><span class="sxs-lookup"><span data-stu-id="c9889-208">Use, Run, and Map</span></span>

<span data-ttu-id="c9889-209">Nakonfigurujte kanál HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span><span class="sxs-lookup"><span data-stu-id="c9889-209">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="c9889-210">Metoda `Use` může vytvořit krátký okruh kanálu (tj. Pokud nevolá delegáta žádosti `next`).</span><span class="sxs-lookup"><span data-stu-id="c9889-210">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="c9889-211">`Run` je konvence a některé komponenty middlewaru můžou vystavovat metody `Run[Middleware]`, které se spouštějí na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-211">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="c9889-212">rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> se používají jako konvence pro větvení kanálu.</span><span class="sxs-lookup"><span data-stu-id="c9889-212"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="c9889-213">`Map` větví kanálu požadavků na základě shody dané cesty požadavku.</span><span class="sxs-lookup"><span data-stu-id="c9889-213">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="c9889-214">Pokud cesta k požadavku začíná danou cestou, je větev spuštěná.</span><span class="sxs-lookup"><span data-stu-id="c9889-214">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="c9889-215">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="c9889-215">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="c9889-216">Žádost</span><span class="sxs-lookup"><span data-stu-id="c9889-216">Request</span></span>             | <span data-ttu-id="c9889-217">Základě</span><span class="sxs-lookup"><span data-stu-id="c9889-217">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="c9889-218">localhost: 1234</span><span class="sxs-lookup"><span data-stu-id="c9889-218">localhost:1234</span></span>      | <span data-ttu-id="c9889-219">Hello z delegáta bez mapy.</span><span class="sxs-lookup"><span data-stu-id="c9889-219">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="c9889-220">localhost: 1234/Map1</span><span class="sxs-lookup"><span data-stu-id="c9889-220">localhost:1234/map1</span></span> | <span data-ttu-id="c9889-221">Mapování testu 1</span><span class="sxs-lookup"><span data-stu-id="c9889-221">Map Test 1</span></span>                   |
| <span data-ttu-id="c9889-222">localhost: 1234/MAP2 –</span><span class="sxs-lookup"><span data-stu-id="c9889-222">localhost:1234/map2</span></span> | <span data-ttu-id="c9889-223">Mapovat test 2</span><span class="sxs-lookup"><span data-stu-id="c9889-223">Map Test 2</span></span>                   |
| <span data-ttu-id="c9889-224">localhost: 1234/map3 –</span><span class="sxs-lookup"><span data-stu-id="c9889-224">localhost:1234/map3</span></span> | <span data-ttu-id="c9889-225">Hello z delegáta bez mapy.</span><span class="sxs-lookup"><span data-stu-id="c9889-225">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="c9889-226">Při použití `Map` se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="c9889-226">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="c9889-227"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větví kanálu požadavků na základě výsledku daného predikátu.</span><span class="sxs-lookup"><span data-stu-id="c9889-227"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="c9889-228">K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu `Func<HttpContext, bool>`.</span><span class="sxs-lookup"><span data-stu-id="c9889-228">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="c9889-229">V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch`:</span><span class="sxs-lookup"><span data-stu-id="c9889-229">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="c9889-230">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="c9889-230">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="c9889-231">Žádost</span><span class="sxs-lookup"><span data-stu-id="c9889-231">Request</span></span>                       | <span data-ttu-id="c9889-232">Základě</span><span class="sxs-lookup"><span data-stu-id="c9889-232">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="c9889-233">localhost: 1234</span><span class="sxs-lookup"><span data-stu-id="c9889-233">localhost:1234</span></span>                | <span data-ttu-id="c9889-234">Hello z delegáta bez mapy.</span><span class="sxs-lookup"><span data-stu-id="c9889-234">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="c9889-235">localhost: 1234/? větev = hlavní</span><span class="sxs-lookup"><span data-stu-id="c9889-235">localhost:1234/?branch=master</span></span> | <span data-ttu-id="c9889-236">Použitá větev = hlavní</span><span class="sxs-lookup"><span data-stu-id="c9889-236">Branch used = master</span></span>         |

<span data-ttu-id="c9889-237">`Map` podporuje vnořování, například:</span><span class="sxs-lookup"><span data-stu-id="c9889-237">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="c9889-238">`Map` může současně odpovídat více segmentům:</span><span class="sxs-lookup"><span data-stu-id="c9889-238">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="c9889-239">Vestavěný middleware</span><span class="sxs-lookup"><span data-stu-id="c9889-239">Built-in middleware</span></span>

<span data-ttu-id="c9889-240">ASP.NET Core se dodává s následujícími součástmi middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c9889-240">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="c9889-241">Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-241">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="c9889-242">Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*.</span><span class="sxs-lookup"><span data-stu-id="c9889-242">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="c9889-243">Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .</span><span class="sxs-lookup"><span data-stu-id="c9889-243">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="c9889-244">Jiné</span><span class="sxs-lookup"><span data-stu-id="c9889-244">Middleware</span></span> | <span data-ttu-id="c9889-245">Description</span><span class="sxs-lookup"><span data-stu-id="c9889-245">Description</span></span> | <span data-ttu-id="c9889-246">Pořadí</span><span class="sxs-lookup"><span data-stu-id="c9889-246">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="c9889-247">Přihlašovací</span><span class="sxs-lookup"><span data-stu-id="c9889-247">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="c9889-248">Poskytuje podporu ověřování.</span><span class="sxs-lookup"><span data-stu-id="c9889-248">Provides authentication support.</span></span> | <span data-ttu-id="c9889-249">Před `HttpContext.User` je potřeba.</span><span class="sxs-lookup"><span data-stu-id="c9889-249">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="c9889-250">Terminál pro zpětná volání OAuth.</span><span class="sxs-lookup"><span data-stu-id="c9889-250">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="c9889-251">Zásady souborů cookie</span><span class="sxs-lookup"><span data-stu-id="c9889-251">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="c9889-252">Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro pole cookie, například `secure` a `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="c9889-252">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="c9889-253">Před middlewarem, který vydává soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="c9889-253">Before middleware that issues cookies.</span></span> <span data-ttu-id="c9889-254">Příklady: ověřování, relace, MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="c9889-254">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="c9889-255">CORS</span><span class="sxs-lookup"><span data-stu-id="c9889-255">CORS</span></span>](xref:security/cors) | <span data-ttu-id="c9889-256">Konfiguruje sdílení prostředků mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="c9889-256">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="c9889-257">Před komponenty, které používají CORS.</span><span class="sxs-lookup"><span data-stu-id="c9889-257">Before components that use CORS.</span></span> |
| [<span data-ttu-id="c9889-258">Diagnostiky</span><span class="sxs-lookup"><span data-stu-id="c9889-258">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="c9889-259">Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace.</span><span class="sxs-lookup"><span data-stu-id="c9889-259">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="c9889-260">Před komponenty, které generují chyby.</span><span class="sxs-lookup"><span data-stu-id="c9889-260">Before components that generate errors.</span></span> <span data-ttu-id="c9889-261">Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace</span><span class="sxs-lookup"><span data-stu-id="c9889-261">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="c9889-262">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="c9889-262">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="c9889-263">Přepošle hlavičky proxy na aktuální požadavek.</span><span class="sxs-lookup"><span data-stu-id="c9889-263">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="c9889-264">Před komponenty, které používají aktualizované pole.</span><span class="sxs-lookup"><span data-stu-id="c9889-264">Before components that consume the updated fields.</span></span> <span data-ttu-id="c9889-265">Příklady: schéma, hostitel, IP adresa klienta, metoda.</span><span class="sxs-lookup"><span data-stu-id="c9889-265">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="c9889-266">Kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="c9889-266">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="c9889-267">Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například Kontrola dostupnosti databáze.</span><span class="sxs-lookup"><span data-stu-id="c9889-267">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="c9889-268">Terminál, je-li požadavek shodný s koncovým bodem kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="c9889-268">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="c9889-269">Přepsání metody HTTP</span><span class="sxs-lookup"><span data-stu-id="c9889-269">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="c9889-270">Umožňuje příchozí žádosti POST přepsat metodu.</span><span class="sxs-lookup"><span data-stu-id="c9889-270">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="c9889-271">Před komponenty, které používají aktualizovanou metodu.</span><span class="sxs-lookup"><span data-stu-id="c9889-271">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="c9889-272">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="c9889-272">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="c9889-273">Přesměrovat všechny požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c9889-273">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="c9889-274">Před komponenty, které používají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c9889-274">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="c9889-275">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="c9889-275">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="c9889-276">Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c9889-276">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="c9889-277">Před odesláním odpovědí a po součástech, které upravují požadavky.</span><span class="sxs-lookup"><span data-stu-id="c9889-277">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="c9889-278">Příklady: předávané hlavičky, přepis adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c9889-278">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="c9889-279">NÁVRHOVÝ</span><span class="sxs-lookup"><span data-stu-id="c9889-279">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="c9889-280">Zpracovává požadavky pomocí MVC/Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c9889-280">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="c9889-281">Terminál, pokud požadavek odpovídá trase.</span><span class="sxs-lookup"><span data-stu-id="c9889-281">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="c9889-282">OWIN</span><span class="sxs-lookup"><span data-stu-id="c9889-282">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="c9889-283">Interoperabilita s aplikacemi, servery a middlewarem založeným na OWIN</span><span class="sxs-lookup"><span data-stu-id="c9889-283">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="c9889-284">Terminál, pokud middleware OWIN plně zpracovává požadavek.</span><span class="sxs-lookup"><span data-stu-id="c9889-284">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="c9889-285">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="c9889-285">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="c9889-286">Poskytuje podporu pro ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c9889-286">Provides support for caching responses.</span></span> | <span data-ttu-id="c9889-287">Před součástmi, které vyžadují ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="c9889-287">Before components that require caching.</span></span> |
| [<span data-ttu-id="c9889-288">Komprese odezvy</span><span class="sxs-lookup"><span data-stu-id="c9889-288">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="c9889-289">Poskytuje podporu pro komprimaci odpovědí.</span><span class="sxs-lookup"><span data-stu-id="c9889-289">Provides support for compressing responses.</span></span> | <span data-ttu-id="c9889-290">Před součástmi, které vyžadují kompresi.</span><span class="sxs-lookup"><span data-stu-id="c9889-290">Before components that require compression.</span></span> |
| [<span data-ttu-id="c9889-291">Lokalizace žádosti</span><span class="sxs-lookup"><span data-stu-id="c9889-291">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="c9889-292">Poskytuje podporu lokalizace.</span><span class="sxs-lookup"><span data-stu-id="c9889-292">Provides localization support.</span></span> | <span data-ttu-id="c9889-293">Před lokalizací citlivých komponent.</span><span class="sxs-lookup"><span data-stu-id="c9889-293">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="c9889-294">Směrování koncových bodů</span><span class="sxs-lookup"><span data-stu-id="c9889-294">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="c9889-295">Definuje a omezuje trasy požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-295">Defines and constrains request routes.</span></span> | <span data-ttu-id="c9889-296">Terminál pro vyhovující trasy.</span><span class="sxs-lookup"><span data-stu-id="c9889-296">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="c9889-297">Jednání</span><span class="sxs-lookup"><span data-stu-id="c9889-297">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="c9889-298">Poskytuje podporu pro správu uživatelských relací.</span><span class="sxs-lookup"><span data-stu-id="c9889-298">Provides support for managing user sessions.</span></span> | <span data-ttu-id="c9889-299">Před komponenty, které vyžadují relaci.</span><span class="sxs-lookup"><span data-stu-id="c9889-299">Before components that require Session.</span></span> |
| [<span data-ttu-id="c9889-300">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="c9889-300">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="c9889-301">Poskytuje podporu pro poskytování statických souborů a procházení adresářů.</span><span class="sxs-lookup"><span data-stu-id="c9889-301">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="c9889-302">Terminál, pokud požadavek odpovídá souboru</span><span class="sxs-lookup"><span data-stu-id="c9889-302">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="c9889-303">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="c9889-303">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="c9889-304">Poskytuje podporu pro přepisování adres URL a přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="c9889-304">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="c9889-305">Před komponenty, které používají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c9889-305">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="c9889-306">WebSockets</span><span class="sxs-lookup"><span data-stu-id="c9889-306">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="c9889-307">Povolí protokol WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c9889-307">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="c9889-308">Před komponenty, které jsou vyžadovány pro příjem požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c9889-308">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c9889-309">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="c9889-309">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
