---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/01/2019
uid: fundamentals/error-handling
ms.openlocfilehash: a2ae2cb25c8cc5048b189b4035abbfc32a29aaff
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345488"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="81adb-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81adb-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="81adb-104">Podle [Petr Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="81adb-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="81adb-105">Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81adb-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="81adb-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81adb-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="81adb-107">Stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="81adb-107">Developer Exception Page</span></span>

<span data-ttu-id="81adb-108">Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o výjimkách, použijte *stránku výjimek pro vývojáře*.</span><span class="sxs-lookup"><span data-stu-id="81adb-108">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="81adb-109">Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="81adb-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="81adb-110">Přidejte řádek, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="81adb-110">Add a line to the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=5)]

<span data-ttu-id="81adb-111">Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před veškerý middleware, ve které chcete zaznamenat tak výjimky.</span><span class="sxs-lookup"><span data-stu-id="81adb-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="81adb-112">Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="81adb-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="81adb-113">Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="81adb-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="81adb-114">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="81adb-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="81adb-115">Stránce výjimek pro vývojáře najdete spuštění ukázkové aplikace s prostředím nastavena na `Development` a přidejte `?throw=true` k základní adrese URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="81adb-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="81adb-116">Stránka obsahuje následující informace o výjimku a požadavek:</span><span class="sxs-lookup"><span data-stu-id="81adb-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="81adb-117">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="81adb-117">Stack trace</span></span>
* <span data-ttu-id="81adb-118">Parametry řetězce dotazu (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="81adb-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="81adb-119">Soubory cookie (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="81adb-119">Cookies (if any)</span></span>
* <span data-ttu-id="81adb-120">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="81adb-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="81adb-121">Konfigurace vlastní výjimky zpracování stránky</span><span class="sxs-lookup"><span data-stu-id="81adb-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="81adb-122">Pokud není aplikace spuštěna ve vývojovém prostředí, zavolejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> metodu rozšíření k přidání Middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="81adb-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="81adb-123">Middleware:</span><span class="sxs-lookup"><span data-stu-id="81adb-123">The middleware:</span></span>

* <span data-ttu-id="81adb-124">Zachytává výjimky.</span><span class="sxs-lookup"><span data-stu-id="81adb-124">Catches exceptions.</span></span>
* <span data-ttu-id="81adb-125">Protokoly výjimky.</span><span class="sxs-lookup"><span data-stu-id="81adb-125">Logs exceptions.</span></span>
* <span data-ttu-id="81adb-126">Znovu provede požadavek na alternativní kanálu pro stránku nebo řadič uvedené.</span><span class="sxs-lookup"><span data-stu-id="81adb-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="81adb-127">Daný požadavek není znovu spustit, pokud odpověď byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="81adb-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="81adb-128">V následujícím příkladu z ukázkové aplikace <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá Middleware zpracování výjimek v jiných vývojových prostředích.</span><span class="sxs-lookup"><span data-stu-id="81adb-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="81adb-129">Určuje metodu rozšíření chybové stránky nebo ovladač na `/Error` koncový bod pro znovu spustit požadavky po výjimky jsou zachyceny a přihlášení:</span><span class="sxs-lookup"><span data-stu-id="81adb-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=9)]

