---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a02b3497684979c1a9e792437f9f1a4c467600f0
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187251"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="762c7-103">Povolit žádosti mezi zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="762c7-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="762c7-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="762c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="762c7-105">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="762c7-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="762c7-106">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="762c7-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="762c7-107">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="762c7-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="762c7-108">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="762c7-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="762c7-109">V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="762c7-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="762c7-110">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="762c7-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="762c7-111">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="762c7-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="762c7-112">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="762c7-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="762c7-113">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="762c7-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="762c7-114">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="762c7-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="762c7-115">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="762c7-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="762c7-116">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="762c7-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="762c7-117">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="762c7-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="762c7-118">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="762c7-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="762c7-119">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="762c7-119">Same origin</span></span>

<span data-ttu-id="762c7-120">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="762c7-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="762c7-121">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="762c7-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="762c7-122">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="762c7-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="762c7-123">`https://example.net`&ndash; Jiná doména</span><span class="sxs-lookup"><span data-stu-id="762c7-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="762c7-124">`https://www.example.com/foo.html`&ndash; Jiná subdoména</span><span class="sxs-lookup"><span data-stu-id="762c7-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="762c7-125">`http://example.com/foo.html`&ndash; Jiné schéma</span><span class="sxs-lookup"><span data-stu-id="762c7-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="762c7-126">`https://example.com:9000/foo.html`&ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="762c7-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="762c7-127">Internet Explorer při porovnávání zdrojů nebere v úvahu port.</span><span class="sxs-lookup"><span data-stu-id="762c7-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="762c7-128">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="762c7-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="762c7-129">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="762c7-130">Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:</span><span class="sxs-lookup"><span data-stu-id="762c7-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="762c7-131">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="762c7-131">The preceding code:</span></span>

