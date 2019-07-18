---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/10/2019
uid: fundamentals/error-handling
ms.openlocfilehash: f9f91455b273b99608ca6f1524df6cb748a26669
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308197"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="188ae-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="188ae-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="188ae-104">[Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="188ae-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="188ae-105">Tento článek se věnuje běžným přístupům ke zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="188ae-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="188ae-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="188ae-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="188ae-107">([Stažení](xref:index#how-to-download-a-sample).) Tento článek obsahuje pokyny, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci pro povolení různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="188ae-107">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="188ae-108">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="188ae-108">Developer Exception Page</span></span>

<span data-ttu-id="188ae-109">*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí.</span><span class="sxs-lookup"><span data-stu-id="188ae-109">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="188ae-110">Stránku zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="188ae-110">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="188ae-111">Přidejte kód do `Startup.Configure` metody pro povolení stránky, když aplikace běží ve vývojovém [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="188ae-111">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="188ae-112">Umístěte volání do <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před jakýmkoli middlewarem, který chcete zachytit výjimky.</span><span class="sxs-lookup"><span data-stu-id="188ae-112">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="188ae-113">Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="188ae-113">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="188ae-114">Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="188ae-114">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="188ae-115">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="188ae-115">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="188ae-116">Stránka obsahuje následující informace o výjimce a žádosti:</span><span class="sxs-lookup"><span data-stu-id="188ae-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="188ae-117">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="188ae-117">Stack trace</span></span>
* <span data-ttu-id="188ae-118">Parametry řetězce dotazu (pokud existují)</span><span class="sxs-lookup"><span data-stu-id="188ae-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="188ae-119">Soubory cookie (pokud existují)</span><span class="sxs-lookup"><span data-stu-id="188ae-119">Cookies (if any)</span></span>
* <span data-ttu-id="188ae-120">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="188ae-120">Headers</span></span>

<span data-ttu-id="188ae-121">Chcete-li zobrazit stránku s výjimkou vývojáře v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), `DevEnvironment` Použijte direktivu preprocesoru a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="188ae-121">To see the Developer Exception Page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="188ae-122">Stránka obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="188ae-122">Exception handler page</span></span>

<span data-ttu-id="188ae-123">Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="188ae-123">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="188ae-124">Middleware:</span><span class="sxs-lookup"><span data-stu-id="188ae-124">The middleware:</span></span>

* <span data-ttu-id="188ae-125">Zachycuje a zaznamenává výjimky.</span><span class="sxs-lookup"><span data-stu-id="188ae-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="188ae-126">Znovu spustí požadavek v alternativním kanálu pro uvedenou stránku nebo kontroler.</span><span class="sxs-lookup"><span data-stu-id="188ae-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="188ae-127">Požadavek se znovu nespustí, pokud byla odpověď spuštěná.</span><span class="sxs-lookup"><span data-stu-id="188ae-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="188ae-128">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá middleware pro zpracování výjimek v nevývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="188ae-128">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="188ae-129">Šablona aplikace Razor Pages poskytuje chybovou stránku ( *. cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu (`ErrorModel`) ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="188ae-129">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="188ae-130">V případě aplikace MVC obsahuje šablona projektu metodu chybové akce a zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="188ae-130">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="188ae-131">Zde je metoda akce:</span><span class="sxs-lookup"><span data-stu-id="188ae-131">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="188ae-132">Neupravujte metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, `HttpGet`jako je například.</span><span class="sxs-lookup"><span data-stu-id="188ae-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="188ae-133">Explicitní příkazy brání, aby některé žádosti dosáhly metody.</span><span class="sxs-lookup"><span data-stu-id="188ae-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="188ae-134">Povolí anonymní přístup k metodě, aby neověření uživatelé mohli získat zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="188ae-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="188ae-135">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="188ae-135">Access the exception</span></span>

<span data-ttu-id="188ae-136">Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce:</span><span class="sxs-lookup"><span data-stu-id="188ae-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="188ae-137">Neslouží **klientům** informace o citlivých chybách.</span><span class="sxs-lookup"><span data-stu-id="188ae-137">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="188ae-138">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="188ae-138">Serving errors is a security risk.</span></span>

<span data-ttu-id="188ae-139">Chcete-li zobrazit stránku zpracování výjimek v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` direktivy `ErrorHandlerPage` preprocesoru a a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="188ae-139">To see the exception handling page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="188ae-140">Lambda obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="188ae-140">Exception handler lambda</span></span>

<span data-ttu-id="188ae-141">Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="188ae-141">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="188ae-142">Použití lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="188ae-142">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="188ae-143">Zde je příklad použití výrazu lambda pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="188ae-143">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> <span data-ttu-id="188ae-144">**Neobsluhujte** citlivé informace o chybách <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientů ani do nich.</span><span class="sxs-lookup"><span data-stu-id="188ae-144">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="188ae-145">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="188ae-145">Serving errors is a security risk.</span></span>

<span data-ttu-id="188ae-146">Chcete-li zobrazit výsledek lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` direktivy preprocesoru `ErrorHandlerLambda` a a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="188ae-146">To see the result of the exception handling lambda in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="188ae-147">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="188ae-147">UseStatusCodePages</span></span>