<span data-ttu-id="81adb-130">Šablona aplikace Razor Pages poskytuje chybovou stránku (*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy (`ErrorModel`) ve složce stránky.</span><span class="sxs-lookup"><span data-stu-id="81adb-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="81adb-131">V aplikaci MVC následující metody obslužné rutiny chyb je součástí šablony aplikace MVC a zobrazí se v kontroler Home:</span><span class="sxs-lookup"><span data-stu-id="81adb-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="81adb-132">Není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="81adb-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="81adb-133">Explicitní příkazy zabránit v dosažení metodu některé požadavky.</span><span class="sxs-lookup"><span data-stu-id="81adb-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="81adb-134">Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="81adb-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="81adb-135">Konfigurace stavu znakové stránky</span><span class="sxs-lookup"><span data-stu-id="81adb-135">Configure status code pages</span></span>

<span data-ttu-id="81adb-136">Ve výchozím nastavení, aplikace ASP.NET Core neposkytuje znakovou stránku Stav pro stavové kódy HTTP, jako například *404 - Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="81adb-136">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="81adb-137">Aplikace se vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="81adb-137">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="81adb-138">Pokud chcete poskytnout stav znakové stránky, použijte Middleware stránky stavový kód.</span><span class="sxs-lookup"><span data-stu-id="81adb-138">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="81adb-139">Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="81adb-139">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="81adb-140">Přidejte řádek, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="81adb-140">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="81adb-141">Volání <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> metoda před požadavkem zpracování middleware (například Middleware statické soubory a Middlewarem MVC).</span><span class="sxs-lookup"><span data-stu-id="81adb-141">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="81adb-142">Ve výchozím nastavení, Middleware stránky stavový kód přidá prostého textu obslužné rutiny pro běžné stavové kódy, jako například *404 - Nenalezeno*:</span><span class="sxs-lookup"><span data-stu-id="81adb-142">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as *404 - Not Found*:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="81adb-143">Middleware podporuje několik metod rozšíření, které umožňují přizpůsobit chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="81adb-143">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

<span data-ttu-id="81adb-144">Přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> používá výraz lambda, který můžete použít ke zpracování vlastní logiku zpracování chyb a ručně zápisu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="81adb-144">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="81adb-145">Přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> obsahu typ a formát řetězce, který můžete použít pro přizpůsobení obsahu textu, typ a odpovědi:</span><span class="sxs-lookup"><span data-stu-id="81adb-145">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a><span data-ttu-id="81adb-146">Přesměrování a znovu spusťte rozšiřující metody</span><span class="sxs-lookup"><span data-stu-id="81adb-146">Redirect and re-execute extension methods</span></span>

<span data-ttu-id="81adb-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="81adb-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="81adb-148">Odešle *302 - nalezen* stavový kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="81adb-148">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="81adb-149">Přesměruje klienta do umístění, které jsou součástí adresy URL šablony.</span><span class="sxs-lookup"><span data-stu-id="81adb-149">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="81adb-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> se obvykle používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="81adb-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> is commonly used when the app:</span></span>

* <span data-ttu-id="81adb-151">Klient by se měla přesměrovat na jiný koncový bod, obvykle v případech, kde různé aplikace zpracovává chyby.</span><span class="sxs-lookup"><span data-stu-id="81adb-151">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="81adb-152">Pro web apps odráží adresního řádku prohlížeče klienta přesměrovaného koncový bod.</span><span class="sxs-lookup"><span data-stu-id="81adb-152">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="81adb-153">Neměli zachovat a vrátí původní stavový kód odpovědi počáteční přesměrování.</span><span class="sxs-lookup"><span data-stu-id="81adb-153">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="81adb-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="81adb-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="81adb-155">Vrátí původní stavový kód do klienta.</span><span class="sxs-lookup"><span data-stu-id="81adb-155">Returns the original status code to the client.</span></span>
* <span data-ttu-id="81adb-156">Generuje text odpovědi znovu spuštěním kanálu požadavku pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="81adb-156">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<span data-ttu-id="81adb-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> se obvykle používá při aplikace:</span><span class="sxs-lookup"><span data-stu-id="81adb-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> is commonly used when the app should:</span></span>

* <span data-ttu-id="81adb-158">Zpracování žádosti bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="81adb-158">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="81adb-159">Pro web apps odráží adresního řádku prohlížeče klienta na původně požadovanou koncový bod.</span><span class="sxs-lookup"><span data-stu-id="81adb-159">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="81adb-160">Zachovat a vrátí původní kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="81adb-160">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="81adb-161">Šablony mohou zahrnovat zástupný symbol (`{0}`) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="81adb-161">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="81adb-162">Šablona musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="81adb-162">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="81adb-163">Při použití zástupného symbolu, potvrďte, že koncový bod (stránka nebo řadič) může zpracovat segmentu cesty.</span><span class="sxs-lookup"><span data-stu-id="81adb-163">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="81adb-164">Například stránky Razor pro chyby by měl přijmout hodnotu volitelná cesta segmentu se `@page` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="81adb-164">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="81adb-165">Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC.</span><span class="sxs-lookup"><span data-stu-id="81adb-165">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="81adb-166">Zakázat stav znakových stránek, pokus o načtení <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> z identifikátoru požadavku [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) kolekce a zakažte funkci, pokud je k dispozici:</span><span class="sxs-lookup"><span data-stu-id="81adb-166">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="81adb-167">Použití <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> přetížení body do koncového bodu v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="81adb-167">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="81adb-168">Například šablona aplikace Razor Pages vytváří následující stránky a modelu třídy stránky:</span><span class="sxs-lookup"><span data-stu-id="81adb-168">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="81adb-169">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="81adb-169">*Error.cshtml*:</span></span>

::: moniker range=">= aspnetcore-2.2"

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
    Swapping to the <strong>Development</strong> environment displays 
    detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed 
    applications.</strong> It can result in displaying sensitive information 
    from exceptions to end users. For local debugging, enable the 
    <strong>Development</strong> environment by setting the 
    <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to 
    <strong>Development</strong> and restarting the app.
</p>
```

<span data-ttu-id="81adb-170">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="81adb-170">*Error.cshtml.cs*:</span></span>

```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

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

<span data-ttu-id="81adb-171">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="81adb-171">*Error.cshtml.cs*:</span></span>

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

::: moniker-end

## <a name="exception-handling-code"></a><span data-ttu-id="81adb-172">Kód zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="81adb-172">Exception-handling code</span></span>

<span data-ttu-id="81adb-173">Kód výjimky zpracování stránky může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="81adb-173">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="81adb-174">Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="81adb-174">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="81adb-175">Také, mějte na paměti, jakmile jsou odeslány hlavičky odpovědi:</span><span class="sxs-lookup"><span data-stu-id="81adb-175">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="81adb-176">Aplikaci nelze změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="81adb-176">The app can't change the response's status code.</span></span>
* <span data-ttu-id="81adb-177">Žádné výjimce stránky nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="81adb-177">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="81adb-178">Odpovědi musí dokončit, nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="81adb-178">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="81adb-179">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="81adb-179">Server exception handling</span></span>

<span data-ttu-id="81adb-180">Kromě ve vaší aplikaci logiky zpracování výjimek [implementaci serveru](xref:fundamentals/servers/index) dokáže zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="81adb-180">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="81adb-181">Pokud server zachytí výjimku, před odesláním hlavičky odpovědi, odešle server *500 – Interní chyba serveru* odpovědi bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="81adb-181">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="81adb-182">Pokud server zachytí výjimku po odeslání hlaviček odpovědí, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="81adb-182">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="81adb-183">Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem.</span><span class="sxs-lookup"><span data-stu-id="81adb-183">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="81adb-184">Všechny vyvolané výjimky jsou zpracována výjimka serveru zpracování.</span><span class="sxs-lookup"><span data-stu-id="81adb-184">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="81adb-185">Žádné nakonfigurované vlastní chybové stránky nebo zpracování výjimek middleware nebo filtry nemají vliv na toto chování.</span><span class="sxs-lookup"><span data-stu-id="81adb-185">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="81adb-186">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="81adb-186">Startup exception handling</span></span>

<span data-ttu-id="81adb-187">Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="81adb-187">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="81adb-188">Pomocí [webového hostitele](xref:fundamentals/host/web-host), můžete [konfigurace hostitele chování v reakci na chyby při spuštění](xref:fundamentals/host/web-host#detailed-errors) s `captureStartupErrors` a `detailedErrors` klíče.</span><span class="sxs-lookup"><span data-stu-id="81adb-188">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="81adb-189">Hostování můžete jenom zobrazit chybovou stránku pro chyby zaznamenané při spouštění, pokud dojde k chybě po adresa/port hostitele vazby.</span><span class="sxs-lookup"><span data-stu-id="81adb-189">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="81adb-190">Pokud z nějakého důvodu selže všechny vazby:</span><span class="sxs-lookup"><span data-stu-id="81adb-190">If any binding fails for any reason:</span></span>

* <span data-ttu-id="81adb-191">Hostování vrstvy zaznamená kritické výjimky.</span><span class="sxs-lookup"><span data-stu-id="81adb-191">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="81adb-192">Dotnet procesu dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="81adb-192">The dotnet process crashes.</span></span>
* <span data-ttu-id="81adb-193">Žádná chybová stránka se zobrazí, když je aplikace spuštěná na [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="81adb-193">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="81adb-194">Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 – selhání procesu* vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) Pokud proces nelze spustit. .</span><span class="sxs-lookup"><span data-stu-id="81adb-194">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="81adb-195">Další informace naleznete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="81adb-195">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="81adb-196">Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="81adb-196">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="81adb-197">Zpracování chyb technologie ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="81adb-197">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="81adb-198">[MVC](xref:mvc/overview) aplikace mají některé další možnosti pro zpracování chyb, jako je například konfigurace filtry výjimek a provedení ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="81adb-198">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="81adb-199">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="81adb-199">Exception filters</span></span>

<span data-ttu-id="81adb-200">Filtry výjimek se dá nakonfigurovat globálně nebo na základě na kontroler nebo akcích v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="81adb-200">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="81adb-201">Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="81adb-201">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="81adb-202">Tyto filtry nejsou volány jinak.</span><span class="sxs-lookup"><span data-stu-id="81adb-202">These filters aren't called otherwise.</span></span> <span data-ttu-id="81adb-203">Další informace najdete v tématu <xref:mvc/controllers/filters>.</span><span class="sxs-lookup"><span data-stu-id="81adb-203">To learn more, see <xref:mvc/controllers/filters>.</span></span>

> [!TIP]
> <span data-ttu-id="81adb-204">Filtry výjimek jsou užitečné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako middleware pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="81adb-204">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="81adb-205">Doporučujeme používat middleware.</span><span class="sxs-lookup"><span data-stu-id="81adb-205">We recommend the use of middleware.</span></span> <span data-ttu-id="81adb-206">Použití filtrů jenom tam, kde potřebujete provádět zpracování chyb *jinak* závislosti na zvolené akci, která MVC.</span><span class="sxs-lookup"><span data-stu-id="81adb-206">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="81adb-207">Zpracování chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="81adb-207">Handle model state errors</span></span>

<span data-ttu-id="81adb-208">[Ověření modelu](xref:mvc/models/validation) vyvolá se před vyvoláním každé akce kontroleru a zodpovídá za metodu akce ke kontrole [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) a reagují odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="81adb-208">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="81adb-209">Některé aplikace zvolte dodržovat standardní zásady pro nakládání s [ověření modelu](xref:mvc/models/validation) chyby, v takovém případě [filtr](xref:mvc/controllers/filters) může být vhodné místo pro implementaci tuto zásadu.</span><span class="sxs-lookup"><span data-stu-id="81adb-209">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="81adb-210">Měli byste otestovat chování vaše akce se stavy modelu je neplatný.</span><span class="sxs-lookup"><span data-stu-id="81adb-210">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="81adb-211">Další informace naleznete v tématu <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="81adb-211">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81adb-212">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="81adb-212">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