* <span data-ttu-id="762c7-132">Nastaví název zásady na "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="762c7-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="762c7-133">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="762c7-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="762c7-134">Volá metodu <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozšíření, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="762c7-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="762c7-135">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="762c7-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="762c7-136">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="762c7-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="762c7-137">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="762c7-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="762c7-138">Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="762c7-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="762c7-139">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="762c7-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="762c7-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda může řetězit metody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="762c7-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="762c7-141">Poznámka: Adresa URL nesmí **obsahovat koncové** lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="762c7-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="762c7-142">Pokud adresa URL končí `/`, porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="762c7-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="762c7-143">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="762c7-143">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
> <span data-ttu-id="762c7-144">Při směrování koncových bodů musí být middleware CORS nakonfigurované tak, aby se `UseRouting` spustilo mezi voláními a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="762c7-144">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="762c7-145">Nesprávná konfigurace způsobí, že middleware přestane fungovat správně.</span><span class="sxs-lookup"><span data-stu-id="762c7-145">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="762c7-146">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="762c7-146">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="762c7-147">Poznámka: `UseCors` musí být volána před `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="762c7-147">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="762c7-148">Viz [Povolení CORS v Razor Pages, řadičích a metodách akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="762c7-148">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="762c7-149">Pokyny k testování předchozího kódu najdete v části [test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="762c7-149">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="762c7-150">Povolení CORS s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="762c7-150">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="762c7-151">S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů `RequireCors` pomocí sady rozšiřujících metod.</span><span class="sxs-lookup"><span data-stu-id="762c7-151">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="762c7-152">Podobně lze CORS povolit také pro všechny řadiče:</span><span class="sxs-lookup"><span data-stu-id="762c7-152">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="762c7-153">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="762c7-153">Enable CORS with attributes</span></span>

<span data-ttu-id="762c7-154">Atribut [EnableCors&rbrack;představuje alternativu k použití CORS globálně. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="762c7-154">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="762c7-155">`[EnableCors]` Atribut povoluje CORS pro vybrané koncové body místo všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="762c7-155">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="762c7-156">Slouží `[EnableCors]` k zadání výchozích zásad a `[EnableCors("{Policy String}")]` k určení zásad.</span><span class="sxs-lookup"><span data-stu-id="762c7-156">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="762c7-157">`[EnableCors]` Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="762c7-157">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="762c7-158">Stránka Razor`PageModel`</span><span class="sxs-lookup"><span data-stu-id="762c7-158">Razor Page `PageModel`</span></span>
* <span data-ttu-id="762c7-159">kontrolér</span><span class="sxs-lookup"><span data-stu-id="762c7-159">Controller</span></span>
* <span data-ttu-id="762c7-160">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="762c7-160">Controller action method</span></span>

<span data-ttu-id="762c7-161">S `[EnableCors]` atributem lze použít různé zásady pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="762c7-161">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="762c7-162">Pokud je `[EnableCors]` atribut použit na řadičích, na úrovni stránky nebo na metodu a akci a v middlewaru je povoleno CORS, jsou obě zásady aplikovány.</span><span class="sxs-lookup"><span data-stu-id="762c7-162">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="762c7-163">Doporučujeme před kombinováním zásad.</span><span class="sxs-lookup"><span data-stu-id="762c7-163">We recommend against combining policies.</span></span> <span data-ttu-id="762c7-164">`[EnableCors]` Použijte atribut nebo middleware, nikoli oba ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="762c7-164">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="762c7-165">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="762c7-165">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="762c7-166">Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="762c7-166">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="762c7-167">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="762c7-167">Disable CORS</span></span>

<span data-ttu-id="762c7-168">Atribut [DisableCors&rbrack;zakáže CORS pro kontroler/Page-model/Action. &lbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="762c7-168">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="762c7-169">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="762c7-169">CORS policy options</span></span>

<span data-ttu-id="762c7-170">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="762c7-170">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="762c7-171">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="762c7-171">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="762c7-172">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="762c7-172">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="762c7-173">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="762c7-173">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="762c7-174">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="762c7-174">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="762c7-175">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="762c7-175">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="762c7-176">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="762c7-176">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="762c7-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="762c7-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="762c7-178">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="762c7-178">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="762c7-179">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="762c7-179">Set the allowed origins</span></span>

<span data-ttu-id="762c7-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem`http` ( `https`nebo). &ndash;</span><span class="sxs-lookup"><span data-stu-id="762c7-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="762c7-181">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-181">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="762c7-182">Určení `AllowAnyOrigin` a`AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="762c7-182">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="762c7-183">Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.</span><span class="sxs-lookup"><span data-stu-id="762c7-183">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="762c7-184">Určení `AllowAnyOrigin` a`AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="762c7-184">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="762c7-185">V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="762c7-185">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="762c7-186">`AllowAnyOrigin`má vliv na požadavky na `Access-Control-Allow-Origin` kontrolu a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="762c7-186">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="762c7-187">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="762c7-187">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="762c7-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví vlastnostzásadynafunkci,kteráumožňuje,abysepřivyhodnocování,jestlijepůvodpovolený,shodovalyskonfigurovanoudoménouse<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> zástupnými znaky.</span><span class="sxs-lookup"><span data-stu-id="762c7-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="762c7-189">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="762c7-189">Set the allowed HTTP methods</span></span>

<span data-ttu-id="762c7-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="762c7-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="762c7-191">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="762c7-191">Allows any HTTP method:</span></span>
* <span data-ttu-id="762c7-192">Má vliv na požadavky na `Access-Control-Allow-Methods` kontrolu a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="762c7-192">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="762c7-193">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="762c7-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="762c7-194">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="762c7-194">Set the allowed request headers</span></span>

<span data-ttu-id="762c7-195">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="762c7-195">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="762c7-196">Pokud chcete povolení všech hlaviček žádostí o autora <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="762c7-196">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="762c7-197">Toto nastavení má vliv na `Access-Control-Request-Headers` žádosti o předběžné kontroly a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="762c7-197">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="762c7-198">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="762c7-198">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="762c7-199">Zásada middlewaru CORS odpovídající konkrétním hlavičkám, které `WithHeaders` určuje, je možná jenom v případě, `Access-Control-Request-Headers` že se záhlaví poslala přesně `WithHeaders`a odpovídají hlavičkám uvedeným v.</span><span class="sxs-lookup"><span data-stu-id="762c7-199">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="762c7-200">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="762c7-200">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="762c7-201">Middleware CORS odmítá požadavek na předběžné kontroly s následující hlavičkou `Content-Language` požadavku, protože ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) `WithHeaders`není uvedená v tomto seznamu:</span><span class="sxs-lookup"><span data-stu-id="762c7-201">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="762c7-202">Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="762c7-202">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="762c7-203">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-203">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="762c7-204">Middleware CORS vždycky povoluje posílání čtyř `Access-Control-Request-Headers` hlaviček v, bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="762c7-204">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="762c7-205">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="762c7-205">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="762c7-206">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="762c7-206">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="762c7-207">Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující `Content-Language` hlavičkou požadavku, protože je vždy na seznamu povolených:</span><span class="sxs-lookup"><span data-stu-id="762c7-207">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="762c7-208">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="762c7-208">Set the exposed response headers</span></span>

<span data-ttu-id="762c7-209">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="762c7-209">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="762c7-210">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): Jednoduchá hlavička](https://www.w3.org/TR/cors/#simple-response-header)odpovědi</span><span class="sxs-lookup"><span data-stu-id="762c7-210">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="762c7-211">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="762c7-211">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="762c7-212">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="762c7-212">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="762c7-213">Pokud chcete, aby byla aplikace k dispozici ostatním <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hlavičkám, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="762c7-213">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="762c7-214">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="762c7-214">Credentials in cross-origin requests</span></span>

<span data-ttu-id="762c7-215">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="762c7-215">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="762c7-216">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="762c7-216">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="762c7-217">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="762c7-217">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="762c7-218">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být `XMLHttpRequest.withCredentials` klient `true`nastaven na.</span><span class="sxs-lookup"><span data-stu-id="762c7-218">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="762c7-219">Přímé `XMLHttpRequest` použití:</span><span class="sxs-lookup"><span data-stu-id="762c7-219">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="762c7-220">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="762c7-220">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="762c7-221">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="762c7-221">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="762c7-222">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="762c7-222">The server must allow the credentials.</span></span> <span data-ttu-id="762c7-223">Pokud chcete povolení přihlašovacích údajů mezi zdroji <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="762c7-223">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="762c7-224">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-224">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="762c7-225">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="762c7-225">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="762c7-226">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="762c7-226">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="762c7-227">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="762c7-227">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="762c7-228">Specifikace CORS také uvádí, že `"*"` `Access-Control-Allow-Credentials` Pokud je hlavička k dispozici, nastavení původu na (všechny zdroje) je neplatné.</span><span class="sxs-lookup"><span data-stu-id="762c7-228">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="762c7-229">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="762c7-229">Preflight requests</span></span>

<span data-ttu-id="762c7-230">U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek.</span><span class="sxs-lookup"><span data-stu-id="762c7-230">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="762c7-231">Tento požadavek se nazývá *žádost o kontrolu před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="762c7-231">This request is called a *preflight request*.</span></span> <span data-ttu-id="762c7-232">Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="762c7-232">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="762c7-233">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="762c7-233">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="762c7-234">Aplikace nenastaví záhlaví `Accept`žádostí s výjimkou, `Accept-Language`, `Content-Language` `Content-Type`, nebo `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="762c7-234">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="762c7-235">`Content-Type` Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="762c7-235">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="762c7-236">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` `XMLHttpRequest` na objekt.</span><span class="sxs-lookup"><span data-stu-id="762c7-236">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="762c7-237">Specifikace CORS volá *záhlaví požadavku autora*záhlaví.</span><span class="sxs-lookup"><span data-stu-id="762c7-237">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="762c7-238">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent`, `Host`nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="762c7-238">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="762c7-239">Následuje příklad žádosti o kontrolu před výstupem:</span><span class="sxs-lookup"><span data-stu-id="762c7-239">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="762c7-240">Požadavek na lety používá metodu HTTP.</span><span class="sxs-lookup"><span data-stu-id="762c7-240">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="762c7-241">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="762c7-241">It includes two special headers:</span></span>

