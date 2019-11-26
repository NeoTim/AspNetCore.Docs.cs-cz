---
title: Middleware ASP.NET Core
author: rick-anderson
description: Přečtěte si o ASP.NET Core middlewaru a kanálu požadavků.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: d678f3d1f6ca10e486543a2965506236e4e61b82
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239840"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="bfec2-103">Middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bfec2-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="bfec2-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bfec2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bfec2-105">Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi.</span><span class="sxs-lookup"><span data-stu-id="bfec2-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="bfec2-106">Každá komponenta:</span><span class="sxs-lookup"><span data-stu-id="bfec2-106">Each component:</span></span>

* <span data-ttu-id="bfec2-107">Zvolí, zda předá požadavek další komponentě v kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="bfec2-108">Může provádět práci před voláním a po volání další komponenty v kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="bfec2-109">Delegáti požadavku se používají k vytvoření kanálu požadavku.</span><span class="sxs-lookup"><span data-stu-id="bfec2-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="bfec2-110">Delegáti požadavků zpracovávají každý HTTP požadavek.</span><span class="sxs-lookup"><span data-stu-id="bfec2-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="bfec2-111">Delegáti žádostí jsou nakonfigurováni pomocí metod rozšíření <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>a <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="bfec2-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="bfec2-112">Jednotliví delegáti požadavků mohou být definováni v jednom řádku jako anonymní metoda (tzv. in-line middlewary), nebo mohou být definováni ve znovupoužitelné třídě.</span><span class="sxs-lookup"><span data-stu-id="bfec2-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="bfec2-113">Tyto opakovaně použitelné třídy a vložené anonymní metody jsou *middleware*, označované také jako *komponenty middlewaru*.</span><span class="sxs-lookup"><span data-stu-id="bfec2-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="bfec2-114">Každá middlewarová komponenta v kanálu požadavku zodpovídá za vyvolání další komponenty v kanálu nebo předčasné ukončení kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="bfec2-115">Když jsou krátkodobé okruhy middleware, nazývá se *middleware terminálu* , protože zabrání dalšímu middlewaru ve zpracování žádosti.</span><span class="sxs-lookup"><span data-stu-id="bfec2-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="bfec2-116"><xref:migration/http-modules> vysvětluje rozdíl mezi kanály požadavků v ASP.NET Core a ASP.NET 4. x a poskytuje další ukázky middlewaru.</span><span class="sxs-lookup"><span data-stu-id="bfec2-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="bfec2-117">Vytvoření kanálu middlewaru pomocí IApplicationBuilder</span><span class="sxs-lookup"><span data-stu-id="bfec2-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="bfec2-118">Kanál požadavků ASP.NET Core se skládá z posloupnosti delegátů požadavku a volají se jeden po druhém.</span><span class="sxs-lookup"><span data-stu-id="bfec2-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="bfec2-119">Následující diagram znázorňuje tento koncept.</span><span class="sxs-lookup"><span data-stu-id="bfec2-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="bfec2-120">Vlákno provádění postupuje po směru černé šipky.</span><span class="sxs-lookup"><span data-stu-id="bfec2-120">The thread of execution follows the black arrows.</span></span>

