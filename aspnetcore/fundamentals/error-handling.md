---
title: Zpracování chyb v ASP.NET Core
author: rick-anderson
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658814"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="f77c0-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f77c0-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="f77c0-104">Tím, že [Dykstra](https://github.com/tdykstra/) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f77c0-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f77c0-105">Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="f77c0-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="f77c0-106">Přečtěte si téma <xref:web-api/handle-errors> pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f77c0-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="f77c0-107">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f77c0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="f77c0-108">([Stažení](xref:index#how-to-download-a-sample).) Tento článek obsahuje pokyny, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci pro povolení různých scénářů.</span><span class="sxs-lookup"><span data-stu-id="f77c0-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f77c0-109">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="f77c0-109">Developer Exception Page</span></span>

<span data-ttu-id="f77c0-110">*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí.</span><span class="sxs-lookup"><span data-stu-id="f77c0-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="f77c0-111">Stránku zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f77c0-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f77c0-112">Přidejte kód do metody `Startup.Configure` pro povolení stránky, když aplikace běží ve vývojovém [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="f77c0-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="f77c0-113">Umístěte volání do <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před jakýmkoli middlewarem, který chcete zachytit výjimky.</span><span class="sxs-lookup"><span data-stu-id="f77c0-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="f77c0-114">Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="f77c0-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f77c0-115">Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f77c0-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="f77c0-116">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f77c0-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f77c0-117">Stránka obsahuje následující informace o výjimce a žádosti:</span><span class="sxs-lookup"><span data-stu-id="f77c0-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="f77c0-118">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="f77c0-118">Stack trace</span></span>
* <span data-ttu-id="f77c0-119">Parametry řetězce dotazu (pokud existují)</span><span class="sxs-lookup"><span data-stu-id="f77c0-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="f77c0-120">Soubory cookie (pokud existují)</span><span class="sxs-lookup"><span data-stu-id="f77c0-120">Cookies (if any)</span></span>
* <span data-ttu-id="f77c0-121">Hlavičky</span><span class="sxs-lookup"><span data-stu-id="f77c0-121">Headers</span></span>

<span data-ttu-id="f77c0-122">Chcete-li zobrazit stránku s výjimkou vývojáře v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivu preprocesoru `DevEnvironment` a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f77c0-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="f77c0-123">Stránka obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="f77c0-123">Exception handler page</span></span>

<span data-ttu-id="f77c0-124">Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="f77c0-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="f77c0-125">Middleware:</span><span class="sxs-lookup"><span data-stu-id="f77c0-125">The middleware:</span></span>

* <span data-ttu-id="f77c0-126">Zachycuje a zaznamenává výjimky.</span><span class="sxs-lookup"><span data-stu-id="f77c0-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="f77c0-127">Znovu spustí požadavek v alternativním kanálu pro uvedenou stránku nebo kontroler.</span><span class="sxs-lookup"><span data-stu-id="f77c0-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="f77c0-128">Požadavek se znovu nespustí, pokud byla odpověď spuštěná.</span><span class="sxs-lookup"><span data-stu-id="f77c0-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="f77c0-129">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá middleware pro zpracování výjimek v nevývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="f77c0-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="f77c0-130">Šablona aplikace Razor Pages poskytuje chybovou stránku ( *. cshtml*) a třídu <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> (`ErrorModel`) ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="f77c0-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="f77c0-131">V případě aplikace MVC obsahuje šablona projektu metodu chybové akce a zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="f77c0-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="f77c0-132">Zde je metoda akce:</span><span class="sxs-lookup"><span data-stu-id="f77c0-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="f77c0-133">Neoznačovat metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="f77c0-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="f77c0-134">Explicitní příkazy brání, aby některé žádosti dosáhly metody.</span><span class="sxs-lookup"><span data-stu-id="f77c0-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="f77c0-135">Povolí anonymní přístup k metodě, aby neověření uživatelé mohli získat zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="f77c0-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="f77c0-136">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="f77c0-136">Access the exception</span></span>

<span data-ttu-id="f77c0-137">Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě k požadavku v řadiči obslužné rutiny chyb nebo na stránce:</span><span class="sxs-lookup"><span data-stu-id="f77c0-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="f77c0-138">Neslouží **klientům** informace o citlivých chybách.</span><span class="sxs-lookup"><span data-stu-id="f77c0-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="f77c0-139">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="f77c0-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="f77c0-140">Chcete-li zobrazit stránku zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivy preprocesoru `ProdEnvironment` a `ErrorHandlerPage` a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f77c0-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="f77c0-141">Lambda obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="f77c0-141">Exception handler lambda</span></span>

<span data-ttu-id="f77c0-142">Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz pro <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="f77c0-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="f77c0-143">Použití lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f77c0-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="f77c0-144">Zde je příklad použití výrazu lambda pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="f77c0-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="f77c0-145">V předchozím kódu je `await context.Response.WriteAsync(new string(' ', 512));` přidáno, aby prohlížeč Internet Explorer zobrazil chybovou zprávu, nikoli chybovou zprávu IE.</span><span class="sxs-lookup"><span data-stu-id="f77c0-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="f77c0-146">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="f77c0-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="f77c0-147">**Neobsluhujte** citlivé informace o chybách od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ani <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům.</span><span class="sxs-lookup"><span data-stu-id="f77c0-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="f77c0-148">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="f77c0-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="f77c0-149">Chcete-li zobrazit výsledek lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivy preprocesoru `ProdEnvironment` a `ErrorHandlerLambda` a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="f77c0-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="f77c0-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="f77c0-150">UseStatusCodePages</span></span>

<span data-ttu-id="f77c0-151">Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy HTTP, například *404-Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="f77c0-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="f77c0-152">Aplikace vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f77c0-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="f77c0-153">Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="f77c0-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="f77c0-154">Middleware zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) , který je ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f77c0-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f77c0-155">Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových kódů, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> v metodě `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f77c0-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="f77c0-156">Před zpracováním middlewaru vyžaduje volání `UseStatusCodePages` (například pro middleware statických souborů a middleware MVC).</span><span class="sxs-lookup"><span data-stu-id="f77c0-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="f77c0-157">Tady je příklad textu zobrazovaného výchozími obslužnými rutinami:</span><span class="sxs-lookup"><span data-stu-id="f77c0-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="f77c0-158">Pokud chcete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)zobrazit jeden z různých formátů stavových kódů, použijte jednu z direktiv preprocesoru, která začíná na `StatusCodePages`, a na domovské stránce vyberte **aktivovat 404** .</span><span class="sxs-lookup"><span data-stu-id="f77c0-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="f77c0-159">UseStatusCodePages s formátovacím řetězcem</span><span class="sxs-lookup"><span data-stu-id="f77c0-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="f77c0-160">Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, které přebírá typ obsahu a řetězec formátu:</span><span class="sxs-lookup"><span data-stu-id="f77c0-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="f77c0-161">UseStatusCodePages s výrazem lambda</span><span class="sxs-lookup"><span data-stu-id="f77c0-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="f77c0-162">Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>, které přebírají výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="f77c0-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="f77c0-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="f77c0-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="f77c0-164">Metoda rozšíření <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="f77c0-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="f77c0-165">Odešle klientovi stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="f77c0-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="f77c0-166">Přesměruje klienta na umístění zadané v šabloně adresy URL.</span><span class="sxs-lookup"><span data-stu-id="f77c0-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="f77c0-167">Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v příkladu.</span><span class="sxs-lookup"><span data-stu-id="f77c0-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="f77c0-168">Pokud šablona adresy URL začíná vlnovkou (~), bude znak tildy nahrazen `PathBase`em aplikace.</span><span class="sxs-lookup"><span data-stu-id="f77c0-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="f77c0-169">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránku Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f77c0-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f77c0-170">Příklad Razor Pages naleznete v části *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f77c0-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f77c0-171">Tato metoda se běžně používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="f77c0-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="f77c0-172">By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f77c0-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="f77c0-173">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f77c0-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="f77c0-174">Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="f77c0-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="f77c0-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="f77c0-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="f77c0-176">Metoda rozšíření <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="f77c0-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="f77c0-177">Vrátí původní stavový kód klientovi.</span><span class="sxs-lookup"><span data-stu-id="f77c0-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="f77c0-178">Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f77c0-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="f77c0-179">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo stránku Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f77c0-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="f77c0-180">Příklad Razor Pages naleznete v části *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="f77c0-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="f77c0-181">Tato metoda se běžně používá v případě, kdy by měla aplikace:</span><span class="sxs-lookup"><span data-stu-id="f77c0-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="f77c0-182">Zpracuje požadavek bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f77c0-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="f77c0-183">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="f77c0-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="f77c0-184">Zachovejte a vraťte původní stavový kód s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="f77c0-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="f77c0-185">Šablony URL a řetězce dotazu mohou obsahovat zástupný symbol (`{0}`) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="f77c0-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="f77c0-186">Šablona adresy URL musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="f77c0-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="f77c0-187">Při použití zástupného symbolu v cestě potvrďte, že koncový bod (stránka nebo kontroler) může zpracovat segment cesty.</span><span class="sxs-lookup"><span data-stu-id="f77c0-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="f77c0-188">Například stránka Razor pro chyby by měla přijmout volitelnou hodnotu segmentu cesty s direktivou `@page`:</span><span class="sxs-lookup"><span data-stu-id="f77c0-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="f77c0-189">Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f77c0-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="f77c0-190">Zakázat stavové stránky</span><span class="sxs-lookup"><span data-stu-id="f77c0-190">Disable status code pages</span></span>

<span data-ttu-id="f77c0-191">Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte atribut [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f77c0-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="f77c0-192">Chcete-li zakázat stránky se stavovým kódem pro konkrétní požadavky v metodě Razor Pages obslužné rutině nebo v řadiči MVC, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span><span class="sxs-lookup"><span data-stu-id="f77c0-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="f77c0-193">Kód pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="f77c0-193">Exception-handling code</span></span>

<span data-ttu-id="f77c0-194">Kód na stránkách zpracování výjimek může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="f77c0-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="f77c0-195">Pro produkční chybové stránky je často vhodné vytvořit pouze čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="f77c0-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="f77c0-196">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="f77c0-196">Response headers</span></span>

<span data-ttu-id="f77c0-197">Po odeslání hlaviček pro odpověď:</span><span class="sxs-lookup"><span data-stu-id="f77c0-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="f77c0-198">Aplikace nemůže změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f77c0-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="f77c0-199">Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="f77c0-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="f77c0-200">Odpověď musí být dokončena nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="f77c0-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="f77c0-201">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="f77c0-201">Server exception handling</span></span>

<span data-ttu-id="f77c0-202">Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="f77c0-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="f77c0-203">Pokud server zachytí výjimku před odesláním hlaviček odpovědi, server pošle *500 interní odpověď na chybu serveru* bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f77c0-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="f77c0-204">Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="f77c0-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="f77c0-205">Požadavky, které nejsou zpracovávány vaší aplikací, jsou zpracovávány serverem aplikace.</span><span class="sxs-lookup"><span data-stu-id="f77c0-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="f77c0-206">Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru.</span><span class="sxs-lookup"><span data-stu-id="f77c0-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="f77c0-207">Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.</span><span class="sxs-lookup"><span data-stu-id="f77c0-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="f77c0-208">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="f77c0-208">Startup exception handling</span></span>

<span data-ttu-id="f77c0-209">Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f77c0-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="f77c0-210">Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="f77c0-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="f77c0-211">Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu.</span><span class="sxs-lookup"><span data-stu-id="f77c0-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="f77c0-212">Pokud vazba neproběhne úspěšně:</span><span class="sxs-lookup"><span data-stu-id="f77c0-212">If binding fails:</span></span>

* <span data-ttu-id="f77c0-213">Vrstva hostování zaznamená kritickou výjimku.</span><span class="sxs-lookup"><span data-stu-id="f77c0-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="f77c0-214">Proces dotnet selže.</span><span class="sxs-lookup"><span data-stu-id="f77c0-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="f77c0-215">Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.</span><span class="sxs-lookup"><span data-stu-id="f77c0-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f77c0-216">Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* .</span><span class="sxs-lookup"><span data-stu-id="f77c0-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="f77c0-217">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="f77c0-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="f77c0-218">Chybová stránka databáze</span><span class="sxs-lookup"><span data-stu-id="f77c0-218">Database error page</span></span>

<span data-ttu-id="f77c0-219">Middleware chybové stránky databáze zaznamenává výjimky týkající se databáze, které je možné vyřešit pomocí Entity Framework migrace.</span><span class="sxs-lookup"><span data-stu-id="f77c0-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="f77c0-220">Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému.</span><span class="sxs-lookup"><span data-stu-id="f77c0-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="f77c0-221">Tato stránka by měla být povolena pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f77c0-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="f77c0-222">Povolit stránku přidáním kódu do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f77c0-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="f77c0-223">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="f77c0-223">Exception filters</span></span>

<span data-ttu-id="f77c0-224">V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="f77c0-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="f77c0-225">V Razor Pagesch aplikacích je možné je nakonfigurovat globálně nebo podle modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="f77c0-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="f77c0-226">Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru.</span><span class="sxs-lookup"><span data-stu-id="f77c0-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="f77c0-227">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="f77c0-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="f77c0-228">Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="f77c0-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="f77c0-229">Doporučujeme použít middleware.</span><span class="sxs-lookup"><span data-stu-id="f77c0-229">We recommend using the middleware.</span></span> <span data-ttu-id="f77c0-230">Filtry používejte pouze v případě, že je třeba provést zpracování chyb odlišně v závislosti na vybrané akci MVC.</span><span class="sxs-lookup"><span data-stu-id="f77c0-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="f77c0-231">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="f77c0-231">Model state errors</span></span>

<span data-ttu-id="f77c0-232">Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f77c0-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f77c0-233">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f77c0-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