<span data-ttu-id="188ae-148">Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy HTTP, například *404-* Nenalezeno.</span><span class="sxs-lookup"><span data-stu-id="188ae-148">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="188ae-149">Aplikace vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="188ae-149">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="188ae-150">Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="188ae-150">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="188ae-151">Middleware zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="188ae-151">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="188ae-152">Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> kódů, `Startup.Configure` zavolejte v metodě:</span><span class="sxs-lookup"><span data-stu-id="188ae-152">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="188ae-153">Volejte `UseStatusCodePages` před middlewarem zpracování požadavků (například middleware pro statický soubor a middleware Mvc).</span><span class="sxs-lookup"><span data-stu-id="188ae-153">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="188ae-154">Tady je příklad textu zobrazovaného výchozími obslužnými rutinami:</span><span class="sxs-lookup"><span data-stu-id="188ae-154">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="188ae-155">Pokud chcete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)zobrazit jeden z různých formátů stavových kódů, použijte jednu z direktiv preprocesoru, která začíná `StatusCodePages`na, a na domovské stránce vyberte **aktivovat na 404** .</span><span class="sxs-lookup"><span data-stu-id="188ae-155">To see one of the various status code page formats in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="188ae-156">UseStatusCodePages s formátovacím řetězcem</span><span class="sxs-lookup"><span data-stu-id="188ae-156">UseStatusCodePages with format string</span></span>

<span data-ttu-id="188ae-157">Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> , které přijímá typ obsahu a řetězec formátu:</span><span class="sxs-lookup"><span data-stu-id="188ae-157">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="188ae-158">UseStatusCodePages s výrazem lambda</span><span class="sxs-lookup"><span data-stu-id="188ae-158">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="188ae-159">Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> , které přijímá výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="188ae-159">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a><span data-ttu-id="188ae-160">UseStatusCodePagesWithRedirect</span><span class="sxs-lookup"><span data-stu-id="188ae-160">UseStatusCodePagesWithRedirect</span></span>

<span data-ttu-id="188ae-161">Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> rozšíření:</span><span class="sxs-lookup"><span data-stu-id="188ae-161">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="188ae-162">Odešle klientovi stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="188ae-162">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="188ae-163">Přesměruje klienta na umístění zadané v šabloně adresy URL.</span><span class="sxs-lookup"><span data-stu-id="188ae-163">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="188ae-164">Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v příkladu.</span><span class="sxs-lookup"><span data-stu-id="188ae-164">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="188ae-165">Pokud šablona adresy URL začíná vlnovkou (~), bude znak tildy nahrazen symbolem aplikace `PathBase`.</span><span class="sxs-lookup"><span data-stu-id="188ae-165">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="188ae-166">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránku Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="188ae-166">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="188ae-167">Příklad Razor Pages naleznete v části *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="188ae-167">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="188ae-168">Tato metoda se běžně používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="188ae-168">This method is commonly used when the app:</span></span>

* <span data-ttu-id="188ae-169">By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="188ae-169">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="188ae-170">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="188ae-170">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="188ae-171">Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="188ae-171">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="188ae-172">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="188ae-172">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="188ae-173">Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> rozšíření:</span><span class="sxs-lookup"><span data-stu-id="188ae-173">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="188ae-174">Vrátí původní stavový kód klientovi.</span><span class="sxs-lookup"><span data-stu-id="188ae-174">Returns the original status code to the client.</span></span>
* <span data-ttu-id="188ae-175">Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="188ae-175">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="188ae-176">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránku Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="188ae-176">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="188ae-177">Příklad Razor Pages naleznete v části *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="188ae-177">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="188ae-178">Tato metoda se běžně používá v případě, kdy by měla aplikace:</span><span class="sxs-lookup"><span data-stu-id="188ae-178">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="188ae-179">Zpracuje požadavek bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="188ae-179">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="188ae-180">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="188ae-180">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="188ae-181">Zachovejte a vraťte původní stavový kód s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="188ae-181">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="188ae-182">Šablony URL a řetězce dotazu mohou obsahovat zástupný symbol (`{0}`) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="188ae-182">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="188ae-183">Šablona adresy URL musí začínat lomítkem`/`().</span><span class="sxs-lookup"><span data-stu-id="188ae-183">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="188ae-184">Při použití zástupného symbolu v cestě potvrďte, že koncový bod (stránka nebo kontroler) může zpracovat segment cesty.</span><span class="sxs-lookup"><span data-stu-id="188ae-184">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="188ae-185">Například stránka Razor pro chyby by měla přijmout volitelnou hodnotu segmentu cesty s `@page` direktivou:</span><span class="sxs-lookup"><span data-stu-id="188ae-185">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="188ae-186">Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="188ae-186">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="188ae-187">Zakázat stavové stránky</span><span class="sxs-lookup"><span data-stu-id="188ae-187">Disable status code pages</span></span>

