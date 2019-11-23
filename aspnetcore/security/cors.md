---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: security/cors
ms.openlocfilehash: 3a51d365626c858ad48298a1108e37eba9050fe7
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391298"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="e34e0-103">Povolit žádosti mezi zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e34e0-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="e34e0-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e34e0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e34e0-105">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e34e0-106">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="e34e0-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e34e0-107">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="e34e0-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e34e0-108">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="e34e0-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e34e0-109">V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="e34e0-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="e34e0-110">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="e34e0-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e34e0-111">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="e34e0-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e34e0-112">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="e34e0-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e34e0-113">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="e34e0-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e34e0-114">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e34e0-115">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="e34e0-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e34e0-116">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="e34e0-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e34e0-117">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="e34e0-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e34e0-118">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e34e0-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e34e0-119">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="e34e0-119">Same origin</span></span>

<span data-ttu-id="e34e0-120">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="e34e0-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e34e0-121">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="e34e0-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e34e0-122">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="e34e0-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e34e0-123">`https://example.net` &ndash; jiné doméně</span><span class="sxs-lookup"><span data-stu-id="e34e0-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="e34e0-124">`https://www.example.com/foo.html` &ndash; různé subdomény</span><span class="sxs-lookup"><span data-stu-id="e34e0-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="e34e0-125">`http://example.com/foo.html` &ndash; různých schémat</span><span class="sxs-lookup"><span data-stu-id="e34e0-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="e34e0-126">`https://example.com:9000/foo.html` &ndash; jiný port</span><span class="sxs-lookup"><span data-stu-id="e34e0-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="e34e0-127">Internet Explorer při porovnávání zdrojů nebere v úvahu port.</span><span class="sxs-lookup"><span data-stu-id="e34e0-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e34e0-128">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="e34e0-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="e34e0-129">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e34e0-130">Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:</span><span class="sxs-lookup"><span data-stu-id="e34e0-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="e34e0-131">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="e34e0-131">The preceding code:</span></span>

