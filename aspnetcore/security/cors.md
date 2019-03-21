---
title: Povolení žádostí napříč zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak CORS jako standard pro povolení nebo odmítnutí žádostí nepůvodního v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/27/2019
uid: security/cors
ms.openlocfilehash: 2cad26d0f61519f63888a2bc399bb7e8a0f1ee04
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210129"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="995d1-103">Povolení žádostí napříč zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="995d1-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="995d1-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="995d1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="995d1-105">Tento článek popisuje postup povolení CORS v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="995d1-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="995d1-106">Zabezpečení prohlížečů brání zasílání požadavků na jiné doméně než ten, který obsluhuje webovou stránku na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="995d1-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="995d1-107">Toto omezení je volána *zásada stejného zdroje*.</span><span class="sxs-lookup"><span data-stu-id="995d1-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="995d1-108">Zásada stejného zdroje brání škodlivým webům ve čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="995d1-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="995d1-109">V některých případech můžete chtít povolit, že ostatní lokality provádět požadavky cross-origin do vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="995d1-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="995d1-110">Další informace najdete v tématu [Mozilla CORS článku](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="995d1-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="995d1-111">[Pro různé zdroje sdílení prostředků](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="995d1-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="995d1-112">Je standard, která umožňuje server zmírnit zásadu stejného zdroje W3C.</span><span class="sxs-lookup"><span data-stu-id="995d1-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="995d1-113">Je **není** funkce zabezpečení, CORS zmírňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="995d1-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="995d1-114">Rozhraní API není bezpečnější povolením CORS.</span><span class="sxs-lookup"><span data-stu-id="995d1-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="995d1-115">Další informace najdete v tématu [funguje jak CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="995d1-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="995d1-116">Umožňuje serveru tak, aby výslovně povolit některé požadavky cross-origin, zatímco jiné odmítnout.</span><span class="sxs-lookup"><span data-stu-id="995d1-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="995d1-117">Je bezpečnější a flexibilnější, než starší techniky, jako například [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="995d1-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="995d1-118">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="995d1-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="995d1-119">Stejného původu</span><span class="sxs-lookup"><span data-stu-id="995d1-119">Same origin</span></span>

<span data-ttu-id="995d1-120">Pokud mají stejné schémata, hostitele a porty mít dvě adresy URL stejného původu ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="995d1-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="995d1-121">Tyto dvě adresy URL mají stejného původu:</span><span class="sxs-lookup"><span data-stu-id="995d1-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="995d1-122">Tyto adresy URL mají různé zdroje než předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="995d1-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="995d1-123">`https://example.net` &ndash; Jiné domény</span><span class="sxs-lookup"><span data-stu-id="995d1-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="995d1-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span><span class="sxs-lookup"><span data-stu-id="995d1-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="995d1-125">`http://example.com/foo.html` &ndash; Jiné schéma</span><span class="sxs-lookup"><span data-stu-id="995d1-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="995d1-126">`https://example.com:9000/foo.html` &ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="995d1-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="995d1-127">Aplikace Internet Explorer nezahrne port při porovnání zdrojů.</span><span class="sxs-lookup"><span data-stu-id="995d1-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="995d1-128">S s názvem zásady a middlewarem CORS</span><span class="sxs-lookup"><span data-stu-id="995d1-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="995d1-129">CORS Middleware zpracovává požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="995d1-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="995d1-130">Následující kód umožňuje použití CORS pro celou aplikaci se zadaném umístění:</span><span class="sxs-lookup"><span data-stu-id="995d1-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="995d1-131">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="995d1-131">The preceding code:</span></span>

* <span data-ttu-id="995d1-132">Nastaví název zásady "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="995d1-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="995d1-133">Název zásad je volitelný.</span><span class="sxs-lookup"><span data-stu-id="995d1-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="995d1-134">Volání <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozšiřující metoda, která umožňuje jader.</span><span class="sxs-lookup"><span data-stu-id="995d1-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables cores.</span></span>
* <span data-ttu-id="995d1-135">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výraz lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="995d1-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="995d1-136">Používá výraz lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objektu.</span><span class="sxs-lookup"><span data-stu-id="995d1-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="995d1-137">[Možnosti konfigurace](#cors-policy-options), jako například `WithOrigins`, jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="995d1-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="995d1-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Volání metody přidá CORS služby do aplikace služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="995d1-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="995d1-139">Další informace najdete v tématu [možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="995d1-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="995d1-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda můžete zřetězit metod, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="995d1-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="995d1-141">Následující zvýrazněný kód platí zásady CORS pro všechny koncové body pro aplikace přes CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="995d1-141">The following highlighted code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>

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

<span data-ttu-id="995d1-142">Zobrazit [povolení CORS v stránky Razor, kontrolerů a metod akcí](#ecors) použít zásady CORS na úrovni stránky nebo kontroler nebo akce.</span><span class="sxs-lookup"><span data-stu-id="995d1-142">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="995d1-143">Poznámka:</span><span class="sxs-lookup"><span data-stu-id="995d1-143">Note:</span></span>

* <span data-ttu-id="995d1-144">`UseCors` musí být volána před `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="995d1-144">`UseCors` must be called before `UseMvc`.</span></span>
* <span data-ttu-id="995d1-145">Adresa URL musí **není** obsahovat koncové lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="995d1-145">The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="995d1-146">Pokud adresa URL se ukončí s `/`, porovnání vrátí `false` a vrátí se bez záhlaví.</span><span class="sxs-lookup"><span data-stu-id="995d1-146">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="995d1-147">Zobrazit [Test CORS](#test) pokyny k testování předchozí kód.</span><span class="sxs-lookup"><span data-stu-id="995d1-147">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="995d1-148">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="995d1-148">Enable CORS with attributes</span></span>

<span data-ttu-id="995d1-149">[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="995d1-149">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="995d1-150">`[EnableCors]` Atribut umožňuje použití CORS pro vybrané koncové body, nikoli všechny koncové body.</span><span class="sxs-lookup"><span data-stu-id="995d1-150">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="995d1-151">Použití `[EnableCors]` určit výchozí zásady a `[EnableCors("{Policy String}")]` zadat zásady.</span><span class="sxs-lookup"><span data-stu-id="995d1-151">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="995d1-152">`[EnableCors]` Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="995d1-152">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="995d1-153">Stránka Razor `PageModel`</span><span class="sxs-lookup"><span data-stu-id="995d1-153">Razor Page `PageModel`</span></span>
* <span data-ttu-id="995d1-154">Kontroler</span><span class="sxs-lookup"><span data-stu-id="995d1-154">Controller</span></span>
* <span data-ttu-id="995d1-155">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="995d1-155">Controller action method</span></span>

<span data-ttu-id="995d1-156">Můžete použít různé zásady/stránky modelu/akce kontroleru se `[EnableCors]` atribut.</span><span class="sxs-lookup"><span data-stu-id="995d1-156">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="995d1-157">Když `[EnableCors]` atribut je použít pro metodu řadiče/akce/stránky – model a CORS je povolený v middlewaru, obě zásady se použijí.</span><span class="sxs-lookup"><span data-stu-id="995d1-157">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="995d1-158">Nedoporučujeme kombinování zásad.</span><span class="sxs-lookup"><span data-stu-id="995d1-158">We recommend against combining policies.</span></span> <span data-ttu-id="995d1-159">Použití `[EnableCors]` atribut nebo middleware, nikoli oba současně ve stejné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="995d1-159">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="995d1-160">Následující kód platí jiné zásady pro jednotlivé metody:</span><span class="sxs-lookup"><span data-stu-id="995d1-160">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="995d1-161">Následující kód vytvoří výchozí zásady CORS a zásady s názvem `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="995d1-161">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="995d1-162">Zákazu sdílení CORS</span><span class="sxs-lookup"><span data-stu-id="995d1-162">Disable CORS</span></span>

<span data-ttu-id="995d1-163">[ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) atribut zakáže CORS pro/stránky modelu/akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="995d1-163">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="995d1-164">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="995d1-164">CORS policy options</span></span>

<span data-ttu-id="995d1-165">Tato část popisuje různé možnosti, které je možné nastavit v zásadu CORS:</span><span class="sxs-lookup"><span data-stu-id="995d1-165">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="995d1-166">Nastavte povolené zdroje</span><span class="sxs-lookup"><span data-stu-id="995d1-166">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="995d1-167">Nastavte povolené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="995d1-167">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="995d1-168">Nastavit hlavičku povolené žádosti</span><span class="sxs-lookup"><span data-stu-id="995d1-168">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="995d1-169">Nastavit hlavičky vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="995d1-169">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="995d1-170">Přihlašovací údaje v požadavky cross-origin</span><span class="sxs-lookup"><span data-stu-id="995d1-170">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="995d1-171">Nastavit čas vypršení platnosti předběžné</span><span class="sxs-lookup"><span data-stu-id="995d1-171">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="995d1-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> je volána `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="995d1-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="995d1-173">Pro některé možnosti, může být užitečné ke čtení [funguje jak CORS](#how-cors) první části.</span><span class="sxs-lookup"><span data-stu-id="995d1-173">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="995d1-174">Nastavte povolené zdroje</span><span class="sxs-lookup"><span data-stu-id="995d1-174">Set the allowed origins</span></span>

<span data-ttu-id="995d1-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Umožňuje požadavků CORS z všechny původy žádné schéma (`http` nebo `https`).</span><span class="sxs-lookup"><span data-stu-id="995d1-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="995d1-176">`AllowAnyOrigin` není bezpečné a protože *všechny weby* může aplikace provádět požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="995d1-176">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="995d1-177">Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů.</span><span class="sxs-lookup"><span data-stu-id="995d1-177">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="995d1-178">CORS služby vrátí neplatnou odpověď CORS, pokud aplikace je nakonfigurovaná u obou metod.</span><span class="sxs-lookup"><span data-stu-id="995d1-178">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="995d1-179">Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů.</span><span class="sxs-lookup"><span data-stu-id="995d1-179">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="995d1-180">Pro zabezpečené aplikace zadejte přesný seznam původů, pokud klient musí autorizovat samotné pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="995d1-180">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**.
-->

<span data-ttu-id="995d1-181">`AllowAnyOrigin` ovlivňuje časový limit předběžné požadavky a `Access-Control-Allow-Origin` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="995d1-181">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="995d1-182">Další informace najdete v tématu [časový limit předběžné požadavky](#preflight-requests) oddílu.</span><span class="sxs-lookup"><span data-stu-id="995d1-182">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="995d1-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady tak, aby jako funkce, která umožňuje zdrojů tak, aby odpovídaly nakonfigurovaný zástupnou doménu, když vyhodnocuje se, jestli je povolený původ.</span><span class="sxs-lookup"><span data-stu-id="995d1-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="995d1-184">Nastavte povolené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="995d1-184">Set the allowed HTTP methods</span></span>

<span data-ttu-id="995d1-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="995d1-186">Umožňuje libovolné metody HTTP:</span><span class="sxs-lookup"><span data-stu-id="995d1-186">Allows any HTTP method:</span></span>
* <span data-ttu-id="995d1-187">Ovlivňuje časový limit předběžné požadavky a `Access-Control-Allow-Methods` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="995d1-187">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="995d1-188">Další informace najdete v tématu [časový limit předběžné požadavky](#preflight-requests) oddílu.</span><span class="sxs-lookup"><span data-stu-id="995d1-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="995d1-189">Nastavit hlavičku povolené žádosti</span><span class="sxs-lookup"><span data-stu-id="995d1-189">Set the allowed request headers</span></span>

<span data-ttu-id="995d1-190">Volá se, aby konkrétní hlavičky se odešle žádost CORS *vytvářet hlavičky žádosti*, volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadat povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="995d1-190">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="995d1-191">Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-191">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="995d1-192">Toto nastavení má vliv předběžných požadavků a `Access-Control-Request-Headers` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="995d1-192">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="995d1-193">Další informace najdete v tématu [časový limit předběžné požadavky](#preflight-requests) oddílu.</span><span class="sxs-lookup"><span data-stu-id="995d1-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="995d1-194">Shoda se zásadami Middlewarem CORS pro konkrétní hlavičky určené `WithHeaders` je možné, pouze při odeslání hlaviček `Access-Control-Request-Headers` přesně odpovídat záhlaví uvádí `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="995d1-194">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="995d1-195">Zvažte například aplikaci nakonfigurovány takto:</span><span class="sxs-lookup"><span data-stu-id="995d1-195">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="995d1-196">CORS Middleware předběžný požadavek s následující hlavičky žádosti odmítne, protože `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden ve `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="995d1-196">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="995d1-197">Vrátí aplikaci *200 OK* odpověď, ale nebude odesílat hlavičky CORS zpět.</span><span class="sxs-lookup"><span data-stu-id="995d1-197">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="995d1-198">Prohlížeč proto nebude se pokoušet žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-198">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="995d1-199">CORS Middleware vždy povoluje čtyři záhlaví v `Access-Control-Request-Headers` k odeslání bez ohledu na nakonfigurované v CorsPolicy.Headers hodnoty.</span><span class="sxs-lookup"><span data-stu-id="995d1-199">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="995d1-200">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="995d1-200">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="995d1-201">Zvažte například aplikaci nakonfigurovány takto:</span><span class="sxs-lookup"><span data-stu-id="995d1-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="995d1-202">CORS Middleware se úspěšně odpoví na předběžný požadavek s následující hlavičky žádosti protože `Content-Language` je vždy přidat na seznam povolených:</span><span class="sxs-lookup"><span data-stu-id="995d1-202">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="995d1-203">Nastavit hlavičky vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="995d1-203">Set the exposed response headers</span></span>

<span data-ttu-id="995d1-204">Ve výchozím prohlížeči nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="995d1-204">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="995d1-205">Další informace najdete v tématu [W3C napříč sdílení prostředků různého původu (terminologie): Hlavička odpovědi jednoduché](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="995d1-205">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="995d1-206">Hlavičky odpovědi, které jsou k dispozici ve výchozím nastavení jsou:</span><span class="sxs-lookup"><span data-stu-id="995d1-206">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="995d1-207">Specifikace CORS volá tyto hlavičky *hlavičky odpovědi jednoduché*.</span><span class="sxs-lookup"><span data-stu-id="995d1-207">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="995d1-208">Chcete-li zpřístupnit další hlavičky pro aplikace, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-208">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="995d1-209">Přihlašovací údaje v požadavky cross-origin</span><span class="sxs-lookup"><span data-stu-id="995d1-209">Credentials in cross-origin requests</span></span>

<span data-ttu-id="995d1-210">Přihlašovací údaje vyžadují speciální zacházení v požadavku CORS.</span><span class="sxs-lookup"><span data-stu-id="995d1-210">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="995d1-211">Ve výchozím nastavení nebude prohlížeč odesílá pověření s žádostí nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-211">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="995d1-212">Přihlašovací údaje zahrnují soubory cookie a schémat ověřování protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="995d1-212">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="995d1-213">Odesílá pověření s žádostí nepůvodního zdroje, musíte nastavit klienta `XMLHttpRequest.withCredentials` k `true`.</span><span class="sxs-lookup"><span data-stu-id="995d1-213">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="995d1-214">Pomocí `XMLHttpRequest` přímo:</span><span class="sxs-lookup"><span data-stu-id="995d1-214">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="995d1-215">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="995d1-215">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="995d1-216">Použití [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="995d1-216">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="995d1-217">Na serveru, musíte povolit přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="995d1-217">The server must allow the credentials.</span></span> <span data-ttu-id="995d1-218">Chcete-li povolit přihlašovací údaje mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-218">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="995d1-219">Odpověď HTTP, která zahrnuje `Access-Control-Allow-Credentials` hlavičky, která sděluje prohlížeči, že server umožňuje přihlašovací údaje pro žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-219">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="995d1-220">Pokud prohlížeč odesílá pověření, ale odpověď neobsahuje platný `Access-Control-Allow-Credentials` záhlaví, v prohlížeči nezveřejňuje odpovědi do aplikace a nepůvodního požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="995d1-220">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="995d1-221">Povolení nepůvodního pověření je bezpečnostním rizikem.</span><span class="sxs-lookup"><span data-stu-id="995d1-221">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="995d1-222">Web v jiné doméně odeslat do aplikace jménem uživatele bez vědomí uživatele pověření přihlášeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="995d1-222">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="995d1-223">Specifikace CORS také uvádí nastavení počátky k `"*"` (všechny původy) je neplatná-li `Access-Control-Allow-Credentials` záhlaví je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="995d1-223">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="995d1-224">Předběžných požadavků</span><span class="sxs-lookup"><span data-stu-id="995d1-224">Preflight requests</span></span>

<span data-ttu-id="995d1-225">U některých požadavků CORS prohlížeč odešle požadavek další před provedením aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="995d1-225">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="995d1-226">Tento požadavek je volána *předběžný požadavek*.</span><span class="sxs-lookup"><span data-stu-id="995d1-226">This request is called a *preflight request*.</span></span> <span data-ttu-id="995d1-227">Prohlížeči můžete přeskočit předběžný požadavek, pokud jsou splněny následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="995d1-227">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="995d1-228">Metoda žádosti je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="995d1-228">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="995d1-229">Aplikaci nelze nastavit hlavičku žádosti jiných než `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, nebo `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="995d1-229">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="995d1-230">`Content-Type` Záhlaví,-li nastavit, protože má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="995d1-230">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="995d1-231">Sada pravidel na hlavičky požadavku pro požadavek klienta se vztahuje na hlavičky, které aplikace nastaví voláním `setRequestHeader` na `XMLHttpRequest` objektu.</span><span class="sxs-lookup"><span data-stu-id="995d1-231">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="995d1-232">Specifikace CORS volá tyto hlavičky *vytvářet hlavičky požadavku*.</span><span class="sxs-lookup"><span data-stu-id="995d1-232">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="995d1-233">Toto pravidlo neplatí pro záhlaví můžete nastavit v prohlížeči, jako například `User-Agent`, `Host`, nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="995d1-233">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="995d1-234">Následuje příklad předběžný požadavek:</span><span class="sxs-lookup"><span data-stu-id="995d1-234">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="995d1-235">Přípravné požadavek používá metodu HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="995d1-235">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="995d1-236">Obsahuje dva speciálními záhlavími:</span><span class="sxs-lookup"><span data-stu-id="995d1-236">It includes two special headers:</span></span>

* <span data-ttu-id="995d1-237">`Access-Control-Request-Method`: Metoda protokolu HTTP, který se použije pro aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="995d1-237">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="995d1-238">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví u aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="995d1-238">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="995d1-239">Jak bylo uvedeno dříve, to nezahrnuje hlavičky, které nastaví v prohlížeči, jako například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="995d1-239">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="995d1-240">Předběžný požadavek CORS patří `Access-Control-Request-Headers` hlavičky, která označuje hlavičky, které jsou odeslány pomocí aktuálního požadavku na server.</span><span class="sxs-lookup"><span data-stu-id="995d1-240">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="995d1-241">Chcete-li povolit konkrétní hlavičky, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-241">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="995d1-242">Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-242">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="995d1-243">Nejsou zcela konzistentní v tom, jak je nastavit prohlížeče `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="995d1-243">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="995d1-244">Pokud nastavíte záhlaví na něco jiného než `"*"` (nebo použijte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), by měl obsahovat alespoň `Accept`, `Content-Type`, a `Origin`, a navíc jakékoli vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="995d1-244">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="995d1-245">Následuje příklad odpovědi pro předběžný požadavek (za předpokladu, že server umožňuje žádosti):</span><span class="sxs-lookup"><span data-stu-id="995d1-245">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="995d1-246">Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která uvádí povolené metody a volitelně `Access-Control-Allow-Headers` hlavičky, která uvádí povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="995d1-246">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="995d1-247">Pokud je předběžný požadavek úspěšné, prohlížeč odesílá aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="995d1-247">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="995d1-248">Pokud je předběžný požadavek, vrátí aplikaci *200 OK* odpovědi ale nebude odesílat hlavičky CORS zpět.</span><span class="sxs-lookup"><span data-stu-id="995d1-248">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="995d1-249">Prohlížeč proto nebude se pokoušet žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-249">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="995d1-250">Nastavit čas vypršení platnosti předběžné</span><span class="sxs-lookup"><span data-stu-id="995d1-250">Set the preflight expiration time</span></span>

<span data-ttu-id="995d1-251">`Access-Control-Max-Age` Záhlaví Určuje, jak dlouho může do mezipaměti odpovědi pro předběžný požadavek.</span><span class="sxs-lookup"><span data-stu-id="995d1-251">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="995d1-252">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="995d1-252">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="995d1-253">Jak funguje CORS</span><span class="sxs-lookup"><span data-stu-id="995d1-253">How CORS works</span></span>

<span data-ttu-id="995d1-254">Tato část popisuje, co se stane [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) požadavek na úrovni zprávy HTTP.</span><span class="sxs-lookup"><span data-stu-id="995d1-254">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="995d1-255">CORS je **není** funkce zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="995d1-255">CORS is **not** a security feature.</span></span> <span data-ttu-id="995d1-256">CORS je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-256">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="995d1-257">Například může použít škodlivý objekt actor [zabránit webů skriptování mezi weby (XSS)](xref:security/cross-site-scripting) proti vaší lokality a spuštění podvržení žádosti do své lokality zapnuté CORS odcizit informace.</span><span class="sxs-lookup"><span data-stu-id="995d1-257">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="995d1-258">Vaše rozhraní API není bezpečnější povolením CORS.</span><span class="sxs-lookup"><span data-stu-id="995d1-258">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="995d1-259">To je na klienta (prohlížeč) k vynucení CORS.</span><span class="sxs-lookup"><span data-stu-id="995d1-259">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="995d1-260">Server zpracuje požadavek a vrátí odpověď, je klient, který vrátí odpověď na chybu a bloků.</span><span class="sxs-lookup"><span data-stu-id="995d1-260">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="995d1-261">Například některé z následujících nástrojů se zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="995d1-261">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="995d1-262">Fiddler</span><span class="sxs-lookup"><span data-stu-id="995d1-262">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="995d1-263">Postman</span><span class="sxs-lookup"><span data-stu-id="995d1-263">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="995d1-264">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="995d1-264">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="995d1-265">Zadáním adresy URL do adresního řádku webového prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="995d1-265">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="995d1-266">Jedná se o způsob pro server, aby prohlížeče k provedení různých původů [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) nebo [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) požadavek, který by jinak zakázáno.</span><span class="sxs-lookup"><span data-stu-id="995d1-266">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="995d1-267">Prohlížeče, které (CORS) nemůže provádět požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="995d1-267">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="995d1-268">Před CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) byl použit pro toto omezení obejít.</span><span class="sxs-lookup"><span data-stu-id="995d1-268">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="995d1-269">JSONP nepoužívá XHR, použije `<script>` značky pro příjem odpovědi.</span><span class="sxs-lookup"><span data-stu-id="995d1-269">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="995d1-270">Skripty mohou být načteny nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-270">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="995d1-271">[Specifikace CORS](https://www.w3.org/TR/cors/) zavedené několik nové hlavičky protokolu HTTP, které umožňují požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="995d1-271">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="995d1-272">Pokud je prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="995d1-272">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="995d1-273">Vlastní kód jazyka JavaScript není vyžadován k povolení sdílení CORS.</span><span class="sxs-lookup"><span data-stu-id="995d1-273">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="995d1-274">Následuje příklad žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="995d1-274">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="995d1-275">`Origin` Záhlaví obsahuje domény, lokality, která odeslala žádost:</span><span class="sxs-lookup"><span data-stu-id="995d1-275">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="995d1-276">Pokud server umožňuje, aby žádosti, nastaví `Access-Control-Allow-Origin` hlaviček v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="995d1-276">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="995d1-277">Hodnotu této hlavičky odpovídá buď `Origin` hlavička ze žádosti nebo je hodnota zástupného znaku `"*"`, což znamená, že jakýkoli původ je povoleno:</span><span class="sxs-lookup"><span data-stu-id="995d1-277">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="995d1-278">Pokud odpověď neobsahuje `Access-Control-Allow-Origin` hlavičky žádosti nepůvodního selže.</span><span class="sxs-lookup"><span data-stu-id="995d1-278">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="995d1-279">Konkrétně v prohlížeči zakazuje požadavku.</span><span class="sxs-lookup"><span data-stu-id="995d1-279">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="995d1-280">I v případě, že server vrátí úspěšné odpovědi, prohlížeč nevyužívá odpovědi k dispozici pro klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="995d1-280">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="995d1-281">Test CORS</span><span class="sxs-lookup"><span data-stu-id="995d1-281">Test CORS</span></span>

<span data-ttu-id="995d1-282">Test CORS:</span><span class="sxs-lookup"><span data-stu-id="995d1-282">To test CORS:</span></span>

1. <span data-ttu-id="995d1-283">[Vytvoření projektu aplikace API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="995d1-283">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="995d1-284">Alternativně můžete [Stáhněte ukázku](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="995d1-284">Alternatively, you can [download the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="995d1-285">Povolení CORS pomocí jednoho z postupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="995d1-285">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="995d1-286">Příklad:</span><span class="sxs-lookup"><span data-stu-id="995d1-286">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="995d1-287">`WithOrigins("https://localhost:<port>");` by měla sloužit pouze pro účely testování ukázkové aplikace podobně jako [stáhnout ukázkový kód](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="995d1-287">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="995d1-288">Vytvoření projektu webové aplikace (pro stránky Razor nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="995d1-288">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="995d1-289">Ukázka používá pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="995d1-289">The sample uses Razor Pages.</span></span> <span data-ttu-id="995d1-290">Můžete vytvořit webovou aplikaci ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="995d1-290">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="995d1-291">Přidejte následující zvýrazněný kód do *Index.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="995d1-291">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="995d1-292">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` s adresou URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="995d1-292">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="995d1-293">Nasazení projektu rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="995d1-293">Deploy the API project.</span></span> <span data-ttu-id="995d1-294">Například [nasazení do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="995d1-294">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="995d1-295">Spuštění aplikace Razor Pages nebo MVC z plochy a klikněte na **Test** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="995d1-295">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="995d1-296">Pomocí nástrojů F12 Zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="995d1-296">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="995d1-297">Odebrat localhost očátek z `WithOrigins` a nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="995d1-297">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="995d1-298">Můžete také spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="995d1-298">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="995d1-299">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="995d1-299">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="995d1-300">Testování pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="995d1-300">Test with the client app.</span></span> <span data-ttu-id="995d1-301">Selhání CORS vrátí chybu, ale chybová zpráva není k dispozici pro jazyk JavaScript.</span><span class="sxs-lookup"><span data-stu-id="995d1-301">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="995d1-302">Na kartě konzoly v nástrojích F12 tools chybu.</span><span class="sxs-lookup"><span data-stu-id="995d1-302">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="995d1-303">V závislosti na prohlížeči dojde k chybě (v konzole nástroje F12) podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="995d1-303">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="995d1-304">Pomocí Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="995d1-304">Using Microsoft Edge:</span></span>

     <span data-ttu-id="995d1-305">**SEC7120: [CORS] původ `https://localhost:44375` nenalezl `https://localhost:44375` v hlavička odpovědi Access-Control-Allow-Origin pro prostředek nepůvodního zdroje `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="995d1-305">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="995d1-306">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="995d1-306">Using Chrome:</span></span>

     <span data-ttu-id="995d1-307">**Přístup k XMLHttpRequest na `https://webapi.azurewebsites.net/api/values/1` z počátku `https://localhost:44375` nezablokoval zásada CORS: Žádné záhlaví 'Přístup-Control-Allow-Origin' je k dispozici u požadovaného prostředku.**</span><span class="sxs-lookup"><span data-stu-id="995d1-307">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="995d1-308">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="995d1-308">Additional resources</span></span>

* [<span data-ttu-id="995d1-309">Prostředků mezi zdroji (CORS) pro sdílení obsahu</span><span class="sxs-lookup"><span data-stu-id="995d1-309">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
