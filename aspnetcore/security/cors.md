---
title: Povolit žádosti mezi zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak CORS jako standard pro povolení nebo odmítnutí žádostí o více zdrojů v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773808"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="b2859-103">Povolit žádosti mezi zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2859-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2859-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b2859-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="b2859-105">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b2859-106">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="b2859-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b2859-107">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="b2859-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b2859-108">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="b2859-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b2859-109">V některých případech můžete chtít umožnit jiným webům, aby vaši aplikaci mohli vytvářet žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b2859-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="b2859-110">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b2859-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b2859-111">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b2859-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b2859-112">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="b2859-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b2859-113">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="b2859-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b2859-114">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b2859-115">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b2859-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b2859-116">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="b2859-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b2859-117">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="b2859-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b2859-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b2859-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b2859-119">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="b2859-119">Same origin</span></span>

<span data-ttu-id="b2859-120">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b2859-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b2859-121">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="b2859-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b2859-122">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="b2859-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b2859-123">`https://example.net`&ndash; Jiná doména</span><span class="sxs-lookup"><span data-stu-id="b2859-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="b2859-124">`https://www.example.com/foo.html`&ndash; Jiná subdoména</span><span class="sxs-lookup"><span data-stu-id="b2859-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="b2859-125">`http://example.com/foo.html`&ndash; Jiné schéma</span><span class="sxs-lookup"><span data-stu-id="b2859-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="b2859-126">`https://example.com:9000/foo.html`&ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="b2859-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="b2859-127">Povolení CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-127">Enable CORS</span></span>