![Vzor zpracování požadavku zobrazujující příchod, zpracování požadavku prostřednictvím tři middlewarů a odpověď opouštějící aplikaci.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="bfec2-124">Každý delegát můžet provádět operace před vyvoláním a po vyvolání dalšího delegáta.</span><span class="sxs-lookup"><span data-stu-id="bfec2-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="bfec2-125">Delegáti zpracovávající výjimky by měli být voláni v kanálu co nejdříve, aby mohli zachytit výjimky, ke kterým dochází v pozdějších etapách kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="bfec2-126">Nejjednodušší možná aplikace ASP.NET Core nastavuje jediného delegáta požadavků, který zpracovává všechny požadavky.</span><span class="sxs-lookup"><span data-stu-id="bfec2-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="bfec2-127">Tento případ ve skutečnosti neobsahuje kanál požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="bfec2-128">Místo toho se volá jediná anonymní funkce v reakci na každý požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="bfec2-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="bfec2-129">První <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegát ukončí kanál.</span><span class="sxs-lookup"><span data-stu-id="bfec2-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="bfec2-130">Řetězení více požadavků delegátů společně s <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span><span class="sxs-lookup"><span data-stu-id="bfec2-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="bfec2-131">Parametr `next` představuje dalšího delegáta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="bfec2-132">Kanál můžete pro krátké okruhy vymezit tím *, že nevoláte* *Další* parametr.</span><span class="sxs-lookup"><span data-stu-id="bfec2-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="bfec2-133">Obvykle můžete provádět akce před vykonáním i po vykonání dalšího delegáta, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="bfec2-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="bfec2-134">Když delegát neprojde požadavek na dalšího delegáta, nazývá se to *krátkodobý kanál žádosti*.</span><span class="sxs-lookup"><span data-stu-id="bfec2-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="bfec2-135">Předčasné ukončení kanálu je často žádoucí, protože eliminuje zbytečně vykonanou práci.</span><span class="sxs-lookup"><span data-stu-id="bfec2-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="bfec2-136">Například [middleware statických souborů](xref:fundamentals/static-files) může fungovat jako *middleware terminálu* tím, že zpracovává požadavek na statický soubor a krátký okruh zbývajících částí kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="bfec2-137">Middleware přidané do kanálu předtím, než middleware, který ukončí další zpracování, stále zpracovává kód po jejich `next.Invoke`ch příkazech.</span><span class="sxs-lookup"><span data-stu-id="bfec2-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="bfec2-138">V takovém případě se však zobrazí varování před pokusem o zápis do odpovědi, která se už odeslala.</span><span class="sxs-lookup"><span data-stu-id="bfec2-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="bfec2-139">Po odeslání odpovědi klientovi Nevolejte `next.Invoke`.</span><span class="sxs-lookup"><span data-stu-id="bfec2-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="bfec2-140">Změny <xref:Microsoft.AspNetCore.Http.HttpResponse> po zahájení reakce vyvolávají výjimku.</span><span class="sxs-lookup"><span data-stu-id="bfec2-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="bfec2-141">Výjimku vyvolají například změny, jako jsou nastavení hlaviček a stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="bfec2-142">Zápis do těla odpovědi po volání `next`:</span><span class="sxs-lookup"><span data-stu-id="bfec2-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="bfec2-143">Může způsobit porušení protokolu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-143">May cause a protocol violation.</span></span> <span data-ttu-id="bfec2-144">Například zápis více než uvedených `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="bfec2-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="bfec2-145">Může porušit formát těla zprávy.</span><span class="sxs-lookup"><span data-stu-id="bfec2-145">May corrupt the body format.</span></span> <span data-ttu-id="bfec2-146">Například zápis zápatí HTML do souboru CSS.</span><span class="sxs-lookup"><span data-stu-id="bfec2-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="bfec2-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> je užitečnou nápovědou, která označuje, zda byla odeslána hlavička nebo byl text napsán do.</span><span class="sxs-lookup"><span data-stu-id="bfec2-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="bfec2-148">Pořadí middlewaru</span><span class="sxs-lookup"><span data-stu-id="bfec2-148">Middleware order</span></span>

<span data-ttu-id="bfec2-149">Pořadí, v jakém jsou komponenty middlewaru přidány v metodě `Startup.Configure` definuje pořadí, ve kterém jsou komponenty middleware vyvolány na žádostech a obráceném pořadí pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="bfec2-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="bfec2-150">Pořadí je **důležité** pro zabezpečení, výkon a funkčnost.</span><span class="sxs-lookup"><span data-stu-id="bfec2-150">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="bfec2-151">Následující metoda `Startup.Configure` přidá do doporučeného pořadí součásti middlewaru související se zabezpečením:</span><span class="sxs-lookup"><span data-stu-id="bfec2-151">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="bfec2-152">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="bfec2-152">In the preceding code:</span></span>

* <span data-ttu-id="bfec2-153">Middleware, které se nepřidaly při vytváření nové webové aplikace s [jednotlivými účty uživatele](xref:security/authentication/identity) , jsou zakomentovány.</span><span class="sxs-lookup"><span data-stu-id="bfec2-153">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="bfec2-154">Ne každý middleware potřebuje přejít v tomto přesném pořadí, ale mnoho do něj.</span><span class="sxs-lookup"><span data-stu-id="bfec2-154">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="bfec2-155">Například `UseCors`, `UseAuthentication`a `UseAuthorization` musí jít v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="bfec2-155">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="bfec2-156">Následující metoda `Startup.Configure` přidává komponenty middlewaru pro běžné scénáře aplikací:</span><span class="sxs-lookup"><span data-stu-id="bfec2-156">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="bfec2-157">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="bfec2-157">Exception/error handling</span></span>
   * <span data-ttu-id="bfec2-158">Když aplikace běží ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="bfec2-158">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="bfec2-159">Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-159">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="bfec2-160">Zpráva middlewaru chybové stránky databáze oznamuje chyby běhového běhu databáze.</span><span class="sxs-lookup"><span data-stu-id="bfec2-160">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="bfec2-161">Když aplikace běží v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="bfec2-161">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="bfec2-162">Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.</span><span class="sxs-lookup"><span data-stu-id="bfec2-162">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="bfec2-163">Middleware HSTS (HTTP Strict Transport Security Protocol) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidává `Strict-Transport-Security` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="bfec2-163">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="bfec2-164">Middleware pro přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bfec2-164">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="bfec2-165">Middleware pro statický soubor (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.</span><span class="sxs-lookup"><span data-stu-id="bfec2-165">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="bfec2-166">Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) vyhovuje předpisům platným v EU Obecné nařízení o ochraně osobních údajů (GDPR).</span><span class="sxs-lookup"><span data-stu-id="bfec2-166">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="bfec2-167">Směrování middlewaru (`UseRouting`) na požadavky směrování.</span><span class="sxs-lookup"><span data-stu-id="bfec2-167">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="bfec2-168">Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="bfec2-168">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="bfec2-169">Middleware autorizace (`UseAuthorization`) opravňuje uživatele k přístupu k zabezpečeným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="bfec2-169">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="bfec2-170">Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-170">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="bfec2-171">Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.</span><span class="sxs-lookup"><span data-stu-id="bfec2-171">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="bfec2-172">Middleware směrování koncového bodu (`UseEndpoints` s `MapRazorPages`) pro přidání Razor Pages koncových bodů do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-172">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="bfec2-173">V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>ho oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="bfec2-173">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="bfec2-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="bfec2-175">Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.</span><span class="sxs-lookup"><span data-stu-id="bfec2-175">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="bfec2-176">Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami.</span><span class="sxs-lookup"><span data-stu-id="bfec2-176">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="bfec2-177">Middleware statických souborů neposkytuje **žádné** autorizační kontroly.</span><span class="sxs-lookup"><span data-stu-id="bfec2-177">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="bfec2-178">Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné.</span><span class="sxs-lookup"><span data-stu-id="bfec2-178">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="bfec2-179">Přístup k zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bfec2-179">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="bfec2-180">Pokud požadavek nezpracovává middleware pro statický soubor, je předán do middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="bfec2-180">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="bfec2-181">Autentizace neukončuje předčasně neověřené žádosti.</span><span class="sxs-lookup"><span data-stu-id="bfec2-181">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="bfec2-182">Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.</span><span class="sxs-lookup"><span data-stu-id="bfec2-182">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="bfec2-183">Následující příklad ukazuje pořadí middlewarů, ve kterém se požadavky na statické soubory zpracovávají middlewarem pro statické soubory před middlewarem pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="bfec2-183">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="bfec2-184">Statické soubory se v tomto uspořádání middlewarů nekomprimují.</span><span class="sxs-lookup"><span data-stu-id="bfec2-184">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="bfec2-185">Odezvy Razor Pages lze komprimovat.</span><span class="sxs-lookup"><span data-stu-id="bfec2-185">The Razor Pages responses can be compressed.</span></span>

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

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="bfec2-186">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="bfec2-186">In the preceding code:</span></span>

* <span data-ttu-id="bfec2-187">Middleware, které se nepřidaly při vytváření nové webové aplikace s [jednotlivými účty uživatele](xref:security/authentication/identity) , jsou zakomentovány.</span><span class="sxs-lookup"><span data-stu-id="bfec2-187">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="bfec2-188">Ne každý middleware potřebuje přejít v tomto přesném pořadí, ale mnoho do něj.</span><span class="sxs-lookup"><span data-stu-id="bfec2-188">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="bfec2-189">Například `UseCors` a `UseAuthentication` musí jít v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="bfec2-189">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="bfec2-190">Následující metoda `Startup.Configure` přidává komponenty middlewaru pro běžné scénáře aplikací:</span><span class="sxs-lookup"><span data-stu-id="bfec2-190">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="bfec2-191">Zpracování výjimek a chyb</span><span class="sxs-lookup"><span data-stu-id="bfec2-191">Exception/error handling</span></span>
   * <span data-ttu-id="bfec2-192">Když aplikace běží ve vývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="bfec2-192">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="bfec2-193">Middleware stránky s výjimkou pro vývojáře (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) hlásí chyby modulu runtime aplikace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-193">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="bfec2-194">Middleware chybových stránek databáze (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) oznamuje chyby běhového běhu databáze.</span><span class="sxs-lookup"><span data-stu-id="bfec2-194">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="bfec2-195">Když aplikace běží v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="bfec2-195">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="bfec2-196">Middleware obslužné rutiny výjimek (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) zachytává výjimky vyvolané v následujících middlewarech.</span><span class="sxs-lookup"><span data-stu-id="bfec2-196">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="bfec2-197">Middleware HSTS (HTTP Strict Transport Security Protocol) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) přidává `Strict-Transport-Security` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="bfec2-197">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="bfec2-198">Middleware pro přesměrování HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) přesměruje požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bfec2-198">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="bfec2-199">Middleware pro statický soubor (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) vrátí statické soubory a další zpracování žádostí o krátkodobé okruhy.</span><span class="sxs-lookup"><span data-stu-id="bfec2-199">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="bfec2-200">Middleware zásad souborů cookie (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) vyhovuje předpisům platným v EU Obecné nařízení o ochraně osobních údajů (GDPR).</span><span class="sxs-lookup"><span data-stu-id="bfec2-200">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="bfec2-201">Middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) se pokusí ověřit uživatele předtím, než budou mít přístup k zabezpečeným prostředkům.</span><span class="sxs-lookup"><span data-stu-id="bfec2-201">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="bfec2-202">Middleware relace (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) vytváří a udržuje stav relace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-202">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="bfec2-203">Pokud aplikace používá stav relace, volejte middleware relace za middlewarem zásad souborů cookie a před middlewarem MVC.</span><span class="sxs-lookup"><span data-stu-id="bfec2-203">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="bfec2-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) Chcete-li přidat MVC do kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="bfec2-205">V předchozím příkladu kódu je každá metoda rozšíření middleware vystavena <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> prostřednictvím <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>ho oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="bfec2-205">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="bfec2-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> je první součást middleware přidaná do kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="bfec2-207">Z toho důvodu zachytává middleware pro zpracování výjimek všechny výjimky, ke kterým dochází v pozdějších voláních.</span><span class="sxs-lookup"><span data-stu-id="bfec2-207">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="bfec2-208">Middleware statických souborů je volán brzy v průběhu kanálu tak, aby mohl zpracovat požadavky a předčasně ukončit kanál bez nutnosti procházet zbylými komponentami.</span><span class="sxs-lookup"><span data-stu-id="bfec2-208">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="bfec2-209">Middleware statických souborů neposkytuje **žádné** autorizační kontroly.</span><span class="sxs-lookup"><span data-stu-id="bfec2-209">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="bfec2-210">Všechny soubory, které poskytuje middleware pro statický soubor, včetně těch v rámci *wwwroot*, jsou veřejně dostupné.</span><span class="sxs-lookup"><span data-stu-id="bfec2-210">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="bfec2-211">Přístup k zabezpečení statických souborů naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="bfec2-211">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="bfec2-212">Pokud požadavek nezpracovává middleware pro statický soubor, je předán do middleware ověřování (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), který provádí ověřování.</span><span class="sxs-lookup"><span data-stu-id="bfec2-212">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="bfec2-213">Autentizace neukončuje předčasně neověřené žádosti.</span><span class="sxs-lookup"><span data-stu-id="bfec2-213">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="bfec2-214">Přestože autentizační middleware autentizuje požadavky, autentizace (a odmítnutí) nastane pouze po výběru specifické stránky Razor nebo MVC kontroleru a akce pomocí MVC.</span><span class="sxs-lookup"><span data-stu-id="bfec2-214">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="bfec2-215">Následující příklad ukazuje pořadí middlewarů, ve kterém se požadavky na statické soubory zpracovávají middlewarem pro statické soubory před middlewarem pro kompresi odpovědí.</span><span class="sxs-lookup"><span data-stu-id="bfec2-215">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="bfec2-216">Statické soubory se v tomto uspořádání middlewarů nekomprimují.</span><span class="sxs-lookup"><span data-stu-id="bfec2-216">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="bfec2-217">Odpovědi MVC z <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> lze zkomprimovat.</span><span class="sxs-lookup"><span data-stu-id="bfec2-217">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

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