<span data-ttu-id="188ae-188">Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte atribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="188ae-188">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="188ae-189">Chcete-li zakázat stránky se stavovým kódem pro konkrétní požadavky v metodě obslužné rutiny Razor Pages nebo v <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>kontroleru MVC, použijte:</span><span class="sxs-lookup"><span data-stu-id="188ae-189">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="188ae-190">Kód pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="188ae-190">Exception-handling code</span></span>

<span data-ttu-id="188ae-191">Kód na stránkách zpracování výjimek může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="188ae-191">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="188ae-192">Pro produkční chybové stránky je často vhodné vytvořit pouze čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="188ae-192">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="188ae-193">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="188ae-193">Response headers</span></span>

<span data-ttu-id="188ae-194">Po odeslání hlaviček pro odpověď:</span><span class="sxs-lookup"><span data-stu-id="188ae-194">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="188ae-195">Aplikace nemůže změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="188ae-195">The app can't change the response's status code.</span></span>
* <span data-ttu-id="188ae-196">Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="188ae-196">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="188ae-197">Odpověď musí být dokončena nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="188ae-197">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="188ae-198">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="188ae-198">Server exception handling</span></span>

<span data-ttu-id="188ae-199">Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="188ae-199">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="188ae-200">Pokud server zachytí výjimku před odesláním hlaviček odpovědi, server pošle *500 interní odpověď na chybu serveru* bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="188ae-200">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="188ae-201">Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="188ae-201">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="188ae-202">Požadavky, které nejsou zpracovávány vaší aplikací, jsou zpracovávány serverem aplikace.</span><span class="sxs-lookup"><span data-stu-id="188ae-202">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="188ae-203">Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru.</span><span class="sxs-lookup"><span data-stu-id="188ae-203">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="188ae-204">Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.</span><span class="sxs-lookup"><span data-stu-id="188ae-204">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="188ae-205">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="188ae-205">Startup exception handling</span></span>

<span data-ttu-id="188ae-206">Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="188ae-206">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="188ae-207">Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="188ae-207">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="188ae-208">Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu.</span><span class="sxs-lookup"><span data-stu-id="188ae-208">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="188ae-209">Pokud vazba neproběhne úspěšně:</span><span class="sxs-lookup"><span data-stu-id="188ae-209">If binding fails:</span></span>

* <span data-ttu-id="188ae-210">Vrstva hostování zaznamená kritickou výjimku.</span><span class="sxs-lookup"><span data-stu-id="188ae-210">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="188ae-211">Proces dotnet selže.</span><span class="sxs-lookup"><span data-stu-id="188ae-211">The dotnet process crashes.</span></span>
* <span data-ttu-id="188ae-212">Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.</span><span class="sxs-lookup"><span data-stu-id="188ae-212">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="188ae-213">Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* .</span><span class="sxs-lookup"><span data-stu-id="188ae-213">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="188ae-214">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="188ae-214">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="188ae-215">Chybová stránka databáze</span><span class="sxs-lookup"><span data-stu-id="188ae-215">Database error page</span></span>

<span data-ttu-id="188ae-216">Middleware [chybové stránky databáze](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) zachycuje výjimky týkající se databáze, které je možné vyřešit pomocí Entity Framework migrace.</span><span class="sxs-lookup"><span data-stu-id="188ae-216">The [Database Error Page](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="188ae-217">Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému.</span><span class="sxs-lookup"><span data-stu-id="188ae-217">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="188ae-218">Tato stránka by měla být povolena pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="188ae-218">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="188ae-219">Povolit stránku přidáním kódu do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="188ae-219">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a><span data-ttu-id="188ae-220">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="188ae-220">Exception filters</span></span>

<span data-ttu-id="188ae-221">V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="188ae-221">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="188ae-222">V Razor Pagesch aplikacích je možné je nakonfigurovat globálně nebo podle modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="188ae-222">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="188ae-223">Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru.</span><span class="sxs-lookup"><span data-stu-id="188ae-223">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="188ae-224">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="188ae-224">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="188ae-225">Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="188ae-225">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="188ae-226">Doporučujeme použít middleware.</span><span class="sxs-lookup"><span data-stu-id="188ae-226">We recommend using the middleware.</span></span> <span data-ttu-id="188ae-227">Filtry používejte pouze v případě, že je třeba provést zpracování chyb odlišně v závislosti na vybrané akci MVC.</span><span class="sxs-lookup"><span data-stu-id="188ae-227">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="188ae-228">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="188ae-228">Model state errors</span></span>

<span data-ttu-id="188ae-229">Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="188ae-229">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="188ae-230">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="188ae-230">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
