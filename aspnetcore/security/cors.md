---
title: Povolit požadavky na příčný původ (CORS) v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak cors jako standard pro povolení nebo odmítnutí požadavků napříč původem v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642689"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="e518b-103">Povolit požadavky na příčný původ (CORS) v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="e518b-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e518b-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="e518b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="e518b-105">Tento článek ukazuje, jak povolit CORS v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e518b-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e518b-106">Zabezpečení prohlížeče zabraňuje webové stránce podávat žádosti do jiné domény, než je doména, která webovou stránku obsluhovala.</span><span class="sxs-lookup"><span data-stu-id="e518b-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e518b-107">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="e518b-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e518b-108">Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu.</span><span class="sxs-lookup"><span data-stu-id="e518b-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e518b-109">Někdy můžete chtít povolit jiným webům, aby do vaší aplikace pokládaly žádosti o více původů.</span><span class="sxs-lookup"><span data-stu-id="e518b-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="e518b-110">Další informace naleznete v [článku Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="e518b-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e518b-111">[Sdílení prostředků napříč původem](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="e518b-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e518b-112">Je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e518b-113">**Není** bezpečnostní funkce, CORS uvolňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e518b-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e518b-114">Rozhraní API není bezpečnější povolením CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e518b-115">Další informace naleznete v tématu [Jak cors funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="e518b-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e518b-116">Umožňuje serveru explicitně povolit některé požadavky napříč původy při odmítnutí jiných.</span><span class="sxs-lookup"><span data-stu-id="e518b-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e518b-117">Je bezpečnější a flexibilnější než předchozí techniky, jako je [Například JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="e518b-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e518b-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e518b-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e518b-119">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="e518b-119">Same origin</span></span>

<span data-ttu-id="e518b-120">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="e518b-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e518b-121">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="e518b-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e518b-122">Tyto adresy URL mají jiný původ než předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="e518b-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e518b-123">`https://example.net`&ndash; Jiná doména</span><span class="sxs-lookup"><span data-stu-id="e518b-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="e518b-124">`https://www.example.com/foo.html`&ndash; Různé subdomény</span><span class="sxs-lookup"><span data-stu-id="e518b-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="e518b-125">`http://example.com/foo.html`&ndash; Jiný režim</span><span class="sxs-lookup"><span data-stu-id="e518b-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="e518b-126">`https://example.com:9000/foo.html`&ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="e518b-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="e518b-127">Povolení CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-127">Enable CORS</span></span>

<span data-ttu-id="e518b-128">Existují tři způsoby, jak povolit CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="e518b-129">V middlewaru pomocí [pojmenované zásady](#np) nebo [výchozí zásady](#dp).</span><span class="sxs-lookup"><span data-stu-id="e518b-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="e518b-130">Použití [směrování koncového bodu](#ecors).</span><span class="sxs-lookup"><span data-stu-id="e518b-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="e518b-131">S atributem [[EnableCors].](#attr)</span><span class="sxs-lookup"><span data-stu-id="e518b-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="e518b-132">Použití atributu [[EnableCors]](#attr) s pojmenovanou zásadou poskytuje nejlepší ovládací prvek v omezení koncových bodů, které podporují CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="e518b-133">Každý přístup je podrobně popsán v následujících částech.</span><span class="sxs-lookup"><span data-stu-id="e518b-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e518b-134">CORS s názvem politiky a middleware</span><span class="sxs-lookup"><span data-stu-id="e518b-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="e518b-135">CORS Middleware zpracovává požadavky napříč původy.</span><span class="sxs-lookup"><span data-stu-id="e518b-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e518b-136">Následující kód použije zásady CORS pro všechny koncové body aplikace se zadaným původem:</span><span class="sxs-lookup"><span data-stu-id="e518b-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="e518b-137">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e518b-137">The preceding code:</span></span>

* <span data-ttu-id="e518b-138">Nastaví název `_myAllowSpecificOrigins`zásady na .</span><span class="sxs-lookup"><span data-stu-id="e518b-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="e518b-139">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="e518b-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e518b-140">Volá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření a `_myAllowSpecificOrigins` určuje zásady CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="e518b-141">`UseCors`dodává middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="e518b-142">Volání do `UseCors` musí být `UseRouting`umístěno `UseAuthorization`po , ale před .</span><span class="sxs-lookup"><span data-stu-id="e518b-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="e518b-143">Další informace naleznete [v tématu Middleware order](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="e518b-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="e518b-144">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [lambda výrazem](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="e518b-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e518b-145">Lambda má <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> předmět.</span><span class="sxs-lookup"><span data-stu-id="e518b-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e518b-146">[Možnosti konfigurace](#cors-policy-options) `WithOrigins`, například , jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e518b-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="e518b-147">Povolí `_myAllowSpecificOrigins` zásady CORS pro všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="e518b-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="e518b-148">Viz [směrování koncového bodu](#ecors) použít zásadu CORS pro konkrétní koncové body.</span><span class="sxs-lookup"><span data-stu-id="e518b-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="e518b-149">Při směrování koncových bodů ***musí*** být middleware CORS `UseRouting` nakonfigurován tak, aby se spouštěl mezi voláními do a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="e518b-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="e518b-150">Pokyny k testování kódu podobnépředchozímu kódu naleznete v [tématu Test CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="e518b-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="e518b-151">Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služeb aplikace:</span><span class="sxs-lookup"><span data-stu-id="e518b-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e518b-152">Další informace naleznete v [tématu možnosti zásad CORS](#cpo) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e518b-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="e518b-153">Metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> mohou být zřetězené, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="e518b-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="e518b-154">Poznámka: Zadaná adresa URL **nesmí** `/`obsahovat koncové lomítko ( ).</span><span class="sxs-lookup"><span data-stu-id="e518b-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e518b-155">Pokud adresa URL `/`končí pomocí `false` , vrátí porovnání a není vrácena žádná hlavička.</span><span class="sxs-lookup"><span data-stu-id="e518b-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="e518b-156">CORS s výchozími zásadami a middlewarem</span><span class="sxs-lookup"><span data-stu-id="e518b-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="e518b-157">Následující zvýrazněný kód umožňuje výchozí zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="e518b-158">Předchozí kód použije výchozí zásadu CORS pro všechny koncové body řadiče.</span><span class="sxs-lookup"><span data-stu-id="e518b-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="e518b-159">Povolení corsu s směrováním koncových bodů</span><span class="sxs-lookup"><span data-stu-id="e518b-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="e518b-160">Povolení cors na základě koncového `RequireCors` bodu pomocí aktuálně ***nepodporuje*** [automatické požadavky kontroly před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="e518b-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="e518b-161">Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/aspnetcore/issues/20709) a [Testování CORS s směrování montovny koncového bodu a [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="e518b-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="e518b-162">S směrováním koncových bodů může být CORS povoleno <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> na základě koncového bodu pomocí sady metod rozšíření:</span><span class="sxs-lookup"><span data-stu-id="e518b-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="e518b-163">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="e518b-163">In the preceding code:</span></span>

* <span data-ttu-id="e518b-164">`app.UseCors`umožňuje middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="e518b-165">Vzhledem k tomu, že `app.UseCors()` výchozí zásady nebyly nakonfigurovány, sám neumožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="e518b-166">Koncové `/echo` body a řadič umožňují požadavky na příčný původ pomocí zadané zásady.</span><span class="sxs-lookup"><span data-stu-id="e518b-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="e518b-167">Koncové `/echo2` body a Razor Pages ***neumožňují*** požadavky na příčný původ, protože nebyla zadána žádná výchozí zásada.</span><span class="sxs-lookup"><span data-stu-id="e518b-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="e518b-168">Atribut [[DisableCors]](#dc) ***nezakáže*** cors, který byl `RequireCors`povolen směrováním koncových bodů s .</span><span class="sxs-lookup"><span data-stu-id="e518b-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="e518b-169">Pokyny k testování kódu podobnépředchozímu tématu [Test CORS s směrováním koncových bodů a [HttpOptions]](#tcer) naleznete v tématu Test CORS s směrováním koncových bodů a [HttpOptions].</span><span class="sxs-lookup"><span data-stu-id="e518b-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e518b-170">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="e518b-170">Enable CORS with attributes</span></span>

<span data-ttu-id="e518b-171">Povolení CORS s atributem [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) a použití pojmenované zásady pouze na ty koncové body, které vyžadují CORS, poskytuje nejlepší ovládací prvek.</span><span class="sxs-lookup"><span data-stu-id="e518b-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="e518b-172">Atribut [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="e518b-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e518b-173">Atribut `[EnableCors]` umožňuje CORS pro vybrané koncové body, nikoli všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="e518b-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="e518b-174">`[EnableCors]`určuje výchozí zásadu.</span><span class="sxs-lookup"><span data-stu-id="e518b-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="e518b-175">`[EnableCors("{Policy String}")]`určuje pojmenovanou zásadu.</span><span class="sxs-lookup"><span data-stu-id="e518b-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="e518b-176">Atribut `[EnableCors]` lze použít na:</span><span class="sxs-lookup"><span data-stu-id="e518b-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="e518b-177">Holicí strojek stránka`PageModel`</span><span class="sxs-lookup"><span data-stu-id="e518b-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="e518b-178">Řadič</span><span class="sxs-lookup"><span data-stu-id="e518b-178">Controller</span></span>
* <span data-ttu-id="e518b-179">Metoda akce řadiče</span><span class="sxs-lookup"><span data-stu-id="e518b-179">Controller action method</span></span>

<span data-ttu-id="e518b-180">Různé zásady lze použít pro řadiče, modely `[EnableCors]` stránek nebo metody akce s atributem.</span><span class="sxs-lookup"><span data-stu-id="e518b-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="e518b-181">Pokud `[EnableCors]` je atribut použit na řadič, model stránky nebo metodu akce a CORS je povolena v middlewaru, jsou použity ***obě*** zásady.</span><span class="sxs-lookup"><span data-stu-id="e518b-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="e518b-182">***Doporučujeme proti kombinování zásad. Použijte*** ***atribut nebo middleware, ne obojí ve stejné aplikaci.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="e518b-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="e518b-183">Následující kód platí pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="e518b-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e518b-184">Následující kód vytvoří dvě zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="e518b-185">Pro nejlepší kontrolu omezení požadavků CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="e518b-186">Použití `[EnableCors("MyPolicy")]` s pojmenovanou zásadou.</span><span class="sxs-lookup"><span data-stu-id="e518b-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="e518b-187">Nedefinujte výchozí zásadu.</span><span class="sxs-lookup"><span data-stu-id="e518b-187">Don't define a default policy.</span></span>
* <span data-ttu-id="e518b-188">Nepoužívejte [směrování koncového bodu](#ecors).</span><span class="sxs-lookup"><span data-stu-id="e518b-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="e518b-189">Kód v další části splňuje předchozí seznam.</span><span class="sxs-lookup"><span data-stu-id="e518b-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="e518b-190">Pokyny k testování kódu podobnépředchozímu kódu naleznete v [tématu Test CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="e518b-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="e518b-191">Zakázat CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-191">Disable CORS</span></span>

<span data-ttu-id="e518b-192">Atribut [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***nezakáže*** cors, který byl povolen [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="e518b-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="e518b-193">Následující kód definuje zásady `"MyPolicy"`CORS :</span><span class="sxs-lookup"><span data-stu-id="e518b-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="e518b-194">Následující kód zakáže CORS pro `GetValues2` akci:</span><span class="sxs-lookup"><span data-stu-id="e518b-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="e518b-195">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e518b-195">The preceding code:</span></span>

* <span data-ttu-id="e518b-196">Neumožňuje CORS s [směrováním koncových bodů](#ecors).</span><span class="sxs-lookup"><span data-stu-id="e518b-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="e518b-197">Nedefinuje výchozí [zásady CORS](#dp).</span><span class="sxs-lookup"><span data-stu-id="e518b-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="e518b-198">Používá [[EnableCors("MyPolicy")]](#attr) k `"MyPolicy"` povolení zásady CORS pro řadič.</span><span class="sxs-lookup"><span data-stu-id="e518b-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="e518b-199">Zakáže CORS `GetValues2` pro metodu.</span><span class="sxs-lookup"><span data-stu-id="e518b-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="e518b-200">Pokyny k testování předchozího kódu naleznete v [tématu Test CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="e518b-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e518b-201">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-201">CORS policy options</span></span>

<span data-ttu-id="e518b-202">Tato část popisuje různé možnosti, které lze nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e518b-203">Nastavení povolených počátků</span><span class="sxs-lookup"><span data-stu-id="e518b-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e518b-204">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="e518b-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e518b-205">Nastavení záhlaví povolených požadavků</span><span class="sxs-lookup"><span data-stu-id="e518b-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e518b-206">Nastavení exponovaných hlaviček odpovědí</span><span class="sxs-lookup"><span data-stu-id="e518b-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e518b-207">Pověření v požadavcích na příčové počátky</span><span class="sxs-lookup"><span data-stu-id="e518b-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e518b-208">Nastavení doby vypršení platnosti kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e518b-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je přivolána . `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="e518b-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e518b-210">U některých možností může být užitečné nejprve si přečíst část [Jak cors funguje.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="e518b-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e518b-211">Nastavení povolených počátků</span><span class="sxs-lookup"><span data-stu-id="e518b-211">Set the allowed origins</span></span>

<span data-ttu-id="e518b-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umožňuje CORS požadavky ze všech původů`http` `https`s jakýmkoli schématem ( nebo ).</span><span class="sxs-lookup"><span data-stu-id="e518b-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e518b-213">`AllowAnyOrigin`je nejistá, protože *jakýkoli web* může do aplikace provádět požadavky napříč původy.</span><span class="sxs-lookup"><span data-stu-id="e518b-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e518b-214">Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může mít za následek padělání požadavků mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="e518b-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e518b-215">Služba CORS vrátí neplatnou odpověď CORS, když je aplikace nakonfigurována s oběma způsoby.</span><span class="sxs-lookup"><span data-stu-id="e518b-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="e518b-216">`AllowAnyOrigin`ovlivňuje požadavky kontroly `Access-Control-Allow-Origin` před výstupem a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e518b-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e518b-217">Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e518b-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e518b-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady jako funkci, která umožňuje počátkům tak, aby odpovídala nakonfigurované doméně se zástupnými kartami při vyhodnocování, pokud je povolen původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e518b-219">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="e518b-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e518b-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e518b-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e518b-221">Umožňuje libovolnou metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="e518b-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="e518b-222">Ovlivňuje požadavky kontroly `Access-Control-Allow-Methods` před výstupem a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e518b-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e518b-223">Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e518b-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e518b-224">Nastavení záhlaví povolených požadavků</span><span class="sxs-lookup"><span data-stu-id="e518b-224">Set the allowed request headers</span></span>

<span data-ttu-id="e518b-225">Chcete-li povolit odeslání určitých hlaviček v požadavku CORS, [nazývaném hlavičky žádostí o autora](https://xhr.spec.whatwg.org/#request), zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="e518b-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="e518b-226">Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [hlavičky žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="e518b-227">`AllowAnyHeader`ovlivňuje požadavky kontroly před výstupem a hlavičku [Hlavičky access-control-request-headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="e518b-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="e518b-228">Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e518b-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e518b-229">Zásady CORS Middleware odpovídají určitým `WithHeaders` záhlavím určeným v písmenu a) je možné pouze v případě, že hlavičky odeslané přesně `Access-Control-Request-Headers` odpovídají záhlavím uvedeným v . `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="e518b-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="e518b-230">Zvažte například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="e518b-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="e518b-231">Cors Middleware odmítne požadavek kontroly před `Content-Language` výstupem s následující hlavičkou požadavku, `WithHeaders`protože ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden v :</span><span class="sxs-lookup"><span data-stu-id="e518b-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="e518b-232">Aplikace vrátí odpověď *200 OK,* ale neodešle záhlaví CORS zpět.</span><span class="sxs-lookup"><span data-stu-id="e518b-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e518b-233">Proto prohlížeč nepokouší požadavek křížového původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e518b-234">Nastavení exponovaných hlaviček odpovědí</span><span class="sxs-lookup"><span data-stu-id="e518b-234">Set the exposed response headers</span></span>

<span data-ttu-id="e518b-235">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e518b-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e518b-236">Další informace naleznete v tématu [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="e518b-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e518b-237">Záhlaví odpovědí, která jsou ve výchozím nastavení k dispozici, jsou:</span><span class="sxs-lookup"><span data-stu-id="e518b-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e518b-238">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědí*.</span><span class="sxs-lookup"><span data-stu-id="e518b-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e518b-239">Chcete-li aplikaci zpřístupnit další <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>záhlaví, zavolejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e518b-240">Pověření v požadavcích na příčové počátky</span><span class="sxs-lookup"><span data-stu-id="e518b-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e518b-241">Pověření vyžadují zvláštní zpracování v požadavku CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e518b-242">Ve výchozím nastavení prohlížeč neodesílá přihlašovací údaje s požadavkem na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e518b-243">Pověření zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="e518b-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e518b-244">Chcete-li odeslat pověření s požadavkem `XMLHttpRequest.withCredentials` na `true`příčný původ, musí klient nastavit .</span><span class="sxs-lookup"><span data-stu-id="e518b-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e518b-245">Použití `XMLHttpRequest` přímo:</span><span class="sxs-lookup"><span data-stu-id="e518b-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e518b-246">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="e518b-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e518b-247">Použití rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="e518b-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e518b-248">Server musí povolit pověření.</span><span class="sxs-lookup"><span data-stu-id="e518b-248">The server must allow the credentials.</span></span> <span data-ttu-id="e518b-249">Chcete-li povolit pověření <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>pro více původů, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="e518b-250">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` záhlaví, které prohlížeči říká, že server povoluje pověření pro požadavek na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e518b-251">Pokud prohlížeč odešle pověření, ale odpověď neobsahuje platné `Access-Control-Allow-Credentials` záhlaví, prohlížeč nezveřejňuje odpověď na aplikaci a požadavek na příčný původ se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e518b-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e518b-252">Povolení pověření pro více původ je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="e518b-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e518b-253">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="e518b-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e518b-254">Specifikace CORS také uvádí, že `"*"` nastavení původu (všechny počátky) je neplatná, pokud `Access-Control-Allow-Credentials` je záhlaví k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e518b-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="e518b-255">Požadavky kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-255">Preflight requests</span></span>

<span data-ttu-id="e518b-256">U některých požadavků CORS prohlížeč odešle další [požadavky options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) před provedením skutečné žádosti.</span><span class="sxs-lookup"><span data-stu-id="e518b-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="e518b-257">Tento požadavek se nazývá [požadavek kontroly před výstupem](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="e518b-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="e518b-258">Prohlížeč může přeskočit požadavek kontroly před výstupem, pokud jsou splněny ***všechny*** následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="e518b-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="e518b-259">Metoda požadavku je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="e518b-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e518b-260">Aplikace nenastavuje záhlaví požadavků `Accept`kromě `Accept-Language` `Content-Language`, `Content-Type`, `Last-Event-ID`, nebo .</span><span class="sxs-lookup"><span data-stu-id="e518b-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e518b-261">Záhlaví, `Content-Type` pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="e518b-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e518b-262">Pravidlo pro hlavičky požadavku nastavené pro požadavek klienta se `setRequestHeader` vztahuje `XMLHttpRequest` na záhlaví, která aplikace nastaví voláním objektu.</span><span class="sxs-lookup"><span data-stu-id="e518b-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e518b-263">Specifikace CORS volá tyto hlavičky [záhlaví hlavičky požadavku autora](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="e518b-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="e518b-264">Pravidlo se nevztahuje na záhlaví, která může `User-Agent`prohlížeč `Host`nastavit, například , nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="e518b-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e518b-265">Následuje příklad odpovědi podobné žádosti před výstupem z tlačítka **[Put test]** v části [Test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e518b-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="e518b-266">Požadavek kontroly před výstupem používá metodu [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="e518b-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="e518b-267">Může obsahovat následující záhlaví:</span><span class="sxs-lookup"><span data-stu-id="e518b-267">It may include the following headers:</span></span>

* <span data-ttu-id="e518b-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Metoda HTTP, která bude použita pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e518b-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Seznam hlaviček požadavků, které aplikace nastaví na skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e518b-270">Jak již bylo uvedeno dříve, nezahrnuje záhlaví, která `User-Agent`prohlížeč nastaví, například .</span><span class="sxs-lookup"><span data-stu-id="e518b-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="e518b-271">Metody access-control-allow-allow-</span><span class="sxs-lookup"><span data-stu-id="e518b-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="e518b-272">Pokud je požadavek kontroly před `200 OK` výstupem zamítnut, aplikace vrátí odpověď, ale nenastaví záhlaví CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="e518b-273">Proto prohlížeč nepokouší požadavek křížového původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="e518b-274">Příklad zamítnuté žádosti o předběžné kontroly naleznete v části [Test CORS](#testc) tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e518b-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="e518b-275">Pomocí nástrojů F12 aplikace konzoly zobrazuje chybu podobnou jedné z následujících, v závislosti na prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="e518b-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="e518b-276">Firefox: Žádost o cross-origin blokována: Stejné zásady `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`původu zakáže čtení vzdáleného prostředku na adrese .</span><span class="sxs-lookup"><span data-stu-id="e518b-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="e518b-277">(Důvod: Žádost CORS nebyla úspěšná).</span><span class="sxs-lookup"><span data-stu-id="e518b-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="e518b-278">Další informace</span><span class="sxs-lookup"><span data-stu-id="e518b-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="e518b-279">Na bázi chromu: Přístuphttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5k načtení na ' od původu 'https://cors3.azurewebsites.net' byl zablokován zásadami CORS: Odpověď na požadavek kontroly před výstupem neprojde kontrolou řízení přístupu: Na požadovaném prostředku není k dispozici žádná hlavička "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="e518b-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="e518b-280">Pokud neprůhledná odpověď slouží vašim potřebám, nastavte režim požadavku na "no-cors" pro načtení prostředku se zakázaným CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="e518b-281">Chcete-li povolit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>určitá záhlaví, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="e518b-282">Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> [hlavičky žádostí o autora](https://www.w3.org/TR/cors/#author-request-headers), volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="e518b-283">Prohlížeče nejsou konzistentní v tom, `Access-Control-Request-Headers`jak nastavují .</span><span class="sxs-lookup"><span data-stu-id="e518b-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e518b-284">Pokud:</span><span class="sxs-lookup"><span data-stu-id="e518b-284">If either:</span></span>

* <span data-ttu-id="e518b-285">Záhlaví jsou nastavena na cokoli jiného než`"*"`</span><span class="sxs-lookup"><span data-stu-id="e518b-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="e518b-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>se nazývá: Zahrnout `Accept` `Content-Type`alespoň `Origin`, a , plus všechny vlastní záhlaví, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="e518b-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="e518b-287">Kód automatického požadavku kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-287">Automatic preflight request code</span></span>

<span data-ttu-id="e518b-288">Při použití zásady CORS buď:</span><span class="sxs-lookup"><span data-stu-id="e518b-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="e518b-289">Globálně voláním `app.UseCors` `Startup.Configure`v .</span><span class="sxs-lookup"><span data-stu-id="e518b-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="e518b-290">Použití `[EnableCors]` atributu.</span><span class="sxs-lookup"><span data-stu-id="e518b-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="e518b-291">ASP.NET Core reaguje na požadavek možnosti kontroly před výstupem.</span><span class="sxs-lookup"><span data-stu-id="e518b-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="e518b-292">Povolení CORS na základě koncového `RequireCors` bodu pomocí aktuálně ***nepodporuje*** automatické požadavky kontroly před výstupem.</span><span class="sxs-lookup"><span data-stu-id="e518b-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="e518b-293">[Test CORS](#testc) části tohoto dokumentu ukazuje toto chování.</span><span class="sxs-lookup"><span data-stu-id="e518b-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="e518b-294">[HttpOptions] atribut pro požadavky kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="e518b-295">Pokud je cors povoleno s příslušnou zásadou, ASP.NET Core obecně reaguje na požadavky cors kontroly před výstupem automaticky.</span><span class="sxs-lookup"><span data-stu-id="e518b-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="e518b-296">V některých scénářích to nemusí být tento případ.</span><span class="sxs-lookup"><span data-stu-id="e518b-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="e518b-297">Například pomocí [CORS s směrováním koncového bodu](#ecors).</span><span class="sxs-lookup"><span data-stu-id="e518b-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="e518b-298">Následující kód používá atribut [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) k vytvoření koncových bodů pro požadavky OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="e518b-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="e518b-299">Pokyny k testování předchozího kódu naleznete v [tématu Test CORS s směrováním koncových bodů a [HttpOptions].](#tcer)</span><span class="sxs-lookup"><span data-stu-id="e518b-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e518b-300">Nastavení doby vypršení platnosti kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-300">Set the preflight expiration time</span></span>

<span data-ttu-id="e518b-301">Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek kontroly před výstupem uložena do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e518b-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e518b-302">Chcete-li nastavit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>toto záhlaví, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e518b-303">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="e518b-303">How CORS works</span></span>

<span data-ttu-id="e518b-304">Tato část popisuje, co se děje v požadavku [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="e518b-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e518b-305">CORS **není** funkce zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e518b-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="e518b-306">CORS je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e518b-307">Škodlivý objekt actor může například použít [skriptování mezi webovými servery (XSS)](xref:security/cross-site-scripting) proti vašemu webu a provést požadavek na více webů na jejich web s povoleným cors, aby ukradl informace.</span><span class="sxs-lookup"><span data-stu-id="e518b-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e518b-308">Rozhraní API není bezpečnější povolením CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="e518b-309">Je na klientovi (prohlížeči), aby vynucoval CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e518b-310">Server provede požadavek a vrátí odpověď, je klient, který vrátí chybu a blokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="e518b-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e518b-311">Například některý z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="e518b-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e518b-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e518b-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e518b-313">Postman</span><span class="sxs-lookup"><span data-stu-id="e518b-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e518b-314">Httpklient rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="e518b-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e518b-315">Webový prohlížeč zadáním adresy URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="e518b-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e518b-316">Je to způsob, jak server umožňuje prohlížečům spustit požadavek [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro křížový původ, který by jinak byl zakázán.</span><span class="sxs-lookup"><span data-stu-id="e518b-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e518b-317">Prohlížeče bez CORS nemohou dělat požadavky na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="e518b-318">Před CORS byl [jsonp](https://www.w3schools.com/js/js_json_jsonp.asp) použit k obcházení tohoto omezení.</span><span class="sxs-lookup"><span data-stu-id="e518b-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e518b-319">JSONP nepoužívá XHR, používá `<script>` značku pro příjem odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e518b-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e518b-320">Skripty mohou být načteny cross-origin.</span><span class="sxs-lookup"><span data-stu-id="e518b-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e518b-321">[Specifikace CORS](https://www.w3.org/TR/cors/) zavedla několik nových hlaviček HTTP, které umožňují požadavky na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e518b-322">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky křížového původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e518b-323">Vlastní kód JavaScriptu není nutné povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e518b-324">Tlačítko [PUT test](https://cors3.azurewebsites.net/test) na nasazeném [vzorku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="e518b-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="e518b-325">Následuje příklad požadavku na příčný původ [Values](https://cors3.azurewebsites.net/) z testovacího `https://cors1.azurewebsites.net/api/values`tlačítka Hodnoty na .</span><span class="sxs-lookup"><span data-stu-id="e518b-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="e518b-326">Záhlaví: `Origin`</span><span class="sxs-lookup"><span data-stu-id="e518b-326">The `Origin` header:</span></span>

* <span data-ttu-id="e518b-327">Poskytuje doménu webu, který je podání žádosti.</span><span class="sxs-lookup"><span data-stu-id="e518b-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="e518b-328">Je vyžadována a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="e518b-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="e518b-329">**Obecná záhlaví**</span><span class="sxs-lookup"><span data-stu-id="e518b-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="e518b-330">**Záhlaví odpovědí**</span><span class="sxs-lookup"><span data-stu-id="e518b-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="e518b-331">**Záhlaví požadavku**</span><span class="sxs-lookup"><span data-stu-id="e518b-331">**Request headers**</span></span>

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

<span data-ttu-id="e518b-332">V `OPTIONS` požadavcích server nastaví **Response headers** `Access-Control-Allow-Origin: {allowed origin}` hlavičku záhlaví odpovědí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e518b-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="e518b-333">Například nasazený [ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)požadavek tlačítka `OPTIONS` [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) obsahuje následující záhlaví:</span><span class="sxs-lookup"><span data-stu-id="e518b-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="e518b-334">**Obecná záhlaví**</span><span class="sxs-lookup"><span data-stu-id="e518b-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="e518b-335">**Záhlaví odpovědí**</span><span class="sxs-lookup"><span data-stu-id="e518b-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="e518b-336">**Záhlaví požadavku**</span><span class="sxs-lookup"><span data-stu-id="e518b-336">**Request headers**</span></span>

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

<span data-ttu-id="e518b-337">V předchozích **hlavičkách odpovědí**server v odpovědi nastaví hlavičku [Access-Control-Allow-Origin.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)</span><span class="sxs-lookup"><span data-stu-id="e518b-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="e518b-338">Hodnota `https://cors1.azurewebsites.net` tohoto záhlaví odpovídá `Origin` záhlaví z požadavku.</span><span class="sxs-lookup"><span data-stu-id="e518b-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="e518b-339">Pokud <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> je volána, `Access-Control-Allow-Origin: *`, hodnota zástupný znak, je vrácena.</span><span class="sxs-lookup"><span data-stu-id="e518b-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="e518b-340">`AllowAnyOrigin`umožňuje jakýkoli původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="e518b-341">Pokud odpověď neobsahuje `Access-Control-Allow-Origin` záhlaví, požadavek mezi původem se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e518b-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e518b-342">Konkrétně prohlížeč zakáže požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e518b-343">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupní odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e518b-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="e518b-344">Zobrazit požadavky na možnosti zobrazení</span><span class="sxs-lookup"><span data-stu-id="e518b-344">Display OPTIONS requests</span></span>

<span data-ttu-id="e518b-345">Ve výchozím nastavení prohlížeče Chrome a Edge nezobrazují požadavky OPTIONS na kartě sítě nástrojů F12.</span><span class="sxs-lookup"><span data-stu-id="e518b-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="e518b-346">Zobrazení požadavků OPTIONS v těchto prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="e518b-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="e518b-347">`chrome://flags/#out-of-blink-cors` nebo `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="e518b-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="e518b-348">zneškodnit příznak.</span><span class="sxs-lookup"><span data-stu-id="e518b-348">disable the flag.</span></span>
* <span data-ttu-id="e518b-349">restart (restartovat).</span><span class="sxs-lookup"><span data-stu-id="e518b-349">restart.</span></span>

<span data-ttu-id="e518b-350">Firefox ve výchozím nastavení zobrazuje požadavky OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="e518b-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="e518b-351">CORS ve S.r.o.</span><span class="sxs-lookup"><span data-stu-id="e518b-351">CORS in IIS</span></span>

<span data-ttu-id="e518b-352">Při nasazování do služby IIS musí být cors spuštěn před ověřováním systému Windows, pokud server není nakonfigurován tak, aby umožňoval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="e518b-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="e518b-353">Pro podporu tohoto scénáře je třeba nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e518b-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="e518b-354">Test CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-354">Test CORS</span></span>

<span data-ttu-id="e518b-355">[Ukázkové stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) má kód pro testování CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="e518b-356">Podívejte [se, jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e518b-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="e518b-357">Ukázka je projekt ROZHRANÍ API s přidanou stránkou Razor:</span><span class="sxs-lookup"><span data-stu-id="e518b-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="e518b-358">`WithOrigins("https://localhost:<port>");`by měl být použit pouze pro testování ukázkové aplikace podobné [ukázkový kód ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="e518b-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="e518b-359">Následující `ValuesController` poskytuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="e518b-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="e518b-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) je poskytována [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet balíček a zobrazí informace o trase.</span><span class="sxs-lookup"><span data-stu-id="e518b-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="e518b-361">Otestujte předchozí ukázkový kód pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e518b-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="e518b-362">Použijte nasazenou ukázkovou [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)aplikaci na adrese .</span><span class="sxs-lookup"><span data-stu-id="e518b-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="e518b-363">Není třeba stáhnout ukázku.</span><span class="sxs-lookup"><span data-stu-id="e518b-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="e518b-364">Spusťte `dotnet run` ukázku pomocí `https://localhost:5001`výchozí adresy URL aplikace .</span><span class="sxs-lookup"><span data-stu-id="e518b-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="e518b-365">Spusťte ukázku z Visual Studia s portem nastaveným `https://localhost:44398`na 44398 pro adresu URL aplikace .</span><span class="sxs-lookup"><span data-stu-id="e518b-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="e518b-366">Použití prohlížeče s nástroji F12:</span><span class="sxs-lookup"><span data-stu-id="e518b-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="e518b-367">Vyberte tlačítko **Hodnoty** a zkontrolujte záhlaví na kartě **Síť.**</span><span class="sxs-lookup"><span data-stu-id="e518b-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="e518b-368">Vyberte **tlačítko PUT test.**</span><span class="sxs-lookup"><span data-stu-id="e518b-368">Select the **PUT test** button.</span></span> <span data-ttu-id="e518b-369">Viz [Požadavky na možnosti zobrazení](#options) pro pokyny k zobrazení žádosti možnosti.</span><span class="sxs-lookup"><span data-stu-id="e518b-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="e518b-370">**Test PUT** vytvoří dva požadavky, požadavek kontroly před výstupem OPTIONS a požadavek PUT.</span><span class="sxs-lookup"><span data-stu-id="e518b-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="e518b-371">Vyberte **`GetValues2 [DisableCors]`** tlačítko pro aktivaci neúspěšného požadavku CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="e518b-372">Jak je uvedeno v dokumentu, odpověď vrátí 200 úspěch, ale požadavek CORS není podána.</span><span class="sxs-lookup"><span data-stu-id="e518b-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="e518b-373">Chcete-li zobrazit chybu CORS, vyberte kartu **Konzola.**</span><span class="sxs-lookup"><span data-stu-id="e518b-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="e518b-374">V závislosti na prohlížeči se zobrazí chyba podobná následující:</span><span class="sxs-lookup"><span data-stu-id="e518b-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="e518b-375">Přístup k `'https://cors1.azurewebsites.net/api/values/GetValues2'` načtení `'https://cors3.azurewebsites.net'` z původu byl zablokován zásadami CORS: V požadovaném prostředku není k dispozici žádná hlavička "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="e518b-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="e518b-376">Pokud neprůhledná odpověď slouží vašim potřebám, nastavte režim požadavku na "no-cors" pro načtení prostředku se zakázaným CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="e518b-377">Koncové body s podporou CORS lze testovat pomocí nástroje, například [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)nebo [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="e518b-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e518b-378">Při použití nástroje se počátek požadavku `Origin` určeného hlavičkou musí lišit od hostitele, který požadavek obdržel.</span><span class="sxs-lookup"><span data-stu-id="e518b-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e518b-379">Pokud požadavek není *cross-origin* na základě `Origin` hodnoty záhlaví:</span><span class="sxs-lookup"><span data-stu-id="e518b-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e518b-380">Není třeba, aby CORS Middleware zpracoval žádost.</span><span class="sxs-lookup"><span data-stu-id="e518b-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e518b-381">Hlavičky CORS nejsou vráceny v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e518b-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="e518b-382">Následující příkaz `curl` používá k vydání požadavku OPTIONS s informacemi:</span><span class="sxs-lookup"><span data-stu-id="e518b-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="e518b-383">Testování CORS s směrováním koncového bodu a [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="e518b-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="e518b-384">Povolení cors na základě koncového `RequireCors` bodu pomocí aktuálně ***nepodporuje*** [automatické požadavky kontroly před výstupem](#apf).</span><span class="sxs-lookup"><span data-stu-id="e518b-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="e518b-385">Zvažte následující kód, který používá [směrování koncového bodu k povolení CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="e518b-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="e518b-386">Následující `TodoItems1Controller` obsahuje koncové body pro testování:</span><span class="sxs-lookup"><span data-stu-id="e518b-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="e518b-387">Otestujte předchozí kód ze [zkušební stránky](https://cors1.azurewebsites.net/test?number=1) nasazené [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="e518b-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="e518b-388">Tlačítka **Delete [EnableCors]** a **GET [EnableCors]** jsou `[EnableCors]` úspěšná, protože koncové body mají požadavky kontroly před výstupem a reagují na ně.</span><span class="sxs-lookup"><span data-stu-id="e518b-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="e518b-389">Ostatní koncové body se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e518b-389">The other endpoints fails.</span></span> <span data-ttu-id="e518b-390">Tlačítko **GET** se nezdaří, protože [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) odešle:</span><span class="sxs-lookup"><span data-stu-id="e518b-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="e518b-391">Následující `TodoItems2Controller` poskytuje podobné koncové body, ale obsahuje explicitní kód reagovat na požadavky options:</span><span class="sxs-lookup"><span data-stu-id="e518b-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="e518b-392">Otestujte předchozí kód ze [zkušební stránky](https://cors1.azurewebsites.net/test?number=2) nasazené ukázky.</span><span class="sxs-lookup"><span data-stu-id="e518b-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="e518b-393">V rozevíracím seznamu **Kontroly kontroly před** výstupem vyberte **kontrola před výstupem** a pak **nastavit ovladač**.</span><span class="sxs-lookup"><span data-stu-id="e518b-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="e518b-394">Všechna volání CORS `TodoItems2Controller` koncových bodů úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e518b-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e518b-395">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e518b-395">Additional resources</span></span>

* [<span data-ttu-id="e518b-396">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="e518b-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="e518b-397">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e518b-398">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e518b-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e518b-399">Tento článek ukazuje, jak povolit CORS v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e518b-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e518b-400">Zabezpečení prohlížeče zabraňuje webové stránce podávat žádosti do jiné domény, než je doména, která webovou stránku obsluhovala.</span><span class="sxs-lookup"><span data-stu-id="e518b-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e518b-401">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="e518b-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e518b-402">Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu.</span><span class="sxs-lookup"><span data-stu-id="e518b-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e518b-403">Někdy můžete povolit jiným webům, aby do vaší aplikace vykládaly žádosti o více původů.</span><span class="sxs-lookup"><span data-stu-id="e518b-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="e518b-404">Další informace naleznete v [článku Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="e518b-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e518b-405">[Sdílení prostředků napříč původem](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="e518b-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e518b-406">Je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e518b-407">**Není** bezpečnostní funkce, CORS uvolňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e518b-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e518b-408">Rozhraní API není bezpečnější povolením CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e518b-409">Další informace naleznete v tématu [Jak cors funguje](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="e518b-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e518b-410">Umožňuje serveru explicitně povolit některé požadavky napříč původy při odmítnutí jiných.</span><span class="sxs-lookup"><span data-stu-id="e518b-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e518b-411">Je bezpečnější a flexibilnější než předchozí techniky, jako je [Například JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="e518b-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e518b-412">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e518b-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e518b-413">Stejný původ</span><span class="sxs-lookup"><span data-stu-id="e518b-413">Same origin</span></span>

<span data-ttu-id="e518b-414">Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="e518b-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e518b-415">Tyto dvě adresy URL mají stejný původ:</span><span class="sxs-lookup"><span data-stu-id="e518b-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e518b-416">Tyto adresy URL mají jiný původ než předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="e518b-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e518b-417">`https://example.net`&ndash; Jiná doména</span><span class="sxs-lookup"><span data-stu-id="e518b-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="e518b-418">`https://www.example.com/foo.html`&ndash; Různé subdomény</span><span class="sxs-lookup"><span data-stu-id="e518b-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="e518b-419">`http://example.com/foo.html`&ndash; Jiný režim</span><span class="sxs-lookup"><span data-stu-id="e518b-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="e518b-420">`https://example.com:9000/foo.html`&ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="e518b-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="e518b-421">Aplikace Internet Explorer nebere v úvahu port při porovnávání počátků.</span><span class="sxs-lookup"><span data-stu-id="e518b-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e518b-422">CORS s názvem politiky a middleware</span><span class="sxs-lookup"><span data-stu-id="e518b-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="e518b-423">CORS Middleware zpracovává požadavky napříč původy.</span><span class="sxs-lookup"><span data-stu-id="e518b-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e518b-424">Následující kód umožňuje CORS pro celou aplikaci se zadaným původem:</span><span class="sxs-lookup"><span data-stu-id="e518b-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="e518b-425">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="e518b-425">The preceding code:</span></span>

* <span data-ttu-id="e518b-426">Nastaví název zásady na "myAllowSpecificOrigins".\_</span><span class="sxs-lookup"><span data-stu-id="e518b-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="e518b-427">Název zásady je libovolný.</span><span class="sxs-lookup"><span data-stu-id="e518b-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e518b-428">Volá <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodu rozšíření, která umožňuje CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="e518b-429">Volání <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> s [lambda výrazem](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="e518b-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e518b-430">Lambda má <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> předmět.</span><span class="sxs-lookup"><span data-stu-id="e518b-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e518b-431">[Možnosti konfigurace](#cors-policy-options) `WithOrigins`, například , jsou popsány dále v tomto článku.</span><span class="sxs-lookup"><span data-stu-id="e518b-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="e518b-432">Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> metody přidá služby CORS do kontejneru služeb aplikace:</span><span class="sxs-lookup"><span data-stu-id="e518b-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e518b-433">Další informace naleznete v [tématu možnosti zásad CORS](#cpo) v tomto dokumentu .</span><span class="sxs-lookup"><span data-stu-id="e518b-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="e518b-434">Metoda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> může řetězmetody, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="e518b-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="e518b-435">Poznámka: Adresa URL **nesmí** obsahovat`/`koncové lomítko ( ).</span><span class="sxs-lookup"><span data-stu-id="e518b-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e518b-436">Pokud adresa URL `/`končí pomocí `false` , vrátí porovnání a není vrácena žádná hlavička.</span><span class="sxs-lookup"><span data-stu-id="e518b-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="e518b-437">Následující kód aplikuje zásady CORS na všechny koncové body aplikací prostřednictvím CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="e518b-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="e518b-438">Poznámka: `UseCors` musí být `UseMvc`volána před .</span><span class="sxs-lookup"><span data-stu-id="e518b-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="e518b-439">Viz [Povolení CORS v Razor Stránky, ovladače a metody akce](#ecors) použít zásady CORS na stránce/kontroleru/akce úrovni.</span><span class="sxs-lookup"><span data-stu-id="e518b-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="e518b-440">Pokyny k testování kódu podobnépředchozímu kódu naleznete v [tématu Test CORS.](#test)</span><span class="sxs-lookup"><span data-stu-id="e518b-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e518b-441">Povolení CORS s atributy</span><span class="sxs-lookup"><span data-stu-id="e518b-441">Enable CORS with attributes</span></span>

<span data-ttu-id="e518b-442">[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut poskytuje alternativu k použití CORS globálně.</span><span class="sxs-lookup"><span data-stu-id="e518b-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e518b-443">Atribut `[EnableCors]` umožňuje CORS pro vybrané koncové body, nikoli pro všechny koncové body.</span><span class="sxs-lookup"><span data-stu-id="e518b-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="e518b-444">Slouží `[EnableCors]` k určení výchozí `[EnableCors("{Policy String}")]` zásady a k určení zásady.</span><span class="sxs-lookup"><span data-stu-id="e518b-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="e518b-445">Atribut `[EnableCors]` lze použít na:</span><span class="sxs-lookup"><span data-stu-id="e518b-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="e518b-446">Holicí strojek stránka`PageModel`</span><span class="sxs-lookup"><span data-stu-id="e518b-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="e518b-447">Řadič</span><span class="sxs-lookup"><span data-stu-id="e518b-447">Controller</span></span>
* <span data-ttu-id="e518b-448">Metoda akce řadiče</span><span class="sxs-lookup"><span data-stu-id="e518b-448">Controller action method</span></span>

<span data-ttu-id="e518b-449">Můžete použít různé zásady pro řadič/stránku `[EnableCors]` model/akce s atributem.</span><span class="sxs-lookup"><span data-stu-id="e518b-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="e518b-450">Pokud `[EnableCors]` je atribut použit pro řadiče/stránku model/akční metoda a CORS je povolena v middleware, jsou použity ***obě*** zásady.</span><span class="sxs-lookup"><span data-stu-id="e518b-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="e518b-451">Doporučujeme ***nekombinovat*** zásady.</span><span class="sxs-lookup"><span data-stu-id="e518b-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="e518b-452">Použijte `[EnableCors]` atribut nebo middleware, \***ne obojí**.</span><span class="sxs-lookup"><span data-stu-id="e518b-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="e518b-453">Při `[EnableCors]`použití **nedefinujte** výchozí zásadu.</span><span class="sxs-lookup"><span data-stu-id="e518b-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="e518b-454">Následující kód platí pro každou metodu jinou zásadu:</span><span class="sxs-lookup"><span data-stu-id="e518b-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e518b-455">Následující kód vytvoří výchozí zásadu CORS `"AnotherPolicy"`a zásadu s názvem :</span><span class="sxs-lookup"><span data-stu-id="e518b-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="e518b-456">Zakázat CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-456">Disable CORS</span></span>

<span data-ttu-id="e518b-457">Atribut [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) zakáže cors pro řadič/stránku model/akce.</span><span class="sxs-lookup"><span data-stu-id="e518b-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e518b-458">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-458">CORS policy options</span></span>

<span data-ttu-id="e518b-459">Tato část popisuje různé možnosti, které lze nastavit v zásadách CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e518b-460">Nastavení povolených počátků</span><span class="sxs-lookup"><span data-stu-id="e518b-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e518b-461">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="e518b-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e518b-462">Nastavení záhlaví povolených požadavků</span><span class="sxs-lookup"><span data-stu-id="e518b-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e518b-463">Nastavení exponovaných hlaviček odpovědí</span><span class="sxs-lookup"><span data-stu-id="e518b-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e518b-464">Pověření v požadavcích na příčové počátky</span><span class="sxs-lookup"><span data-stu-id="e518b-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e518b-465">Nastavení doby vypršení platnosti kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="e518b-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>je přivolána . `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="e518b-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e518b-467">U některých možností může být užitečné nejprve si přečíst část [Jak cors funguje.](#how-cors)</span><span class="sxs-lookup"><span data-stu-id="e518b-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e518b-468">Nastavení povolených počátků</span><span class="sxs-lookup"><span data-stu-id="e518b-468">Set the allowed origins</span></span>

<span data-ttu-id="e518b-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Umožňuje CORS požadavky ze všech původů`http` `https`s jakýmkoli schématem ( nebo ).</span><span class="sxs-lookup"><span data-stu-id="e518b-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e518b-470">`AllowAnyOrigin`je nejistá, protože *jakýkoli web* může do aplikace provádět požadavky napříč původy.</span><span class="sxs-lookup"><span data-stu-id="e518b-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e518b-471">Zadání `AllowAnyOrigin` a `AllowCredentials` je nezabezpečená konfigurace a může mít za následek padělání požadavků mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="e518b-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e518b-472">Pro zabezpečenou aplikaci zadejte přesný seznam původů, pokud klient musí autorizovat sám pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="e518b-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="e518b-473">`AllowAnyOrigin`ovlivňuje požadavky kontroly `Access-Control-Allow-Origin` před výstupem a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e518b-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e518b-474">Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e518b-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e518b-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Nastaví <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> vlastnost zásady jako funkci, která umožňuje počátkům tak, aby odpovídala nakonfigurované doméně se zástupnými kartami při vyhodnocování, pokud je povolen původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e518b-476">Nastavení povolených metod HTTP</span><span class="sxs-lookup"><span data-stu-id="e518b-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e518b-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="e518b-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e518b-478">Umožňuje libovolnou metodu HTTP:</span><span class="sxs-lookup"><span data-stu-id="e518b-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="e518b-479">Ovlivňuje požadavky kontroly `Access-Control-Allow-Methods` před výstupem a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e518b-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e518b-480">Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e518b-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e518b-481">Nastavení záhlaví povolených požadavků</span><span class="sxs-lookup"><span data-stu-id="e518b-481">Set the allowed request headers</span></span>

<span data-ttu-id="e518b-482">Chcete-li povolit odeslání určitých hlaviček v požadavku CORS, *nazývaném hlavičky žádostí o autora*, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadejte povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="e518b-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e518b-483">Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>hlavičky žádostí o autora, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e518b-484">Toto nastavení ovlivní požadavky `Access-Control-Request-Headers` kontroly před výstupem a záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e518b-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="e518b-485">Další informace naleznete v části [Požadavky kontroly před výstupem.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="e518b-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="e518b-486">CORS Middleware vždy umožňuje čtyři `Access-Control-Request-Headers` hlavičky v a) které mají být odeslány bez ohledu na hodnoty nakonfigurované v CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="e518b-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="e518b-487">Tento seznam záhlaví obsahuje:</span><span class="sxs-lookup"><span data-stu-id="e518b-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="e518b-488">Zvažte například aplikaci nakonfigurovanou takto:</span><span class="sxs-lookup"><span data-stu-id="e518b-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e518b-489">Cors Middleware úspěšně reaguje na požadavek kontroly před `Content-Language` výstupem s následující hlavičkou požadavku, protože je vždy na seznamu povolených:</span><span class="sxs-lookup"><span data-stu-id="e518b-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e518b-490">Nastavení exponovaných hlaviček odpovědí</span><span class="sxs-lookup"><span data-stu-id="e518b-490">Set the exposed response headers</span></span>

<span data-ttu-id="e518b-491">Ve výchozím nastavení prohlížeč nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e518b-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e518b-492">Další informace naleznete v tématu [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="e518b-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e518b-493">Záhlaví odpovědí, která jsou ve výchozím nastavení k dispozici, jsou:</span><span class="sxs-lookup"><span data-stu-id="e518b-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e518b-494">Specifikace CORS volá tyto hlavičky *jednoduché hlavičky odpovědí*.</span><span class="sxs-lookup"><span data-stu-id="e518b-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e518b-495">Chcete-li aplikaci zpřístupnit další <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>záhlaví, zavolejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e518b-496">Pověření v požadavcích na příčové počátky</span><span class="sxs-lookup"><span data-stu-id="e518b-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e518b-497">Pověření vyžadují zvláštní zpracování v požadavku CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e518b-498">Ve výchozím nastavení prohlížeč neodesílá přihlašovací údaje s požadavkem na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e518b-499">Pověření zahrnují soubory cookie a schémata ověřování HTTP.</span><span class="sxs-lookup"><span data-stu-id="e518b-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e518b-500">Chcete-li odeslat pověření s požadavkem `XMLHttpRequest.withCredentials` na `true`příčný původ, musí klient nastavit .</span><span class="sxs-lookup"><span data-stu-id="e518b-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e518b-501">Použití `XMLHttpRequest` přímo:</span><span class="sxs-lookup"><span data-stu-id="e518b-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e518b-502">Pomocí jQuery:</span><span class="sxs-lookup"><span data-stu-id="e518b-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e518b-503">Použití rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="e518b-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e518b-504">Server musí povolit pověření.</span><span class="sxs-lookup"><span data-stu-id="e518b-504">The server must allow the credentials.</span></span> <span data-ttu-id="e518b-505">Chcete-li povolit pověření <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>pro více původů, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="e518b-506">Odpověď HTTP obsahuje `Access-Control-Allow-Credentials` záhlaví, které prohlížeči říká, že server povoluje pověření pro požadavek na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e518b-507">Pokud prohlížeč odešle pověření, ale odpověď neobsahuje platné `Access-Control-Allow-Credentials` záhlaví, prohlížeč nezveřejňuje odpověď na aplikaci a požadavek na příčný původ se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e518b-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e518b-508">Povolení pověření pro více původ je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="e518b-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e518b-509">Web v jiné doméně může odeslat přihlašovací údaje přihlášeného uživatele do aplikace jménem uživatele bez vědomí uživatele.</span><span class="sxs-lookup"><span data-stu-id="e518b-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e518b-510">Specifikace CORS také uvádí, že `"*"` nastavení původu (všechny počátky) je neplatná, pokud `Access-Control-Allow-Credentials` je záhlaví k dispozici.</span><span class="sxs-lookup"><span data-stu-id="e518b-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="e518b-511">Požadavky kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-511">Preflight requests</span></span>

<span data-ttu-id="e518b-512">U některých požadavků CORS prohlížeč odešle další požadavek před provedením skutečné žádosti.</span><span class="sxs-lookup"><span data-stu-id="e518b-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="e518b-513">Tento požadavek se nazývá *požadavek kontroly před výstupem*.</span><span class="sxs-lookup"><span data-stu-id="e518b-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="e518b-514">Prohlížeč může přeskočit požadavek kontroly před výstupem, pokud jsou splněny následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="e518b-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="e518b-515">Metoda požadavku je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="e518b-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e518b-516">Aplikace nenastavuje záhlaví požadavků `Accept`kromě `Accept-Language` `Content-Language`, `Content-Type`, `Last-Event-ID`, nebo .</span><span class="sxs-lookup"><span data-stu-id="e518b-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e518b-517">Záhlaví, `Content-Type` pokud je nastaveno, má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="e518b-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e518b-518">Pravidlo pro hlavičky požadavku nastavené pro požadavek klienta se `setRequestHeader` vztahuje `XMLHttpRequest` na záhlaví, která aplikace nastaví voláním objektu.</span><span class="sxs-lookup"><span data-stu-id="e518b-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e518b-519">Specifikace CORS volá tyto hlavičky *záhlaví hlavičky požadavku autora*.</span><span class="sxs-lookup"><span data-stu-id="e518b-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="e518b-520">Pravidlo se nevztahuje na záhlaví, která může `User-Agent`prohlížeč `Host`nastavit, například , nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="e518b-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e518b-521">Následuje příklad požadavku kontroly před výstupem:</span><span class="sxs-lookup"><span data-stu-id="e518b-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="e518b-522">Předletový požadavek používá metodu HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="e518b-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="e518b-523">Obsahuje dvě speciální hlavičky:</span><span class="sxs-lookup"><span data-stu-id="e518b-523">It includes two special headers:</span></span>

* <span data-ttu-id="e518b-524">`Access-Control-Request-Method`: Metoda HTTP, která bude použita pro skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e518b-525">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví na skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e518b-526">Jak již bylo uvedeno dříve, nezahrnuje záhlaví, která `User-Agent`prohlížeč nastaví, například .</span><span class="sxs-lookup"><span data-stu-id="e518b-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="e518b-527">Pokud je cors povoleno s příslušnou zásadou, ASP.NET Core obecně automaticky reaguje na požadavky cors kontroly před výstupem.</span><span class="sxs-lookup"><span data-stu-id="e518b-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="e518b-528">Požadavky na kontroly před výstupem naleznete v [atributu [HttpOptions].](#pro)</span><span class="sxs-lookup"><span data-stu-id="e518b-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="e518b-529">Požadavek kontroly před výstupem CORS může obsahovat hlavičku, `Access-Control-Request-Headers` která označuje serveru záhlaví, která jsou odeslána se skutečným požadavkem.</span><span class="sxs-lookup"><span data-stu-id="e518b-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="e518b-530">Chcete-li povolit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>určitá záhlaví, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e518b-531">Chcete-li povolit všechny <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>hlavičky žádostí o autora, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e518b-532">Prohlížeče nejsou zcela konzistentní v tom, `Access-Control-Request-Headers`jak nastavit .</span><span class="sxs-lookup"><span data-stu-id="e518b-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e518b-533">Pokud nastavíte záhlaví na `"*"` cokoli <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>jiného než (nebo `Content-Type`použít `Origin`), měli byste zahrnout alespoň `Accept`, a , plus všechny vlastní záhlaví, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="e518b-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="e518b-534">Následuje příklad odpovědi na požadavek kontroly před výstupem (za předpokladu, že server tento požadavek povolí):</span><span class="sxs-lookup"><span data-stu-id="e518b-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="e518b-535">Odpověď obsahuje `Access-Control-Allow-Methods` záhlaví, které obsahuje seznam povolených metod a volitelně `Access-Control-Allow-Headers` záhlaví, které obsahuje povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="e518b-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="e518b-536">Pokud je požadavek kontroly před výstupem úspěšný, prohlížeč odešle skutečný požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="e518b-537">Pokud je požadavek kontroly před výstupem zamítnut, aplikace vrátí odpověď *200 OK,* ale neodešle záhlaví CORS zpět.</span><span class="sxs-lookup"><span data-stu-id="e518b-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e518b-538">Proto prohlížeč nepokouší požadavek křížového původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e518b-539">Nastavení doby vypršení platnosti kontroly před výstupem</span><span class="sxs-lookup"><span data-stu-id="e518b-539">Set the preflight expiration time</span></span>

<span data-ttu-id="e518b-540">Záhlaví `Access-Control-Max-Age` určuje, jak dlouho může být odpověď na požadavek kontroly před výstupem uložena do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="e518b-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e518b-541">Chcete-li nastavit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>toto záhlaví, volejte :</span><span class="sxs-lookup"><span data-stu-id="e518b-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e518b-542">Jak CORS funguje</span><span class="sxs-lookup"><span data-stu-id="e518b-542">How CORS works</span></span>

<span data-ttu-id="e518b-543">Tato část popisuje, co se děje v požadavku [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) na úrovni zpráv HTTP.</span><span class="sxs-lookup"><span data-stu-id="e518b-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e518b-544">CORS **není** funkce zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="e518b-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="e518b-545">CORS je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e518b-546">Škodlivý objekt actor může například použít [použít zabránit skriptování mezi webovými servery (XSS)](xref:security/cross-site-scripting) proti vašemu webu a provést požadavek na více webů na jejich webu s povoleným cors, aby ukradl informace.</span><span class="sxs-lookup"><span data-stu-id="e518b-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e518b-547">Vaše rozhraní API není bezpečnější povolením CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="e518b-548">Je na klientovi (prohlížeči), aby vynucoval CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e518b-549">Server provede požadavek a vrátí odpověď, je klient, který vrátí chybu a blokuje odpověď.</span><span class="sxs-lookup"><span data-stu-id="e518b-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e518b-550">Například některý z následujících nástrojů zobrazí odpověď serveru:</span><span class="sxs-lookup"><span data-stu-id="e518b-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="e518b-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e518b-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="e518b-552">Postman</span><span class="sxs-lookup"><span data-stu-id="e518b-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="e518b-553">Httpklient rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="e518b-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="e518b-554">Webový prohlížeč zadáním adresy URL do adresního řádku.</span><span class="sxs-lookup"><span data-stu-id="e518b-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e518b-555">Je to způsob, jak server umožňuje prohlížečům spustit požadavek [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) nebo [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro křížový původ, který by jinak byl zakázán.</span><span class="sxs-lookup"><span data-stu-id="e518b-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e518b-556">Prohlížeče (bez CORS) nemohou dělat požadavky na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="e518b-557">Před CORS byl [jsonp](https://www.w3schools.com/js/js_json_jsonp.asp) použit k obcházení tohoto omezení.</span><span class="sxs-lookup"><span data-stu-id="e518b-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e518b-558">JSONP nepoužívá XHR, používá `<script>` značku pro příjem odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e518b-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e518b-559">Skripty mohou být načteny cross-origin.</span><span class="sxs-lookup"><span data-stu-id="e518b-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e518b-560">[Specifikace CORS](https://www.w3.org/TR/cors/) zavedla několik nových hlaviček HTTP, které umožňují požadavky na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e518b-561">Pokud prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky křížového původu.</span><span class="sxs-lookup"><span data-stu-id="e518b-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e518b-562">Vlastní kód JavaScriptu není nutné povolit CORS.</span><span class="sxs-lookup"><span data-stu-id="e518b-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e518b-563">Následuje příklad požadavku na příčný původ.</span><span class="sxs-lookup"><span data-stu-id="e518b-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="e518b-564">Záhlaví `Origin` poskytuje doménu webu, který podává požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="e518b-565">Záhlaví `Origin` je povinné a musí se lišit od hostitele.</span><span class="sxs-lookup"><span data-stu-id="e518b-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="e518b-566">Pokud server umožňuje požadavek, `Access-Control-Allow-Origin` nastaví záhlaví v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e518b-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="e518b-567">Hodnota tohoto záhlaví se `Origin` shoduje s hlavičkou z požadavku `"*"`nebo je zástupnou hodnotou , což znamená, že je povolen libovolný původ:</span><span class="sxs-lookup"><span data-stu-id="e518b-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="e518b-568">Pokud odpověď neobsahuje `Access-Control-Allow-Origin` záhlaví, požadavek mezi původem se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="e518b-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e518b-569">Konkrétně prohlížeč zakáže požadavek.</span><span class="sxs-lookup"><span data-stu-id="e518b-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e518b-570">I v případě, že server vrátí úspěšnou odpověď, prohlížeč nezpřístupní odpověď klientské aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e518b-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="e518b-571">Test CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-571">Test CORS</span></span>

<span data-ttu-id="e518b-572">Chcete-li otestovat CORS:</span><span class="sxs-lookup"><span data-stu-id="e518b-572">To test CORS:</span></span>

1. <span data-ttu-id="e518b-573">[Vytvořte projekt rozhraní API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="e518b-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="e518b-574">Případně si můžete [stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="e518b-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="e518b-575">Povolte CORS pomocí jednoho z přístupů v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e518b-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="e518b-576">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e518b-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="e518b-577">`WithOrigins("https://localhost:<port>");`by měl být použit pouze pro testování ukázkové aplikace podobné [ukázkový kód ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="e518b-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="e518b-578">Vytvořte projekt webové aplikace (Razor Pages nebo MVC).</span><span class="sxs-lookup"><span data-stu-id="e518b-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="e518b-579">Ukázka používá Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="e518b-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="e518b-580">Webovou aplikaci můžete vytvořit ve stejném řešení jako projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e518b-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="e518b-581">Do souboru *Index.cshtml* přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="e518b-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="e518b-582">V předchozím kódu nahraďte `url: 'https://<web app>.azurewebsites.net/api/values/1',` adresou URL nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="e518b-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="e518b-583">Nasaďte projekt rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="e518b-583">Deploy the API project.</span></span> <span data-ttu-id="e518b-584">Například [nasazení do Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="e518b-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="e518b-585">Spusťte aplikaci Razor Pages nebo MVC z plochy a klikněte na tlačítko **Test.**</span><span class="sxs-lookup"><span data-stu-id="e518b-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="e518b-586">Pomocí nástrojů F12 zkontrolujte chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="e518b-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="e518b-587">Odeberte původ `WithOrigins` localhost z aplikace a nasadit aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e518b-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="e518b-588">Případně spusťte klientskou aplikaci s jiným portem.</span><span class="sxs-lookup"><span data-stu-id="e518b-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="e518b-589">Například spustit z Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e518b-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="e518b-590">Test ujte pomocí klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="e518b-590">Test with the client app.</span></span> <span data-ttu-id="e518b-591">Selhání CORS vrátí chybu, ale chybová zpráva není k dispozici pro JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e518b-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="e518b-592">K zobrazení chyby použijte kartu konzoly v nástrojích F12.</span><span class="sxs-lookup"><span data-stu-id="e518b-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="e518b-593">V závislosti na prohlížeči se zobrazí chyba (v konzole nástrojů F12) podobná následující:</span><span class="sxs-lookup"><span data-stu-id="e518b-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="e518b-594">Použití microsoft edge:</span><span class="sxs-lookup"><span data-stu-id="e518b-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="e518b-595">**SEC7120: [CORS] `https://localhost:44375` Původ nebyl `https://localhost:44375` našel v hlavičce odpovědi Access-Control-Allow-Origin pro prostředek křížového původu na adrese`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="e518b-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="e518b-596">Používání Chromu:</span><span class="sxs-lookup"><span data-stu-id="e518b-596">Using Chrome:</span></span>

     <span data-ttu-id="e518b-597">**Přístup k XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` na počátku byl zablokován zásadami CORS: Na požadovaném prostředku není k dispozici žádná hlavička "Access-Control-Allow-Origin".**</span><span class="sxs-lookup"><span data-stu-id="e518b-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="e518b-598">Koncové body s podporou CORS lze testovat pomocí nástroje, například [Fiddler](https://www.telerik.com/fiddler) nebo [Postman](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="e518b-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="e518b-599">Při použití nástroje se počátek požadavku `Origin` určeného hlavičkou musí lišit od hostitele, který požadavek obdržel.</span><span class="sxs-lookup"><span data-stu-id="e518b-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="e518b-600">Pokud požadavek není *cross-origin* na základě `Origin` hodnoty záhlaví:</span><span class="sxs-lookup"><span data-stu-id="e518b-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="e518b-601">Není třeba, aby CORS Middleware zpracoval žádost.</span><span class="sxs-lookup"><span data-stu-id="e518b-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="e518b-602">Hlavičky CORS nejsou vráceny v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e518b-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="e518b-603">CORS ve S.r.o.</span><span class="sxs-lookup"><span data-stu-id="e518b-603">CORS in IIS</span></span>

<span data-ttu-id="e518b-604">Při nasazování do služby IIS musí být cors spuštěn před ověřováním systému Windows, pokud server není nakonfigurován tak, aby umožňoval anonymní přístup.</span><span class="sxs-lookup"><span data-stu-id="e518b-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="e518b-605">Pro podporu tohoto scénáře je třeba nainstalovat a nakonfigurovat [modul IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e518b-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e518b-606">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e518b-606">Additional resources</span></span>

* [<span data-ttu-id="e518b-607">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="e518b-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="e518b-608">Začínáme s modulem IIS CORS</span><span class="sxs-lookup"><span data-stu-id="e518b-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