* <span data-ttu-id="762c7-242">`Access-Control-Request-Method`: Metoda HTTP, která se bude používat pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="762c7-242">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="762c7-243">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="762c7-243">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="762c7-244">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="762c7-244">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="762c7-245">Požadavek předběžné kontroly CORS může zahrnovat `Access-Control-Request-Headers` hlavičku, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="762c7-245">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="762c7-246">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="762c7-246">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="762c7-247">Pokud chcete povolení všech hlaviček žádostí o autora <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="762c7-247">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="762c7-248">Prohlížeče nejsou zcela konzistentní v tom, jak `Access-Control-Request-Headers`nastavily.</span><span class="sxs-lookup"><span data-stu-id="762c7-248">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="762c7-249">Pokud nastavíte záhlaví na jinou hodnotu než `"*"` (nebo použít <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), měli byste zahrnout aspoň `Accept`, `Content-Type`a a `Origin`také libovolné vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="762c7-249">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="762c7-250">Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):</span><span class="sxs-lookup"><span data-stu-id="762c7-250">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="762c7-251">Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která obsahuje seznam povolených metod a `Access-Control-Allow-Headers` volitelně záhlaví, ve kterém jsou uvedeny povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="762c7-251">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="762c7-252">Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="762c7-252">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="762c7-253">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="762c7-253">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="762c7-254">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-254">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="762c7-255">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="762c7-255">Set the preflight expiration time</span></span>

