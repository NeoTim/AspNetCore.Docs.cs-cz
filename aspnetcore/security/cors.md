---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: security/cors
ms.openlocfilehash: 13f22b59a8d066981133508cceccd941e2ec4505
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334185"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="b6dca-103">Povolit žádosti mezi zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6dca-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="b6dca-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b6dca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b6dca-105">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b6dca-106">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="b6dca-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b6dca-107">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="b6dca-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b6dca-108">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="b6dca-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b6dca-109">V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b6dca-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="b6dca-110">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b6dca-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b6dca-111">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b6dca-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b6dca-112">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="b6dca-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b6dca-113">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="b6dca-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b6dca-114">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b6dca-115">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b6dca-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b6dca-116">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="b6dca-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b6dca-117">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="b6dca-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b6dca-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b6dca-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b6dca-119">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="b6dca-119">Same origin</span></span>

<span data-ttu-id="b6dca-120">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b6dca-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b6dca-121">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="b6dca-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b6dca-122">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="b6dca-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b6dca-123">`https://example.net` &ndash; odlišná doména</span><span class="sxs-lookup"><span data-stu-id="b6dca-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="b6dca-124">`https://www.example.com/foo.html` &ndash; odlišná subdoména</span><span class="sxs-lookup"><span data-stu-id="b6dca-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="b6dca-125">`http://example.com/foo.html` &ndash; odlišné schéma</span><span class="sxs-lookup"><span data-stu-id="b6dca-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="b6dca-126">`https://example.com:9000/foo.html` &ndash; jiný port</span><span class="sxs-lookup"><span data-stu-id="b6dca-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="b6dca-127">Internet Explorer při porovnávání zdrojů nebere v úvahu port.</span><span class="sxs-lookup"><span data-stu-id="b6dca-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b6dca-128">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="b6dca-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="b6dca-129">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b6dca-130">Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:</span><span class="sxs-lookup"><span data-stu-id="b6dca-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="b6dca-131">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="b6dca-131">The preceding code:</span></span>

