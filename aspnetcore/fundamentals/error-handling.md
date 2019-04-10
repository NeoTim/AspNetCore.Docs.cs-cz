---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/error-handling
ms.openlocfilehash: cbb9462a3c6010e074dc391aa128ac2cbb901456
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468747"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="f8c46-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8c46-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="f8c46-104">Podle [Petr Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f8c46-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f8c46-105">Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f8c46-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="f8c46-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f8c46-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f8c46-107">([Stažení](xref:index#how-to-download-a-sample).) Tento článek obsahuje pokyny ohledně toho, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci povolit různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="f8c46-107">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f8c46-108">Stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="f8c46-108">Developer Exception Page</span></span>

<span data-ttu-id="f8c46-109">*Stránku výjimek pro vývojáře* zobrazí podrobné informace o žádosti o výjimkách.</span><span class="sxs-lookup"><span data-stu-id="f8c46-109">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f8c46-110">Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f8c46-110">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f8c46-111">Přidejte kód, který `Startup.Configure` metoda povolit na stránce, když aplikace běží při vývoji [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="f8c46-111">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="f8c46-112">Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před veškerý middleware, který chcete zaznamenat tak výjimky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-112">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="f8c46-113">Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="f8c46-113">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f8c46-114">Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f8c46-114">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="f8c46-115">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f8c46-115">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f8c46-116">Stránka obsahuje následující informace o výjimku a požadavek:</span><span class="sxs-lookup"><span data-stu-id="f8c46-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f8c46-117">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="f8c46-117">Stack trace</span></span>
* <span data-ttu-id="f8c46-118">Parametry řetězce dotazu (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="f8c46-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="f8c46-119">Soubory cookie (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="f8c46-119">Cookies (if any)</span></span>
* <span data-ttu-id="f8c46-120">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="f8c46-120">Headers</span></span>

<span data-ttu-id="f8c46-121">Zobrazíte na stránce výjimek pro vývojáře v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `DevEnvironment` preprocesor směrnice a vyberte **spuštění výjimky** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f8c46-121">To see the Developer Exception Page in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f8c46-122">Stránka obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="f8c46-122">Exception handler page</span></span>

<span data-ttu-id="f8c46-123">Pokud chcete nakonfigurovat vlastní chybové stránky pro produkční prostředí zpracování, použijte Middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="f8c46-123">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="f8c46-124">Middleware:</span><span class="sxs-lookup"><span data-stu-id="f8c46-124">The middleware:</span></span>

* <span data-ttu-id="f8c46-125">Zachytí a protokolů výjimek.</span><span class="sxs-lookup"><span data-stu-id="f8c46-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f8c46-126">Znovu provede požadavek na alternativní kanálu pro stránku nebo řadič uvedené.</span><span class="sxs-lookup"><span data-stu-id="f8c46-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="f8c46-127">Daný požadavek není znovu spustit, pokud odpověď byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="f8c46-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="f8c46-128">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá Middleware zpracování výjimek v jiných vývojových prostředích:</span><span class="sxs-lookup"><span data-stu-id="f8c46-128">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f8c46-129">Šablona aplikace Razor Pages poskytuje chybovou stránku (*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy (`ErrorModel`) v *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-129">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f8c46-130">Šablona projektu pro aplikace MVC, obsahuje metodu akce chyba a zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="f8c46-130">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="f8c46-131">Tady je metoda akce:</span><span class="sxs-lookup"><span data-stu-id="f8c46-131">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="f8c46-132">Není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="f8c46-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f8c46-133">Explicitní příkazy zabránit v dosažení metodu některé požadavky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="f8c46-134">Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="f8c46-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f8c46-135">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="f8c46-135">Access the exception</span></span>

<span data-ttu-id="f8c46-136">Použití <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestu požadavku v kontroleru obslužná rutina chyby nebo stránky:</span><span class="sxs-lookup"><span data-stu-id="f8c46-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="f8c46-137">Proveďte **není** poskytovat informace o chybě citlivé klientům.</span><span class="sxs-lookup"><span data-stu-id="f8c46-137">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f8c46-138">Obsluhuje chyby představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="f8c46-138">Serving errors is a security risk.</span></span>

<span data-ttu-id="f8c46-139">Chcete zobrazit stránku zpracování výjimek v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` a `ErrorHandlerPage` direktivy preprocesoru a vyberte **spuštění výjimky** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f8c46-139">To see the exception handling page in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f8c46-140">Lambda obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="f8c46-140">Exception handler lambda</span></span>

<span data-ttu-id="f8c46-141">Alternativa k [stránku obslužné rutiny vlastních výjimek](#exception-handler-page) je poskytnout lambda <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="f8c46-141">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="f8c46-142">Použití výrazu lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f8c46-142">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f8c46-143">Tady je příklad použití výrazu lambda pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="f8c46-143">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> <span data-ttu-id="f8c46-144">Proveďte **není** poskytovat informace o chybě citlivé z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům.</span><span class="sxs-lookup"><span data-stu-id="f8c46-144">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f8c46-145">Obsluhuje chyby představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="f8c46-145">Serving errors is a security risk.</span></span>

<span data-ttu-id="f8c46-146">Chcete-li zobrazit výsledek tohoto výrazu lambda zpracování výjimek v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` a `ErrorHandlerLambda` direktivy preprocesoru a vyberte **spuštění výjimky** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f8c46-146">To see the result of the exception handling lambda in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f8c46-147">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f8c46-147">UseStatusCodePages</span></span>

<span data-ttu-id="f8c46-148">Ve výchozím nastavení, aplikace ASP.NET Core neposkytuje znakovou stránku Stav pro stavové kódy HTTP, jako například *404 - Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="f8c46-148">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f8c46-149">Aplikace se vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f8c46-149">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="f8c46-150">Pokud chcete poskytnout stav znakové stránky, použijte middleware stav znakové stránky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-150">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="f8c46-151">Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f8c46-151">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f8c46-152">Chcete-li povolit výchozích prostého textu obslužných rutin pro běžné kódy chyb stav, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> v `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f8c46-152">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="f8c46-153">Volání `UseStatusCodePages` před požadavkem zpracování middleware (například Middleware statické soubory a Middlewarem MVC).</span><span class="sxs-lookup"><span data-stu-id="f8c46-153">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="f8c46-154">Tady je příklad text, zobrazený výchozích obslužných rutin:</span><span class="sxs-lookup"><span data-stu-id="f8c46-154">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="f8c46-155">Chcete-li zobrazit jeden z různých formátů stránky kód stavu v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte jednu z direktivy preprocesoru, které začínají `StatusCodePages`a vyberte **aktivační události a 404** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f8c46-155">To see one of the various status code page formats in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f8c46-156">UseStatusCodePages řetězcem formátu</span><span class="sxs-lookup"><span data-stu-id="f8c46-156">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f8c46-157">Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> , která přebírá typ a formát řetězec obsahu:</span><span class="sxs-lookup"><span data-stu-id="f8c46-157">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f8c46-158">UseStatusCodePages pomocí výrazu lambda</span><span class="sxs-lookup"><span data-stu-id="f8c46-158">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f8c46-159">Zadat vlastní zpracování chyb a odezvy psaní kódu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> , který přijímá výrazy lambda:</span><span class="sxs-lookup"><span data-stu-id="f8c46-159">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a><span data-ttu-id="f8c46-160">UseStatusCodePagesWithRedirect</span><span class="sxs-lookup"><span data-stu-id="f8c46-160">UseStatusCodePagesWithRedirect</span></span>

<span data-ttu-id="f8c46-161"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> – Metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="f8c46-161">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="f8c46-162">Odešle *302 - nalezen* stavový kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f8c46-162">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="f8c46-163">Přesměruje klienta do umístění, které jsou součástí adresy URL šablony.</span><span class="sxs-lookup"><span data-stu-id="f8c46-163">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="f8c46-164">Šablona adresy URL může obsahovat `{0}` zástupný symbol pro kód stavu, jak je znázorněno v příkladu.</span><span class="sxs-lookup"><span data-stu-id="f8c46-164">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="f8c46-165">Pokud šablona adresa URL začíná tildou (~), aplikace nahrazuje tilda `PathBase`.</span><span class="sxs-lookup"><span data-stu-id="f8c46-165">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f8c46-166">Pokud přejdete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f8c46-166">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f8c46-167">Příklad stránky Razor, naleznete v tématu [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f8c46-167">For a Razor Pages example, see [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f8c46-168">Tato metoda se obvykle nepoužívá, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="f8c46-168">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f8c46-169">Klient by se měla přesměrovat na jiný koncový bod, obvykle v případech, kde různé aplikace zpracovává chyby.</span><span class="sxs-lookup"><span data-stu-id="f8c46-169">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f8c46-170">Pro web apps odráží adresního řádku prohlížeče klienta přesměrovaného koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f8c46-170">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f8c46-171">Neměli zachovat a vrátí původní stavový kód odpovědi počáteční přesměrování.</span><span class="sxs-lookup"><span data-stu-id="f8c46-171">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f8c46-172">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f8c46-172">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f8c46-173"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> – Metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="f8c46-173">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="f8c46-174">Vrátí původní stavový kód do klienta.</span><span class="sxs-lookup"><span data-stu-id="f8c46-174">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f8c46-175">Generuje text odpovědi znovu spuštěním kanálu požadavku pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f8c46-175">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="f8c46-176">Pokud přejdete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f8c46-176">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f8c46-177">Příklad stránky Razor, naleznete v tématu [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f8c46-177">For a Razor Pages example, see [StatusCode.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/Pages/StatusCode.cshtml) in the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f8c46-178">Tato metoda se obvykle používá při aplikace:</span><span class="sxs-lookup"><span data-stu-id="f8c46-178">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f8c46-179">Zpracování žádosti bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f8c46-179">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f8c46-180">Pro web apps odráží adresního řádku prohlížeče klienta na původně požadovanou koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f8c46-180">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f8c46-181">Zachovat a vrátí původní kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f8c46-181">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f8c46-182">Adresa URL a šablon řetězců se mohou zahrnovat zástupný text dotazu (`{0}`) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="f8c46-182">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="f8c46-183">Šablona adresy URL musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="f8c46-183">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="f8c46-184">Pokud používáte zástupný symbol v cestě, potvrďte, že koncový bod (stránka nebo řadič) může zpracovat segmentu cesty.</span><span class="sxs-lookup"><span data-stu-id="f8c46-184">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="f8c46-185">Například stránky Razor pro chyby by měl přijmout hodnotu volitelná cesta segmentu se `@page` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="f8c46-185">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="f8c46-186">Koncový bod, který zpracovává chybu můžete získat původní adresu URL, který vytvořil chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f8c46-186">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="f8c46-187">Zakázat stav znakové stránky</span><span class="sxs-lookup"><span data-stu-id="f8c46-187">Disable status code pages</span></span>

<span data-ttu-id="f8c46-188">Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC.</span><span class="sxs-lookup"><span data-stu-id="f8c46-188">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="f8c46-189">Chcete-li zakázat stav znakové stránky, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="f8c46-189">To disable status code pages, use the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="f8c46-190">Kód zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="f8c46-190">Exception-handling code</span></span>

<span data-ttu-id="f8c46-191">Kód výjimky zpracování stránky může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-191">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="f8c46-192">Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="f8c46-192">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="f8c46-193">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="f8c46-193">Response headers</span></span>

<span data-ttu-id="f8c46-194">Jakmile jsou odeslány hlavičky odpovědi:</span><span class="sxs-lookup"><span data-stu-id="f8c46-194">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f8c46-195">Aplikaci nelze změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f8c46-195">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f8c46-196">Žádné výjimce stránky nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="f8c46-196">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f8c46-197">Odpovědi musí dokončit, nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="f8c46-197">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f8c46-198">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="f8c46-198">Server exception handling</span></span>

<span data-ttu-id="f8c46-199">Kromě ve vaší aplikaci logiky zpracování výjimek [implementaci serveru HTTP](xref:fundamentals/servers/index) dokáže zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-199">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f8c46-200">Pokud server zachytí výjimku, před odesláním hlavičky odpovědi, odešle server *500 – Interní chyba serveru* odpovědi bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f8c46-200">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="f8c46-201">Pokud server zachytí výjimku po odeslání hlaviček odpovědí, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="f8c46-201">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f8c46-202">Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem.</span><span class="sxs-lookup"><span data-stu-id="f8c46-202">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="f8c46-203">Jakoukoliv výjimku, která vyvolá se v případě, že server zpracovává žádost je zpracována výjimka serveru zpracování.</span><span class="sxs-lookup"><span data-stu-id="f8c46-203">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f8c46-204">Aplikace vlastní chybové stránky, zpracování middleware a filtry výjimek nemají vliv na toto chování.</span><span class="sxs-lookup"><span data-stu-id="f8c46-204">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f8c46-205">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="f8c46-205">Startup exception handling</span></span>

<span data-ttu-id="f8c46-206">Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8c46-206">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f8c46-207">Hostitele lze nakonfigurovat pro [zachycení chyb při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="f8c46-207">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f8c46-208">Hostování vrstvy můžete zobrazit chybovou stránku pro chyby zaznamenané při spouštění jenom v případě, že po adresa/port hostitele vazby, dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f8c46-208">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f8c46-209">Pokud se nezdaří vazby:</span><span class="sxs-lookup"><span data-stu-id="f8c46-209">If binding fails:</span></span>

* <span data-ttu-id="f8c46-210">Hostování vrstvy zaznamená kritické výjimky.</span><span class="sxs-lookup"><span data-stu-id="f8c46-210">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f8c46-211">Dotnet procesu dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f8c46-211">The dotnet process crashes.</span></span>
* <span data-ttu-id="f8c46-212">Žádná chybová stránka se zobrazí, když je HTTP server [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f8c46-212">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f8c46-213">Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 – selhání procesu* vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) Pokud proces nelze spustit. .</span><span class="sxs-lookup"><span data-stu-id="f8c46-213">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f8c46-214">Další informace naleznete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="f8c46-214">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="f8c46-215">Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="f8c46-215">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f8c46-216">Databáze chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="f8c46-216">Database error page</span></span>

<span data-ttu-id="f8c46-217">[Databáze chybovou stránku](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) middleware zachycuje vztahující se k databázi výjimky, které se dají vyřešit pomocí migrace Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f8c46-217">The [Database Error Page](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="f8c46-218">Pokud dojde k tyto výjimky, je generována odpověď jazyka HTML s podrobnostmi o možných akcí k problému.</span><span class="sxs-lookup"><span data-stu-id="f8c46-218">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="f8c46-219">Na této stránce musí být povolené pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f8c46-219">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="f8c46-220">Povolit na stránce přidáním kódu k `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8c46-220">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a><span data-ttu-id="f8c46-221">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="f8c46-221">Exception filters</span></span>

<span data-ttu-id="f8c46-222">V aplikacích MVC filtry výjimek lze nastavit, globálně nebo na základě na kontroler nebo jednotlivé akce.</span><span class="sxs-lookup"><span data-stu-id="f8c46-222">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f8c46-223">V aplikacích pro stránky Razor se dají Konfigurovat globálně nebo na stránce modelu.</span><span class="sxs-lookup"><span data-stu-id="f8c46-223">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f8c46-224">Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="f8c46-224">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f8c46-225">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="f8c46-225">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="f8c46-226">Filtry výjimek jsou užitečné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako Middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="f8c46-226">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="f8c46-227">Doporučujeme používat middleware.</span><span class="sxs-lookup"><span data-stu-id="f8c46-227">We recommend using the middleware.</span></span> <span data-ttu-id="f8c46-228">Použití filtrů jenom tam, kde potřebujete provádět zpracování chyb různě v závislosti na zvolené akci, která MVC.</span><span class="sxs-lookup"><span data-stu-id="f8c46-228">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="f8c46-229">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="f8c46-229">Model state errors</span></span>

<span data-ttu-id="f8c46-230">Informace o tom, jak zpracovávat chyby stavu modelu, naleznete v tématu [vazby modelu](xref:mvc/models/model-binding) a [ověření modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f8c46-230">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8c46-231">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f8c46-231">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
