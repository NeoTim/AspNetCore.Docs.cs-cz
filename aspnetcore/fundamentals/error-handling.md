---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/01/2018
uid: fundamentals/error-handling
ms.openlocfilehash: fbc86d36f66e71e6ebd84f536148fba2e3c452d8
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570058"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="9f0f8-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f0f8-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="9f0f8-104">Podle [Steve Smith](https://ardalis.com/) a [Petr Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="9f0f8-104">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="9f0f8-105">Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="9f0f8-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f0f8-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-developer-exception-page"></a><span data-ttu-id="9f0f8-107">Stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="9f0f8-107">The Developer Exception Page</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9f0f8-108">Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-108">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="9f0f8-109">Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="9f0f8-110">Přidejte řádek, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-110">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9f0f8-111">Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-111">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="9f0f8-112">Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-112">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="9f0f8-113">Přidejte řádek, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-113">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9f0f8-114">Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-114">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="9f0f8-115">Na stránce je k dispozici tak, že přidáte odkaz na balíček pro [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíčku v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-115">The page is made available by adding a package reference for the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package in the project file.</span></span> <span data-ttu-id="9f0f8-116">Přidejte řádek, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-116">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

<span data-ttu-id="9f0f8-117">Umístěte volání [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) před veškerý middleware, ve které chcete zaznamenat tak výjimky, jako například `app.UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-117">Place the call to [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) in front of any middleware where you want to catch exceptions, such as `app.UseMvc`.</span></span>

> [!WARNING]
> <span data-ttu-id="9f0f8-118">Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-118">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="9f0f8-119">Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-119">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="9f0f8-120">[Další informace o konfiguraci prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-120">[Learn more about configuring environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9f0f8-121">Stránce výjimek pro vývojáře najdete spuštění ukázkové aplikace s prostředím nastavena na `Development` a přidejte `?throw=true` k základní adrese URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-121">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="9f0f8-122">Stránka obsahuje několik karet s informacemi o výjimku a požadavek.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-122">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="9f0f8-123">První karta obsahuje trasování zásobníku:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-123">The first tab includes a stack trace:</span></span>

![Trasování zásobníku](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="9f0f8-125">Další karta ukazuje dotaz případné parametry řetězce:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-125">The next tab shows the query string parameters, if any:</span></span>

![Parametry řetězce dotazu](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="9f0f8-127">Pokud požadavek má soubory cookie, zobrazí se na **soubory cookie** kartu. Záhlaví se zobrazují na kartě poslední:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-127">If the request has cookies, they appear on the **Cookies** tab. Headers are seen in the last tab:</span></span>

![Záhlaví](error-handling/_static/developer-exception-page-headers.png)

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="9f0f8-129">Konfigurace vlastní výjimky zpracování stránky</span><span class="sxs-lookup"><span data-stu-id="9f0f8-129">Configure a custom exception handling page</span></span>

<span data-ttu-id="9f0f8-130">Na stránce obslužné rutiny výjimky pro použití při není aplikace spuštěna konfiguraci `Development` prostředí:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-130">Configure an exception handler page to use when the app isn't running in the `Development` environment:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

<span data-ttu-id="9f0f8-131">V aplikaci s Razor Pages [dotnet nové](/dotnet/core/tools/dotnet-new) šablona Razor Pages nabízí chybovou stránku a chybě `PageModel` třídy v *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-131">In a Razor Pages app, the [dotnet new](/dotnet/core/tools/dotnet-new) Razor Pages template provides an Error page and an error `PageModel` class in the *Pages* folder.</span></span>

<span data-ttu-id="9f0f8-132">V aplikaci MVC, není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-132">In an MVC app, don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="9f0f8-133">Explicitní příkazy zabránit v dosažení metodu některé požadavky.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="9f0f8-134">Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

<span data-ttu-id="9f0f8-135">Například poskytuje následující metody obslužné rutiny chyb [dotnet nové](/dotnet/core/tools/dotnet-new) šablona MVC a zobrazí se v kontroler Home:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-135">For example, the following error handler method is provided by the [dotnet new](/dotnet/core/tools/dotnet-new) MVC template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

## <a name="configure-status-code-pages"></a><span data-ttu-id="9f0f8-136">Konfigurace stavu znakové stránky</span><span class="sxs-lookup"><span data-stu-id="9f0f8-136">Configure status code pages</span></span>

<span data-ttu-id="9f0f8-137">Ve výchozím nastavení, aplikace neposkytuje znakovou stránku bohaté stav pro stavové kódy HTTP, jako například *404 Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-137">By default, an app doesn't provide a rich status code page for HTTP status codes, such as *404 Not Found*.</span></span> <span data-ttu-id="9f0f8-138">Pokud chcete poskytnout stav znakové stránky, použijte Middleware stránky stavový kód.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-138">To provide status code pages, use Status Code Pages Middleware.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9f0f8-139">Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-139">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9f0f8-140">Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-140">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9f0f8-141">Middleware je k dispozici tak, že přidáte odkaz na balíček pro [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíčku v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-141">The middleware is made available by adding a package reference for the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package in the project file.</span></span>

::: moniker-end

<span data-ttu-id="9f0f8-142">Přidejte řádek, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-142">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="9f0f8-143"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> by měla být volána před požadavkem zpracování middlewares v kanálu (třeba Middleware statické soubory a Middlewarem MVC).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-143"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> should be called before request handling middlewares in the pipeline (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="9f0f8-144">Ve výchozím nastavení přidá Middleware stránky stavový kód obslužné rutiny pro běžné stavové kódy, jako je například 404 prostého textu:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-144">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as 404:</span></span>

![404 – Stránka](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="9f0f8-146">Middleware podporuje několik metod rozšíření.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-146">The middleware supports several extension methods.</span></span> <span data-ttu-id="9f0f8-147">Jedna metoda má výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-147">One method takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="9f0f8-148">Přetížení `UseStatusCodePages` obsahu typ a formát řetězce:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-148">An overload of `UseStatusCodePages` takes a content type and format string:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```
### <a name="redirect-re-execute-extension-methods"></a><span data-ttu-id="9f0f8-149">Znovu provést rozšiřující metody pro přesměrování</span><span class="sxs-lookup"><span data-stu-id="9f0f8-149">Redirect re-execute extension methods</span></span>

<span data-ttu-id="9f0f8-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="9f0f8-151">Odešle *302 - nalezen* stavový kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-151">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="9f0f8-152">Přesměruje klienta do umístění, které jsou součástí adresy URL šablony.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-152">Redirects the client to the location provided in the URL template.</span></span> 

<span data-ttu-id="9f0f8-153">Šablona může obsahovat `{0}` zástupný symbol pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-153">The template may include a `{0}` placeholder for the status code.</span></span> <span data-ttu-id="9f0f8-154">Šablona musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-154">The template must start with a forward slash (`/`).</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="9f0f8-155"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-155"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="9f0f8-156">Vrátí původní stavový kód do klienta.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-156">Returns the original status code to the client.</span></span>
* <span data-ttu-id="9f0f8-157">Určuje, že tělo odpovědi by měl být vygenerován znovu spuštěním kanálu požadavku pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-157">Specifies that the response body should be generated by re-executing the request pipeline using an alternate path.</span></span> 

<span data-ttu-id="9f0f8-158">Šablona může obsahovat `{0}` zástupný symbol pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-158">The template may include a `{0}` placeholder for the status code.</span></span> <span data-ttu-id="9f0f8-159">Šablona musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-159">The template must start with a forward slash (`/`).</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="9f0f8-160">Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-160">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="9f0f8-161">Zakázat stav znakových stránek, pokus o načtení [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) z identifikátoru požadavku [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) kolekce a zakažte funkci, pokud je k dispozici:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-161">To disable status code pages, attempt to retrieve the [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) from the request's [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="9f0f8-162">Použití `UseStatusCodePages*` přetížení body do koncového bodu v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-162">To use a `UseStatusCodePages*` overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="9f0f8-163">Například [dotnet nové](/dotnet/core/tools/dotnet-new) šablony pro aplikace Razor Pages vytvoří následující stránky a modelu třídy stránky:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-163">For example, the [dotnet new](/dotnet/core/tools/dotnet-new) template for a Razor Pages app produces the following page and page model class:</span></span>

<span data-ttu-id="9f0f8-164">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-164">*Error.cshtml*:</span></span>

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

<span data-ttu-id="9f0f8-165">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9f0f8-165">*Error.cshtml.cs*:</span></span>

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="9f0f8-166">Kód zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="9f0f8-166">Exception-handling code</span></span>

<span data-ttu-id="9f0f8-167">Kód výjimky zpracování stránky může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-167">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="9f0f8-168">Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-168">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="9f0f8-169">Také mějte na paměti, že po odeslání hlaviček pro odpovědi, nelze změnit stavový kód odpovědi ani žádné výjimce stránky nebo obslužné rutiny poběží.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-169">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="9f0f8-170">Odpovědi musí dokončit, nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-170">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="9f0f8-171">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="9f0f8-171">Server exception handling</span></span>

<span data-ttu-id="9f0f8-172">Kromě ve vaší aplikaci logiky zpracování výjimek [server](xref:fundamentals/servers/index) hostování vaší aplikace provádí nějaké zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-172">In addition to the exception handling logic in your app, the [server](xref:fundamentals/servers/index) hosting your app performs some exception handling.</span></span> <span data-ttu-id="9f0f8-173">Pokud server zachytí výjimku, před odesláním záhlaví, odešle server *500 – Interní chyba serveru* odpověď se žádný text.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-173">If the server catches an exception before the headers are sent, the server sends a *500 Internal Server Error* response with no body.</span></span> <span data-ttu-id="9f0f8-174">Pokud server zachytí výjimku po odeslání hlavičky, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-174">If the server catches an exception after the headers have been sent, the server closes the connection.</span></span> <span data-ttu-id="9f0f8-175">Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-175">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="9f0f8-176">Všechny vyvolané výjimky jsou zpracována výjimka serveru zpracování.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-176">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="9f0f8-177">Žádné nakonfigurované vlastní chybové stránky nebo zpracování výjimek middleware nebo filtry nemají vliv na toto chování.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-177">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="9f0f8-178">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="9f0f8-178">Startup exception handling</span></span>

<span data-ttu-id="9f0f8-179">Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-179">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="9f0f8-180">Použití [webového hostitele](xref:fundamentals/host/web-host), můžete [konfigurace hostitele chování v reakci na chyby při spuštění](xref:fundamentals/host/web-host#detailed-errors) s `captureStartupErrors` a `detailedErrors` klíče.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-180">Using the [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="9f0f8-181">Hostování můžete jenom zobrazit chybovou stránku pro chyby zaznamenané při spouštění, pokud dojde k chybě po adresa/port hostitele vazby.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-181">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="9f0f8-182">Pokud z nějakého důvodu selže všechny vazby, hostování vrstvy zaznamená kritické výjimky, dojde k chybě dotnet procesu a žádná chybová stránka se zobrazí, když je aplikace spuštěná na [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-182">If any binding fails for any reason, the hosting layer logs a critical exception, the dotnet process crashes, and no error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="9f0f8-183">Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 selhání procesu* je vrácený [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) Pokud proces nemůže být bylo zahájeno.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-183">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 Process Failure* is returned by the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) if the process can't be started.</span></span> <span data-ttu-id="9f0f8-184">Informace o řešení problémů se spouštěním, při hostování za nástrojem službou IIS najdete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-184">For information on troubleshooting startup issues when hosting with IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="9f0f8-185">Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-185">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="9f0f8-186">Zpracování chyb technologie ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="9f0f8-186">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="9f0f8-187">[MVC](xref:mvc/overview) aplikace mají některé další možnosti pro zpracování chyb, jako je například konfigurace filtry výjimek a provedení ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-187">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="9f0f8-188">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="9f0f8-188">Exception filters</span></span>

<span data-ttu-id="9f0f8-189">Filtry výjimek se dá nakonfigurovat globálně nebo na základě na kontroler nebo akcích v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-189">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="9f0f8-190">Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-190">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="9f0f8-191">Tyto filtry nejsou volány jinak.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-191">These filters aren't called otherwise.</span></span> <span data-ttu-id="9f0f8-192">Další informace najdete v tématu [filtry](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-192">To learn more, see [Filters](xref:mvc/controllers/filters).</span></span>

> [!TIP]
> <span data-ttu-id="9f0f8-193">Filtry výjimek jsou vhodné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako middleware pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-193">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="9f0f8-194">Obecně přednost použití middlewaru a použití filtrů jenom tam, kde potřebujete provádět zpracování chyb *jinak* závislosti na zvolené akci, která MVC.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-194">Generally prefer the use of middleware, and use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="9f0f8-195">Zpracování chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="9f0f8-195">Handling model state errors</span></span>

<span data-ttu-id="9f0f8-196">[Ověření modelu](xref:mvc/models/validation) vyvolá se před vyvoláním každé akce kontroleru a zodpovídá za metodu akce ke kontrole `ModelState.IsValid` a reagují odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-196">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="9f0f8-197">Některé aplikace zvolte dodržovat standardní konvence týkající se chyby ověření modelu, v takovém případě [filtr](xref:mvc/controllers/filters) může být vhodné místo pro implementaci tuto zásadu.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-197">Some apps choose to follow a standard convention for dealing with model validation errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="9f0f8-198">Měli byste otestovat chování vaše akce se stavy modelu je neplatný.</span><span class="sxs-lookup"><span data-stu-id="9f0f8-198">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="9f0f8-199">Další informace najdete v [testovací kontroler logiku](xref:mvc/controllers/testing).</span><span class="sxs-lookup"><span data-stu-id="9f0f8-199">Learn more in [Test controller logic](xref:mvc/controllers/testing).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f0f8-200">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9f0f8-200">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