* <span data-ttu-id="e34e0-132">Nastaví název zásady na "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="e34e0-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="e34e0-133">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="e34e0-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e34e0-134">Volá metodu rozšíření <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="e34e0-135">Volá <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="e34e0-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e34e0-136">Lambda převezme objekt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e34e0-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e34e0-137">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e34e0-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="e34e0-138">Volání metody <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="e34e0-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e34e0-139">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e34e0-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="e34e0-140">Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> může řetězit metody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="e34e0-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="e34e0-141">Poznámka: adresa URL nesmí **obsahovat koncové** lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="e34e0-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e34e0-142">Pokud se adresa URL ukončí s `/`, porovnávání vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e34e0-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="e34e0-143">Použití zásad CORS u všech koncových bodů</span><span class="sxs-lookup"><span data-stu-id="e34e0-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="e34e0-144">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="e34e0-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> <span data-ttu-id="e34e0-145">Při směrování koncových bodů musí být middleware CORS nakonfigurované tak, aby se spouštěla mezi voláními `UseRouting` a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="e34e0-146">Nesprávná konfigurace způsobí, že middleware přestane fungovat správně.</span><span class="sxs-lookup"><span data-stu-id="e34e0-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="e34e0-147">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="e34e0-147">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="e34e0-148">Poznámka: před `UseMvc`je nutné volat `UseCors`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-148">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="e34e0-149">Viz [Povolení CORS v Razor Pages, řadičích a metodách akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="e34e0-149">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="e34e0-150">Pokyny k testování předchozího kódu najdete v části [test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="e34e0-150">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="e34e0-151">Povolení CORS s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="e34e0-151">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="e34e0-152">S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů pomocí `RequireCors` sady rozšiřujících metod.</span><span class="sxs-lookup"><span data-stu-id="e34e0-152">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="e34e0-153">Podobně lze CORS povolit také pro všechny řadiče:</span><span class="sxs-lookup"><span data-stu-id="e34e0-153">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e34e0-154">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="e34e0-154">Enable CORS with attributes</span></span>

<span data-ttu-id="e34e0-155">Atribut [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="e34e0-155">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e34e0-156">Atribut `[EnableCors]` povoluje CORS pro vybrané koncové body místo všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="e34e0-156">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="e34e0-157">Pomocí `[EnableCors]` můžete zadat výchozí zásady a `[EnableCors("{Policy String}")]` zadat zásadu.</span><span class="sxs-lookup"><span data-stu-id="e34e0-157">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="e34e0-158">Atribut `[EnableCors]` lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="e34e0-158">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="e34e0-159">`PageModel` stránky Razor</span><span class="sxs-lookup"><span data-stu-id="e34e0-159">Razor Page `PageModel`</span></span>
* <span data-ttu-id="e34e0-160">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="e34e0-160">Controller</span></span>
* <span data-ttu-id="e34e0-161">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="e34e0-161">Controller action method</span></span>

<span data-ttu-id="e34e0-162">Pomocí atributu `[EnableCors]` můžete použít různé zásady na řadič nebo stránku-model/akce.</span><span class="sxs-lookup"><span data-stu-id="e34e0-162">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="e34e0-163">Pokud je atribut `[EnableCors]` aplikován na řadiče/stránky – model/akce a v middleware je povolená CORS, uplatní se obě zásady.</span><span class="sxs-lookup"><span data-stu-id="e34e0-163">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="e34e0-164">Doporučujeme před kombinováním zásad.</span><span class="sxs-lookup"><span data-stu-id="e34e0-164">We recommend against combining policies.</span></span> <span data-ttu-id="e34e0-165">Použijte atribut `[EnableCors]` nebo middleware, nikoli oba ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e34e0-165">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="e34e0-166">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="e34e0-166">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e34e0-167">Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="e34e0-167">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="e34e0-168">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="e34e0-168">Disable CORS</span></span>

<span data-ttu-id="e34e0-169">Atribut [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="e34e0-169">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e34e0-170">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="e34e0-170">CORS policy options</span></span>

<span data-ttu-id="e34e0-171">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="e34e0-171">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e34e0-172">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="e34e0-172">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e34e0-173">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="e34e0-173">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e34e0-174">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="e34e0-174">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e34e0-175">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="e34e0-175">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e34e0-176">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="e34e0-176">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e34e0-177">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="e34e0-177">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e34e0-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> se volá v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e34e0-179">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="e34e0-179">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e34e0-180">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="e34e0-180">Set the allowed origins</span></span>

<span data-ttu-id="e34e0-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem (`http` nebo `https`).</span><span class="sxs-lookup"><span data-stu-id="e34e0-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e34e0-182">`AllowAnyOrigin` je nezabezpečený, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-182">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="e34e0-183">Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může vést k padělání požadavků mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="e34e0-183">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e34e0-184">Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.</span><span class="sxs-lookup"><span data-stu-id="e34e0-184">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="e34e0-185">Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může vést k padělání požadavků mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="e34e0-185">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e34e0-186">V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="e34e0-186">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="e34e0-187">`AllowAnyOrigin` má vliv na požadavky na kontrolu a `Access-Control-Allow-Origin` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="e34e0-187">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e34e0-188">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="e34e0-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e34e0-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; nastaví vlastnost <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> zásady tak, aby byla funkce, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.</span><span class="sxs-lookup"><span data-stu-id="e34e0-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e34e0-190">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="e34e0-190">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e34e0-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e34e0-192">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="e34e0-192">Allows any HTTP method:</span></span>
* <span data-ttu-id="e34e0-193">Má vliv na požadavky na kontrolu a `Access-Control-Allow-Methods` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="e34e0-193">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e34e0-194">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="e34e0-194">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e34e0-195">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="e34e0-195">Set the allowed request headers</span></span>

<span data-ttu-id="e34e0-196">Chcete-li povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="e34e0-196">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e34e0-197">Chcete-li povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-197">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e34e0-198">Toto nastavení má vliv na žádosti o kontrolu a `Access-Control-Request-Headers` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="e34e0-198">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="e34e0-199">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="e34e0-199">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e34e0-200">Zásada middlewaru CORS odpovídá konkrétním hlavičkám určeným `WithHeaders` je možné pouze v případě, že hlavičky odeslané `Access-Control-Request-Headers` přesně odpovídají hlavičkám uvedeným v `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-200">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="e34e0-201">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="e34e0-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e34e0-202">Middleware CORS odmítá požadavek na kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden v `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="e34e0-202">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="e34e0-203">Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="e34e0-203">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e34e0-204">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-204">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e34e0-205">Middleware CORS vždycky povoluje posílání čtyř hlaviček v `Access-Control-Request-Headers` bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="e34e0-205">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="e34e0-206">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="e34e0-206">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="e34e0-207">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="e34e0-207">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e34e0-208">Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` je vždy na seznamu povolených:</span><span class="sxs-lookup"><span data-stu-id="e34e0-208">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e34e0-209">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="e34e0-209">Set the exposed response headers</span></span>

<span data-ttu-id="e34e0-210">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e34e0-210">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e34e0-211">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="e34e0-211">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e34e0-212">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="e34e0-212">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e34e0-213">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="e34e0-213">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e34e0-214">K zpřístupnění dalších hlaviček pro aplikaci volejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-214">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e34e0-215">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="e34e0-215">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e34e0-216">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-216">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e34e0-217">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="e34e0-217">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e34e0-218">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="e34e0-218">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e34e0-219">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí klient nastavit `XMLHttpRequest.withCredentials` `true`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-219">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e34e0-220">Přímé použití `XMLHttpRequest`:</span><span class="sxs-lookup"><span data-stu-id="e34e0-220">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e34e0-221">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="e34e0-221">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e34e0-222">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="e34e0-222">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e34e0-223">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="e34e0-223">The server must allow the credentials.</span></span> <span data-ttu-id="e34e0-224">Pokud chcete povolení přihlašovacích údajů pro více zdrojů, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-224">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="e34e0-225">Odpověď HTTP obsahuje hlavičku `Access-Control-Allow-Credentials`, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-225">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e34e0-226">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou hlavičku `Access-Control-Allow-Credentials`, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="e34e0-226">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e34e0-227">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="e34e0-227">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e34e0-228">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="e34e0-228">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e34e0-229">Specifikace CORS také uvádí, že nastavení původes na `"*"` (všechny zdroje) je neplatné, pokud je k dispozici `Access-Control-Allow-Credentials` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e34e0-229">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="e34e0-230">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="e34e0-230">Preflight requests</span></span>

<span data-ttu-id="e34e0-231">U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek.</span><span class="sxs-lookup"><span data-stu-id="e34e0-231">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="e34e0-232">Tento požadavek se nazývá *žádost o kontrolu před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="e34e0-232">This request is called a *preflight request*.</span></span> <span data-ttu-id="e34e0-233">Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="e34e0-233">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="e34e0-234">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="e34e0-234">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e34e0-235">Aplikace nenastaví záhlaví žádostí kromě `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`nebo `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-235">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e34e0-236">`Content-Type` záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="e34e0-236">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e34e0-237">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada App Sets nastaví voláním `setRequestHeader` na objektu `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-237">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e34e0-238">Specifikace CORS volá *záhlaví požadavku autora*záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e34e0-238">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="e34e0-239">Toto pravidlo se nevztahuje na záhlaví, která může prohlížeč nastavit, například `User-Agent`, `Host`nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-239">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e34e0-240">Následuje příklad žádosti o kontrolu před výstupem:</span><span class="sxs-lookup"><span data-stu-id="e34e0-240">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="e34e0-241">Požadavek na lety používá metodu HTTP.</span><span class="sxs-lookup"><span data-stu-id="e34e0-241">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="e34e0-242">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="e34e0-242">It includes two special headers:</span></span>

* <span data-ttu-id="e34e0-243">`Access-Control-Request-Method`: metoda HTTP, která se bude používat pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e34e0-243">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e34e0-244">`Access-Control-Request-Headers`: seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="e34e0-244">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e34e0-245">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-245">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="e34e0-246">Požadavek předběžné kontroly CORS může zahrnovat hlavičku `Access-Control-Request-Headers`, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="e34e0-246">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="e34e0-247">Pro povolení konkrétních hlaviček volejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-247">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e34e0-248">Chcete-li povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-248">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e34e0-249">Prohlížeče nejsou zcela konzistentní v tom, jak nastavují `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-249">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e34e0-250">Pokud jste nastavili záhlaví na jinou hodnotu než `"*"` (nebo používáte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), měli byste zahrnout alespoň `Accept`, `Content-Type`a `Origin`a také všechna vlastní záhlaví, která chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="e34e0-250">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="e34e0-251">Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):</span><span class="sxs-lookup"><span data-stu-id="e34e0-251">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="e34e0-252">Odpověď obsahuje `Access-Control-Allow-Methods` záhlaví, ve kterém jsou uvedeny povolené metody a volitelně `Access-Control-Allow-Headers` záhlaví, které obsahuje seznam povolených hlaviček.</span><span class="sxs-lookup"><span data-stu-id="e34e0-252">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="e34e0-253">Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e34e0-253">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="e34e0-254">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="e34e0-254">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e34e0-255">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-255">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e34e0-256">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="e34e0-256">Set the preflight expiration time</span></span>

<span data-ttu-id="e34e0-257">Hlavička `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e34e0-257">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e34e0-258">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="e34e0-258">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e34e0-259">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="e34e0-259">How CORS works</span></span>

<span data-ttu-id="e34e0-260">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="e34e0-260">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e34e0-261">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e34e0-261">CORS is **not** a security feature.</span></span> <span data-ttu-id="e34e0-262">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="e34e0-262">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e34e0-263">Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="e34e0-263">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e34e0-264">Vaše rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-264">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="e34e0-265">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="e34e0-265">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e34e0-266">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="e34e0-266">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e34e0-267">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="e34e0-267">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e34e0-268">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e34e0-268">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e34e0-269">Postman</span><span class="sxs-lookup"><span data-stu-id="e34e0-269">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e34e0-270">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="e34e0-270">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e34e0-271">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="e34e0-271">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e34e0-272">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="e34e0-272">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e34e0-273">Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-273">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="e34e0-274">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="e34e0-274">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e34e0-275">JSONP nepoužívá XHR, používá značku `<script>` k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e34e0-275">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e34e0-276">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-276">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e34e0-277">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="e34e0-277">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e34e0-278">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="e34e0-278">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e34e0-279">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e34e0-279">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e34e0-280">Následuje příklad žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="e34e0-280">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="e34e0-281">Hlavička `Origin` poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="e34e0-281">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="e34e0-282">Hlavička `Origin` je povinná a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="e34e0-282">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="e34e0-283">Pokud server tuto žádost povoluje, nastaví v odpovědi hlavičku `Access-Control-Allow-Origin`.</span><span class="sxs-lookup"><span data-stu-id="e34e0-283">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="e34e0-284">Hodnota této hlavičky se buď shoduje s hlavičkou `Origin` z požadavku, nebo se jedná o zástupnou hodnotu `"*"`, což znamená, že je povolený libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="e34e0-284">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="e34e0-285">Pokud odpověď neobsahuje hlavičku `Access-Control-Allow-Origin`, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="e34e0-285">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e34e0-286">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="e34e0-286">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e34e0-287">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e34e0-287">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="e34e0-288">Test CORS</span><span class="sxs-lookup"><span data-stu-id="e34e0-288">Test CORS</span></span>

<span data-ttu-id="e34e0-289">Testování CORS:</span><span class="sxs-lookup"><span data-stu-id="e34e0-289">To test CORS:</span></span>

1. <span data-ttu-id="e34e0-290">[Vytvořte projekt API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="e34e0-290">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="e34e0-291">Alternativně si můžete [Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="e34e0-291">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="e34e0-292">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e34e0-292">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="e34e0-293">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e34e0-293">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="e34e0-294">`WithOrigins("https://localhost:<port>");` by se měla použít jenom pro testování ukázkové aplikace podobné [ukázkovému kódu ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="e34e0-294">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="e34e0-295">Vytvořte projekt webové aplikace (Razor Pages nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="e34e0-295">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="e34e0-296">Ukázka používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e34e0-296">The sample uses Razor Pages.</span></span> <span data-ttu-id="e34e0-297">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e34e0-297">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="e34e0-298">Do souboru *index. cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="e34e0-298">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="e34e0-299">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresou URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="e34e0-299">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="e34e0-300">Nasaďte projekt API.</span><span class="sxs-lookup"><span data-stu-id="e34e0-300">Deploy the API project.</span></span> <span data-ttu-id="e34e0-301">Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="e34e0-301">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="e34e0-302">Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **test** .</span><span class="sxs-lookup"><span data-stu-id="e34e0-302">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="e34e0-303">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="e34e0-303">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="e34e0-304">Odeberte původ localhost z `WithOrigins` a aplikaci nasaďte.</span><span class="sxs-lookup"><span data-stu-id="e34e0-304">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="e34e0-305">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="e34e0-305">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="e34e0-306">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e34e0-306">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="e34e0-307">Otestujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="e34e0-307">Test with the client app.</span></span> <span data-ttu-id="e34e0-308">Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e34e0-308">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="e34e0-309">K zobrazení chyby použijte kartu konzola v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="e34e0-309">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="e34e0-310">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="e34e0-310">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="e34e0-311">Používání Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="e34e0-311">Using Microsoft Edge:</span></span>

     <span data-ttu-id="e34e0-312">**SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů, v `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="e34e0-312">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="e34e0-313">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="e34e0-313">Using Chrome:</span></span>

     <span data-ttu-id="e34e0-314">**Zásada CORS zablokovala přístup k XMLHttpRequest v `https://webapi.azurewebsites.net/api/values/1` od původu `https://localhost:44375`: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**</span><span class="sxs-lookup"><span data-stu-id="e34e0-314">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="e34e0-315">Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="e34e0-315">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e34e0-316">Při použití nástroje se musí původ požadavku určeného hlavičkou `Origin` lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="e34e0-316">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e34e0-317">Pokud požadavek není *mezi zdroji* založen na hodnotě hlavičky `Origin`:</span><span class="sxs-lookup"><span data-stu-id="e34e0-317">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e34e0-318">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-318">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e34e0-319">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="e34e0-319">CORS headers aren't returned in the response.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e34e0-320">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e34e0-320">Additional resources</span></span>

* [<span data-ttu-id="e34e0-321">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="e34e0-321">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
