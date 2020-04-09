---
title: Zpracování chyb v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak zpracovat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658814"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="38613-103">Zpracování chyb v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="38613-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="38613-104">Tom [Dykstra](https://github.com/tdykstra/) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="38613-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="38613-105">Tento článek popisuje běžné přístupy ke zpracování chyb v ASP.NET webových aplikací core.</span><span class="sxs-lookup"><span data-stu-id="38613-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="38613-106">Viz <xref:web-api/handle-errors> webová api.</span><span class="sxs-lookup"><span data-stu-id="38613-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="38613-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="38613-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="38613-108">([Jak stáhnout](xref:index#how-to-download-a-sample).) Článek obsahuje pokyny, jak nastavit direktivy preprocesoru (`#if`, `#endif`, `#define`) v ukázkové aplikaci, aby bylo možné povolit různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="38613-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="38613-109">Stránka výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="38613-109">Developer Exception Page</span></span>

<span data-ttu-id="38613-110">*Stránka výjimky vývojáře* zobrazuje podrobné informace o výjimkách požadavku.</span><span class="sxs-lookup"><span data-stu-id="38613-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="38613-111">Stránka je zpřístupněna balíčkem [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) který je v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="38613-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="38613-112">Přidejte kód `Startup.Configure` k metodě, která povoluje stránku, když je aplikace spuštěná ve vývojovém [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="38613-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="38613-113">Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před všechny middleware, které chcete zachytit výjimky.</span><span class="sxs-lookup"><span data-stu-id="38613-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="38613-114">Stránku výjimky pro vývojáře povolte **pouze v případě, že je aplikace spuštěná ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="38613-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="38613-115">Nechcete sdílet podrobné informace o výjimce veřejně, když se aplikace spustí v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="38613-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="38613-116">Další informace o konfiguraci prostředí <xref:fundamentals/environments>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="38613-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="38613-117">Stránka obsahuje následující informace o výjimce a požadavku:</span><span class="sxs-lookup"><span data-stu-id="38613-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="38613-118">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="38613-118">Stack trace</span></span>
* <span data-ttu-id="38613-119">Parametry řetězce dotazu (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="38613-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="38613-120">Cookies (pokud nějaké)</span><span class="sxs-lookup"><span data-stu-id="38613-120">Cookies (if any)</span></span>
* <span data-ttu-id="38613-121">Hlavičky</span><span class="sxs-lookup"><span data-stu-id="38613-121">Headers</span></span>

<span data-ttu-id="38613-122">Chcete-li zobrazit stránku výjimky pro `DevEnvironment` vývojáře v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivu preprocesoru a na domovské stránce vyberte **Aktivovat výjimku.**</span><span class="sxs-lookup"><span data-stu-id="38613-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="38613-123">Stránka obslužné rutiny výjimky</span><span class="sxs-lookup"><span data-stu-id="38613-123">Exception handler page</span></span>

<span data-ttu-id="38613-124">Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="38613-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="38613-125">Middleware:</span><span class="sxs-lookup"><span data-stu-id="38613-125">The middleware:</span></span>

* <span data-ttu-id="38613-126">Úlovky a protokoly výjimky.</span><span class="sxs-lookup"><span data-stu-id="38613-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="38613-127">Znovu provede požadavek v alternativním kanálu pro uvedenou stránku nebo řadič.</span><span class="sxs-lookup"><span data-stu-id="38613-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="38613-128">Požadavek není znovu proveden, pokud byla odpověď spuštěna.</span><span class="sxs-lookup"><span data-stu-id="38613-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="38613-129">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá middleware pro zpracování výjimek v prostředích, která nejsou ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="38613-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="38613-130">Šablona aplikace Razor Pages obsahuje chybovou stránku`ErrorModel`(*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( ) ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="38613-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="38613-131">Pro aplikaci MVC obsahuje šablona projektu metodu akce Chyba a zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="38613-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="38613-132">Zde je akční metoda:</span><span class="sxs-lookup"><span data-stu-id="38613-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="38613-133">Neoznačte metodu akce obslužné rutiny chyby atributy metody HTTP, například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="38613-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="38613-134">Explicitní slovesa zabránit některé požadavky z dosažení metody.</span><span class="sxs-lookup"><span data-stu-id="38613-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="38613-135">Povolit anonymní přístup k metodě tak, aby neověřené uživatelé mohli získat zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="38613-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="38613-136">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="38613-136">Access the exception</span></span>

<span data-ttu-id="38613-137">Slouží <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> k přístupu k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce:</span><span class="sxs-lookup"><span data-stu-id="38613-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="38613-138">**Neobsluhovat** citlivé informace o chybách klientům.</span><span class="sxs-lookup"><span data-stu-id="38613-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="38613-139">Chyby obsluhy jsou bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="38613-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="38613-140">Chcete-li zobrazit stránku zpracování výjimek `ProdEnvironment` `ErrorHandlerPage` v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte direktivy a preprocesora a vyberte **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="38613-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="38613-141">Obslužná rutina výjimky lambda</span><span class="sxs-lookup"><span data-stu-id="38613-141">Exception handler lambda</span></span>

<span data-ttu-id="38613-142">Alternativou k [vlastní stránce obslužné rutiny výjimky](#exception-handler-page) je poskytnout lambda na <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="38613-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="38613-143">Použití lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="38613-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="38613-144">Tady je příklad použití lambdy pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="38613-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="38613-145">V předchozím kódu `await context.Response.WriteAsync(new string(' ', 512));` je přidán, takže prohlížeč Internet Explorer zobrazí chybovou zprávu, nikoli iE chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="38613-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="38613-146">Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="38613-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="38613-147">**Neobsluhovat** citlivé informace <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> o chybách od klientů nebo klientům.</span><span class="sxs-lookup"><span data-stu-id="38613-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="38613-148">Chyby obsluhy jsou bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="38613-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="38613-149">Chcete-li zobrazit výsledek zpracování výjimky lambda v `ProdEnvironment` `ErrorHandlerLambda` [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte a preprocesordirektia a vyberte **Aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="38613-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="38613-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="38613-150">UseStatusCodePages</span></span>

<span data-ttu-id="38613-151">Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stránku se stavovým kódem pro stavové kódy HTTP, například *404 – nebylo nalezeno*.</span><span class="sxs-lookup"><span data-stu-id="38613-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="38613-152">Aplikace vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="38613-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="38613-153">Chcete-li poskytnout stránky se stavovým kódem, použijte middleware Stránky stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="38613-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="38613-154">Middleware je k dispozici prostřednictvím balíčku [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) který je v [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="38613-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="38613-155">Chcete-li povolit výchozí obslužné <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> rutiny `Startup.Configure` pouze text pro běžné kódy stavu chyb, volejte metodu:</span><span class="sxs-lookup"><span data-stu-id="38613-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="38613-156">Volání `UseStatusCodePages` před požadavkem zpracování middleware (například statické soubor middleware a MVC Middleware).</span><span class="sxs-lookup"><span data-stu-id="38613-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="38613-157">Tady je příklad textu zobrazeného výchozími obslužnými rutinami:</span><span class="sxs-lookup"><span data-stu-id="38613-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="38613-158">Chcete-li zobrazit jeden z různých formátů stavového kódu stránky v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte jednu ze směrnic preprocesoru, které začínají na `StatusCodePages`a vyberte **Aktivovat 404** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="38613-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="38613-159">UseStatusCodePages s formátovacím řetězcem</span><span class="sxs-lookup"><span data-stu-id="38613-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="38613-160">Chcete-li přizpůsobit typ obsahu odpovědi a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> text, použijte přetížení, které přebírá typ obsahu a formátovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="38613-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="38613-161">UseStatusCodePages s lambdou</span><span class="sxs-lookup"><span data-stu-id="38613-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="38613-162">Chcete-li zadat vlastní kód pro zpracování chyb <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> a psaní odpovědí, použijte přetížení, které přebírá výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="38613-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="38613-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="38613-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="38613-164">Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> rozšíření:</span><span class="sxs-lookup"><span data-stu-id="38613-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="38613-165">Odešle klientovi stavový kód *302 - Found.*</span><span class="sxs-lookup"><span data-stu-id="38613-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="38613-166">Přesměruje klienta do umístění uvedeného v šabloně adresy URL.</span><span class="sxs-lookup"><span data-stu-id="38613-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="38613-167">Šablona adresy URL `{0}` může obsahovat zástupný symbol pro stavový kód, jak je znázorněno v příkladu.</span><span class="sxs-lookup"><span data-stu-id="38613-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="38613-168">Pokud šablona adresy URL začíná vlnovkou (~), bude vlnovka nahrazena šablonou aplikace `PathBase`.</span><span class="sxs-lookup"><span data-stu-id="38613-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="38613-169">Pokud ukážete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="38613-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="38613-170">Příklad Stránky razor viz *Stránky/StatusCode.cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="38613-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="38613-171">Tato metoda se běžně používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="38613-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="38613-172">By měl přesměrovat klienta na jiný koncový bod, obvykle v případech, kdy jiná aplikace zpracovává chybu.</span><span class="sxs-lookup"><span data-stu-id="38613-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="38613-173">U webových aplikací odráží adresní řádek prohlížeče klienta přesměrovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="38613-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="38613-174">Nemělby zachovat a vrátit původní stavový kód s počáteční redirect odpověď.</span><span class="sxs-lookup"><span data-stu-id="38613-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="38613-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="38613-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="38613-176">Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> rozšíření:</span><span class="sxs-lookup"><span data-stu-id="38613-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="38613-177">Vrátí klientovi původní stavový kód.</span><span class="sxs-lookup"><span data-stu-id="38613-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="38613-178">Generuje tělo odpovědi opětovným spuštěním kanálu požadavku pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="38613-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="38613-179">Pokud ukážete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="38613-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="38613-180">Příklad Stránky razor viz *Stránky/StatusCode.cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="38613-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="38613-181">Tato metoda se běžně používá, když by aplikace měla:</span><span class="sxs-lookup"><span data-stu-id="38613-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="38613-182">Zpracujte požadavek bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="38613-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="38613-183">U webových aplikací odráží adresní řádek prohlížeče klienta původně požadovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="38613-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="38613-184">Zachovat a vrátit původní stavový kód s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="38613-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="38613-185">Šablony adres URL a řetězců dotazu`{0}`mohou obsahovat zástupný symbol ( ) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="38613-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="38613-186">Šablona adresy URL musí`/`začínat lomítkem ( ).</span><span class="sxs-lookup"><span data-stu-id="38613-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="38613-187">Při použití zástupného symbolu v cestě zkontrolujte, zda koncový bod (stránka nebo řadič) může segment cesty zpracovat.</span><span class="sxs-lookup"><span data-stu-id="38613-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="38613-188">Například razor Page pro chyby by měla přijmout `@page` volitelnou hodnotu segmentu cesty se směrnicí:</span><span class="sxs-lookup"><span data-stu-id="38613-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="38613-189">Koncový bod, který zpracovává chybu může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="38613-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="38613-190">Zakázání stránek stavového kódu</span><span class="sxs-lookup"><span data-stu-id="38613-190">Disable status code pages</span></span>

<span data-ttu-id="38613-191">Chcete-li zakázat stránky stavového kódu pro řadič [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) nebo metodu akce MVC, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="38613-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="38613-192">Chcete-li zakázat stránky stavového kódu pro konkrétní požadavky v metodě <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>obslužné rutiny Razor Pages nebo v řadiči MVC, použijte :</span><span class="sxs-lookup"><span data-stu-id="38613-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="38613-193">Kód pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="38613-193">Exception-handling code</span></span>

<span data-ttu-id="38613-194">Kód na stránkách zpracování výjimek může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="38613-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="38613-195">Často je vhodné, aby se stránky výrobních chyb skládaly z čistě statického obsahu.</span><span class="sxs-lookup"><span data-stu-id="38613-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="38613-196">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="38613-196">Response headers</span></span>

<span data-ttu-id="38613-197">Po odeslání záhlaví pro odpověď:</span><span class="sxs-lookup"><span data-stu-id="38613-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="38613-198">Aplikace nemůže změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="38613-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="38613-199">Stránky nebo obslužné rutiny výjimek nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="38613-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="38613-200">Odpověď musí být dokončena nebo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="38613-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="38613-201">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="38613-201">Server exception handling</span></span>

<span data-ttu-id="38613-202">Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace http serveru](xref:fundamentals/servers/index) zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="38613-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="38613-203">Pokud server zachytí výjimku před odesláním hlaviček odpovědí, odešle odpověď *500 - Internal Server Error* bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="38613-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="38613-204">Pokud server zachytí výjimku po odeslání hlavičky odpovědí, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="38613-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="38613-205">Požadavky, které vaše aplikace nezpracovává, zpracovává server.</span><span class="sxs-lookup"><span data-stu-id="38613-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="38613-206">Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru.</span><span class="sxs-lookup"><span data-stu-id="38613-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="38613-207">Vlastní chybové stránky aplikace, výjimky zpracování middleware a filtry nemají vliv na toto chování.</span><span class="sxs-lookup"><span data-stu-id="38613-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="38613-208">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="38613-208">Startup exception handling</span></span>

<span data-ttu-id="38613-209">Pouze vrstva hostování může zpracovávat výjimky, ke kterým dochází při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="38613-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="38613-210">Hostitele lze nakonfigurovat tak, aby [zaznamenával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="38613-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="38613-211">Hostitelská vrstva může zobrazit chybovou stránku pro zachycenou chybu při spuštění pouze v případě, že k chybě dojde po vazbě adresy/portu hostitele.</span><span class="sxs-lookup"><span data-stu-id="38613-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="38613-212">Pokud vazba selže:</span><span class="sxs-lookup"><span data-stu-id="38613-212">If binding fails:</span></span>

* <span data-ttu-id="38613-213">Hostitelská vrstva zaznamenává kritickou výjimku.</span><span class="sxs-lookup"><span data-stu-id="38613-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="38613-214">Dotnet proces dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="38613-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="38613-215">Pokud je http server [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka .</span><span class="sxs-lookup"><span data-stu-id="38613-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="38613-216">Při spuštění ve službě [IIS](/iis) (nebo služby Azure App Service) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)je *502.5 – selhání procesu* vrácena [ASP.NET základního modulu,](xref:host-and-deploy/aspnet-core-module) pokud proces nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="38613-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="38613-217">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="38613-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="38613-218">Chybová stránka databáze</span><span class="sxs-lookup"><span data-stu-id="38613-218">Database error page</span></span>

<span data-ttu-id="38613-219">Middleware s chybou databáze zachycuje výjimky související s databází, které lze vyřešit pomocí migrace entity framework.</span><span class="sxs-lookup"><span data-stu-id="38613-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="38613-220">Dojde-li k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému.</span><span class="sxs-lookup"><span data-stu-id="38613-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="38613-221">Tato stránka by měla být povolena pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="38613-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="38613-222">Povolte stránku přidáním kódu do aplikace `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="38613-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="38613-223">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="38613-223">Exception filters</span></span>

<span data-ttu-id="38613-224">V aplikacích MVC lze filtry výjimek konfigurovat globálně nebo na základě kontroleru nebo akce.</span><span class="sxs-lookup"><span data-stu-id="38613-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="38613-225">V aplikacích Razor Pages je lze konfigurovat globálně nebo na stránce modelu.</span><span class="sxs-lookup"><span data-stu-id="38613-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="38613-226">Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dochází při provádění akce kontroleru nebo jiného filtru.</span><span class="sxs-lookup"><span data-stu-id="38613-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="38613-227">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="38613-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="38613-228">Filtry výjimek jsou užitečné pro vytváření přesahů výjimky, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="38613-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="38613-229">Doporučujeme používat middleware.</span><span class="sxs-lookup"><span data-stu-id="38613-229">We recommend using the middleware.</span></span> <span data-ttu-id="38613-230">Filtry používejte pouze tam, kde je třeba provádět zpracování chyb odlišně podle toho, která akce MVC je vybrána.</span><span class="sxs-lookup"><span data-stu-id="38613-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="38613-231">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="38613-231">Model state errors</span></span>

<span data-ttu-id="38613-232">Informace o tom, jak zpracovat chyby stavu modelu, naleznete v [tématu Model vazby](xref:mvc/models/model-binding) a [model ověření](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="38613-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38613-233">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="38613-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
