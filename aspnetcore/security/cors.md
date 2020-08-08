---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: ee640ded37f40175e3e150f713fa970e9705b62c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021104"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="0e3be-103">Povolit žádosti mezi zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e3be-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e3be-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="0e3be-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="0e3be-105">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="0e3be-106">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="0e3be-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0e3be-107">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="0e3be-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0e3be-108">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="0e3be-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0e3be-109">V některých případech můžete chtít umožnit jiným webům, aby vaši aplikaci mohli vytvářet žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="0e3be-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="0e3be-110">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="0e3be-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="0e3be-111">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="0e3be-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="0e3be-112">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="0e3be-113">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="0e3be-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="0e3be-114">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="0e3be-115">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="0e3be-116">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="0e3be-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="0e3be-117">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="0e3be-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="0e3be-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e3be-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="0e3be-119">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="0e3be-119">Same origin</span></span>

<span data-ttu-id="0e3be-120">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="0e3be-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="0e3be-121">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="0e3be-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="0e3be-122">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="0e3be-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="0e3be-123">`https://example.net`: Odlišná doména</span><span class="sxs-lookup"><span data-stu-id="0e3be-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="0e3be-124">`https://www.example.com/foo.html`: Odlišná subdoména</span><span class="sxs-lookup"><span data-stu-id="0e3be-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="0e3be-125">`http://example.com/foo.html`: Odlišné schéma</span><span class="sxs-lookup"><span data-stu-id="0e3be-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="0e3be-126">`https://example.com:9000/foo.html`: Jiný port</span><span class="sxs-lookup"><span data-stu-id="0e3be-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="0e3be-127">Povolení CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-127">Enable CORS</span></span>

