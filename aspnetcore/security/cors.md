---
title: Povolení žádostí napříč zdroji (CORS) v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak CORS jako standard pro povolení nebo odmítnutí žádostí nepůvodního v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2018
uid: security/cors
ms.openlocfilehash: 8e5056b448d47d75272e9394b03ce8a58b05a0f4
ms.sourcegitcommit: 09affee3d234cb27ea6fe33bc113b79e68900d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51191318"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="8b120-103">Povolení žádostí napříč zdroji (CORS) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b120-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="8b120-104">Podle [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8b120-104">By [Mike Wasson](https://github.com/mikewasson), [Shayne Boyer](https://twitter.com/spboyer), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8b120-105">Zabezpečení prohlížečů brání zasílání požadavků na jiné doméně než ten, který obsluhuje webovou stránku na webové stránce.</span><span class="sxs-lookup"><span data-stu-id="8b120-105">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8b120-106">Toto omezení je volána *zásada stejného zdroje*.</span><span class="sxs-lookup"><span data-stu-id="8b120-106">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="8b120-107">Zásada stejného zdroje brání škodlivým webům ve čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="8b120-107">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="8b120-108">V některých případech můžete chtít povolit, že ostatní lokality provádět požadavky cross-origin do vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b120-108">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span>

<span data-ttu-id="8b120-109">[Mezi sdílení zdrojů původu](https://www.w3.org/TR/cors/) (CORS) je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-109">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="8b120-110">Pomocí CORS, server explicitně můžou některé požadavky cross-origin zatímco jiné odmítnout.</span><span class="sxs-lookup"><span data-stu-id="8b120-110">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="8b120-111">CORS je bezpečnější a flexibilnější, než starší techniky, jako například [JSONP](https://wikipedia.org/wiki/JSONP).</span><span class="sxs-lookup"><span data-stu-id="8b120-111">CORS is safer and more flexible than earlier techniques, such as [JSONP](https://wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="8b120-112">Toto téma ukazuje, jak povolit CORS v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b120-112">This topic shows how to enable CORS in an ASP.NET Core app.</span></span>

## <a name="same-origin"></a><span data-ttu-id="8b120-113">Stejného původu</span><span class="sxs-lookup"><span data-stu-id="8b120-113">Same origin</span></span>

<span data-ttu-id="8b120-114">Pokud mají stejné schémata, hostitele a porty mít dvě adresy URL stejného původu ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="8b120-114">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="8b120-115">Tyto dvě adresy URL mají stejného původu:</span><span class="sxs-lookup"><span data-stu-id="8b120-115">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="8b120-116">Tyto adresy URL mají různé zdroje než předchozí dvě adresy URL:</span><span class="sxs-lookup"><span data-stu-id="8b120-116">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="8b120-117">`https://example.net` &ndash; Jiné domény</span><span class="sxs-lookup"><span data-stu-id="8b120-117">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="8b120-118">`https://www.example.com/foo.html` &ndash; Různé subdomény</span><span class="sxs-lookup"><span data-stu-id="8b120-118">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="8b120-119">`http://example.com/foo.html` &ndash; Jiné schéma</span><span class="sxs-lookup"><span data-stu-id="8b120-119">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="8b120-120">`https://example.com:9000/foo.html` &ndash; Jiný port</span><span class="sxs-lookup"><span data-stu-id="8b120-120">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

> [!NOTE]
> <span data-ttu-id="8b120-121">Aplikace Internet Explorer nezahrne port při porovnání zdrojů.</span><span class="sxs-lookup"><span data-stu-id="8b120-121">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="register-cors-services"></a><span data-ttu-id="8b120-122">Registrace služby CORS</span><span class="sxs-lookup"><span data-stu-id="8b120-122">Register CORS services</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8b120-123">Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="8b120-123">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8b120-124">Odkaz [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) nebo přidat odkaz na balíček [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="8b120-124">Reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="8b120-125">Přidejte odkaz na balíček [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="8b120-125">Add a package reference to the [Microsoft.AspNetCore.Cors](https://www.nuget.org/packages/Microsoft.AspNetCore.Cors/) package.</span></span>

::: moniker-end

<span data-ttu-id="8b120-126">Volání <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> v `Startup.ConfigureServices` přidejte CORS služby do aplikace služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="8b120-126">Call <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> in `Startup.ConfigureServices` to add CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/CorsExample1/Startup.cs?name=snippet_addcors&highlight=3)]

## <a name="enable-cors"></a><span data-ttu-id="8b120-127">Povolení CORS</span><span class="sxs-lookup"><span data-stu-id="8b120-127">Enable CORS</span></span>

<span data-ttu-id="8b120-128">Po registraci služby CORS, povolení CORS v aplikaci ASP.NET Core pomocí některé z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="8b120-128">After registering CORS services, use either of the following approaches to enable CORS in an ASP.NET Core app:</span></span>

* <span data-ttu-id="8b120-129">[CORS Middleware](#enable-cors-with-cors-middleware) &ndash; zásady CORS platí globálně do aplikace přes middleware.</span><span class="sxs-lookup"><span data-stu-id="8b120-129">[CORS Middleware](#enable-cors-with-cors-middleware) &ndash; Apply CORS policies globally to the app via middleware.</span></span>
* <span data-ttu-id="8b120-130">[CORS v MVC](#enable-cors-in-mvc) &ndash; CORS použít zásady podle akce nebo kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8b120-130">[CORS in MVC](#enable-cors-in-mvc) &ndash; Apply CORS policies per action or per controller.</span></span> <span data-ttu-id="8b120-131">CORS Middleware se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="8b120-131">CORS Middleware isn't used.</span></span>

### <a name="enable-cors-with-cors-middleware"></a><span data-ttu-id="8b120-132">Povolení CORS s Middlewarem CORS</span><span class="sxs-lookup"><span data-stu-id="8b120-132">Enable CORS with CORS Middleware</span></span>

<span data-ttu-id="8b120-133">CORS Middleware zpracovává požadavky cross-origin do aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b120-133">CORS Middleware handles cross-origin requests to the app.</span></span> <span data-ttu-id="8b120-134">Chcete-li povolit CORS Middleware v kanálu zpracování požadavků, zavolejte <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metody rozšíření v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="8b120-134">To enable CORS Middleware in the request processing pipeline, call the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method in `Startup.Configure`.</span></span>

<span data-ttu-id="8b120-135">CORS Middleware musí předcházet všechny koncové body definované ve vaší aplikaci ve které chcete podporovat požadavky cross-origin (například před voláním `UseMvc` pro Middleware stránky MVC a Razor).</span><span class="sxs-lookup"><span data-stu-id="8b120-135">CORS Middleware must precede any defined endpoints in your app where you want to support cross-origin requests (for example, before the call to `UseMvc` for MVC/Razor Pages Middleware).</span></span>

<span data-ttu-id="8b120-136">A *nepůvodního zdroje zásad* lze zadat při přidání pomocí CORS Middleware <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> třídy.</span><span class="sxs-lookup"><span data-stu-id="8b120-136">A *cross-origin policy* can be specified when adding the CORS Middleware using the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> class.</span></span> <span data-ttu-id="8b120-137">Existují dva přístupy k definování zásad CORS:</span><span class="sxs-lookup"><span data-stu-id="8b120-137">There are two approaches for defining a CORS policy:</span></span>

* <span data-ttu-id="8b120-138">Volání `UseCors` pomocí výrazu lambda:</span><span class="sxs-lookup"><span data-stu-id="8b120-138">Call `UseCors` with a lambda:</span></span>

  [!code-csharp[](cors/sample/CorsExample1/Startup.cs?highlight=11,12&range=22-38)]

  <span data-ttu-id="8b120-139">Používá výraz lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objektu.</span><span class="sxs-lookup"><span data-stu-id="8b120-139">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="8b120-140">[Možnosti konfigurace](#cors-policy-options), jako například `WithOrigins`, jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="8b120-140">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this topic.</span></span> <span data-ttu-id="8b120-141">V předchozím příkladu, zásada umožňuje požadavky cross-origin z `https://example.com` a žádné jiné zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-141">In the preceding example, the policy allows cross-origin requests from `https://example.com` and no other origins.</span></span>

  <span data-ttu-id="8b120-142">Adresa URL musí být zadán bez koncové lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="8b120-142">The URL must be specified without a trailing slash (`/`).</span></span> <span data-ttu-id="8b120-143">Pokud adresa URL se ukončí s `/`, porovnání vrátí `false` a vrátí se bez záhlaví.</span><span class="sxs-lookup"><span data-stu-id="8b120-143">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

  <span data-ttu-id="8b120-144">`CorsPolicyBuilder` obsahuje rozhraní API fluent, takže můžete vytvořit řetězové volání metody:</span><span class="sxs-lookup"><span data-stu-id="8b120-144">`CorsPolicyBuilder` has a fluent API, so you can chain method calls:</span></span>

  [!code-csharp[](cors/sample/CorsExample3/Startup.cs?highlight=2-3&range=29-32)]

* <span data-ttu-id="8b120-145">Definovat jeden nebo více s názvem zásady CORS a vyberte zásadu podle názvu za běhu.</span><span class="sxs-lookup"><span data-stu-id="8b120-145">Define one or more named CORS policies and select the policy by name at runtime.</span></span> <span data-ttu-id="8b120-146">Následující příklad přidá uživatelem definované zásady CORS s názvem *AllowSpecificOrigin*.</span><span class="sxs-lookup"><span data-stu-id="8b120-146">The following example adds a user-defined CORS policy named *AllowSpecificOrigin*.</span></span> <span data-ttu-id="8b120-147">Vyberte zásadu, předejte název, který má `UseCors`:</span><span class="sxs-lookup"><span data-stu-id="8b120-147">To select the policy, pass the name to `UseCors`:</span></span>

  [!code-csharp[](cors/sample/CorsExample2/Startup.cs?name=snippet_begin&highlight=5-6,21)]

### <a name="enable-cors-in-mvc"></a><span data-ttu-id="8b120-148">Povolení CORS v aplikaci MVC</span><span class="sxs-lookup"><span data-stu-id="8b120-148">Enable CORS in MVC</span></span>

<span data-ttu-id="8b120-149">Můžete také použít MVC použít konkrétní zásady CORS podle akce nebo kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8b120-149">You can alternatively use MVC to apply specific CORS policies per action or per controller.</span></span> <span data-ttu-id="8b120-150">Při povolení CORS pomocí MVC, se používají registrovaných služeb CORS.</span><span class="sxs-lookup"><span data-stu-id="8b120-150">When using MVC to enable CORS, the registered CORS services are used.</span></span> <span data-ttu-id="8b120-151">CORS Middleware se nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="8b120-151">The CORS Middleware isn't used.</span></span>

### <a name="per-action"></a><span data-ttu-id="8b120-152">Za akci</span><span class="sxs-lookup"><span data-stu-id="8b120-152">Per action</span></span>

<span data-ttu-id="8b120-153">Chcete-li určit zásady CORS pro určité akce, přidejte [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut na akci.</span><span class="sxs-lookup"><span data-stu-id="8b120-153">To specify a CORS policy for a specific action, add the [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute to the action.</span></span> <span data-ttu-id="8b120-154">Zadejte název zásady.</span><span class="sxs-lookup"><span data-stu-id="8b120-154">Specify the policy name.</span></span>

[!code-csharp[](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnAction&highlight=2)]

### <a name="per-controller"></a><span data-ttu-id="8b120-155">Na kontroler</span><span class="sxs-lookup"><span data-stu-id="8b120-155">Per controller</span></span>

<span data-ttu-id="8b120-156">Chcete-li určit zásady CORS pro konkrétní ovladač, přidejte [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atribut třídy kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8b120-156">To specify the CORS policy for a specific controller, add the [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute to the controller class.</span></span> <span data-ttu-id="8b120-157">Zadejte název zásady.</span><span class="sxs-lookup"><span data-stu-id="8b120-157">Specify the policy name.</span></span>

[!code-csharp[](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=EnableOnController&highlight=2)]

<span data-ttu-id="8b120-158">Pořadí priority je:</span><span class="sxs-lookup"><span data-stu-id="8b120-158">The precedence order is:</span></span>

1. <span data-ttu-id="8b120-159">Akce</span><span class="sxs-lookup"><span data-stu-id="8b120-159">action</span></span>
1. <span data-ttu-id="8b120-160">kontroler</span><span class="sxs-lookup"><span data-stu-id="8b120-160">controller</span></span>

### <a name="disable-cors"></a><span data-ttu-id="8b120-161">Zákazu sdílení CORS</span><span class="sxs-lookup"><span data-stu-id="8b120-161">Disable CORS</span></span>

<span data-ttu-id="8b120-162">Chcete-li zakázat CORS pro kontroler nebo akce, použijte [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) atribut:</span><span class="sxs-lookup"><span data-stu-id="8b120-162">To disable CORS for a controller or action, use the [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute:</span></span>

[!code-csharp[](cors/sample/CorsMVC/Controllers/ValuesController.cs?name=DisableOnAction&highlight=2)]

## <a name="cors-policy-options"></a><span data-ttu-id="8b120-163">Možnosti zásad CORS</span><span class="sxs-lookup"><span data-stu-id="8b120-163">CORS policy options</span></span>

<span data-ttu-id="8b120-164">Tato část popisuje různé možnosti, které můžete nastavit zásadu CORS.</span><span class="sxs-lookup"><span data-stu-id="8b120-164">This section describes the various options that you can set in a CORS policy.</span></span>

* [<span data-ttu-id="8b120-165">Nastavte povolené zdroje</span><span class="sxs-lookup"><span data-stu-id="8b120-165">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="8b120-166">Nastavte povolené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="8b120-166">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="8b120-167">Nastavit hlavičku povolené žádosti</span><span class="sxs-lookup"><span data-stu-id="8b120-167">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="8b120-168">Nastavit hlavičky vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="8b120-168">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="8b120-169">Přihlašovací údaje v požadavky cross-origin</span><span class="sxs-lookup"><span data-stu-id="8b120-169">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="8b120-170">Nastavit čas vypršení platnosti předběžné</span><span class="sxs-lookup"><span data-stu-id="8b120-170">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="8b120-171">Pro některé možnosti, může být užitečné ke čtení [funguje jak CORS](#how-cors-works) první části.</span><span class="sxs-lookup"><span data-stu-id="8b120-171">For some options, it may be helpful to read the [How CORS works](#how-cors-works) section first.</span></span>

### <a name="set-the-allowed-origins"></a><span data-ttu-id="8b120-172">Nastavte povolené zdroje</span><span class="sxs-lookup"><span data-stu-id="8b120-172">Set the allowed origins</span></span>

<span data-ttu-id="8b120-173">Middleware CORS v ASP.NET Core MVC má několik způsobů, jak určit povolené zdroje:</span><span class="sxs-lookup"><span data-stu-id="8b120-173">The CORS middleware in ASP.NET Core MVC has a few ways to specify allowed origins:</span></span>

* <span data-ttu-id="8b120-174"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithOrigins*>: Umožňuje zadat jednu nebo více adres URL.</span><span class="sxs-lookup"><span data-stu-id="8b120-174"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithOrigins*>: Allows specifying one or more URLs.</span></span> <span data-ttu-id="8b120-175">Adresa URL může obsahovat schéma, název hostitele a port bez jakýchkoli informací o cestě.</span><span class="sxs-lookup"><span data-stu-id="8b120-175">The URL may include the scheme, host name, and port without any path information.</span></span> <span data-ttu-id="8b120-176">Například `https://example.com`.</span><span class="sxs-lookup"><span data-stu-id="8b120-176">For example, `https://example.com`.</span></span> <span data-ttu-id="8b120-177">Adresa URL musí být zadán bez koncové lomítko (`/`).</span><span class="sxs-lookup"><span data-stu-id="8b120-177">The URL must be specified without a trailing slash (`/`).</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=20-24&highlight=4)]

* <span data-ttu-id="8b120-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Umožňuje požadavků CORS z všechny původy žádné schéma (`http` nebo `https`).</span><span class="sxs-lookup"><span data-stu-id="8b120-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=28-32&highlight=4)]

<span data-ttu-id="8b120-179">Pečlivě zvažte předtím, než žádosti z původu.</span><span class="sxs-lookup"><span data-stu-id="8b120-179">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="8b120-180">Povolení žádostí z jakýkoli původ znamená, že *všechny weby* můžete provádět požadavky cross-origin vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b120-180">Allowing requests from any origin means that *any website* can make cross-origin requests to your app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="8b120-181">Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů.</span><span class="sxs-lookup"><span data-stu-id="8b120-181">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="8b120-182">CORS služby vrátí neplatnou odpověď CORS, pokud aplikace je nakonfigurovaná s nimi.</span><span class="sxs-lookup"><span data-stu-id="8b120-182">The CORS service returns an invalid CORS response when an app is configured with the two.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="8b120-183">Určení `AllowAnyOrigin` a `AllowCredentials` je nezabezpečené konfigurace a může vést k padělání žádosti více webů.</span><span class="sxs-lookup"><span data-stu-id="8b120-183">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="8b120-184">Zvažte možnost určení přesný seznam původů, pokud klient potřebuje k autorizaci pro přístup k prostředkům serveru.</span><span class="sxs-lookup"><span data-stu-id="8b120-184">Consider specifying an exact list of origins if your client needs to authorize to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="8b120-185">Toto nastavení má vliv [časový limit předběžné požadavky a hlavičky Access-Control-Allow-Origin](#preflight-requests) (popsáno dále v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="8b120-185">This setting affects [preflight requests and the Access-Control-Allow-Origin header](#preflight-requests) (described later in this topic).</span></span>

* <span data-ttu-id="8b120-186"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> – Umožňuje požadavků CORS domén dílčí dané domény.</span><span class="sxs-lookup"><span data-stu-id="8b120-186"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> - Allows CORS requests from any sub-domain of a given domain.</span></span> <span data-ttu-id="8b120-187">Schéma nemůže být zástupný znak.</span><span class="sxs-lookup"><span data-stu-id="8b120-187">The scheme cannot be a wildcard.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=98-104&highlight=4)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="8b120-188">Nastavte povolené metody HTTP</span><span class="sxs-lookup"><span data-stu-id="8b120-188">Set the allowed HTTP methods</span></span>

<span data-ttu-id="8b120-189">Chcete-li povolit všechny metody HTTP, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-189">To allow all HTTP methods, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=45-50&highlight=5)]

<span data-ttu-id="8b120-190">Toto nastavení má vliv [časový limit předběžné požadavky a záhlaví přístupu – ovládací prvek-Allow-Methods](#preflight-requests) (popsáno dále v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="8b120-190">This setting affects [preflight requests and the Access-Control-Allow-Methods header](#preflight-requests) (described later in this topic).</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="8b120-191">Nastavit hlavičku povolené žádosti</span><span class="sxs-lookup"><span data-stu-id="8b120-191">Set the allowed request headers</span></span>

<span data-ttu-id="8b120-192">Volá se, aby konkrétní hlavičky se odešle žádost CORS *vytvářet hlavičky žádosti*, volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> a zadat povolené hlavičky:</span><span class="sxs-lookup"><span data-stu-id="8b120-192">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=54-59&highlight=5)]

<span data-ttu-id="8b120-193">Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-193">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=63-68&highlight=5)]

<span data-ttu-id="8b120-194">Toto nastavení má vliv [časový limit předběžné požadavky a hlavičky Access-Control-Request-Headers](#preflight-requests) (popsáno dále v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="8b120-194">This setting affects [preflight requests and the Access-Control-Request-Headers header](#preflight-requests) (described later in this topic).</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="8b120-195">Shoda se zásadami Middlewarem CORS pro konkrétní hlavičky určené `WithHeaders` je možné, pouze při odeslání hlaviček `Access-Control-Request-Headers` přesně odpovídat záhlaví uvádí `WithHeaders`.</span><span class="sxs-lookup"><span data-stu-id="8b120-195">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="8b120-196">Zvažte například aplikaci nakonfigurovány takto:</span><span class="sxs-lookup"><span data-stu-id="8b120-196">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="8b120-197">CORS Middleware předběžný požadavek s následující hlavičky žádosti odmítne, protože `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) není uveden ve `WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="8b120-197">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="8b120-198">Vrátí aplikaci *200 OK* odpověď, ale nebude odesílat hlavičky CORS zpět.</span><span class="sxs-lookup"><span data-stu-id="8b120-198">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="8b120-199">Prohlížeč proto nebude se pokoušet žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-199">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="8b120-200">CORS Middleware vždy povoluje čtyři záhlaví v `Access-Control-Request-Headers` k odeslání bez ohledu na nakonfigurované v CorsPolicy.Headers hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8b120-200">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="8b120-201">Tento seznam hlaviček obsahuje:</span><span class="sxs-lookup"><span data-stu-id="8b120-201">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="8b120-202">Zvažte například aplikaci nakonfigurovány takto:</span><span class="sxs-lookup"><span data-stu-id="8b120-202">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="8b120-203">CORS Middleware se úspěšně odpoví na předběžný požadavek s následující hlavičky žádosti protože `Content-Language` je vždy přidat na seznam povolených:</span><span class="sxs-lookup"><span data-stu-id="8b120-203">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="8b120-204">Nastavit hlavičky vystavené odpovědi</span><span class="sxs-lookup"><span data-stu-id="8b120-204">Set the exposed response headers</span></span>

<span data-ttu-id="8b120-205">Ve výchozím prohlížeči nezveřejňuje všechny hlavičky odpovědí do aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b120-205">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="8b120-206">Další informace najdete v tématu [W3C napříč sdílení prostředků různého původu (terminologie): jednoduché hlavička odpovědi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="8b120-206">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="8b120-207">Hlavičky odpovědi, které jsou k dispozici ve výchozím nastavení jsou:</span><span class="sxs-lookup"><span data-stu-id="8b120-207">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="8b120-208">Specifikace CORS volá tyto hlavičky *hlavičky odpovědi jednoduché*.</span><span class="sxs-lookup"><span data-stu-id="8b120-208">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="8b120-209">Chcete-li zpřístupnit další hlavičky pro aplikace, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-209">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=72-77&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="8b120-210">Přihlašovací údaje v požadavky cross-origin</span><span class="sxs-lookup"><span data-stu-id="8b120-210">Credentials in cross-origin requests</span></span>

<span data-ttu-id="8b120-211">Přihlašovací údaje vyžadují speciální zacházení v požadavku CORS.</span><span class="sxs-lookup"><span data-stu-id="8b120-211">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="8b120-212">Ve výchozím nastavení nebude prohlížeč odesílá pověření s žádostí nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-212">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="8b120-213">Přihlašovací údaje zahrnují soubory cookie a schémat ověřování protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b120-213">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="8b120-214">Odesílá pověření s žádostí nepůvodního zdroje, musíte nastavit klienta `XMLHttpRequest.withCredentials` k `true`.</span><span class="sxs-lookup"><span data-stu-id="8b120-214">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="8b120-215">Pomocí `XMLHttpRequest` přímo:</span><span class="sxs-lookup"><span data-stu-id="8b120-215">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="8b120-216">V jQuery:</span><span class="sxs-lookup"><span data-stu-id="8b120-216">In jQuery:</span></span>

```jQuery
$.ajax({
  type: 'get',
  url: 'https://www.example.com/home',
  xhrFields: {
    withCredentials: true
}
```

<span data-ttu-id="8b120-217">Na serveru navíc musíte povolit přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="8b120-217">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="8b120-218">Chcete-li povolit přihlašovací údaje mezi zdroji, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-218">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=81-86&highlight=5)]

<span data-ttu-id="8b120-219">Odpověď HTTP, která zahrnuje `Access-Control-Allow-Credentials` hlavičky, která sděluje prohlížeči, že server umožňuje přihlašovací údaje pro žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-219">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="8b120-220">Pokud prohlížeč odesílá pověření, ale odpověď neobsahuje platný `Access-Control-Allow-Credentials` záhlaví, v prohlížeči nezveřejňuje odpovědi do aplikace a nepůvodního požadavek selže.</span><span class="sxs-lookup"><span data-stu-id="8b120-220">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="8b120-221">Buďte opatrní při povolování pověření nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-221">Be careful when allowing cross-origin credentials.</span></span> <span data-ttu-id="8b120-222">Web v jiné doméně odeslat do aplikace jménem uživatele bez vědomí uživatele pověření přihlášeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="8b120-222">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span>

<span data-ttu-id="8b120-223">Specifikace CORS také uvádí nastavení počátky k `"*"` (všechny původy) je neplatná-li `Access-Control-Allow-Credentials` záhlaví je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="8b120-223">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="8b120-224">Předběžných požadavků</span><span class="sxs-lookup"><span data-stu-id="8b120-224">Preflight requests</span></span>

<span data-ttu-id="8b120-225">U některých požadavků CORS prohlížeč odešle požadavek další před provedením aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b120-225">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="8b120-226">Tento požadavek je volána *předběžný požadavek*.</span><span class="sxs-lookup"><span data-stu-id="8b120-226">This request is called a *preflight request*.</span></span> <span data-ttu-id="8b120-227">Prohlížeči můžete přeskočit předběžný požadavek, pokud jsou splněny následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="8b120-227">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="8b120-228">Metoda žádosti je GET, HEAD nebo POST.</span><span class="sxs-lookup"><span data-stu-id="8b120-228">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="8b120-229">Aplikaci nelze nastavit hlavičku žádosti jiných než `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, nebo `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="8b120-229">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="8b120-230">`Content-Type` Záhlaví,-li nastavit, protože má jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="8b120-230">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="8b120-231">Sada pravidel na hlavičky požadavku pro požadavek klienta se vztahuje na hlavičky, které aplikace nastaví voláním `setRequestHeader` na `XMLHttpRequest` objektu.</span><span class="sxs-lookup"><span data-stu-id="8b120-231">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="8b120-232">Specifikace CORS volá tyto hlavičky *vytvářet hlavičky požadavku*.</span><span class="sxs-lookup"><span data-stu-id="8b120-232">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="8b120-233">Toto pravidlo neplatí pro záhlaví můžete nastavit v prohlížeči, jako například `User-Agent`, `Host`, nebo `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="8b120-233">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="8b120-234">Následuje příklad předběžný požadavek:</span><span class="sxs-lookup"><span data-stu-id="8b120-234">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="8b120-235">Přípravné požadavek používá metodu HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="8b120-235">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="8b120-236">Obsahuje dva speciálními záhlavími:</span><span class="sxs-lookup"><span data-stu-id="8b120-236">It includes two special headers:</span></span>

* <span data-ttu-id="8b120-237">`Access-Control-Request-Method`: Metoda HTTP, který se použije pro aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b120-237">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="8b120-238">`Access-Control-Request-Headers`: Seznam hlaviček požadavků, které aplikace nastaví u aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b120-238">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="8b120-239">Jak bylo uvedeno dříve, to nezahrnuje hlavičky, které nastaví v prohlížeči, jako například `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="8b120-239">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="8b120-240">Předběžný požadavek CORS patří `Access-Control-Request-Headers` hlavičky, která označuje hlavičky, které jsou odeslány pomocí aktuálního požadavku na server.</span><span class="sxs-lookup"><span data-stu-id="8b120-240">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="8b120-241">Chcete-li povolit konkrétní hlavičky, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-241">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=54-59&highlight=5)]

<span data-ttu-id="8b120-242">Chcete-li povolit všechny hlavičky žádosti, vytvářet volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-242">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=63-68&highlight=5)]

<span data-ttu-id="8b120-243">Nejsou zcela konzistentní v tom, jak je nastavit prohlížeče `Access-Control-Request-Headers`.</span><span class="sxs-lookup"><span data-stu-id="8b120-243">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="8b120-244">Pokud nastavíte záhlaví na něco jiného než `"*"` (nebo použijte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), by měl obsahovat alespoň `Accept`, `Content-Type`, a `Origin`, a navíc jakékoli vlastní hlavičky, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="8b120-244">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="8b120-245">Následuje příklad odpovědi pro předběžný požadavek (za předpokladu, že server umožňuje žádosti):</span><span class="sxs-lookup"><span data-stu-id="8b120-245">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="8b120-246">Odpověď obsahuje `Access-Control-Allow-Methods` hlavičku, která uvádí povolené metody a volitelně `Access-Control-Allow-Headers` hlavičky, která uvádí povolené hlavičky.</span><span class="sxs-lookup"><span data-stu-id="8b120-246">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="8b120-247">Pokud je předběžný požadavek úspěšné, prohlížeč odesílá aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b120-247">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="8b120-248">Pokud je předběžný požadavek, vrátí aplikaci *200 OK* odpovědi ale nebude odesílat hlavičky CORS zpět.</span><span class="sxs-lookup"><span data-stu-id="8b120-248">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="8b120-249">Prohlížeč proto nebude se pokoušet žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-249">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="8b120-250">Nastavit čas vypršení platnosti předběžné</span><span class="sxs-lookup"><span data-stu-id="8b120-250">Set the preflight expiration time</span></span>

<span data-ttu-id="8b120-251">`Access-Control-Max-Age` Záhlaví Určuje, jak dlouho může do mezipaměti odpovědi pro předběžný požadavek.</span><span class="sxs-lookup"><span data-stu-id="8b120-251">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="8b120-252">Chcete-li nastavit tuto hlavičku, zavolejte <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="8b120-252">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=90-95&highlight=5)]

## <a name="how-cors-works"></a><span data-ttu-id="8b120-253">Jak funguje CORS</span><span class="sxs-lookup"><span data-stu-id="8b120-253">How CORS works</span></span>

<span data-ttu-id="8b120-254">Tato část popisuje, co se stane, že v požadavku CORS na úrovni zprávy HTTP.</span><span class="sxs-lookup"><span data-stu-id="8b120-254">This section describes what happens in a CORS request at the level of the HTTP messages.</span></span> <span data-ttu-id="8b120-255">Je důležité pochopit, jak CORS funguje tak, aby zásady CORS můžete nakonfigurovat správně a ladit, když dojde k neočekávané chování.</span><span class="sxs-lookup"><span data-stu-id="8b120-255">It's important to understand how CORS works so that the CORS policy can be configured correctly and debugged when unexpected behaviors occur.</span></span>

<span data-ttu-id="8b120-256">Specifikace CORS zavádí několik nové hlavičky protokolu HTTP, které umožňují požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="8b120-256">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="8b120-257">Pokud je prohlížeč podporuje CORS, nastaví tyto hlavičky automaticky pro požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="8b120-257">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="8b120-258">Vlastní kód jazyka JavaScript není vyžadován k povolení sdílení CORS.</span><span class="sxs-lookup"><span data-stu-id="8b120-258">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="8b120-259">Následuje příklad žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="8b120-259">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="8b120-260">`Origin` Záhlaví obsahuje domény, lokality, která odeslala žádost:</span><span class="sxs-lookup"><span data-stu-id="8b120-260">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="8b120-261">Pokud server umožňuje, aby žádosti, nastaví `Access-Control-Allow-Origin` hlaviček v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="8b120-261">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="8b120-262">Hodnotu této hlavičky odpovídá buď `Origin` hlavička ze žádosti nebo je hodnota zástupného znaku `"*"`, což znamená, že jakýkoli původ je povoleno:</span><span class="sxs-lookup"><span data-stu-id="8b120-262">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="8b120-263">Pokud odpověď neobsahuje `Access-Control-Allow-Origin` hlavičky žádosti nepůvodního selže.</span><span class="sxs-lookup"><span data-stu-id="8b120-263">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="8b120-264">Konkrétně v prohlížeči zakazuje požadavku.</span><span class="sxs-lookup"><span data-stu-id="8b120-264">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="8b120-265">I v případě, že server vrátí úspěšné odpovědi, prohlížeč nevyužívá odpovědi k dispozici pro klientské aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b120-265">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b120-266">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b120-266">Additional resources</span></span>

* [<span data-ttu-id="8b120-267">Prostředků mezi zdroji (CORS) pro sdílení obsahu</span><span class="sxs-lookup"><span data-stu-id="8b120-267">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