<span data-ttu-id="762c7-256">Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="762c7-256">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="762c7-257">Chcete-li nastavit tuto hlavičku <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="762c7-257">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="762c7-258">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="762c7-258">How CORS works</span></span>

<span data-ttu-id="762c7-259">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="762c7-259">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="762c7-260">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="762c7-260">CORS is **not** a security feature.</span></span> <span data-ttu-id="762c7-261">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="762c7-261">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="762c7-262">Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="762c7-262">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="762c7-263">Vaše rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="762c7-263">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="762c7-264">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="762c7-264">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="762c7-265">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="762c7-265">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="762c7-266">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="762c7-266">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="762c7-267">Fiddler</span><span class="sxs-lookup"><span data-stu-id="762c7-267">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="762c7-268">Postman</span><span class="sxs-lookup"><span data-stu-id="762c7-268">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="762c7-269">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="762c7-269">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="762c7-270">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="762c7-270">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="762c7-271">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="762c7-271">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="762c7-272">Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-272">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="762c7-273">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="762c7-273">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="762c7-274">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="762c7-274">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="762c7-275">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-275">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="762c7-276">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="762c7-276">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="762c7-277">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="762c7-277">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="762c7-278">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="762c7-278">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="762c7-279">Následuje příklad žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="762c7-279">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="762c7-280">`Origin` Hlavička poskytuje doménu webu, který vytváří požadavek:</span><span class="sxs-lookup"><span data-stu-id="762c7-280">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="762c7-281">Pokud server tuto žádost povoluje, nastaví `Access-Control-Allow-Origin` hlavičku v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="762c7-281">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="762c7-282">Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku nebo se jedná o zástupnou hodnotu `"*"`, což znamená, že je povolen libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="762c7-282">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="762c7-283">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="762c7-283">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="762c7-284">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="762c7-284">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="762c7-285">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="762c7-285">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="762c7-286">Test CORS</span><span class="sxs-lookup"><span data-stu-id="762c7-286">Test CORS</span></span>

<span data-ttu-id="762c7-287">Testování CORS:</span><span class="sxs-lookup"><span data-stu-id="762c7-287">To test CORS:</span></span>

1. <span data-ttu-id="762c7-288">[Vytvořte projekt API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="762c7-288">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="762c7-289">Alternativně si můžete [Stáhnout ukázku](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="762c7-289">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="762c7-290">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="762c7-290">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="762c7-291">Příklad:</span><span class="sxs-lookup"><span data-stu-id="762c7-291">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="762c7-292">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="762c7-292">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="762c7-293">Vytvořte projekt webové aplikace (Razor Pages nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="762c7-293">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="762c7-294">Ukázka používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="762c7-294">The sample uses Razor Pages.</span></span> <span data-ttu-id="762c7-295">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="762c7-295">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="762c7-296">Do souboru *index. cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="762c7-296">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="762c7-297">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresu URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="762c7-297">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="762c7-298">Nasaďte projekt API.</span><span class="sxs-lookup"><span data-stu-id="762c7-298">Deploy the API project.</span></span> <span data-ttu-id="762c7-299">Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="762c7-299">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="762c7-300">Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **test** .</span><span class="sxs-lookup"><span data-stu-id="762c7-300">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="762c7-301">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="762c7-301">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="762c7-302">Odeberte původ localhost z `WithOrigins` a nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="762c7-302">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="762c7-303">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="762c7-303">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="762c7-304">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="762c7-304">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="762c7-305">Otestujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="762c7-305">Test with the client app.</span></span> <span data-ttu-id="762c7-306">Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="762c7-306">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="762c7-307">K zobrazení chyby použijte kartu konzola v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="762c7-307">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="762c7-308">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="762c7-308">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="762c7-309">Používání Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="762c7-309">Using Microsoft Edge:</span></span>

     <span data-ttu-id="762c7-310">**SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="762c7-310">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="762c7-311">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="762c7-311">Using Chrome:</span></span>

     <span data-ttu-id="762c7-312">**Zásada CORS zablokovala přístup `https://localhost:44375` k XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` od původu: U požadovaného prostředku není k dispozici hlavička Access-Control-Allow-Origin.**</span><span class="sxs-lookup"><span data-stu-id="762c7-312">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="762c7-313">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="762c7-313">Additional resources</span></span>

* [<span data-ttu-id="762c7-314">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="762c7-314">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