## <a name="use-run-and-map"></a><span data-ttu-id="bfec2-218">Metody Use, Run a Map</span><span class="sxs-lookup"><span data-stu-id="bfec2-218">Use, Run, and Map</span></span>

<span data-ttu-id="bfec2-219">Nakonfigurujte kanál HTTP pomocí <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>a <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span><span class="sxs-lookup"><span data-stu-id="bfec2-219">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="bfec2-220">Metoda `Use` může vytvořit krátký okruh kanálu (tj. Pokud nevolá delegáta žádosti `next`).</span><span class="sxs-lookup"><span data-stu-id="bfec2-220">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="bfec2-221">`Run` je konvence a některé komponenty middlewaru můžou vystavovat `Run[Middleware]` metody, které běží na konci kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-221">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="bfec2-222">rozšíření <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> se používají jako konvence pro větvení kanálu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="bfec2-223">`Map` větví kanálu požadavků na základě shody dané cesty požadavku.</span><span class="sxs-lookup"><span data-stu-id="bfec2-223">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="bfec2-224">Pokud cesta požadavku začíná danou cestou požadavku, větev se provede.</span><span class="sxs-lookup"><span data-stu-id="bfec2-224">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="bfec2-225">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-225">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="bfec2-226">Žádost</span><span class="sxs-lookup"><span data-stu-id="bfec2-226">Request</span></span>             | <span data-ttu-id="bfec2-227">Odpověď</span><span class="sxs-lookup"><span data-stu-id="bfec2-227">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="bfec2-228">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="bfec2-228">localhost:1234</span></span>      | <span data-ttu-id="bfec2-229">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="bfec2-229">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="bfec2-230">localhost: 1234/Map1</span><span class="sxs-lookup"><span data-stu-id="bfec2-230">localhost:1234/map1</span></span> | <span data-ttu-id="bfec2-231">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="bfec2-231">Map Test 1</span></span>                   |
| <span data-ttu-id="bfec2-232">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="bfec2-232">localhost:1234/map2</span></span> | <span data-ttu-id="bfec2-233">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="bfec2-233">Map Test 2</span></span>                   |
| <span data-ttu-id="bfec2-234">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="bfec2-234">localhost:1234/map3</span></span> | <span data-ttu-id="bfec2-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="bfec2-235">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="bfec2-236">Při použití `Map` se odpovídající segmenty cesty odeberou z `HttpRequest.Path` a připojí se k `HttpRequest.PathBase` pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="bfec2-236">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="bfec2-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> větví kanálu požadavků na základě výsledku daného predikátu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="bfec2-238">K mapování požadavků na novou větev kanálu lze použít jakýkoli predikát typu `Func<HttpContext, bool>`.</span><span class="sxs-lookup"><span data-stu-id="bfec2-238">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="bfec2-239">V následujícím příkladu se k detekci přítomnosti proměnné řetězce dotazu používá predikát `branch`:</span><span class="sxs-lookup"><span data-stu-id="bfec2-239">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="bfec2-240">V následující tabulce jsou uvedeny požadavky a odpovědi z `http://localhost:1234` pomocí předchozího kódu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-240">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="bfec2-241">Žádost</span><span class="sxs-lookup"><span data-stu-id="bfec2-241">Request</span></span>                       | <span data-ttu-id="bfec2-242">Odpověď</span><span class="sxs-lookup"><span data-stu-id="bfec2-242">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="bfec2-243">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="bfec2-243">localhost:1234</span></span>                | <span data-ttu-id="bfec2-244">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="bfec2-244">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="bfec2-245">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="bfec2-245">localhost:1234/?branch=master</span></span> | <span data-ttu-id="bfec2-246">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="bfec2-246">Branch used = master</span></span>         |

