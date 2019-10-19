---
title: Zpracování chyb v ASP.NET Core
author: rick-anderson
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/error-handling
ms.openlocfilehash: bff526e196ecc378d4687e1c38188977aeeccfd9
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589878"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="befb2-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="befb2-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="befb2-104">[Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="befb2-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="befb2-105">Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="befb2-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="befb2-106">Přečtěte si téma <xref:web-api/handle-errors> pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="befb2-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="befb2-107">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="befb2-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="befb2-108">([Stažení](xref:index#how-to-download-a-sample).) Tento článek obsahuje pokyny, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci pro povolení různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="befb2-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="befb2-109">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="befb2-109">Developer Exception Page</span></span>

<span data-ttu-id="befb2-110">*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí.</span><span class="sxs-lookup"><span data-stu-id="befb2-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="befb2-111">Stránku zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="befb2-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="befb2-112">Přidejte kód do metody `Startup.Configure`, abyste stránku mohli povolit, když je aplikace spuštěná ve vývojovém [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="befb2-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="befb2-113">Umístěte volání do <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před jakýmkoli middlewarem, který chcete zachytit výjimky.</span><span class="sxs-lookup"><span data-stu-id="befb2-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="befb2-114">Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="befb2-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="befb2-115">Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="befb2-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="befb2-116">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="befb2-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="befb2-117">Stránka obsahuje následující informace o výjimce a žádosti:</span><span class="sxs-lookup"><span data-stu-id="befb2-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="befb2-118">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="befb2-118">Stack trace</span></span>
* <span data-ttu-id="befb2-119">Parametry řetězce dotazu (pokud existují)</span><span class="sxs-lookup"><span data-stu-id="befb2-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="befb2-120">Soubory cookie (pokud existují)</span><span class="sxs-lookup"><span data-stu-id="befb2-120">Cookies (if any)</span></span>
* <span data-ttu-id="befb2-121">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="befb2-121">Headers</span></span>

<span data-ttu-id="befb2-122">Chcete-li zobrazit stránku s výjimkou vývojáře v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivu preprocesoru `DevEnvironment` a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="befb2-122">To see the Developer Exception Page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="befb2-123">Stránka obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="befb2-123">Exception handler page</span></span>

<span data-ttu-id="befb2-124">Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="befb2-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="befb2-125">Middleware:</span><span class="sxs-lookup"><span data-stu-id="befb2-125">The middleware:</span></span>

* <span data-ttu-id="befb2-126">Zachycuje a zaznamenává výjimky.</span><span class="sxs-lookup"><span data-stu-id="befb2-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="befb2-127">Znovu spustí požadavek v alternativním kanálu pro uvedenou stránku nebo kontroler.</span><span class="sxs-lookup"><span data-stu-id="befb2-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="befb2-128">Požadavek se znovu nespustí, pokud byla odpověď spuštěná.</span><span class="sxs-lookup"><span data-stu-id="befb2-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="befb2-129">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá middleware pro zpracování výjimek v nevývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="befb2-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="befb2-130">Šablona aplikace Razor Pages poskytuje chybovou stránku ( *. cshtml*) a třídu <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="befb2-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="befb2-131">V případě aplikace MVC obsahuje šablona projektu metodu chybové akce a zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="befb2-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="befb2-132">Zde je metoda akce:</span><span class="sxs-lookup"><span data-stu-id="befb2-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="befb2-133">Neupravujte metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="befb2-133">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="befb2-134">Explicitní příkazy brání, aby některé žádosti dosáhly metody.</span><span class="sxs-lookup"><span data-stu-id="befb2-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="befb2-135">Povolí anonymní přístup k metodě, aby neověření uživatelé mohli získat zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="befb2-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="befb2-136">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="befb2-136">Access the exception</span></span>

<span data-ttu-id="befb2-137">Pro přístup k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>:</span><span class="sxs-lookup"><span data-stu-id="befb2-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="befb2-138">Neslouží **klientům** informace o citlivých chybách.</span><span class="sxs-lookup"><span data-stu-id="befb2-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="befb2-139">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="befb2-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="befb2-140">Chcete-li zobrazit stránku zpracování výjimek v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivy preprocesoru `ProdEnvironment` a `ErrorHandlerPage` a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="befb2-140">To see the exception handling page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="befb2-141">Lambda obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="befb2-141">Exception handler lambda</span></span>

<span data-ttu-id="befb2-142">Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz pro <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="befb2-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="befb2-143">Použití lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="befb2-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="befb2-144">Zde je příklad použití výrazu lambda pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="befb2-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> <span data-ttu-id="befb2-145">**Neobsluhujte** citlivé informace o chybách od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům.</span><span class="sxs-lookup"><span data-stu-id="befb2-145">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="befb2-146">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="befb2-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="befb2-147">Chcete-li zobrazit výsledek lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivy preprocesoru `ProdEnvironment` a `ErrorHandlerLambda` a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="befb2-147">To see the result of the exception handling lambda in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="befb2-148">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="befb2-148">UseStatusCodePages</span></span>

<span data-ttu-id="befb2-149">Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy HTTP, například *404-Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="befb2-149">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="befb2-150">Aplikace vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="befb2-150">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="befb2-151">Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="befb2-151">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="befb2-152">Middleware zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="befb2-152">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="befb2-153">Chcete-li povolit výchozí textové obslužné rutiny pro běžné chybové kódy chyb, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> v metodě `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="befb2-153">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="befb2-154">Před zpracováním middlewaru vyvolejte `UseStatusCodePages` (například pro middleware statických souborů a middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="befb2-154">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="befb2-155">Tady je příklad textu zobrazovaného výchozími obslužnými rutinami:</span><span class="sxs-lookup"><span data-stu-id="befb2-155">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="befb2-156">Pokud chcete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)zobrazit jeden z různých formátů stavových kódů, použijte jednu z direktiv preprocesoru, která začíná na `StatusCodePages`, a na domovské stránce vyberte **aktivovat 404** .</span><span class="sxs-lookup"><span data-stu-id="befb2-156">To see one of the various status code page formats in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="befb2-157">UseStatusCodePages s formátovacím řetězcem</span><span class="sxs-lookup"><span data-stu-id="befb2-157">UseStatusCodePages with format string</span></span>

<span data-ttu-id="befb2-158">Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, které přijímá typ obsahu a řetězec formátu:</span><span class="sxs-lookup"><span data-stu-id="befb2-158">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="befb2-159">UseStatusCodePages s výrazem lambda</span><span class="sxs-lookup"><span data-stu-id="befb2-159">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="befb2-160">Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, které přebírá výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="befb2-160">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="befb2-161">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="befb2-161">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="befb2-162">Metoda rozšíření <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="befb2-162">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="befb2-163">Odešle klientovi stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="befb2-163">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="befb2-164">Přesměruje klienta na umístění zadané v šabloně adresy URL.</span><span class="sxs-lookup"><span data-stu-id="befb2-164">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="befb2-165">Šablona adresy URL může obsahovat zástupný text `{0}` pro stavový kód, jak je znázorněno v příkladu.</span><span class="sxs-lookup"><span data-stu-id="befb2-165">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="befb2-166">Pokud šablona URL začíná vlnovkou (~), znak tildy je nahrazen `PathBase` aplikace.</span><span class="sxs-lookup"><span data-stu-id="befb2-166">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="befb2-167">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránku Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="befb2-167">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="befb2-168">Příklad Razor Pages naleznete v části *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="befb2-168">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="befb2-169">Tato metoda se běžně používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="befb2-169">This method is commonly used when the app:</span></span>

* <span data-ttu-id="befb2-170">By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="befb2-170">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="befb2-171">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="befb2-171">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="befb2-172">Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="befb2-172">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="befb2-173">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="befb2-173">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="befb2-174">Metoda rozšíření <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="befb2-174">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="befb2-175">Vrátí původní stavový kód klientovi.</span><span class="sxs-lookup"><span data-stu-id="befb2-175">Returns the original status code to the client.</span></span>
* <span data-ttu-id="befb2-176">Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="befb2-176">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="befb2-177">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránku Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="befb2-177">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="befb2-178">Příklad Razor Pages naleznete v části *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="befb2-178">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="befb2-179">Tato metoda se běžně používá v případě, kdy by měla aplikace:</span><span class="sxs-lookup"><span data-stu-id="befb2-179">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="befb2-180">Zpracuje požadavek bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="befb2-180">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="befb2-181">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="befb2-181">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="befb2-182">Zachovejte a vraťte původní stavový kód s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="befb2-182">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="befb2-183">Šablony URL a řetězce dotazu mohou obsahovat zástupný symbol (`{0}`) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="befb2-183">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="befb2-184">Šablona adresy URL musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="befb2-184">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="befb2-185">Při použití zástupného symbolu v cestě potvrďte, že koncový bod (stránka nebo kontroler) může zpracovat segment cesty.</span><span class="sxs-lookup"><span data-stu-id="befb2-185">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="befb2-186">Například stránka Razor pro chyby by měla přijmout volitelnou hodnotu segmentu cesty s direktivou `@page`:</span><span class="sxs-lookup"><span data-stu-id="befb2-186">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="befb2-187">Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="befb2-187">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="befb2-188">Zakázat stavové stránky</span><span class="sxs-lookup"><span data-stu-id="befb2-188">Disable status code pages</span></span>

<span data-ttu-id="befb2-189">Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte atribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="befb2-189">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="befb2-190">Chcete-li zakázat stránky se stavovým kódem pro konkrétní požadavky v metodě Razor Pages obslužné rutině nebo v řadiči MVC, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="befb2-190">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="befb2-191">Kód pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="befb2-191">Exception-handling code</span></span>

<span data-ttu-id="befb2-192">Kód na stránkách zpracování výjimek může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="befb2-192">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="befb2-193">Pro produkční chybové stránky je často vhodné vytvořit pouze čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="befb2-193">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="befb2-194">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="befb2-194">Response headers</span></span>

<span data-ttu-id="befb2-195">Po odeslání hlaviček pro odpověď:</span><span class="sxs-lookup"><span data-stu-id="befb2-195">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="befb2-196">Aplikace nemůže změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="befb2-196">The app can't change the response's status code.</span></span>
* <span data-ttu-id="befb2-197">Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="befb2-197">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="befb2-198">Odpověď musí být dokončena nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="befb2-198">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="befb2-199">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="befb2-199">Server exception handling</span></span>

<span data-ttu-id="befb2-200">Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="befb2-200">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="befb2-201">Pokud server zachytí výjimku před odesláním hlaviček odpovědi, server pošle *500 interní odpověď na chybu serveru* bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="befb2-201">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="befb2-202">Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="befb2-202">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="befb2-203">Požadavky, které nejsou zpracovávány vaší aplikací, jsou zpracovávány serverem aplikace.</span><span class="sxs-lookup"><span data-stu-id="befb2-203">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="befb2-204">Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru.</span><span class="sxs-lookup"><span data-stu-id="befb2-204">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="befb2-205">Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.</span><span class="sxs-lookup"><span data-stu-id="befb2-205">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="befb2-206">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="befb2-206">Startup exception handling</span></span>

<span data-ttu-id="befb2-207">Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="befb2-207">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="befb2-208">Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="befb2-208">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="befb2-209">Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu.</span><span class="sxs-lookup"><span data-stu-id="befb2-209">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="befb2-210">Pokud vazba neproběhne úspěšně:</span><span class="sxs-lookup"><span data-stu-id="befb2-210">If binding fails:</span></span>

* <span data-ttu-id="befb2-211">Vrstva hostování zaznamená kritickou výjimku.</span><span class="sxs-lookup"><span data-stu-id="befb2-211">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="befb2-212">Proces dotnet selže.</span><span class="sxs-lookup"><span data-stu-id="befb2-212">The dotnet process crashes.</span></span>
* <span data-ttu-id="befb2-213">Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.</span><span class="sxs-lookup"><span data-stu-id="befb2-213">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="befb2-214">Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* .</span><span class="sxs-lookup"><span data-stu-id="befb2-214">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="befb2-215">Další informace najdete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="befb2-215">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="befb2-216">Chybová stránka databáze</span><span class="sxs-lookup"><span data-stu-id="befb2-216">Database error page</span></span>

<span data-ttu-id="befb2-217">Middleware chybové stránky databáze zaznamenává výjimky týkající se databáze, které je možné vyřešit pomocí Entity Framework migrace.</span><span class="sxs-lookup"><span data-stu-id="befb2-217">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="befb2-218">Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému.</span><span class="sxs-lookup"><span data-stu-id="befb2-218">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="befb2-219">Tato stránka by měla být povolena pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="befb2-219">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="befb2-220">Povolit stránku přidáním kódu do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="befb2-220">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="befb2-221">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="befb2-221">Exception filters</span></span>

<span data-ttu-id="befb2-222">V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="befb2-222">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="befb2-223">V Razor Pagesch aplikacích je možné je nakonfigurovat globálně nebo podle modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="befb2-223">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="befb2-224">Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru.</span><span class="sxs-lookup"><span data-stu-id="befb2-224">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="befb2-225">Další informace najdete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="befb2-225">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="befb2-226">Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="befb2-226">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="befb2-227">Doporučujeme použít middleware.</span><span class="sxs-lookup"><span data-stu-id="befb2-227">We recommend using the middleware.</span></span> <span data-ttu-id="befb2-228">Filtry používejte pouze v případě, že je třeba provést zpracování chyb odlišně v závislosti na vybrané akci MVC.</span><span class="sxs-lookup"><span data-stu-id="befb2-228">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="befb2-229">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="befb2-229">Model state errors</span></span>

<span data-ttu-id="befb2-230">Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="befb2-230">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="befb2-231">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="befb2-231">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