* <span data-ttu-id="b6dca-132">Nastaví název zásady na "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="b6dca-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="b6dca-133">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="b6dca-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b6dca-134">Zavolá metodu rozšíření <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="b6dca-135">Volá <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="b6dca-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b6dca-136">Lambda přebírá objekt @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="b6dca-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b6dca-137">[Možnosti konfigurace](#cors-policy-options), jako je například `WithOrigins`, jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="b6dca-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="b6dca-138">Volání metody <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="b6dca-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b6dca-139">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b6dca-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="b6dca-140">Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> může řetězit metody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="b6dca-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="b6dca-141">Poznámka: adresa URL nesmí **obsahovat koncové** lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="b6dca-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b6dca-142">Pokud adresa URL končí `/`, porovnávání vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b6dca-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6dca-143">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="b6dca-143">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
> <span data-ttu-id="b6dca-144">Při směrování koncových bodů musí být middleware CORS nakonfigurované tak, aby se spouštěla mezi voláními `UseRouting` a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-144">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="b6dca-145">Nesprávná konfigurace způsobí, že middleware přestane fungovat správně.</span><span class="sxs-lookup"><span data-stu-id="b6dca-145">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="b6dca-146">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="b6dca-146">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="b6dca-147">Poznámka: `UseCors` se musí volat před `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-147">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="b6dca-148">Viz [Povolení CORS v Razor Pages, řadičích a metodách akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="b6dca-148">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="b6dca-149">Pokyny k testování předchozího kódu najdete v části [test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="b6dca-149">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="b6dca-150">Povolení CORS s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="b6dca-150">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="b6dca-151">S směrováním koncových bodů je možné CORS povolit pro jednotlivé koncové body pomocí sady `RequireCors` sady rozšiřujících metod.</span><span class="sxs-lookup"><span data-stu-id="b6dca-151">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="b6dca-152">Podobně lze CORS povolit také pro všechny řadiče:</span><span class="sxs-lookup"><span data-stu-id="b6dca-152">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b6dca-153">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="b6dca-153">Enable CORS with attributes</span></span>

<span data-ttu-id="b6dca-154">Atribut [&lbrack;EnableCors @ no__t-2](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) nabízí alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="b6dca-154">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b6dca-155">Atribut `[EnableCors]` povoluje CORS pro vybrané koncové body místo všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b6dca-155">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="b6dca-156">Pomocí `[EnableCors]` Určete výchozí zásady a `[EnableCors("{Policy String}")]` pro zadání zásad.</span><span class="sxs-lookup"><span data-stu-id="b6dca-156">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="b6dca-157">Atribut `[EnableCors]` lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="b6dca-157">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="b6dca-158">@No__t stránky Razor – 0</span><span class="sxs-lookup"><span data-stu-id="b6dca-158">Razor Page `PageModel`</span></span>
* <span data-ttu-id="b6dca-159">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="b6dca-159">Controller</span></span>
* <span data-ttu-id="b6dca-160">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="b6dca-160">Controller action method</span></span>

<span data-ttu-id="b6dca-161">Pomocí atributu `[EnableCors]` můžete použít různé zásady pro Controller/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="b6dca-161">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="b6dca-162">Pokud se atribut `[EnableCors]` aplikuje na Controllers/Page-model/Action Method a v middleware je povolená CORS, aplikují se obě zásady.</span><span class="sxs-lookup"><span data-stu-id="b6dca-162">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="b6dca-163">Doporučujeme před kombinováním zásad.</span><span class="sxs-lookup"><span data-stu-id="b6dca-163">We recommend against combining policies.</span></span> <span data-ttu-id="b6dca-164">Použijte atribut `[EnableCors]` nebo middleware, nikoli oba ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b6dca-164">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="b6dca-165">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="b6dca-165">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b6dca-166">Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="b6dca-166">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="b6dca-167">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="b6dca-167">Disable CORS</span></span>

<span data-ttu-id="b6dca-168">Atribut [&lbrack;DisableCors @ no__t-2](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="b6dca-168">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b6dca-169">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="b6dca-169">CORS policy options</span></span>

<span data-ttu-id="b6dca-170">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="b6dca-170">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b6dca-171">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="b6dca-171">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b6dca-172">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="b6dca-172">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b6dca-173">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="b6dca-173">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b6dca-174">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="b6dca-174">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b6dca-175">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b6dca-175">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b6dca-176">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="b6dca-176">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b6dca-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> se volá v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b6dca-178">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="b6dca-178">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b6dca-179">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="b6dca-179">Set the allowed origins</span></span>

<span data-ttu-id="b6dca-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; povolí CORS požadavky ze všech míst původu s jakýmkoli schématem (`http` nebo `https`).</span><span class="sxs-lookup"><span data-stu-id="b6dca-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b6dca-181">`AllowAnyOrigin` je nezabezpečený, protože *libovolný web* může do aplikace dělat žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b6dca-181">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="b6dca-182">Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="b6dca-182">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b6dca-183">Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.</span><span class="sxs-lookup"><span data-stu-id="b6dca-183">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="b6dca-184">Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="b6dca-184">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b6dca-185">V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="b6dca-185">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="b6dca-186">`AllowAnyOrigin` má vliv na požadavky na kontrolu a hlavičku `Access-Control-Allow-Origin`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-186">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b6dca-187">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b6dca-187">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b6dca-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; nastaví vlastnost <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> zásady tak, aby byla funkce, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.</span><span class="sxs-lookup"><span data-stu-id="b6dca-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b6dca-189">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="b6dca-189">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b6dca-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b6dca-191">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="b6dca-191">Allows any HTTP method:</span></span>
* <span data-ttu-id="b6dca-192">Má vliv na požadavky na předběžné kontroly a hlavičku `Access-Control-Allow-Methods`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-192">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b6dca-193">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b6dca-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b6dca-194">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="b6dca-194">Set the allowed request headers</span></span>

<span data-ttu-id="b6dca-195">Chcete-li povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *Autor žádosti o*vytvoření zprávy, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b6dca-195">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b6dca-196">Pokud chcete, aby se povolily všechny hlavičky požadavků autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-196">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b6dca-197">Toto nastavení má vliv na žádosti o kontrolu a hlavičku `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-197">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="b6dca-198">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b6dca-198">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b6dca-199">Zásada middlewaru CORS odpovídá konkrétním hlavičkám určeným parametrem `WithHeaders` je možné pouze v případě, že hlavičky odeslané v `Access-Control-Request-Headers` přesně odpovídají hlavičkám uvedeným v `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-199">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="b6dca-200">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="b6dca-200">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="b6dca-201">Middleware CORS odmítá požadavek na kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden v `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="b6dca-201">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="b6dca-202">Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="b6dca-202">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b6dca-203">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-203">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b6dca-204">Middleware CORS vždycky povoluje posílání čtyř hlaviček v `Access-Control-Request-Headers` bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="b6dca-204">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="b6dca-205">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b6dca-205">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="b6dca-206">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="b6dca-206">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="b6dca-207">Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` je vždy na seznamu povolených:</span><span class="sxs-lookup"><span data-stu-id="b6dca-207">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b6dca-208">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="b6dca-208">Set the exposed response headers</span></span>

<span data-ttu-id="b6dca-209">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6dca-209">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b6dca-210">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b6dca-210">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b6dca-211">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="b6dca-211">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b6dca-212">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="b6dca-212">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="b6dca-213">Pokud chcete, aby byla aplikace k dispozici pro další hlavičky, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-213">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b6dca-214">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b6dca-214">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b6dca-215">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-215">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b6dca-216">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b6dca-216">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b6dca-217">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="b6dca-217">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="b6dca-218">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí klient nastavit `XMLHttpRequest.withCredentials` na `true`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-218">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b6dca-219">Přímé použití `XMLHttpRequest`:</span><span class="sxs-lookup"><span data-stu-id="b6dca-219">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b6dca-220">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="b6dca-220">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b6dca-221">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="b6dca-221">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b6dca-222">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="b6dca-222">The server must allow the credentials.</span></span> <span data-ttu-id="b6dca-223">Pokud chcete povolení přihlašovacích údajů pro více zdrojů, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-223">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="b6dca-224">Odpověď HTTP obsahuje hlavičku `Access-Control-Allow-Credentials`, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-224">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b6dca-225">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou hlavičku `Access-Control-Allow-Credentials`, prohlížeč nezveřejňuje odpověď na aplikaci a požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="b6dca-225">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b6dca-226">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="b6dca-226">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b6dca-227">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="b6dca-227">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b6dca-228">Specifikace CORS také uvádí, že nastavení původes na `"*"` (všechny zdroje) je neplatné, pokud je k dispozici hlavička `Access-Control-Allow-Credentials`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-228">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="b6dca-229">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="b6dca-229">Preflight requests</span></span>

<span data-ttu-id="b6dca-230">U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek.</span><span class="sxs-lookup"><span data-stu-id="b6dca-230">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="b6dca-231">Tento požadavek se nazývá *žádost o kontrolu před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="b6dca-231">This request is called a *preflight request*.</span></span> <span data-ttu-id="b6dca-232">Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="b6dca-232">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="b6dca-233">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="b6dca-233">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b6dca-234">Aplikace nenastaví záhlaví žádostí kromě `Accept`, `Accept-Language`, `Content-Language`, `Content-Type` nebo `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-234">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b6dca-235">Hlavička `Content-Type`, pokud je nastavena, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="b6dca-235">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b6dca-236">Pravidlo pro hlavičky žádosti nastavené pro požadavek klienta se vztahuje na hlavičky, které sada App Sets nastaví voláním `setRequestHeader` u objektu `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-236">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b6dca-237">Specifikace CORS volá *záhlaví požadavku autora*záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b6dca-237">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="b6dca-238">Pravidlo se nevztahuje na záhlaví, která může prohlížeč nastavit, například `User-Agent`, `Host` nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-238">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b6dca-239">Následuje příklad žádosti o kontrolu před výstupem:</span><span class="sxs-lookup"><span data-stu-id="b6dca-239">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="b6dca-240">Požadavek na lety používá metodu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b6dca-240">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="b6dca-241">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b6dca-241">It includes two special headers:</span></span>

* <span data-ttu-id="b6dca-242">`Access-Control-Request-Method`: metoda HTTP, která se bude používat pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="b6dca-242">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b6dca-243">`Access-Control-Request-Headers`: seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="b6dca-243">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b6dca-244">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-244">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="b6dca-245">Požadavek předběžné kontroly CORS může zahrnovat hlavičku `Access-Control-Request-Headers`, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="b6dca-245">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="b6dca-246">Pro povolení konkrétních hlaviček volejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-246">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b6dca-247">Pokud chcete, aby se povolily všechny hlavičky požadavků autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-247">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b6dca-248">Prohlížeče nejsou zcela konzistentní v tom, jak nastavují `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-248">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b6dca-249">Pokud nastavíte záhlaví na jinou hodnotu než `"*"` (nebo použít <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), měli byste zahrnout alespoň `Accept`, `Content-Type` a `Origin` a také libovolná vlastní záhlaví, která chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="b6dca-249">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="b6dca-250">Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):</span><span class="sxs-lookup"><span data-stu-id="b6dca-250">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="b6dca-251">Odpověď obsahuje hlavičku `Access-Control-Allow-Methods`, která obsahuje seznam povolených metod a volitelně záhlaví `Access-Control-Allow-Headers`, které obsahuje seznam povolených hlaviček.</span><span class="sxs-lookup"><span data-stu-id="b6dca-251">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="b6dca-252">Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="b6dca-252">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="b6dca-253">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="b6dca-253">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b6dca-254">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-254">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b6dca-255">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="b6dca-255">Set the preflight expiration time</span></span>

<span data-ttu-id="b6dca-256">Hlavička `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b6dca-256">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b6dca-257">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="b6dca-257">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b6dca-258">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="b6dca-258">How CORS works</span></span>

<span data-ttu-id="b6dca-259">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="b6dca-259">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b6dca-260">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b6dca-260">CORS is **not** a security feature.</span></span> <span data-ttu-id="b6dca-261">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="b6dca-261">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b6dca-262">Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="b6dca-262">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b6dca-263">Vaše rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-263">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="b6dca-264">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="b6dca-264">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b6dca-265">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="b6dca-265">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b6dca-266">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="b6dca-266">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b6dca-267">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b6dca-267">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b6dca-268">Postman</span><span class="sxs-lookup"><span data-stu-id="b6dca-268">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b6dca-269">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="b6dca-269">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b6dca-270">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="b6dca-270">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b6dca-271">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="b6dca-271">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b6dca-272">Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-272">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="b6dca-273">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="b6dca-273">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b6dca-274">JSONP nepoužívá XHR, používá značku `<script>` k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b6dca-274">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b6dca-275">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-275">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b6dca-276">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b6dca-276">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b6dca-277">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="b6dca-277">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b6dca-278">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b6dca-278">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b6dca-279">Následuje příklad žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b6dca-279">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="b6dca-280">Hlavička `Origin` poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="b6dca-280">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="b6dca-281">Hlavička `Origin` je povinná a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="b6dca-281">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="b6dca-282">Pokud server tuto žádost povoluje, nastaví v odpovědi hlavičku `Access-Control-Allow-Origin`.</span><span class="sxs-lookup"><span data-stu-id="b6dca-282">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="b6dca-283">Hodnota této hlavičky buď odpovídá hlavičce `Origin` z požadavku, nebo se jedná o zástupnou hodnotu `"*"`, což znamená, že je povolen libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="b6dca-283">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="b6dca-284">Pokud odpověď neobsahuje hlavičku `Access-Control-Allow-Origin`, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="b6dca-284">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b6dca-285">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="b6dca-285">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b6dca-286">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b6dca-286">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="b6dca-287">Test CORS</span><span class="sxs-lookup"><span data-stu-id="b6dca-287">Test CORS</span></span>

<span data-ttu-id="b6dca-288">Testování CORS:</span><span class="sxs-lookup"><span data-stu-id="b6dca-288">To test CORS:</span></span>

1. <span data-ttu-id="b6dca-289">[Vytvořte projekt API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="b6dca-289">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="b6dca-290">Alternativně si můžete [Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b6dca-290">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="b6dca-291">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b6dca-291">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="b6dca-292">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b6dca-292">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="b6dca-293">`WithOrigins("https://localhost:<port>");` by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b6dca-293">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="b6dca-294">Vytvořte projekt webové aplikace (Razor Pages nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="b6dca-294">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="b6dca-295">Ukázka používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b6dca-295">The sample uses Razor Pages.</span></span> <span data-ttu-id="b6dca-296">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b6dca-296">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="b6dca-297">Do souboru *index. cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="b6dca-297">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="b6dca-298">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresou URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6dca-298">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="b6dca-299">Nasaďte projekt API.</span><span class="sxs-lookup"><span data-stu-id="b6dca-299">Deploy the API project.</span></span> <span data-ttu-id="b6dca-300">Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="b6dca-300">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="b6dca-301">Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **test** .</span><span class="sxs-lookup"><span data-stu-id="b6dca-301">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="b6dca-302">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="b6dca-302">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="b6dca-303">Odeberte původ localhost z `WithOrigins` a aplikaci nasaďte.</span><span class="sxs-lookup"><span data-stu-id="b6dca-303">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="b6dca-304">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="b6dca-304">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="b6dca-305">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b6dca-305">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="b6dca-306">Otestujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="b6dca-306">Test with the client app.</span></span> <span data-ttu-id="b6dca-307">Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b6dca-307">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="b6dca-308">K zobrazení chyby použijte kartu konzola v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="b6dca-308">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="b6dca-309">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="b6dca-309">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="b6dca-310">Používání Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="b6dca-310">Using Microsoft Edge:</span></span>

     <span data-ttu-id="b6dca-311">**SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů v `https://webapi.azurewebsites.net/api/values/1`.**</span><span class="sxs-lookup"><span data-stu-id="b6dca-311">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="b6dca-312">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="b6dca-312">Using Chrome:</span></span>

     <span data-ttu-id="b6dca-313">**Přístup k XMLHttpRequest na adrese `https://webapi.azurewebsites.net/api/values/1` od počátku `https://localhost:44375` byl zablokován zásadami CORS: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**</span><span class="sxs-lookup"><span data-stu-id="b6dca-313">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="b6dca-314">Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="b6dca-314">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b6dca-315">Při použití nástroje se musí původ požadavku určeného hlavičkou `Origin` lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="b6dca-315">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b6dca-316">Pokud požadavek není *mezi zdroji* na základě hodnoty v hlavičce `Origin`:</span><span class="sxs-lookup"><span data-stu-id="b6dca-316">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b6dca-317">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-317">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b6dca-318">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="b6dca-318">CORS headers aren't returned in the response.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b6dca-319">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b6dca-319">Additional resources</span></span>

* [<span data-ttu-id="b6dca-320">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="b6dca-320">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