<span data-ttu-id="bfec2-247">`Map` podporuje vnořování, například:</span><span class="sxs-lookup"><span data-stu-id="bfec2-247">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="bfec2-248">`Map` může také odpovídat více segmentům najednou:</span><span class="sxs-lookup"><span data-stu-id="bfec2-248">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="bfec2-249">Integrované middlewary</span><span class="sxs-lookup"><span data-stu-id="bfec2-249">Built-in middleware</span></span>

<span data-ttu-id="bfec2-250">ASP.NET Core se dodává s následujícími middlewarovými komponenty.</span><span class="sxs-lookup"><span data-stu-id="bfec2-250">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="bfec2-251">Sloupec *Order* poskytuje poznámky k umístění middlewaru v kanálu zpracování požadavků a za jakých podmínek může middleware ukončit zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-251">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="bfec2-252">Když middleware middleware vytvoří kanál zpracování požadavků a zabrání dalšímu podřízenému middlewaru ve zpracování žádosti, nazývá se *middleware terminálu*.</span><span class="sxs-lookup"><span data-stu-id="bfec2-252">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="bfec2-253">Další informace o krátkodobém okruhu najdete v části [vytvoření kanálu middlewaru pomocí IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) .</span><span class="sxs-lookup"><span data-stu-id="bfec2-253">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="bfec2-254">Middleware</span><span class="sxs-lookup"><span data-stu-id="bfec2-254">Middleware</span></span> | <span data-ttu-id="bfec2-255">Popis</span><span class="sxs-lookup"><span data-stu-id="bfec2-255">Description</span></span> | <span data-ttu-id="bfec2-256">Pořadí</span><span class="sxs-lookup"><span data-stu-id="bfec2-256">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="bfec2-257">Ověřování</span><span class="sxs-lookup"><span data-stu-id="bfec2-257">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="bfec2-258">Poskytuje podporu ověřování.</span><span class="sxs-lookup"><span data-stu-id="bfec2-258">Provides authentication support.</span></span> | <span data-ttu-id="bfec2-259">Před `HttpContext.User` je potřeba.</span><span class="sxs-lookup"><span data-stu-id="bfec2-259">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="bfec2-260">Terminál pro zpětná volání OAuth.</span><span class="sxs-lookup"><span data-stu-id="bfec2-260">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="bfec2-261">Zásady souborů cookie</span><span class="sxs-lookup"><span data-stu-id="bfec2-261">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="bfec2-262">Sleduje souhlas uživatelů při ukládání osobních údajů a vynutila minimální standardy pro pole cookie, jako je například `secure` a `SameSite`.</span><span class="sxs-lookup"><span data-stu-id="bfec2-262">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="bfec2-263">Před middlewarem, který vydává cookies.</span><span class="sxs-lookup"><span data-stu-id="bfec2-263">Before middleware that issues cookies.</span></span> <span data-ttu-id="bfec2-264">Příklady: ověřování, relace, MVC (TempData).</span><span class="sxs-lookup"><span data-stu-id="bfec2-264">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="bfec2-265">CORS</span><span class="sxs-lookup"><span data-stu-id="bfec2-265">CORS</span></span>](xref:security/cors) | <span data-ttu-id="bfec2-266">Konfiguruje sdílení prostředků mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="bfec2-266">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="bfec2-267">Před komponentami, které používají CORS.</span><span class="sxs-lookup"><span data-stu-id="bfec2-267">Before components that use CORS.</span></span> |
| [<span data-ttu-id="bfec2-268">Diagnostika</span><span class="sxs-lookup"><span data-stu-id="bfec2-268">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="bfec2-269">Několik samostatných middlewarů, které poskytují stránku s výjimkou vývojářů, zpracování výjimek, stránky stavového kódu a výchozí webovou stránku pro nové aplikace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-269">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="bfec2-270">Před komponentami, které generují chyby.</span><span class="sxs-lookup"><span data-stu-id="bfec2-270">Before components that generate errors.</span></span> <span data-ttu-id="bfec2-271">Terminál pro výjimky nebo pro výchozí webovou stránku pro nové aplikace</span><span class="sxs-lookup"><span data-stu-id="bfec2-271">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="bfec2-272">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="bfec2-272">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="bfec2-273">Přesměrovává hlavičky skryté proxy serverem do aktuální žádosti.</span><span class="sxs-lookup"><span data-stu-id="bfec2-273">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="bfec2-274">Před komponentami, které využívají aktualizovaná pole.</span><span class="sxs-lookup"><span data-stu-id="bfec2-274">Before components that consume the updated fields.</span></span> <span data-ttu-id="bfec2-275">Příklady: schéma, hostitel, IP adresa klienta, metoda.</span><span class="sxs-lookup"><span data-stu-id="bfec2-275">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="bfec2-276">Kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="bfec2-276">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="bfec2-277">Kontroluje stav aplikace ASP.NET Core a jejích závislostí, jako je například kontrola dostupnosti databáze.</span><span class="sxs-lookup"><span data-stu-id="bfec2-277">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="bfec2-278">Terminál, pokud požadavek odpovídá koncovému bodu kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-278">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="bfec2-279">Přepsání metody HTTP</span><span class="sxs-lookup"><span data-stu-id="bfec2-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="bfec2-280">Umožňuje příchozím POST požadavkům přepsat metody.</span><span class="sxs-lookup"><span data-stu-id="bfec2-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="bfec2-281">Před komponentami, které využívají aktualizovanou metodu.</span><span class="sxs-lookup"><span data-stu-id="bfec2-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="bfec2-282">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="bfec2-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="bfec2-283">Přesměrovat všechny požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bfec2-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="bfec2-284">Před komponentami, které využívají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bfec2-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="bfec2-285">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="bfec2-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="bfec2-286">Vylepšení zabezpečení – middleware, který přidává speciální hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="bfec2-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="bfec2-287">Předtím, než se pošlou odpovědi, a potom, co komponenta modifikuje žádost.</span><span class="sxs-lookup"><span data-stu-id="bfec2-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="bfec2-288">Příklady: předávané hlavičky, přepis adresy URL.</span><span class="sxs-lookup"><span data-stu-id="bfec2-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="bfec2-289">MVC</span><span class="sxs-lookup"><span data-stu-id="bfec2-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="bfec2-290">Zpracovává požadavky pomocí MVC/Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bfec2-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="bfec2-291">Terminál, pokud požadavek odpovídá trase.</span><span class="sxs-lookup"><span data-stu-id="bfec2-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="bfec2-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="bfec2-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="bfec2-293">Interoperabilita s aplikacemi, serverem a middlewarem OWIN.</span><span class="sxs-lookup"><span data-stu-id="bfec2-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="bfec2-294">Terminál, pokud OWIN middleware plně zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="bfec2-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="bfec2-295">Ukládání odpovědí do mezipaměti</span><span class="sxs-lookup"><span data-stu-id="bfec2-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="bfec2-296">Poskytuje podporu pro ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="bfec2-296">Provides support for caching responses.</span></span> | <span data-ttu-id="bfec2-297">Před komponentami, které vyžadují ukládání do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="bfec2-297">Before components that require caching.</span></span> |
| [<span data-ttu-id="bfec2-298">Komprese odezvy</span><span class="sxs-lookup"><span data-stu-id="bfec2-298">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="bfec2-299">Poskytuje podporu pro komprimaci odpovědí.</span><span class="sxs-lookup"><span data-stu-id="bfec2-299">Provides support for compressing responses.</span></span> | <span data-ttu-id="bfec2-300">Před komponentami, které vyžadují komprese.</span><span class="sxs-lookup"><span data-stu-id="bfec2-300">Before components that require compression.</span></span> |
| [<span data-ttu-id="bfec2-301">Lokalizace žádosti</span><span class="sxs-lookup"><span data-stu-id="bfec2-301">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="bfec2-302">Poskytuje podporu lokalizace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-302">Provides localization support.</span></span> | <span data-ttu-id="bfec2-303">Před komponentami citlivými na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="bfec2-303">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="bfec2-304">Směrování koncových bodů</span><span class="sxs-lookup"><span data-stu-id="bfec2-304">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="bfec2-305">Definuje a omezuje trasy požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-305">Defines and constrains request routes.</span></span> | <span data-ttu-id="bfec2-306">Terminál pro odpovídající trasy.</span><span class="sxs-lookup"><span data-stu-id="bfec2-306">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="bfec2-307">Relace</span><span class="sxs-lookup"><span data-stu-id="bfec2-307">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="bfec2-308">Poskytuje podporu pro správu uživatelských relací.</span><span class="sxs-lookup"><span data-stu-id="bfec2-308">Provides support for managing user sessions.</span></span> | <span data-ttu-id="bfec2-309">Před komponentami, které vyžadují relace.</span><span class="sxs-lookup"><span data-stu-id="bfec2-309">Before components that require Session.</span></span> |
| [<span data-ttu-id="bfec2-310">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="bfec2-310">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="bfec2-311">Poskytuje podporu pro poskytování statických souborů a výpis adresářů.</span><span class="sxs-lookup"><span data-stu-id="bfec2-311">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="bfec2-312">Terminál, pokud požadavek odpovídá souboru</span><span class="sxs-lookup"><span data-stu-id="bfec2-312">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="bfec2-313">Přepsání adresy URL</span><span class="sxs-lookup"><span data-stu-id="bfec2-313">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="bfec2-314">Poskytuje podporu pro přepisování adres URL a přesměrování požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-314">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="bfec2-315">Před komponentami, které využívají adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bfec2-315">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="bfec2-316">Webové sokety</span><span class="sxs-lookup"><span data-stu-id="bfec2-316">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="bfec2-317">Povolí protokol WebSocket.</span><span class="sxs-lookup"><span data-stu-id="bfec2-317">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="bfec2-318">Před komponentami, které jsou nutné pro příjem WebSocket požadavků.</span><span class="sxs-lookup"><span data-stu-id="bfec2-318">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="bfec2-319">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="bfec2-319">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