<span data-ttu-id="0e3be-128">Existují tři způsoby, jak povolit CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="0e3be-129">V middlewaru pomocí [pojmenované zásady](#np) nebo [výchozí zásady](#dp).</span><span class="sxs-lookup"><span data-stu-id="0e3be-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="0e3be-130">Použití [Směrování koncového bodu](#ecors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="0e3be-131">S atributem [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="0e3be-132">Použití atributu [[EnableCors]](#attr) s pojmenovanou zásadou poskytuje ovládací prvek nejlepší v omezení koncových bodů, které podporují CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="0e3be-133"><xref:Owin.CorsExtensions.UseCors%2A>musí být voláno před <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> při použití `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="0e3be-134">Každý přístup je podrobně popsaný v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="0e3be-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="0e3be-135">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="0e3be-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="0e3be-136">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="0e3be-137">Následující kód aplikuje zásadu CORS na všechny koncové body aplikace se zadanými zdroji:</span><span class="sxs-lookup"><span data-stu-id="0e3be-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="0e3be-138">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="0e3be-138">The preceding code:</span></span>

* <span data-ttu-id="0e3be-139">Nastaví název zásady na `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="0e3be-140">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="0e3be-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="0e3be-141">Zavolá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření a určí `_myAllowSpecificOrigins` zásadu CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="0e3be-142">`UseCors`Přidá middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="0e3be-143">Volání `UseCors` musí být umístěno po `UseRouting` , ale před `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="0e3be-144">Další informace najdete v tématu [pořadí middlewaru](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="0e3be-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="0e3be-145">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0e3be-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0e3be-146">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="0e3be-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="0e3be-147">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins` , jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="0e3be-148">Povolí `_myAllowSpecificOrigins` zásadu CORS pro všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="0e3be-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="0e3be-149">Pokud chcete použít zásadu CORS na konkrétní koncové body, podívejte se na téma [Směrování koncového bodu](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="0e3be-150">Při použití [middlewaru pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware)volejte <xref:Owin.CorsExtensions.UseCors%2A> před <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="0e3be-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="0e3be-151">Při směrování koncových bodů **musí** být MIDDLEWARe CORS nakonfigurované tak, aby se spustilo mezi voláními `UseRouting` a `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="0e3be-152">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="0e3be-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Volání metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e3be-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="0e3be-154">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="0e3be-155"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metody mohou být zřetězeny, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="0e3be-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="0e3be-156">Poznámka: Zadaná adresa URL nesmí **obsahovat koncové** lomítko ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="0e3be-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="0e3be-157">Pokud adresa URL končí `/` , porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="0e3be-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="0e3be-158">CORS s výchozími zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="0e3be-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="0e3be-159">Následující zvýrazněný kód umožňuje výchozí zásadu CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="0e3be-160">Předchozí kód aplikuje výchozí zásadu CORS na všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="0e3be-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="0e3be-161">Povolení CORS s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="0e3be-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="0e3be-162">Povolení CORS na základě jednotlivých koncových bodů pomocí v `RequireCors` současné **not** době nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="0e3be-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="0e3be-163">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/20709) a [testování CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="0e3be-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="0e3be-164">S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů pomocí <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> sady rozšiřujících metod:</span><span class="sxs-lookup"><span data-stu-id="0e3be-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="0e3be-165">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="0e3be-165">In the preceding code:</span></span>

* <span data-ttu-id="0e3be-166">`app.UseCors`povoluje middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="0e3be-167">Vzhledem k tomu, že výchozí zásady nejsou nakonfigurované, `app.UseCors()` nepovolí CORS samotné.</span><span class="sxs-lookup"><span data-stu-id="0e3be-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="0e3be-168">`/echo`Koncové body řadiče a umožňují žádosti mezi zdroji pomocí zadaných zásad.</span><span class="sxs-lookup"><span data-stu-id="0e3be-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="0e3be-169">`/echo2` Razor Koncové body stránky a nepovolují požadavky mezi zdroji, protože **nebyly** zadány žádné výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="0e3be-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="0e3be-170">Atribut [[DisableCors]](#dc) **nevypne CORS** , která byla povolená směrováním koncových bodů s `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="0e3be-171">Pokyny k testování kódu podobného předchozímu najdete v tématu [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="0e3be-172">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="0e3be-172">Enable CORS with attributes</span></span>

<span data-ttu-id="0e3be-173">Povolení CORS s atributem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) a použití pojmenované zásady pouze na koncové body, které vyžadují CORS, poskytuje ovládací prvek nejlepší.</span><span class="sxs-lookup"><span data-stu-id="0e3be-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="0e3be-174">Atribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="0e3be-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="0e3be-175">`[EnableCors]`Atribut umožňuje CORS pro vybrané koncové body místo všech koncových bodů:</span><span class="sxs-lookup"><span data-stu-id="0e3be-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="0e3be-176">`[EnableCors]`Určuje výchozí zásadu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="0e3be-177">`[EnableCors("{Policy String}")]`Určuje pojmenovanou zásadu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="0e3be-178">`[EnableCors]`Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="0e3be-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="0e3be-179">RazorPage`PageModel`</span><span class="sxs-lookup"><span data-stu-id="0e3be-179">Razor Page `PageModel`</span></span>
* <span data-ttu-id="0e3be-180">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="0e3be-180">Controller</span></span>
* <span data-ttu-id="0e3be-181">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="0e3be-181">Controller action method</span></span>

<span data-ttu-id="0e3be-182">U řadičů, modelů stránek nebo metod akcí lze použít různé zásady s `[EnableCors]` atributem.</span><span class="sxs-lookup"><span data-stu-id="0e3be-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="0e3be-183">Pokud je `[EnableCors]` atribut použit na řadič, model stránky nebo metodu akce a CORS je v middleware povolená, uplatní se **obě** zásady.</span><span class="sxs-lookup"><span data-stu-id="0e3be-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="0e3be-184">Doporučujeme **před kombinováním zásad. Použijte** `[EnableCors]` **atribut nebo middleware, nikoli oba ve stejné aplikaci.**</span><span class="sxs-lookup"><span data-stu-id="0e3be-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="0e3be-185">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="0e3be-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="0e3be-186">Následující kód vytvoří dvě zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="0e3be-187">Pro ovládací prvek nejlepší pro omezení požadavků CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="0e3be-188">Použijte `[EnableCors("MyPolicy")]` s pojmenovanou zásadou.</span><span class="sxs-lookup"><span data-stu-id="0e3be-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="0e3be-189">Nedefinujte výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="0e3be-189">Don't define a default policy.</span></span>
* <span data-ttu-id="0e3be-190">Nepoužívejte [Směrování koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="0e3be-191">Kód v další části splňuje předchozí seznam.</span><span class="sxs-lookup"><span data-stu-id="0e3be-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="0e3be-192">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="0e3be-193">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-193">Disable CORS</span></span>

<span data-ttu-id="0e3be-194">Atribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **zakáže rozhraní** CORS, které bylo povoleno [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="0e3be-195">Následující kód definuje zásadu CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="0e3be-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="0e3be-196">Následující kód zakáže CORS pro `GetValues2` akci:</span><span class="sxs-lookup"><span data-stu-id="0e3be-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="0e3be-197">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="0e3be-197">The preceding code:</span></span>

* <span data-ttu-id="0e3be-198">Nepovoluje CORS s [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="0e3be-199">Nedefinuje [výchozí zásadu CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="0e3be-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="0e3be-200">Pro povolení zásad CORS pro kontroler používá [[EnableCors ("MyPolicy")]](#attr) `"MyPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="0e3be-201">Zakáže CORS pro `GetValues2` metodu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="0e3be-202">Pokyny k testování předchozího kódu najdete v části [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="0e3be-203">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-203">CORS policy options</span></span>

<span data-ttu-id="0e3be-204">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="0e3be-205">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="0e3be-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="0e3be-206">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="0e3be-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="0e3be-207">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="0e3be-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="0e3be-208">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="0e3be-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="0e3be-209">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="0e3be-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="0e3be-210">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="0e3be-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="0e3be-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0e3be-212">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="0e3be-213">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="0e3be-213">Set the allowed origins</span></span>

<span data-ttu-id="0e3be-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem ( `http` nebo `https` ).</span><span class="sxs-lookup"><span data-stu-id="0e3be-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="0e3be-215">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0e3be-216">Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="0e3be-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="0e3be-217">Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.</span><span class="sxs-lookup"><span data-stu-id="0e3be-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="0e3be-218">`AllowAnyOrigin`má vliv na požadavky na kontrolu a `Access-Control-Allow-Origin` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="0e3be-219">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0e3be-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásad jako funkci, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.</span><span class="sxs-lookup"><span data-stu-id="0e3be-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="0e3be-221">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="0e3be-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="0e3be-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="0e3be-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="0e3be-223">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="0e3be-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="0e3be-224">Má vliv na požadavky na kontrolu a `Access-Control-Allow-Methods` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="0e3be-225">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="0e3be-226">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="0e3be-226">Set the allowed request headers</span></span>

<span data-ttu-id="0e3be-227">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem [záhlaví žádosti o autora](https://xhr.spec.whatwg.org/#request), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="0e3be-228">Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="0e3be-229">`AllowAnyHeader`má vliv na žádosti o předběžné kontroly a hlavičku [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="0e3be-230">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0e3be-231">Zásada middlewaru CORS odpovídající konkrétním hlavičkám, které určuje, `WithHeaders` je možná jenom v případě, že se záhlaví poslala `Access-Control-Request-Headers` přesně a odpovídají hlavičkám uvedeným v `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="0e3be-232">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="0e3be-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="0e3be-233">Middleware CORS odmítá požadavek na předběžné kontroly s následující hlavičkou požadavku, protože `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uvedená v tomto seznamu `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="0e3be-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="0e3be-234">Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0e3be-235">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="0e3be-236">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="0e3be-236">Set the exposed response headers</span></span>

<span data-ttu-id="0e3be-237">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e3be-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="0e3be-238">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="0e3be-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="0e3be-239">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="0e3be-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="0e3be-240">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="0e3be-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="0e3be-241">Pokud chcete, aby byla aplikace k dispozici ostatním hlavičkám, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="0e3be-242">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="0e3be-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="0e3be-243">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="0e3be-244">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="0e3be-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="0e3be-245">Přihlašovací údaje zahrnují cookie schémata ověřování s a http.</span><span class="sxs-lookup"><span data-stu-id="0e3be-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="0e3be-246">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být klient nastaven `XMLHttpRequest.withCredentials` na `true` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="0e3be-247">`XMLHttpRequest`Přímé použití:</span><span class="sxs-lookup"><span data-stu-id="0e3be-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="0e3be-248">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="0e3be-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="0e3be-249">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="0e3be-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="0e3be-250">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="0e3be-250">The server must allow the credentials.</span></span> <span data-ttu-id="0e3be-251">Pokud chcete povolení přihlašovacích údajů mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="0e3be-252">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="0e3be-253">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="0e3be-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="0e3be-254">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="0e3be-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="0e3be-255">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="0e3be-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="0e3be-256">Specifikace CORS také uvádí, že pokud je hlavička k dispozici, nastavení původu na `"*"` (všechny zdroje) je neplatné `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="0e3be-257">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="0e3be-257">Preflight requests</span></span>

<span data-ttu-id="0e3be-258">U některých požadavků CORS prohlížeč před provedením samotné žádosti pošle žádost o další [Možnosti](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="0e3be-259">Tento požadavek se nazývá [žádost o kontrolu před výstupem](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="0e3be-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="0e3be-260">Prohlížeč může přeskočit požadavek na kontrolu před výstupem, pokud jsou splněné **všechny** následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="0e3be-261">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="0e3be-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="0e3be-262">Aplikace nenastaví záhlaví žádostí s výjimkou `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` nebo `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="0e3be-263">`Content-Type`Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="0e3be-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="0e3be-264">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt.</span><span class="sxs-lookup"><span data-stu-id="0e3be-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="0e3be-265">Specifikace CORS volá [záhlaví požadavku autora](https://www.w3.org/TR/cors/#author-request-headers)záhlaví.</span><span class="sxs-lookup"><span data-stu-id="0e3be-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="0e3be-266">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent` , `Host` nebo `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="0e3be-267">Následuje příklad odpovědi, která se podobá žádosti o kontrolu před výstupem vytvořeným tlačítkem **[PUT test]** v části [test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="0e3be-268">Požadavek na kontrolu před výstupem používá metodu [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="0e3be-269">Může obsahovat následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-269">It may include the following headers:</span></span>

* <span data-ttu-id="0e3be-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): metoda HTTP, která bude použita pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e3be-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="0e3be-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="0e3be-272">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="0e3be-273">Přístup-řízení-povolení-metody</span><span class="sxs-lookup"><span data-stu-id="0e3be-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="0e3be-274">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí `200 OK` odpověď, ale nenastaví HLAVIČKY CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="0e3be-275">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="0e3be-276">Příklad Zamítnuté žádosti o kontrolu před výstupem najdete v části [test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="0e3be-277">Pomocí nástrojů F12 zobrazuje aplikace konzoly chybu podobnou jedné z následujících v závislosti na prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="0e3be-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="0e3be-278">Firefox: požadavek mezi zdroji je zablokován: stejné zásady původu nepovolují čtení vzdáleného prostředku v `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="0e3be-279">(Důvod: požadavek CORS se nezdařil)</span><span class="sxs-lookup"><span data-stu-id="0e3be-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="0e3be-280">Další informace</span><span class="sxs-lookup"><span data-stu-id="0e3be-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="0e3be-281">Chromová: přístup k načtení v ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' od původu ' https://cors3.azurewebsites.net ' byl zablokován zásadou CORS: odpověď na požadavek na kontrolu před výstupem neprojde kontrolou řízení přístupu: v požadovaném prostředku není k dispozici hlavička ' Access-Control-Allow-Origin '.</span><span class="sxs-lookup"><span data-stu-id="0e3be-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="0e3be-282">Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="0e3be-283">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="0e3be-284">Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="0e3be-285">Prohlížeče nejsou konzistentní v tom, jak jsou nastavené `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="0e3be-286">Pokud buď:</span><span class="sxs-lookup"><span data-stu-id="0e3be-286">If either:</span></span>

* <span data-ttu-id="0e3be-287">Záhlaví jsou nastavena na jinou hodnotu než`"*"`</span><span class="sxs-lookup"><span data-stu-id="0e3be-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="0e3be-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se nazývá: uveďte aspoň `Accept` , `Content-Type` a a a `Origin` všechny vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="0e3be-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="0e3be-289">Automatický kód žádosti o kontrolu</span><span class="sxs-lookup"><span data-stu-id="0e3be-289">Automatic preflight request code</span></span>

<span data-ttu-id="0e3be-290">Když se aplikuje zásada CORS, proveďte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="0e3be-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="0e3be-291">Globálně voláním `app.UseCors` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="0e3be-292">Pomocí `[EnableCors]` atributu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="0e3be-293">ASP.NET Core reaguje na požadavek na předběžné možnosti.</span><span class="sxs-lookup"><span data-stu-id="0e3be-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="0e3be-294">Povolení CORS na základě jednotlivých koncových bodů pomocí v `RequireCors` současné **not** době nepodporuje automatické požadavky na kontrolu před výstupem.</span><span class="sxs-lookup"><span data-stu-id="0e3be-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="0e3be-295">V části [test CORS](#testc) tohoto dokumentu se toto chování demonstruje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="0e3be-296">[HttpOptions] atribut pro žádosti o kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="0e3be-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="0e3be-297">Když je v rámci příslušné zásady povolená CORS, ASP.NET Core obecně reaguje na požadavky na předběžné kontroly CORS automaticky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="0e3be-298">V některých scénářích to nemusí být případ.</span><span class="sxs-lookup"><span data-stu-id="0e3be-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="0e3be-299">Například použití [CORS se směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="0e3be-300">Následující kód používá atribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) k vytvoření koncových bodů pro žádosti o možnosti:</span><span class="sxs-lookup"><span data-stu-id="0e3be-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="0e3be-301">Pokyny k testování předchozího kódu najdete v článku [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="0e3be-302">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="0e3be-302">Set the preflight expiration time</span></span>

<span data-ttu-id="0e3be-303">`Access-Control-Max-Age`Záhlaví určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="0e3be-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="0e3be-304">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="0e3be-305">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="0e3be-305">How CORS works</span></span>

<span data-ttu-id="0e3be-306">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3be-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="0e3be-307">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="0e3be-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="0e3be-308">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="0e3be-309">Škodlivý objekt actor může například použít [skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s POVOLENým CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="0e3be-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="0e3be-310">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="0e3be-311">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="0e3be-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="0e3be-312">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="0e3be-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="0e3be-313">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="0e3be-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="0e3be-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="0e3be-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="0e3be-315">Postman</span><span class="sxs-lookup"><span data-stu-id="0e3be-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="0e3be-316">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="0e3be-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="0e3be-317">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="0e3be-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="0e3be-318">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="0e3be-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="0e3be-319">Prohlížeče bez CORS nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="0e3be-320">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="0e3be-321">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e3be-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="0e3be-322">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="0e3be-323">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="0e3be-324">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="0e3be-325">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="0e3be-326">[Tlačítko Vložit test](https://cors3.azurewebsites.net/test) na nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="0e3be-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="0e3be-327">Následuje příklad žádosti o více zdrojů z tlačítka Test [hodnot](https://cors3.azurewebsites.net/) na `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="0e3be-328">`Origin`Hlavička:</span><span class="sxs-lookup"><span data-stu-id="0e3be-328">The `Origin` header:</span></span>

* <span data-ttu-id="0e3be-329">Poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e3be-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="0e3be-330">Je vyžadováno a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="0e3be-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="0e3be-331">**Obecné hlavičky**</span><span class="sxs-lookup"><span data-stu-id="0e3be-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="0e3be-332">**Hlavičky odpovědi**</span><span class="sxs-lookup"><span data-stu-id="0e3be-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="0e3be-333">**Hlavičky požadavku**</span><span class="sxs-lookup"><span data-stu-id="0e3be-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="0e3be-334">V `OPTIONS` části požadavky Server nastaví hlavičku **hlaviček odpovědi** `Access-Control-Allow-Origin: {allowed origin}` v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e3be-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="0e3be-335">Například nasazená [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [DELETE [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` obsahuje následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="0e3be-336">**Obecné hlavičky**</span><span class="sxs-lookup"><span data-stu-id="0e3be-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="0e3be-337">**Hlavičky odpovědi**</span><span class="sxs-lookup"><span data-stu-id="0e3be-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="0e3be-338">**Hlavičky požadavku**</span><span class="sxs-lookup"><span data-stu-id="0e3be-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="0e3be-339">V předchozích **hlavičkách odpovědi**Server nastaví hlavičku [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e3be-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="0e3be-340">`https://cors1.azurewebsites.net`Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="0e3be-341">Pokud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> je volána, `Access-Control-Allow-Origin: *` je vrácena hodnota zástupného znaku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="0e3be-342">`AllowAnyOrigin`umožňuje jakýkoli původ.</span><span class="sxs-lookup"><span data-stu-id="0e3be-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="0e3be-343">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="0e3be-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="0e3be-344">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="0e3be-345">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e3be-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="0e3be-346">Zobrazit žádosti o možnosti</span><span class="sxs-lookup"><span data-stu-id="0e3be-346">Display OPTIONS requests</span></span>

<span data-ttu-id="0e3be-347">Ve výchozím nastavení prohlížeče Chrome a Edge nezobrazují žádosti o možnosti na kartě síť nástrojů F12.</span><span class="sxs-lookup"><span data-stu-id="0e3be-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="0e3be-348">Chcete-li zobrazit žádosti o možnosti v těchto prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="0e3be-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="0e3be-349">`chrome://flags/#out-of-blink-cors` nebo `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="0e3be-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="0e3be-350">zakažte příznak.</span><span class="sxs-lookup"><span data-stu-id="0e3be-350">disable the flag.</span></span>
* <span data-ttu-id="0e3be-351">restart (restartovat).</span><span class="sxs-lookup"><span data-stu-id="0e3be-351">restart.</span></span>

<span data-ttu-id="0e3be-352">Firefox ve výchozím nastavení zobrazuje požadavky na možnosti.</span><span class="sxs-lookup"><span data-stu-id="0e3be-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="0e3be-353">CORS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="0e3be-353">CORS in IIS</span></span>

<span data-ttu-id="0e3be-354">Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="0e3be-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="0e3be-355">Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e3be-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="0e3be-356">Test CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-356">Test CORS</span></span>

<span data-ttu-id="0e3be-357">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) obsahuje kód pro testování CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="0e3be-358">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="0e3be-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="0e3be-359">Ukázka je projekt rozhraní API se Razor stránkami přidanými:</span><span class="sxs-lookup"><span data-stu-id="0e3be-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="0e3be-360">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="0e3be-361">Následující příklad `ValuesController` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="0e3be-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="0e3be-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) poskytuje balíček NuGet [Rick.Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) a zobrazí informace o trasách.</span><span class="sxs-lookup"><span data-stu-id="0e3be-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="0e3be-363">Otestujte předchozí vzorový kód pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="0e3be-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="0e3be-364">Použijte nasazenou ukázkovou aplikaci na adrese [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="0e3be-365">Není nutné stahovat ukázku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="0e3be-366">Spusťte ukázku s `dotnet run` použitím výchozí adresy URL `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="0e3be-367">Spusťte ukázku ze sady Visual Studio s portem nastaveným na 44398 pro adresu URL `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="0e3be-368">Použití prohlížeče s nástroji F12:</span><span class="sxs-lookup"><span data-stu-id="0e3be-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="0e3be-369">Vyberte tlačítko **hodnoty** a zkontrolujte hlavičky na kartě **síť** .</span><span class="sxs-lookup"><span data-stu-id="0e3be-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="0e3be-370">Vyberte tlačítko **Vložit test** .</span><span class="sxs-lookup"><span data-stu-id="0e3be-370">Select the **PUT test** button.</span></span> <span data-ttu-id="0e3be-371">Pokyny k zobrazení žádosti o možnosti najdete v tématu [Možnosti zobrazení žádosti](#options) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="0e3be-372">**Test Put** vytvoří dvě požadavky, požadavek na kontrolu před výstupem a požadavek PUT.</span><span class="sxs-lookup"><span data-stu-id="0e3be-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="0e3be-373">Kliknutím na **`GetValues2 [DisableCors]`** tlačítko aktivujte neúspěšnou žádost CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="0e3be-374">Jak je uvedeno v dokumentu, odpověď vrátí 200 úspěch, ale požadavek CORS se neprovádí.</span><span class="sxs-lookup"><span data-stu-id="0e3be-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="0e3be-375">Kliknutím na kartu **Konzola** zobrazíte chybu CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="0e3be-376">V závislosti na prohlížeči se zobrazí chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="0e3be-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="0e3be-377">`'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` Zásada CORS zablokovala přístup k načtení od původu: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.</span><span class="sxs-lookup"><span data-stu-id="0e3be-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="0e3be-378">Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="0e3be-379">Koncové body s podporou CORS je možné testovat pomocí nástroje, jako je například [kudrlinkou](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="0e3be-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="0e3be-380">Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="0e3be-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="0e3be-381">Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="0e3be-382">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="0e3be-383">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="0e3be-384">Následující příkaz slouží `curl` k vystavení žádosti o možnosti s informacemi:</span><span class="sxs-lookup"><span data-stu-id="0e3be-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="0e3be-385">Testování CORS pomocí směrování koncových bodů a [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="0e3be-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="0e3be-386">Povolení CORS na základě jednotlivých koncových bodů pomocí v `RequireCors` současné **not** době nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="0e3be-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="0e3be-387">Vezměte v úvahu následující kód, který pomocí [Směrování koncových bodů povoluje CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="0e3be-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="0e3be-388">Následující příklad `TodoItems1Controller` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="0e3be-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="0e3be-389">Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=1) v nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="0e3be-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="0e3be-390">Tlačítka **DELETE [EnableCors]** a **Get [EnableCors]** jsou úspěšná, protože koncové body mají `[EnableCors]` a reagují na požadavky na kontrolu před výstupem.</span><span class="sxs-lookup"><span data-stu-id="0e3be-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="0e3be-391">Ostatní koncové body selžou.</span><span class="sxs-lookup"><span data-stu-id="0e3be-391">The other endpoints fails.</span></span> <span data-ttu-id="0e3be-392">Tlačítko **získat** se nepovedlo, protože [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) pošle:</span><span class="sxs-lookup"><span data-stu-id="0e3be-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="0e3be-393">Následující `TodoItems2Controller` příkaz poskytuje podobné koncové body, ale obsahuje explicitní kód pro reakci na požadavky Options:</span><span class="sxs-lookup"><span data-stu-id="0e3be-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="0e3be-394">Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=2) v nasazené ukázce.</span><span class="sxs-lookup"><span data-stu-id="0e3be-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="0e3be-395">V rozevíracím seznamu **řadič** vyberte položku **Kontrola před výstupem** a pak **nastavte kontroler**.</span><span class="sxs-lookup"><span data-stu-id="0e3be-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="0e3be-396">Všechna volání CORS do `TodoItems2Controller` koncových bodů jsou úspěšná.</span><span class="sxs-lookup"><span data-stu-id="0e3be-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e3be-397">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e3be-397">Additional resources</span></span>

* [<span data-ttu-id="0e3be-398">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="0e3be-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="0e3be-399">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e3be-400">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e3be-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0e3be-401">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="0e3be-402">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="0e3be-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0e3be-403">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="0e3be-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0e3be-404">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="0e3be-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0e3be-405">V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="0e3be-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="0e3be-406">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="0e3be-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="0e3be-407">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="0e3be-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="0e3be-408">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="0e3be-409">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="0e3be-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="0e3be-410">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="0e3be-411">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="0e3be-412">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="0e3be-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="0e3be-413">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="0e3be-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="0e3be-414">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e3be-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="0e3be-415">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="0e3be-415">Same origin</span></span>

<span data-ttu-id="0e3be-416">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="0e3be-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="0e3be-417">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="0e3be-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="0e3be-418">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="0e3be-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="0e3be-419">`https://example.net`: Odlišná doména</span><span class="sxs-lookup"><span data-stu-id="0e3be-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="0e3be-420">`https://www.example.com/foo.html`: Odlišná subdoména</span><span class="sxs-lookup"><span data-stu-id="0e3be-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="0e3be-421">`http://example.com/foo.html`: Odlišné schéma</span><span class="sxs-lookup"><span data-stu-id="0e3be-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="0e3be-422">`https://example.com:9000/foo.html`: Jiný port</span><span class="sxs-lookup"><span data-stu-id="0e3be-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="0e3be-423">Internet Explorer při porovnávání zdrojů nebere v úvahu port.</span><span class="sxs-lookup"><span data-stu-id="0e3be-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="0e3be-424">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="0e3be-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="0e3be-425">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="0e3be-426">Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:</span><span class="sxs-lookup"><span data-stu-id="0e3be-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="0e3be-427">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="0e3be-427">The preceding code:</span></span>

* <span data-ttu-id="0e3be-428">Nastaví název zásady na " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="0e3be-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="0e3be-429">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="0e3be-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="0e3be-430">Volá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="0e3be-431">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="0e3be-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0e3be-432">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="0e3be-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="0e3be-433">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins` , jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="0e3be-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Volání metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="0e3be-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="0e3be-435">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="0e3be-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Metoda může řetězit metody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="0e3be-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="0e3be-437">Poznámka: adresa URL nesmí **obsahovat koncové** lomítko ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="0e3be-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="0e3be-438">Pokud adresa URL končí `/` , porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="0e3be-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="0e3be-439">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="0e3be-440">Poznámka: `UseCors` musí být volána před `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="0e3be-441">Viz [Povolení CORS v rámci Razor stránek, řadičů a metod akcí](#ecors) pro APLIKOVÁNí zásad CORS na úrovni stránky, řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="0e3be-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="0e3be-442">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="0e3be-443">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="0e3be-443">Enable CORS with attributes</span></span>

<span data-ttu-id="0e3be-444">Atribut [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) představuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="0e3be-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="0e3be-445">`[EnableCors]`Atribut povoluje CORS pro vybrané koncové body místo všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="0e3be-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="0e3be-446">Slouží `[EnableCors]` k zadání výchozích zásad a `[EnableCors("{Policy String}")]` k určení zásad.</span><span class="sxs-lookup"><span data-stu-id="0e3be-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="0e3be-447">`[EnableCors]`Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="0e3be-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="0e3be-448">RazorPage`PageModel`</span><span class="sxs-lookup"><span data-stu-id="0e3be-448">Razor Page `PageModel`</span></span>
* <span data-ttu-id="0e3be-449">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="0e3be-449">Controller</span></span>
* <span data-ttu-id="0e3be-450">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="0e3be-450">Controller action method</span></span>

<span data-ttu-id="0e3be-451">S atributem lze použít různé zásady pro kontroler/Page-model/Action `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="0e3be-452">Pokud je `[EnableCors]` atribut použit pro řadiče/stránky modelu/akce a v middlewaru je povoleno CORS, jsou **obě** zásady aplikovány.</span><span class="sxs-lookup"><span data-stu-id="0e3be-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="0e3be-453">**Doporučujeme, abyste nekombinují zásady** .</span><span class="sxs-lookup"><span data-stu-id="0e3be-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="0e3be-454">Použijte `[EnableCors]` atribut nebo middleware, **nikoli oba**.</span><span class="sxs-lookup"><span data-stu-id="0e3be-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="0e3be-455">Při použití nástroje `[EnableCors]` nedefinujte výchozí zásady. **not**</span><span class="sxs-lookup"><span data-stu-id="0e3be-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="0e3be-456">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="0e3be-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="0e3be-457">Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="0e3be-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="0e3be-458">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-458">Disable CORS</span></span>

<span data-ttu-id="0e3be-459">Atribut [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="0e3be-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="0e3be-460">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-460">CORS policy options</span></span>

<span data-ttu-id="0e3be-461">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="0e3be-462">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="0e3be-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="0e3be-463">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="0e3be-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="0e3be-464">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="0e3be-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="0e3be-465">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="0e3be-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="0e3be-466">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="0e3be-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="0e3be-467">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="0e3be-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="0e3be-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0e3be-469">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="0e3be-470">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="0e3be-470">Set the allowed origins</span></span>

<span data-ttu-id="0e3be-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem ( `http` nebo `https` ).</span><span class="sxs-lookup"><span data-stu-id="0e3be-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="0e3be-472">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0e3be-473">Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="0e3be-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="0e3be-474">V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="0e3be-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="0e3be-475">`AllowAnyOrigin`má vliv na požadavky na kontrolu a `Access-Control-Allow-Origin` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="0e3be-476">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0e3be-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásad jako funkci, která umožňuje, aby se zdroje shodovaly s konfigurovanou doménou se zástupnými znaky při vyhodnocování, jestli je původ povolený.</span><span class="sxs-lookup"><span data-stu-id="0e3be-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="0e3be-478">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="0e3be-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="0e3be-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="0e3be-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="0e3be-480">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="0e3be-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="0e3be-481">Má vliv na požadavky na kontrolu a `Access-Control-Allow-Methods` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="0e3be-482">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="0e3be-483">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="0e3be-483">Set the allowed request headers</span></span>

<span data-ttu-id="0e3be-484">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0e3be-485">Pokud chcete povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0e3be-486">Toto nastavení má vliv na žádosti o předběžné kontroly a `Access-Control-Request-Headers` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="0e3be-487">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0e3be-488">Middleware CORS vždycky povoluje posílání čtyř hlaviček v, `Access-Control-Request-Headers` bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="0e3be-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="0e3be-489">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="0e3be-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="0e3be-490">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="0e3be-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="0e3be-491">Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující hlavičkou požadavku, protože `Content-Language` je vždy povoleno:</span><span class="sxs-lookup"><span data-stu-id="0e3be-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="0e3be-492">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="0e3be-492">Set the exposed response headers</span></span>

<span data-ttu-id="0e3be-493">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e3be-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="0e3be-494">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="0e3be-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="0e3be-495">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="0e3be-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="0e3be-496">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="0e3be-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="0e3be-497">Pokud chcete, aby byla aplikace k dispozici ostatním hlavičkám, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="0e3be-498">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="0e3be-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="0e3be-499">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="0e3be-500">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="0e3be-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="0e3be-501">Přihlašovací údaje zahrnují cookie schémata ověřování s a http.</span><span class="sxs-lookup"><span data-stu-id="0e3be-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="0e3be-502">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být klient nastaven `XMLHttpRequest.withCredentials` na `true` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="0e3be-503">`XMLHttpRequest`Přímé použití:</span><span class="sxs-lookup"><span data-stu-id="0e3be-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="0e3be-504">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="0e3be-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="0e3be-505">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="0e3be-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="0e3be-506">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="0e3be-506">The server must allow the credentials.</span></span> <span data-ttu-id="0e3be-507">Pokud chcete povolení přihlašovacích údajů mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="0e3be-508">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="0e3be-509">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="0e3be-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="0e3be-510">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="0e3be-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="0e3be-511">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="0e3be-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="0e3be-512">Specifikace CORS také uvádí, že pokud je hlavička k dispozici, nastavení původu na `"*"` (všechny zdroje) je neplatné `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="0e3be-513">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="0e3be-513">Preflight requests</span></span>

<span data-ttu-id="0e3be-514">U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e3be-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="0e3be-515">Tento požadavek se nazývá *žádost o kontrolu před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="0e3be-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="0e3be-516">Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="0e3be-517">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="0e3be-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="0e3be-518">Aplikace nenastaví záhlaví žádostí s výjimkou `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` nebo `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="0e3be-519">`Content-Type`Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="0e3be-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="0e3be-520">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt.</span><span class="sxs-lookup"><span data-stu-id="0e3be-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="0e3be-521">Specifikace CORS volá *záhlaví požadavku autora*záhlaví.</span><span class="sxs-lookup"><span data-stu-id="0e3be-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="0e3be-522">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent` , `Host` nebo `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="0e3be-523">Následuje příklad žádosti o kontrolu před výstupem:</span><span class="sxs-lookup"><span data-stu-id="0e3be-523">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="0e3be-524">Požadavek na lety používá metodu HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3be-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="0e3be-525">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-525">It includes two special headers:</span></span>

* <span data-ttu-id="0e3be-526">`Access-Control-Request-Method`: Metoda HTTP, která se bude používat pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e3be-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="0e3be-527">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="0e3be-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="0e3be-528">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="0e3be-529">Když je v rámci příslušné zásady povolená CORS, ASP.NET Core všeobecně automaticky reagovat na požadavky CORS v rámci kontroly.</span><span class="sxs-lookup"><span data-stu-id="0e3be-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="0e3be-530">V [případě požadavků na kontrolu před výstupem se podívejte na atribut [HttpOptions]](#pro).</span><span class="sxs-lookup"><span data-stu-id="0e3be-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="0e3be-531">Požadavek předběžné kontroly CORS může zahrnovat `Access-Control-Request-Headers` hlavičku, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="0e3be-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="0e3be-532">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0e3be-533">Pokud chcete povolení všech hlaviček žádostí o autora, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0e3be-534">Prohlížeče nejsou zcela konzistentní v tom, jak nastavily `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="0e3be-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="0e3be-535">Pokud nastavíte záhlaví na jinou hodnotu než `"*"` (nebo použít <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), měli byste zahrnout aspoň `Accept` , `Content-Type` a a `Origin` také libovolné vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="0e3be-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="0e3be-536">Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):</span><span class="sxs-lookup"><span data-stu-id="0e3be-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="0e3be-537">Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která obsahuje seznam povolených metod a volitelně `Access-Control-Allow-Headers` záhlaví, ve kterém jsou uvedeny povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="0e3be-538">Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e3be-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="0e3be-539">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0e3be-540">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="0e3be-541">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="0e3be-541">Set the preflight expiration time</span></span>

<span data-ttu-id="0e3be-542">`Access-Control-Max-Age`Záhlaví určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="0e3be-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="0e3be-543">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="0e3be-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="0e3be-544">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="0e3be-544">How CORS works</span></span>

<span data-ttu-id="0e3be-545">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="0e3be-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="0e3be-546">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="0e3be-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="0e3be-547">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="0e3be-548">Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="0e3be-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="0e3be-549">Vaše rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="0e3be-550">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="0e3be-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="0e3be-551">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="0e3be-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="0e3be-552">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="0e3be-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="0e3be-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="0e3be-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="0e3be-554">Postman</span><span class="sxs-lookup"><span data-stu-id="0e3be-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="0e3be-555">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="0e3be-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="0e3be-556">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="0e3be-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="0e3be-557">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="0e3be-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="0e3be-558">Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="0e3be-559">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="0e3be-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="0e3be-560">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e3be-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="0e3be-561">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="0e3be-562">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0e3be-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="0e3be-563">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="0e3be-564">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="0e3be-565">Následuje příklad žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="0e3be-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="0e3be-566">`Origin`Hlavička poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="0e3be-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="0e3be-567">`Origin`Hlavička je povinná a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="0e3be-567">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="0e3be-568">Pokud server tuto žádost povoluje, nastaví `Access-Control-Allow-Origin` hlavičku v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="0e3be-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="0e3be-569">Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku nebo se jedná o zástupnou hodnotu `"*"` , což znamená, že je povolen libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="0e3be-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="0e3be-570">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="0e3be-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="0e3be-571">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="0e3be-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="0e3be-572">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e3be-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="0e3be-573">Test CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-573">Test CORS</span></span>

<span data-ttu-id="0e3be-574">Testování CORS:</span><span class="sxs-lookup"><span data-stu-id="0e3be-574">To test CORS:</span></span>

1. <span data-ttu-id="0e3be-575">[Vytvořte projekt API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="0e3be-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="0e3be-576">Alternativně si můžete [Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="0e3be-577">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="0e3be-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="0e3be-578">Například:</span><span class="sxs-lookup"><span data-stu-id="0e3be-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="0e3be-579">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="0e3be-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="0e3be-580">Vytvořte projekt webové aplikace ( Razor stránky nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="0e3be-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="0e3be-581">Ukázka používá Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="0e3be-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="0e3be-582">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0e3be-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="0e3be-583">Do souboru *index. cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="0e3be-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="0e3be-584">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresu URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e3be-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="0e3be-585">Nasaďte projekt API.</span><span class="sxs-lookup"><span data-stu-id="0e3be-585">Deploy the API project.</span></span> <span data-ttu-id="0e3be-586">Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="0e3be-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="0e3be-587">Spusťte Razor stránky nebo aplikaci MVC z plochy a klikněte na tlačítko **test** .</span><span class="sxs-lookup"><span data-stu-id="0e3be-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="0e3be-588">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="0e3be-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="0e3be-589">Odeberte původ localhost z `WithOrigins` a nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e3be-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="0e3be-590">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="0e3be-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="0e3be-591">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0e3be-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="0e3be-592">Otestujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="0e3be-592">Test with the client app.</span></span> <span data-ttu-id="0e3be-593">Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0e3be-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="0e3be-594">K zobrazení chyby použijte kartu konzola v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="0e3be-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="0e3be-595">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="0e3be-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="0e3be-596">Používání Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="0e3be-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="0e3be-597">**SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="0e3be-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="0e3be-598">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="0e3be-598">Using Chrome:</span></span>

     <span data-ttu-id="0e3be-599">**`https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` Zásada CORS zablokovala přístup k XMLHttpRequest od původu: v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**</span><span class="sxs-lookup"><span data-stu-id="0e3be-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="0e3be-600">Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="0e3be-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="0e3be-601">Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="0e3be-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="0e3be-602">Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="0e3be-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="0e3be-603">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="0e3be-604">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="0e3be-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="0e3be-605">CORS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="0e3be-605">CORS in IIS</span></span>

<span data-ttu-id="0e3be-606">Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="0e3be-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="0e3be-607">Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0e3be-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e3be-608">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e3be-608">Additional resources</span></span>

* [<span data-ttu-id="0e3be-609">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="0e3be-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="0e3be-610">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="0e3be-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