<span data-ttu-id="b2859-128">Existují tři způsoby, jak povolit CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="b2859-129">V middlewaru pomocí [pojmenované zásady](#np) nebo [výchozí zásady](#dp).</span><span class="sxs-lookup"><span data-stu-id="b2859-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="b2859-130">Použití [Směrování koncového bodu](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b2859-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="b2859-131">S atributem [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="b2859-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="b2859-132">Použití atributu [[EnableCors]](#attr) s pojmenovanou zásadou poskytuje ovládací prvek nejlepší v omezení koncových bodů, které podporují CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="b2859-133">Každý přístup je podrobně popsaný v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="b2859-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b2859-134">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="b2859-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="b2859-135">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b2859-136">Následující kód aplikuje zásadu CORS na všechny koncové body aplikace se zadanými zdroji:</span><span class="sxs-lookup"><span data-stu-id="b2859-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="b2859-137">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="b2859-137">The preceding code:</span></span>

* <span data-ttu-id="b2859-138">Nastaví název zásady na `_myAllowSpecificOrigins`.</span><span class="sxs-lookup"><span data-stu-id="b2859-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="b2859-139">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="b2859-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b2859-140">Zavolá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření a určí zásadu `_myAllowSpecificOrigins` CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="b2859-141">`UseCors`Přidá middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="b2859-142">Volání `UseCors` musí být umístěno po `UseRouting`, ale před. `UseAuthorization`</span><span class="sxs-lookup"><span data-stu-id="b2859-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="b2859-143">Další informace najdete v tématu [pořadí middlewaru](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="b2859-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="b2859-144">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="b2859-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b2859-145">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="b2859-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b2859-146">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="b2859-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="b2859-147">Povolí zásadu `_myAllowSpecificOrigins` CORS pro všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="b2859-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="b2859-148">Pokud chcete použít zásadu CORS na konkrétní koncové body, podívejte se na téma [Směrování koncového bodu](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="b2859-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="b2859-149">Při směrování koncových bodů ***musí*** být MIDDLEWARe CORS nakonfigurované tak, aby se `UseRouting` spustilo mezi voláními a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="b2859-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="b2859-150">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="b2859-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="b2859-151">Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="b2859-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b2859-152">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b2859-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="b2859-153"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metody mohou být zřetězeny, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="b2859-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="b2859-154">Poznámka: Zadaná adresa URL nesmí **obsahovat koncové** lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="b2859-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b2859-155">Pokud adresa URL končí `/`, porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b2859-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="b2859-156">CORS s výchozími zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="b2859-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="b2859-157">Následující zvýrazněný kód umožňuje výchozí zásadu CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="b2859-158">Předchozí kód aplikuje výchozí zásadu CORS na všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="b2859-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="b2859-159">Povolení CORS s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="b2859-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="b2859-160">Povolení CORS na základě jednotlivých koncových bodů pomocí `RequireCors` v současné ***době*** nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="b2859-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="b2859-161">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/20709) a [testování CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="b2859-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="b2859-162">S směrováním koncových bodů je možné CORS povolit na základě jednotlivých koncových bodů <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> pomocí sady rozšiřujících metod:</span><span class="sxs-lookup"><span data-stu-id="b2859-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="b2859-163">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="b2859-163">In the preceding code:</span></span>

* <span data-ttu-id="b2859-164">`app.UseCors`povoluje middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="b2859-165">Vzhledem k tomu, že výchozí zásady nejsou `app.UseCors()` nakonfigurované, nepovolí CORS samotné.</span><span class="sxs-lookup"><span data-stu-id="b2859-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="b2859-166">Koncové `/echo` body řadiče a umožňují žádosti mezi zdroji pomocí zadaných zásad.</span><span class="sxs-lookup"><span data-stu-id="b2859-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="b2859-167">Koncové `/echo2` body Razor stránky a ***nepovolují požadavky*** mezi zdroji, protože nebyly zadány žádné výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="b2859-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="b2859-168">Atribut [[DisableCors]](#dc) ***nevypne CORS*** , která byla povolená směrováním koncových bodů `RequireCors`s.</span><span class="sxs-lookup"><span data-stu-id="b2859-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="b2859-169">Pokyny k testování kódu podobného předchozímu najdete v tématu [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="b2859-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b2859-170">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="b2859-170">Enable CORS with attributes</span></span>

<span data-ttu-id="b2859-171">Povolení CORS s atributem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) a použití pojmenované zásady pouze na koncové body, které vyžadují CORS, poskytuje ovládací prvek nejlepší.</span><span class="sxs-lookup"><span data-stu-id="b2859-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="b2859-172">Atribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="b2859-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b2859-173">`[EnableCors]` Atribut umožňuje CORS pro vybrané koncové body místo všech koncových bodů:</span><span class="sxs-lookup"><span data-stu-id="b2859-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="b2859-174">`[EnableCors]`Určuje výchozí zásadu.</span><span class="sxs-lookup"><span data-stu-id="b2859-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="b2859-175">`[EnableCors("{Policy String}")]`Určuje pojmenovanou zásadu.</span><span class="sxs-lookup"><span data-stu-id="b2859-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="b2859-176">`[EnableCors]` Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="b2859-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="b2859-177">Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="b2859-177"> Page `PageModel`</span></span>
* <span data-ttu-id="b2859-178">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="b2859-178">Controller</span></span>
* <span data-ttu-id="b2859-179">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="b2859-179">Controller action method</span></span>

<span data-ttu-id="b2859-180">U řadičů, modelů stránek nebo metod akcí lze použít různé zásady s `[EnableCors]` atributem.</span><span class="sxs-lookup"><span data-stu-id="b2859-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="b2859-181">Pokud je `[EnableCors]` atribut použit na řadič, model stránky nebo metodu akce a CORS je v middleware povolená, uplatní se ***obě*** zásady.</span><span class="sxs-lookup"><span data-stu-id="b2859-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="b2859-182">Doporučujeme ***před kombinováním zásad. Použijte*** atribut ***nebo middleware, nikoli oba ve stejné aplikaci.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="b2859-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="b2859-183">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="b2859-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b2859-184">Následující kód vytvoří dvě zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="b2859-185">Pro ovládací prvek nejlepší pro omezení požadavků CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="b2859-186">Použijte `[EnableCors("MyPolicy")]` s pojmenovanou zásadou.</span><span class="sxs-lookup"><span data-stu-id="b2859-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="b2859-187">Nedefinujte výchozí zásady.</span><span class="sxs-lookup"><span data-stu-id="b2859-187">Don't define a default policy.</span></span>
* <span data-ttu-id="b2859-188">Nepoužívejte [Směrování koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b2859-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="b2859-189">Kód v další části splňuje předchozí seznam.</span><span class="sxs-lookup"><span data-stu-id="b2859-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="b2859-190">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="b2859-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="b2859-191">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-191">Disable CORS</span></span>

<span data-ttu-id="b2859-192">Atribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***zakáže rozhraní*** CORS, které bylo povoleno [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b2859-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="b2859-193">Následující kód definuje zásadu `"MyPolicy"`CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="b2859-194">Následující kód zakáže CORS pro `GetValues2` akci:</span><span class="sxs-lookup"><span data-stu-id="b2859-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="b2859-195">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="b2859-195">The preceding code:</span></span>

* <span data-ttu-id="b2859-196">Nepovoluje CORS s [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b2859-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="b2859-197">Nedefinuje [výchozí zásadu CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="b2859-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="b2859-198">Pro povolení zásad `"MyPolicy"` CORS pro kontroler používá [[EnableCors ("MyPolicy")]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="b2859-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="b2859-199">Zakáže CORS pro `GetValues2` metodu.</span><span class="sxs-lookup"><span data-stu-id="b2859-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="b2859-200">Pokyny k testování předchozího kódu najdete v části [test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="b2859-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b2859-201">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-201">CORS policy options</span></span>

<span data-ttu-id="b2859-202">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b2859-203">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="b2859-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b2859-204">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="b2859-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b2859-205">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="b2859-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b2859-206">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="b2859-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b2859-207">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b2859-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b2859-208">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="b2859-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b2859-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b2859-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b2859-210">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="b2859-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b2859-211">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="b2859-211">Set the allowed origins</span></span>

<span data-ttu-id="b2859-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem`http` ( `https`nebo).</span><span class="sxs-lookup"><span data-stu-id="b2859-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b2859-213">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b2859-214">Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="b2859-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b2859-215">Služba CORS vrátí neplatnou odpověď CORS, pokud je aplikace nakonfigurovaná pomocí obou metod.</span><span class="sxs-lookup"><span data-stu-id="b2859-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="b2859-216">`AllowAnyOrigin`má vliv na požadavky na `Access-Control-Allow-Origin` kontrolu a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b2859-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b2859-217">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b2859-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b2859-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady na funkci, která umožňuje, aby se při vyhodnocování, jestli je původ povolený, shodovaly s konfigurovanou doménou se zástupnými znaky.</span><span class="sxs-lookup"><span data-stu-id="b2859-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b2859-219">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="b2859-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b2859-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b2859-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b2859-221">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="b2859-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="b2859-222">Má vliv na požadavky na `Access-Control-Allow-Methods` kontrolu a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b2859-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b2859-223">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b2859-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b2859-224">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="b2859-224">Set the allowed request headers</span></span>

<span data-ttu-id="b2859-225">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem [záhlaví žádosti o autora](https://xhr.spec.whatwg.org/#request), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="b2859-226">Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="b2859-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="b2859-227">`AllowAnyHeader`má vliv na žádosti o předběžné kontroly a hlavičku [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="b2859-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="b2859-228">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b2859-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b2859-229">Zásada middlewaru CORS odpovídající konkrétním hlavičkám, které `WithHeaders` určuje, je možná jenom v případě, `Access-Control-Request-Headers` že se záhlaví poslala přesně `WithHeaders`a odpovídají hlavičkám uvedeným v.</span><span class="sxs-lookup"><span data-stu-id="b2859-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="b2859-230">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="b2859-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="b2859-231">Middleware CORS odmítá požadavek na předběžné kontroly s následující hlavičkou `Content-Language` požadavku, protože ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) `WithHeaders`není uvedená v tomto seznamu:</span><span class="sxs-lookup"><span data-stu-id="b2859-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="b2859-232">Aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="b2859-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b2859-233">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b2859-234">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="b2859-234">Set the exposed response headers</span></span>

<span data-ttu-id="b2859-235">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2859-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b2859-236">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b2859-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b2859-237">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="b2859-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b2859-238">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="b2859-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="b2859-239">Pokud chcete, aby byla aplikace k dispozici ostatním <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hlavičkám, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b2859-240">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b2859-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b2859-241">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b2859-242">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b2859-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b2859-243">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2859-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="b2859-244">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být `XMLHttpRequest.withCredentials` klient `true`nastaven na.</span><span class="sxs-lookup"><span data-stu-id="b2859-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b2859-245">Přímé `XMLHttpRequest` použití:</span><span class="sxs-lookup"><span data-stu-id="b2859-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b2859-246">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="b2859-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b2859-247">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="b2859-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b2859-248">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="b2859-248">The server must allow the credentials.</span></span> <span data-ttu-id="b2859-249">Pokud chcete povolení přihlašovacích údajů mezi zdroji <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="b2859-250">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b2859-251">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="b2859-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b2859-252">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="b2859-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b2859-253">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="b2859-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b2859-254">Specifikace CORS také uvádí, že pokud je `"*"` `Access-Control-Allow-Credentials` hlavička k dispozici, nastavení původu na (všechny zdroje) je neplatné.</span><span class="sxs-lookup"><span data-stu-id="b2859-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="b2859-255">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="b2859-255">Preflight requests</span></span>

<span data-ttu-id="b2859-256">U některých požadavků CORS prohlížeč před provedením samotné žádosti pošle žádost o další [Možnosti](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="b2859-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="b2859-257">Tento požadavek se nazývá [žádost o kontrolu před výstupem](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="b2859-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="b2859-258">Prohlížeč může přeskočit požadavek na kontrolu před výstupem, pokud jsou splněné ***všechny*** následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="b2859-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="b2859-259">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="b2859-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b2859-260">Aplikace nenastaví záhlaví `Accept`žádostí s výjimkou, `Accept-Language`, `Content-Language` `Content-Type`, nebo. `Last-Event-ID`</span><span class="sxs-lookup"><span data-stu-id="b2859-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b2859-261">`Content-Type` Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="b2859-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b2859-262">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt.</span><span class="sxs-lookup"><span data-stu-id="b2859-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b2859-263">Specifikace CORS volá [záhlaví požadavku autora](https://www.w3.org/TR/cors/#author-request-headers)záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b2859-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="b2859-264">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent`, `Host`nebo. `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="b2859-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b2859-265">Následuje příklad odpovědi, která se podobá žádosti o kontrolu před výstupem vytvořeným tlačítkem **[PUT test]** v části [test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b2859-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="b2859-266">Požadavek na kontrolu před výstupem používá metodu [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="b2859-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="b2859-267">Může obsahovat následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-267">It may include the following headers:</span></span>

* <span data-ttu-id="b2859-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): metoda HTTP, která bude použita pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="b2859-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b2859-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="b2859-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b2859-270">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="b2859-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="b2859-271">Přístup-řízení-povolení-metody</span><span class="sxs-lookup"><span data-stu-id="b2859-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="b2859-272">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí `200 OK` odpověď, ale nenastaví hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="b2859-273">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="b2859-274">Příklad Zamítnuté žádosti o kontrolu před výstupem najdete v části [test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b2859-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="b2859-275">Pomocí nástrojů F12 zobrazuje aplikace konzoly chybu podobnou jedné z následujících v závislosti na prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="b2859-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="b2859-276">Firefox: požadavek mezi zdroji je zablokován: stejné zásady původu nepovolují čtení vzdáleného prostředku v `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span><span class="sxs-lookup"><span data-stu-id="b2859-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="b2859-277">(Důvod: požadavek CORS se nezdařil)</span><span class="sxs-lookup"><span data-stu-id="b2859-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="b2859-278">Další informace</span><span class="sxs-lookup"><span data-stu-id="b2859-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="b2859-279">Chromová: přístup k načtení v 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' od původu 'https://cors3.azurewebsites.net' byl zablokován zásadou CORS: odpověď na požadavek na kontrolu před výstupem neprojde kontrolou řízení přístupu: v požadovaném prostředku není k dispozici hlavička ' Access-Control-Allow-Origin '.</span><span class="sxs-lookup"><span data-stu-id="b2859-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="b2859-280">Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="b2859-281">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="b2859-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="b2859-282">Pokud chcete povolení všech [hlaviček žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="b2859-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="b2859-283">Prohlížeče nejsou konzistentní v tom, jak `Access-Control-Request-Headers`jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="b2859-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b2859-284">Pokud buď:</span><span class="sxs-lookup"><span data-stu-id="b2859-284">If either:</span></span>

* <span data-ttu-id="b2859-285">Záhlaví jsou nastavena na jinou hodnotu než`"*"`</span><span class="sxs-lookup"><span data-stu-id="b2859-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="b2859-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se nazývá: uveďte aspoň `Accept`, `Content-Type`a a `Origin`a všechny vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="b2859-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="b2859-287">Automatický kód žádosti o kontrolu</span><span class="sxs-lookup"><span data-stu-id="b2859-287">Automatic preflight request code</span></span>

<span data-ttu-id="b2859-288">Když se aplikuje zásada CORS, proveďte jednu z těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="b2859-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="b2859-289">Globálně voláním `app.UseCors` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b2859-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="b2859-290">Pomocí `[EnableCors]` atributu.</span><span class="sxs-lookup"><span data-stu-id="b2859-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="b2859-291">ASP.NET Core reaguje na požadavek na předběžné možnosti.</span><span class="sxs-lookup"><span data-stu-id="b2859-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="b2859-292">Povolení CORS na základě jednotlivých koncových bodů pomocí `RequireCors` v současné ***době nepodporuje automatické*** požadavky na kontrolu před výstupem.</span><span class="sxs-lookup"><span data-stu-id="b2859-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="b2859-293">V části [test CORS](#testc) tohoto dokumentu se toto chování demonstruje.</span><span class="sxs-lookup"><span data-stu-id="b2859-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="b2859-294">[HttpOptions] atribut pro žádosti o kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="b2859-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="b2859-295">Když je v rámci příslušné zásady povolená CORS, ASP.NET Core obecně reaguje na požadavky na předběžné kontroly CORS automaticky.</span><span class="sxs-lookup"><span data-stu-id="b2859-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="b2859-296">V některých scénářích to nemusí být případ.</span><span class="sxs-lookup"><span data-stu-id="b2859-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="b2859-297">Například použití [CORS se směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="b2859-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="b2859-298">Následující kód používá atribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) k vytvoření koncových bodů pro žádosti o možnosti:</span><span class="sxs-lookup"><span data-stu-id="b2859-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="b2859-299">Pokyny k testování předchozího kódu najdete v článku [test CORS pomocí směrování koncových bodů a [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="b2859-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b2859-300">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="b2859-300">Set the preflight expiration time</span></span>

<span data-ttu-id="b2859-301">Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b2859-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b2859-302">Chcete-li nastavit tuto hlavičku <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b2859-303">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="b2859-303">How CORS works</span></span>

<span data-ttu-id="b2859-304">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2859-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b2859-305">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b2859-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="b2859-306">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="b2859-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b2859-307">Škodlivý objekt actor může například použít [skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s POVOLENým CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="b2859-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b2859-308">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="b2859-309">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="b2859-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b2859-310">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="b2859-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b2859-311">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="b2859-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b2859-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b2859-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b2859-313">Postman</span><span class="sxs-lookup"><span data-stu-id="b2859-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b2859-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="b2859-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b2859-315">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="b2859-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b2859-316">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="b2859-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b2859-317">Prohlížeče bez CORS nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="b2859-318">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="b2859-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b2859-319">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b2859-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b2859-320">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b2859-321">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b2859-322">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="b2859-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b2859-323">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b2859-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b2859-324">[Tlačítko Vložit test](https://cors3.azurewebsites.net/test) na nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="b2859-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="b2859-325">Následuje příklad žádosti o více zdrojů z tlačítka Test [hodnot](https://cors3.azurewebsites.net/) na `https://cors1.azurewebsites.net/api/values`.</span><span class="sxs-lookup"><span data-stu-id="b2859-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="b2859-326">`Origin` Hlavička:</span><span class="sxs-lookup"><span data-stu-id="b2859-326">The `Origin` header:</span></span>

* <span data-ttu-id="b2859-327">Poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="b2859-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="b2859-328">Je vyžadováno a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2859-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="b2859-329">**Obecné hlavičky**</span><span class="sxs-lookup"><span data-stu-id="b2859-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="b2859-330">**Hlavičky odpovědi**</span><span class="sxs-lookup"><span data-stu-id="b2859-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="b2859-331">**Hlavičky požadavku**</span><span class="sxs-lookup"><span data-stu-id="b2859-331">**Request headers**</span></span>

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

<span data-ttu-id="b2859-332">V `OPTIONS` části požadavky Server nastaví hlavičku **hlaviček** `Access-Control-Allow-Origin: {allowed origin}` odpovědi v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b2859-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="b2859-333">Například nasazená [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [DELETE [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` obsahuje následující hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="b2859-334">**Obecné hlavičky**</span><span class="sxs-lookup"><span data-stu-id="b2859-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="b2859-335">**Hlavičky odpovědi**</span><span class="sxs-lookup"><span data-stu-id="b2859-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="b2859-336">**Hlavičky požadavku**</span><span class="sxs-lookup"><span data-stu-id="b2859-336">**Request headers**</span></span>

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

<span data-ttu-id="b2859-337">V předchozích **hlavičkách odpovědi**Server nastaví hlavičku [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b2859-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="b2859-338">`https://cors1.azurewebsites.net` Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku.</span><span class="sxs-lookup"><span data-stu-id="b2859-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="b2859-339">Pokud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> je volána, `Access-Control-Allow-Origin: *`je vrácena hodnota zástupného znaku.</span><span class="sxs-lookup"><span data-stu-id="b2859-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="b2859-340">`AllowAnyOrigin`umožňuje jakýkoli původ.</span><span class="sxs-lookup"><span data-stu-id="b2859-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="b2859-341">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="b2859-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b2859-342">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="b2859-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b2859-343">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2859-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="b2859-344">Zobrazit žádosti o možnosti</span><span class="sxs-lookup"><span data-stu-id="b2859-344">Display OPTIONS requests</span></span>

<span data-ttu-id="b2859-345">Ve výchozím nastavení prohlížeče Chrome a Edge nezobrazují žádosti o možnosti na kartě síť nástrojů F12.</span><span class="sxs-lookup"><span data-stu-id="b2859-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="b2859-346">Chcete-li zobrazit žádosti o možnosti v těchto prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="b2859-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="b2859-347">`chrome://flags/#out-of-blink-cors` nebo `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="b2859-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="b2859-348">zakažte příznak.</span><span class="sxs-lookup"><span data-stu-id="b2859-348">disable the flag.</span></span>
* <span data-ttu-id="b2859-349">restart (restartovat).</span><span class="sxs-lookup"><span data-stu-id="b2859-349">restart.</span></span>

<span data-ttu-id="b2859-350">Firefox ve výchozím nastavení zobrazuje požadavky na možnosti.</span><span class="sxs-lookup"><span data-stu-id="b2859-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="b2859-351">CORS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="b2859-351">CORS in IIS</span></span>

<span data-ttu-id="b2859-352">Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="b2859-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="b2859-353">Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2859-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="b2859-354">Test CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-354">Test CORS</span></span>

<span data-ttu-id="b2859-355">[Ukázka stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) obsahuje kód pro testování CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="b2859-356">Viz [Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b2859-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="b2859-357">Ukázka je projekt rozhraní API se Razor stránkami přidanými:</span><span class="sxs-lookup"><span data-stu-id="b2859-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="b2859-358">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b2859-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="b2859-359">Následující příklad `ValuesController` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="b2859-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="b2859-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) je k dispozici v balíčku NuGet [Rick. Docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) a zobrazí informace o trasách.</span><span class="sxs-lookup"><span data-stu-id="b2859-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="b2859-361">Otestujte předchozí vzorový kód pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b2859-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="b2859-362">Použijte nasazenou ukázkovou aplikaci [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)na adrese.</span><span class="sxs-lookup"><span data-stu-id="b2859-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="b2859-363">Není nutné stahovat ukázku.</span><span class="sxs-lookup"><span data-stu-id="b2859-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="b2859-364">Spusťte ukázku s `dotnet run` použitím výchozí adresy URL `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="b2859-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="b2859-365">Spusťte ukázku ze sady Visual Studio s portem nastaveným na 44398 pro adresu URL `https://localhost:44398`.</span><span class="sxs-lookup"><span data-stu-id="b2859-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="b2859-366">Použití prohlížeče s nástroji F12:</span><span class="sxs-lookup"><span data-stu-id="b2859-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="b2859-367">Vyberte tlačítko **hodnoty** a zkontrolujte hlavičky na kartě **síť** .</span><span class="sxs-lookup"><span data-stu-id="b2859-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="b2859-368">Vyberte tlačítko **Vložit test** .</span><span class="sxs-lookup"><span data-stu-id="b2859-368">Select the **PUT test** button.</span></span> <span data-ttu-id="b2859-369">Pokyny k zobrazení žádosti o možnosti najdete v tématu [Možnosti zobrazení žádosti](#options) .</span><span class="sxs-lookup"><span data-stu-id="b2859-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="b2859-370">**Test Put** vytvoří dvě požadavky, požadavek na kontrolu před výstupem a požadavek PUT.</span><span class="sxs-lookup"><span data-stu-id="b2859-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="b2859-371">Kliknutím na **`GetValues2 [DisableCors]`** tlačítko aktivujte neúspěšnou žádost CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="b2859-372">Jak je uvedeno v dokumentu, odpověď vrátí 200 úspěch, ale požadavek CORS se neprovádí.</span><span class="sxs-lookup"><span data-stu-id="b2859-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="b2859-373">Kliknutím na kartu **Konzola** zobrazíte chybu CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="b2859-374">V závislosti na prohlížeči se zobrazí chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="b2859-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="b2859-375">Zásada CORS zablokovala přístup k načtení `'https://cors1.azurewebsites.net/api/values/GetValues2'` od původu `'https://cors3.azurewebsites.net'` : v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.</span><span class="sxs-lookup"><span data-stu-id="b2859-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="b2859-376">Pokud neprůhledná odpověď vyhovuje vašim potřebám, nastavte režim žádosti na No-CORS, aby bylo možné načíst prostředek s zakázáním CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="b2859-377">Koncové body s podporou CORS je možné testovat pomocí nástroje, jako je například [kudrlinkou](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="b2859-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b2859-378">Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="b2859-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b2859-379">Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b2859-380">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b2859-381">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="b2859-382">Následující příkaz slouží `curl` k vystavení žádosti o možnosti s informacemi:</span><span class="sxs-lookup"><span data-stu-id="b2859-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="b2859-383">Testování CORS pomocí směrování koncových bodů a [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="b2859-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="b2859-384">Povolení CORS na základě jednotlivých koncových bodů pomocí `RequireCors` v současné ***době*** nepodporuje [Automatické požadavky na kontrolu před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="b2859-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="b2859-385">Vezměte v úvahu následující kód, který pomocí [Směrování koncových bodů povoluje CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="b2859-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="b2859-386">Následující příklad `TodoItems1Controller` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="b2859-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="b2859-387">Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=1) v nasazené [ukázce](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="b2859-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="b2859-388">Tlačítka **DELETE [EnableCors]** a **Get [EnableCors]** jsou úspěšná, protože koncové body mají `[EnableCors]` a reagují na požadavky na kontrolu před výstupem.</span><span class="sxs-lookup"><span data-stu-id="b2859-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="b2859-389">Ostatní koncové body selžou.</span><span class="sxs-lookup"><span data-stu-id="b2859-389">The other endpoints fails.</span></span> <span data-ttu-id="b2859-390">Tlačítko **získat** se nepovedlo, protože [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) pošle:</span><span class="sxs-lookup"><span data-stu-id="b2859-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="b2859-391">Následující `TodoItems2Controller` příkaz poskytuje podobné koncové body, ale obsahuje explicitní kód pro reakci na požadavky Options:</span><span class="sxs-lookup"><span data-stu-id="b2859-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="b2859-392">Otestujte předchozí kód ze [stránky test](https://cors1.azurewebsites.net/test?number=2) v nasazené ukázce.</span><span class="sxs-lookup"><span data-stu-id="b2859-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="b2859-393">V rozevíracím seznamu **řadič** vyberte položku **Kontrola před výstupem** a pak **nastavte kontroler**.</span><span class="sxs-lookup"><span data-stu-id="b2859-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="b2859-394">Všechna volání CORS do koncových `TodoItems2Controller` bodů jsou úspěšná.</span><span class="sxs-lookup"><span data-stu-id="b2859-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2859-395">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b2859-395">Additional resources</span></span>

* [<span data-ttu-id="b2859-396">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="b2859-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="b2859-397">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2859-398">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2859-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b2859-399">Tento článek popisuje, jak v aplikaci ASP.NET Core povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b2859-400">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="b2859-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b2859-401">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="b2859-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b2859-402">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="b2859-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b2859-403">V některých případech můžete chtít, aby ostatní weby ve vaší aplikaci provedly žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b2859-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="b2859-404">Další informace najdete v článku věnovaném [Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b2859-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b2859-405">[Sdílení prostředků mezi zdroji](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b2859-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b2859-406">Je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="b2859-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b2859-407">Nejedná **se o funkci** zabezpečení, CORS zabezpečení CORS zmírnit.</span><span class="sxs-lookup"><span data-stu-id="b2859-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b2859-408">Rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b2859-409">Další informace najdete v tématu [jak CORS funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b2859-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b2859-410">Umožňuje serveru explicitně povolit některé žádosti mezi zdroji a současně odmítat jiné.</span><span class="sxs-lookup"><span data-stu-id="b2859-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b2859-411">Je bezpečnější a pružnější než u předchozích technik, jako třeba [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="b2859-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b2859-412">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b2859-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b2859-413">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="b2859-413">Same origin</span></span>

<span data-ttu-id="b2859-414">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b2859-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b2859-415">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="b2859-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b2859-416">Tyto adresy URL mají různé zdroje, než jsou předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="b2859-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b2859-417">`https://example.net`&ndash; Jiná doména</span><span class="sxs-lookup"><span data-stu-id="b2859-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="b2859-418">`https://www.example.com/foo.html`&ndash; Jiná subdoména</span><span class="sxs-lookup"><span data-stu-id="b2859-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="b2859-419">`http://example.com/foo.html`&ndash; Jiné schéma</span><span class="sxs-lookup"><span data-stu-id="b2859-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="b2859-420">`https://example.com:9000/foo.html`&ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="b2859-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="b2859-421">Internet Explorer při porovnávání zdrojů nebere v úvahu port.</span><span class="sxs-lookup"><span data-stu-id="b2859-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b2859-422">CORS s pojmenovanými zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="b2859-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="b2859-423">Middleware CORS zpracovává požadavky mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b2859-424">Následující kód umožňuje CORS pro celou aplikaci se zadaným počátkem:</span><span class="sxs-lookup"><span data-stu-id="b2859-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="b2859-425">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="b2859-425">The preceding code:</span></span>

* <span data-ttu-id="b2859-426">Nastaví název zásady na "\_myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="b2859-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="b2859-427">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="b2859-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b2859-428">Volá metodu <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> rozšíření, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="b2859-429">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [výrazem lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="b2859-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b2859-430">Lambda převezme <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objekt.</span><span class="sxs-lookup"><span data-stu-id="b2859-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b2859-431">[Možnosti konfigurace](#cors-policy-options), například `WithOrigins`, jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="b2859-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="b2859-432">Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služby aplikace:</span><span class="sxs-lookup"><span data-stu-id="b2859-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b2859-433">Další informace najdete v tématu [Možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b2859-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="b2859-434"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Metoda může řetězit metody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="b2859-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="b2859-435">Poznámka: adresa URL nesmí **obsahovat koncové** lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="b2859-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b2859-436">Pokud adresa URL končí `/`, porovnávání se vrátí `false` a nevrátí se žádné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b2859-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="b2859-437">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím middlewaru CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="b2859-438">Poznámka: `UseCors` musí být volána před `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="b2859-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="b2859-439">Viz [Povolení CORS v Razor rámci stránek, řadičů a metod akcí](#ecors) pro aplikování zásad CORS na úrovni stránky, řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="b2859-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="b2859-440">Pokyny k testování kódu podobného předchozímu kódu naleznete v tématu [test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="b2859-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b2859-441">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="b2859-441">Enable CORS with attributes</span></span>

<span data-ttu-id="b2859-442">Atribut [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) představuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="b2859-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b2859-443">`[EnableCors]` Atribut povoluje CORS pro vybrané koncové body místo všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b2859-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="b2859-444">Slouží `[EnableCors]` k zadání výchozích zásad a `[EnableCors("{Policy String}")]` k určení zásad.</span><span class="sxs-lookup"><span data-stu-id="b2859-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="b2859-445">`[EnableCors]` Atribut lze použít pro:</span><span class="sxs-lookup"><span data-stu-id="b2859-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="b2859-446">Page`PageModel`</span><span class="sxs-lookup"><span data-stu-id="b2859-446"> Page `PageModel`</span></span>
* <span data-ttu-id="b2859-447">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="b2859-447">Controller</span></span>
* <span data-ttu-id="b2859-448">Metoda akce kontroleru</span><span class="sxs-lookup"><span data-stu-id="b2859-448">Controller action method</span></span>

<span data-ttu-id="b2859-449">S `[EnableCors]` atributem lze použít různé zásady pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="b2859-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="b2859-450">Pokud je `[EnableCors]` atribut použit pro řadiče/stránky modelu/akce a v middlewaru je povoleno CORS, jsou ***obě*** zásady aplikovány.</span><span class="sxs-lookup"><span data-stu-id="b2859-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="b2859-451">***Doporučujeme, abyste nekombinují zásady*** .</span><span class="sxs-lookup"><span data-stu-id="b2859-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="b2859-452">Použijte `[EnableCors]` atribut nebo middleware, \***ne obojí**.</span><span class="sxs-lookup"><span data-stu-id="b2859-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="b2859-453">Při použití `[EnableCors]`nástroje **nedefinujte výchozí** zásady.</span><span class="sxs-lookup"><span data-stu-id="b2859-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="b2859-454">Následující kód používá pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="b2859-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b2859-455">Následující kód vytvoří výchozí zásadu CORS a zásadu s názvem `"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="b2859-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="b2859-456">Zakázání CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-456">Disable CORS</span></span>

<span data-ttu-id="b2859-457">Atribut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže CORS pro kontroler/Page-model/Action.</span><span class="sxs-lookup"><span data-stu-id="b2859-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b2859-458">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-458">CORS policy options</span></span>

<span data-ttu-id="b2859-459">Tato část popisuje různé možnosti, které je možné nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b2859-460">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="b2859-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b2859-461">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="b2859-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b2859-462">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="b2859-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b2859-463">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="b2859-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b2859-464">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b2859-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b2859-465">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="b2859-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b2859-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je volána v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b2859-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b2859-467">U některých možností může být užitečné si nejdřív přečíst oddíl [jak CORS funguje](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="b2859-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b2859-468">Nastavení povolených zdrojů</span><span class="sxs-lookup"><span data-stu-id="b2859-468">Set the allowed origins</span></span>

<span data-ttu-id="b2859-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Povoluje žádosti CORS ze všech míst původu s jakýmkoli schématem`http` ( `https`nebo).</span><span class="sxs-lookup"><span data-stu-id="b2859-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b2859-470">`AllowAnyOrigin`je nezabezpečené, protože *libovolný web* může do aplikace dělat žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b2859-471">Určení `AllowAnyOrigin` a `AllowCredentials` jedná se o nezabezpečenou konfiguraci a může mít za následek padělání žádostí mezi weby.</span><span class="sxs-lookup"><span data-stu-id="b2859-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b2859-472">V případě zabezpečené aplikace zadejte přesný seznam zdrojů, pokud je klient musí autorizovat pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="b2859-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="b2859-473">`AllowAnyOrigin`má vliv na požadavky na `Access-Control-Allow-Origin` kontrolu a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b2859-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b2859-474">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b2859-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b2859-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady na funkci, která umožňuje, aby se při vyhodnocování, jestli je původ povolený, shodovaly s konfigurovanou doménou se zástupnými znaky.</span><span class="sxs-lookup"><span data-stu-id="b2859-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b2859-476">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="b2859-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b2859-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b2859-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b2859-478">Umožňuje jakoukoli metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="b2859-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="b2859-479">Má vliv na požadavky na `Access-Control-Allow-Methods` kontrolu a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b2859-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b2859-480">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b2859-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b2859-481">Nastavení povolených hlaviček žádosti</span><span class="sxs-lookup"><span data-stu-id="b2859-481">Set the allowed request headers</span></span>

<span data-ttu-id="b2859-482">Pokud chcete povolit odeslání konkrétních hlaviček v žádosti CORS s názvem *záhlaví žádosti o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b2859-483">Pokud chcete povolení všech hlaviček žádostí o autora <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b2859-484">Toto nastavení má vliv na `Access-Control-Request-Headers` žádosti o předběžné kontroly a hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b2859-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="b2859-485">Další informace najdete v části [požadavky na kontrolu před výstupem](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="b2859-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b2859-486">Middleware CORS vždycky povoluje posílání čtyř `Access-Control-Request-Headers` hlaviček v, bez ohledu na hodnoty nakonfigurované v CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="b2859-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="b2859-487">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b2859-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="b2859-488">Představte si například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="b2859-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="b2859-489">Middleware CORS odpoví úspěšně do žádosti o kontrolu před výstupem s následující `Content-Language` hlavičkou požadavku, protože je vždy na seznamu povolených:</span><span class="sxs-lookup"><span data-stu-id="b2859-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b2859-490">Nastavení hlaviček vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="b2859-490">Set the exposed response headers</span></span>

<span data-ttu-id="b2859-491">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2859-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b2859-492">Další informace najdete v tématu [sdílení prostředků mezi zdroji W3C (terminologie): jednoduchá hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b2859-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b2859-493">Ve výchozím nastavení jsou k dispozici následující hlavičky odpovědí:</span><span class="sxs-lookup"><span data-stu-id="b2859-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b2859-494">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědi*.</span><span class="sxs-lookup"><span data-stu-id="b2859-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="b2859-495">Pokud chcete, aby byla aplikace k dispozici ostatním <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hlavičkám, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b2859-496">Přihlašovací údaje v žádostech mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b2859-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b2859-497">Přihlašovací údaje vyžadují zvláštní zpracování v žádosti CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b2859-498">V prohlížeči se ve výchozím nastavení neodesílají přihlašovací údaje s žádostí o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b2859-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b2859-499">Přihlašovací údaje zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2859-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="b2859-500">Aby bylo možné odesílat přihlašovací údaje pomocí žádosti o více zdrojů, musí být `XMLHttpRequest.withCredentials` klient `true`nastaven na.</span><span class="sxs-lookup"><span data-stu-id="b2859-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b2859-501">Přímé `XMLHttpRequest` použití:</span><span class="sxs-lookup"><span data-stu-id="b2859-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b2859-502">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="b2859-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b2859-503">Pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="b2859-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b2859-504">Server musí přihlašovací údaje umožňovat.</span><span class="sxs-lookup"><span data-stu-id="b2859-504">The server must allow the credentials.</span></span> <span data-ttu-id="b2859-505">Pokud chcete povolení přihlašovacích údajů mezi zdroji <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="b2859-506">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` hlavičku, která oznamuje prohlížeči, že server povoluje přihlašovací údaje pro požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b2859-507">Pokud prohlížeč odesílá přihlašovací údaje, ale odpověď neobsahuje platnou `Access-Control-Allow-Credentials` hlavičku, prohlížeč nezveřejňuje odpověď na aplikaci a žádost o více zdrojů se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="b2859-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b2859-508">Povolení přihlašovacích údajů mezi zdroji je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="b2859-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b2859-509">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="b2859-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b2859-510">Specifikace CORS také uvádí, že pokud je `"*"` `Access-Control-Allow-Credentials` hlavička k dispozici, nastavení původu na (všechny zdroje) je neplatné.</span><span class="sxs-lookup"><span data-stu-id="b2859-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="b2859-511">Požadavky na kontrolu před výstupem</span><span class="sxs-lookup"><span data-stu-id="b2859-511">Preflight requests</span></span>

<span data-ttu-id="b2859-512">U některých požadavků CORS prohlížeč před provedením samotného požadavku pošle další požadavek.</span><span class="sxs-lookup"><span data-stu-id="b2859-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="b2859-513">Tento požadavek se nazývá *žádost o kontrolu před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="b2859-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="b2859-514">Prohlížeč může požadavek na předběžné kontroly přeskočit, pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="b2859-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="b2859-515">Metoda Request je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="b2859-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b2859-516">Aplikace nenastaví záhlaví `Accept`žádostí s výjimkou, `Accept-Language`, `Content-Language` `Content-Type`, nebo. `Last-Event-ID`</span><span class="sxs-lookup"><span data-stu-id="b2859-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b2859-517">`Content-Type` Záhlaví, pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="b2859-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b2859-518">Pravidlo pro záhlaví požadavku nastavené pro požadavek klienta se vztahuje na hlavičky, které sada aplikací zavolá `setRequestHeader` na `XMLHttpRequest` objekt.</span><span class="sxs-lookup"><span data-stu-id="b2859-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b2859-519">Specifikace CORS volá *záhlaví požadavku autora*záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b2859-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="b2859-520">Pravidlo se nevztahuje na hlavičky, které může prohlížeč nastavit, například `User-Agent`, `Host`nebo. `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="b2859-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b2859-521">Následuje příklad žádosti o kontrolu před výstupem:</span><span class="sxs-lookup"><span data-stu-id="b2859-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="b2859-522">Požadavek na lety používá metodu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2859-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="b2859-523">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-523">It includes two special headers:</span></span>

* <span data-ttu-id="b2859-524">`Access-Control-Request-Method`: Metoda HTTP, která se bude používat pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="b2859-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b2859-525">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečném požadavku.</span><span class="sxs-lookup"><span data-stu-id="b2859-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b2859-526">Jak bylo uvedeno výše, nezahrnuje hlavičky, které prohlížeč nastavuje, například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="b2859-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="b2859-527">Když je v rámci příslušné zásady povolená CORS, ASP.NET Core všeobecně automaticky reagovat na požadavky CORS v rámci kontroly.</span><span class="sxs-lookup"><span data-stu-id="b2859-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="b2859-528">V [případě požadavků na kontrolu před výstupem se podívejte na atribut [HttpOptions]](#pro).</span><span class="sxs-lookup"><span data-stu-id="b2859-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="b2859-529">Požadavek předběžné kontroly CORS může zahrnovat `Access-Control-Request-Headers` hlavičku, která serveru oznamuje hlavičkám, které jsou odesílány se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="b2859-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="b2859-530">Pro povolení konkrétních hlaviček zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="b2859-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b2859-531">Pokud chcete povolení všech hlaviček žádostí o autora <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b2859-532">Prohlížeče nejsou zcela konzistentní v tom, jak `Access-Control-Request-Headers`nastavily.</span><span class="sxs-lookup"><span data-stu-id="b2859-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b2859-533">Pokud nastavíte záhlaví na jinou hodnotu než `"*"` (nebo použít <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), měli byste zahrnout aspoň `Accept`, `Content-Type`a a `Origin`také libovolné vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="b2859-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="b2859-534">Následuje příklad odpovědi na žádost o kontrolu před výstupem (za předpokladu, že server povoluje požadavek):</span><span class="sxs-lookup"><span data-stu-id="b2859-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="b2859-535">Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která obsahuje seznam povolených metod a volitelně `Access-Control-Allow-Headers` záhlaví, ve kterém jsou uvedeny povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="b2859-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="b2859-536">Pokud je žádost o kontrolu před výstupem úspěšná, prohlížeč pošle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="b2859-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="b2859-537">Pokud je žádost o předběžné kontroly zamítnutá, aplikace vrátí odpověď *200 OK* , ale nepošle hlavičky CORS zpátky.</span><span class="sxs-lookup"><span data-stu-id="b2859-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b2859-538">Proto prohlížeč nezkouší požadavek mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b2859-539">Nastavit čas vypršení platnosti předběžné kontroly</span><span class="sxs-lookup"><span data-stu-id="b2859-539">Set the preflight expiration time</span></span>

<span data-ttu-id="b2859-540">Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek na kontrolu před výstupem ukládána do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b2859-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b2859-541">Chcete-li nastavit tuto hlavičku <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="b2859-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b2859-542">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="b2859-542">How CORS works</span></span>

<span data-ttu-id="b2859-543">Tato část popisuje, co se stane v žádosti [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="b2859-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b2859-544">CORS není **funkce** zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b2859-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="b2859-545">CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="b2859-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b2859-546">Škodlivý objekt actor by například mohl použít možnost [zabránit skriptování mezi weby (XSS)](xref:security/cross-site-scripting) na vašem webu a provést požadavek napříč lokalitami na lokalitu s povoleným CORS, aby mohl ukrást informace.</span><span class="sxs-lookup"><span data-stu-id="b2859-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b2859-547">Vaše rozhraní API není bezpečnější díky povolení CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="b2859-548">Pro vymáhání CORS je to až klient (prohlížeč).</span><span class="sxs-lookup"><span data-stu-id="b2859-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b2859-549">Server požadavek spustí a vrátí odpověď, jedná se o klienta, který vrátí chybu a zablokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="b2859-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b2859-550">Například kterýkoli z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="b2859-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b2859-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b2859-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b2859-552">Postman</span><span class="sxs-lookup"><span data-stu-id="b2859-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b2859-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="b2859-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b2859-554">Webový prohlížeč zadáním adresy URL do panelu Adresa.</span><span class="sxs-lookup"><span data-stu-id="b2859-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b2859-555">To je způsob, jak serveru dovolit prohlížečům spustit [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [načíst požadavek rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro různé zdroje, které by jinak bylo zakázané.</span><span class="sxs-lookup"><span data-stu-id="b2859-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b2859-556">Prohlížeče (bez CORS) nemůžou provádět žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="b2859-557">Před CORS se k obcházení tohoto omezení použil [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) .</span><span class="sxs-lookup"><span data-stu-id="b2859-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b2859-558">JSONP nepoužívá XHR, používá `<script>` značku k přijetí odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b2859-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b2859-559">Skripty mohou být načteny mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b2859-560">[Specifikace CORS](https://www.w3.org/TR/cors/) představila několik nových hlaviček protokolu HTTP, které umožňují žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="b2859-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b2859-561">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky pro žádosti mezi zdroji automaticky.</span><span class="sxs-lookup"><span data-stu-id="b2859-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b2859-562">Pro povolení CORS není nutný vlastní kód JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b2859-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b2859-563">Následuje příklad žádosti o více zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b2859-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="b2859-564">`Origin` Hlavička poskytuje doménu lokality, ze které se vytváří požadavek.</span><span class="sxs-lookup"><span data-stu-id="b2859-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="b2859-565">`Origin` Hlavička je povinná a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="b2859-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="b2859-566">Pokud server tuto žádost povoluje, nastaví `Access-Control-Allow-Origin` hlavičku v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b2859-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="b2859-567">Hodnota této hlavičky se shoduje s `Origin` hlavičkou z požadavku nebo se jedná o zástupnou `"*"`hodnotu, což znamená, že je povolen libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="b2859-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="b2859-568">Pokud odpověď nezahrnuje `Access-Control-Allow-Origin` hlavičku, požadavek na více zdrojů se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="b2859-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b2859-569">Konkrétně prohlížeč požadavek nepovoluje.</span><span class="sxs-lookup"><span data-stu-id="b2859-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b2859-570">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupňuje odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2859-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="b2859-571">Test CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-571">Test CORS</span></span>

<span data-ttu-id="b2859-572">Testování CORS:</span><span class="sxs-lookup"><span data-stu-id="b2859-572">To test CORS:</span></span>

1. <span data-ttu-id="b2859-573">[Vytvořte projekt API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="b2859-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="b2859-574">Alternativně si můžete [Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b2859-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="b2859-575">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b2859-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="b2859-576">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b2859-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="b2859-577">`WithOrigins("https://localhost:<port>");`by se mělo používat jenom pro testování ukázkové aplikace, podobně jako u [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b2859-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="b2859-578">Vytvořte projekt webové aplikace (Razor stránky nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="b2859-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="b2859-579">Ukázka používá Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="b2859-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="b2859-580">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b2859-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="b2859-581">Do souboru *index. cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="b2859-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="b2859-582">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresu URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2859-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="b2859-583">Nasaďte projekt API.</span><span class="sxs-lookup"><span data-stu-id="b2859-583">Deploy the API project.</span></span> <span data-ttu-id="b2859-584">Nasaďte například [do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="b2859-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="b2859-585">Spusťte Razor stránky nebo aplikaci MVC z plochy a klikněte na tlačítko **test** .</span><span class="sxs-lookup"><span data-stu-id="b2859-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="b2859-586">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="b2859-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="b2859-587">Odeberte původ localhost z `WithOrigins` a nasaďte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2859-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="b2859-588">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="b2859-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="b2859-589">Například spusťte ze sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b2859-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="b2859-590">Otestujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="b2859-590">Test with the client app.</span></span> <span data-ttu-id="b2859-591">Chyby CORS vracejí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b2859-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="b2859-592">K zobrazení chyby použijte kartu konzola v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="b2859-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="b2859-593">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12), která je podobná následující:</span><span class="sxs-lookup"><span data-stu-id="b2859-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="b2859-594">Používání Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="b2859-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="b2859-595">**SEC7120: [CORS] původ `https://localhost:44375` nebyl nalezen `https://localhost:44375` v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek pro více zdrojů.`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="b2859-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="b2859-596">Použití Chrome:</span><span class="sxs-lookup"><span data-stu-id="b2859-596">Using Chrome:</span></span>

     <span data-ttu-id="b2859-597">**Zásada CORS zablokovala přístup k XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` od původu `https://localhost:44375` : v požadovaném prostředku není k dispozici hlavička Access-Control-Allow-Origin.**</span><span class="sxs-lookup"><span data-stu-id="b2859-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="b2859-598">Koncové body s podporou CORS se dají testovat pomocí nástroje, jako je [Fiddler](https://www.telerik.com/fiddler) nebo [post](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="b2859-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b2859-599">Při použití nástroje se musí původ požadavku určeného `Origin` hlavičkou lišit od hostitele, který požadavek přijal.</span><span class="sxs-lookup"><span data-stu-id="b2859-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b2859-600">Pokud požadavek není *mezi zdroji* založen na hodnotě `Origin` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="b2859-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b2859-601">Pro zpracování žádosti není nutné middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b2859-602">V odpovědi nejsou vraceny hlavičky CORS.</span><span class="sxs-lookup"><span data-stu-id="b2859-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="b2859-603">CORS ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="b2859-603">CORS in IIS</span></span>

<span data-ttu-id="b2859-604">Při nasazování do služby IIS musí CORS běžet před ověřováním systému Windows, pokud server není nakonfigurovaný tak, aby povoloval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="b2859-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="b2859-605">Pro podporu tohoto scénáře je nutné nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b2859-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2859-606">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b2859-606">Additional resources</span></span>

* [<span data-ttu-id="b2859-607">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="b2859-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="b2859-608">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="b2859-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
