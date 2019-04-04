---
title: Zpracování chyb v ASP.NET Core
author: tdykstra
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: ae0b80baed814cd4c7c1dddce2f26a6facfdbaad
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012705"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="a5956-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5956-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="a5956-104">Podle [Petr Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a5956-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a5956-105">Tento článek se věnuje běžné přístupy k zpracování chyb v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5956-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="a5956-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5956-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a5956-107">Stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="a5956-107">Developer Exception Page</span></span>

<span data-ttu-id="a5956-108">Chcete-li nakonfigurovat aplikaci, která zobrazí stránka, která jsou uvedeny podrobné informace o žádosti o výjimkách, použijte *stránku výjimek pro vývojáře*.</span><span class="sxs-lookup"><span data-stu-id="a5956-108">To configure an app to display a page that shows detailed information about request exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="a5956-109">Na stránce je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a5956-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a5956-110">Přidejte řádek, který `Startup.Configure` metodu, když aplikace běží při vývoji [prostředí](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="a5956-110">Add a line to the `Startup.Configure` method when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseDeveloperExceptionPage)]

<span data-ttu-id="a5956-111">Umístěte volání <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> před veškerý middleware, ve které chcete zaznamenat tak výjimky.</span><span class="sxs-lookup"><span data-stu-id="a5956-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="a5956-112">Povolit na stránce výjimek pro vývojáře **pouze, když je aplikace spuštěna ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="a5956-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a5956-113">Nechcete veřejně sdílet podrobné informace o výjimce při spuštění aplikace v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="a5956-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a5956-114">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a5956-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a5956-115">Stránce výjimek pro vývojáře najdete spuštění ukázkové aplikace s prostředím nastavena na `Development` a přidejte `?throw=true` k základní adrese URL aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5956-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="a5956-116">Stránka obsahuje následující informace o výjimku a požadavek:</span><span class="sxs-lookup"><span data-stu-id="a5956-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="a5956-117">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="a5956-117">Stack trace</span></span>
* <span data-ttu-id="a5956-118">Parametry řetězce dotazu (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="a5956-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="a5956-119">Soubory cookie (pokud existuje)</span><span class="sxs-lookup"><span data-stu-id="a5956-119">Cookies (if any)</span></span>
* <span data-ttu-id="a5956-120">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="a5956-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="a5956-121">Konfigurace vlastní výjimky zpracování stránky</span><span class="sxs-lookup"><span data-stu-id="a5956-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="a5956-122">Pokud není aplikace spuštěna ve vývojovém prostředí, zavolejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> metodu rozšíření k přidání Middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="a5956-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="a5956-123">Middleware:</span><span class="sxs-lookup"><span data-stu-id="a5956-123">The middleware:</span></span>

* <span data-ttu-id="a5956-124">Zachytává výjimky.</span><span class="sxs-lookup"><span data-stu-id="a5956-124">Catches exceptions.</span></span>
* <span data-ttu-id="a5956-125">Protokoly výjimky.</span><span class="sxs-lookup"><span data-stu-id="a5956-125">Logs exceptions.</span></span>
* <span data-ttu-id="a5956-126">Znovu provede požadavek na alternativní kanálu pro stránku nebo řadič uvedené.</span><span class="sxs-lookup"><span data-stu-id="a5956-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="a5956-127">Daný požadavek není znovu spustit, pokud odpověď byla spuštěna.</span><span class="sxs-lookup"><span data-stu-id="a5956-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="a5956-128">V následujícím příkladu z ukázkové aplikace <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> přidá Middleware zpracování výjimek v jiných vývojových prostředích.</span><span class="sxs-lookup"><span data-stu-id="a5956-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="a5956-129">Určuje metodu rozšíření chybové stránky nebo ovladač na `/Error` koncový bod pro znovu spustit požadavky po výjimky jsou zachyceny a přihlášení:</span><span class="sxs-lookup"><span data-stu-id="a5956-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler1)]

<span data-ttu-id="a5956-130">Šablona aplikace Razor Pages poskytuje chybovou stránku (*.cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídy (`ErrorModel`) ve složce stránky.</span><span class="sxs-lookup"><span data-stu-id="a5956-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="a5956-131">V aplikaci MVC následující metody obslužné rutiny chyb je součástí šablony aplikace MVC a zobrazí se v kontroler Home:</span><span class="sxs-lookup"><span data-stu-id="a5956-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="a5956-132">Není uspořádání metody akce obslužná rutina chyby s atributy metody HTTP, jako například `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="a5956-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="a5956-133">Explicitní příkazy zabránit v dosažení metodu některé požadavky.</span><span class="sxs-lookup"><span data-stu-id="a5956-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="a5956-134">Povolit anonymní přístup k metodě tak, aby se neověřené uživatele dostávají zobrazení chyb.</span><span class="sxs-lookup"><span data-stu-id="a5956-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="access-the-exception"></a><span data-ttu-id="a5956-135">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="a5956-135">Access the exception</span></span>

<span data-ttu-id="a5956-136">Použití <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce nebo původní cestu požadavku v kontroleru nebo stránky:</span><span class="sxs-lookup"><span data-stu-id="a5956-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception or the original request path in a controller or page:</span></span>

* <span data-ttu-id="a5956-137">Cesta je k dispozici <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path> vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a5956-137">The path is available from the <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature.Path> property.</span></span>
* <span data-ttu-id="a5956-138">Přečtěte si <xref:System.Exception?displayProperty=fullName> z zděděnou [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a5956-138">Read the <xref:System.Exception?displayProperty=fullName> from the inherited [IExceptionHandlerFeature.Error](xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature.Error) property.</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics;

var exceptionHandlerPathFeature = 
    HttpContext.Features.Get<IExceptionHandlerPathFeature>();
var path = exceptionHandlerPathFeature?.Path;
var error = exceptionHandlerPathFeature?.Error;
```

> [!WARNING]
> <span data-ttu-id="a5956-139">Proveďte **není** poskytovat informace o chybě citlivé z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům.</span><span class="sxs-lookup"><span data-stu-id="a5956-139">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="a5956-140">Obsluhuje chyby představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="a5956-140">Serving errors is a security risk.</span></span>

## <a name="configure-custom-exception-handling-code"></a><span data-ttu-id="a5956-141">Konfigurace vlastního kódu pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="a5956-141">Configure custom exception handling code</span></span>

<span data-ttu-id="a5956-142">Alternativu se, že koncový bod pro chyby pomocí [vlastní výjimky zpracování stránky](#configure-a-custom-exception-handling-page) je poskytnout lambda <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span><span class="sxs-lookup"><span data-stu-id="a5956-142">An alternative to serving an endpoint for errors with a [custom exception handling page](#configure-a-custom-exception-handling-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="a5956-143">Použití výrazu lambda s <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a5956-143">Using a lambda with <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> allows access to the error before returning the response.</span></span>

<span data-ttu-id="a5956-144">Ukázková aplikace předvádí vlastní kód ve zpracování výjimek `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a5956-144">The sample app demonstrates custom exception handling code in `Startup.Configure`.</span></span> <span data-ttu-id="a5956-145">Aktivuje výjimku **vyvolat výjimky** odkaz na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="a5956-145">Trigger an exception with the **Throw Exception** link on the Index page.</span></span> <span data-ttu-id="a5956-146">Následující výraz lambda spustí:</span><span class="sxs-lookup"><span data-stu-id="a5956-146">The following lambda runs:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_UseExceptionHandler2)]

> [!WARNING]
> <span data-ttu-id="a5956-147">Proveďte **není** poskytovat informace o chybě citlivé z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> nebo <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientům.</span><span class="sxs-lookup"><span data-stu-id="a5956-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="a5956-148">Obsluhuje chyby představuje bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="a5956-148">Serving errors is a security risk.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="a5956-149">Konfigurace stavu znakové stránky</span><span class="sxs-lookup"><span data-stu-id="a5956-149">Configure status code pages</span></span>

<span data-ttu-id="a5956-150">Ve výchozím nastavení, aplikace ASP.NET Core neposkytuje znakovou stránku Stav pro stavové kódy HTTP, jako například *404 - Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="a5956-150">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="a5956-151">Aplikace se vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a5956-151">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="a5956-152">Pokud chcete poskytnout stav znakové stránky, použijte Middleware stránky stavový kód.</span><span class="sxs-lookup"><span data-stu-id="a5956-152">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="a5956-153">Middleware je k dispozici ve [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) balíček, který je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a5956-153">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

### <a name="usestatuscodepages"></a><span data-ttu-id="a5956-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="a5956-154">UseStatusCodePages</span></span>

<span data-ttu-id="a5956-155">Pokud chcete povolit výchozích prostého textu obslužných rutin pro běžné kódy chyb stavu, přidejte následující kód, který `Startup.Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="a5956-155">To enable default text-only handlers for common error status codes, add the following code to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="a5956-156">Volání <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> metoda před požadavkem zpracování middleware (například Middleware statické soubory a Middlewarem MVC).</span><span class="sxs-lookup"><span data-stu-id="a5956-156">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="a5956-157">Tady je příklad text, zobrazený výchozích obslužných rutin:</span><span class="sxs-lookup"><span data-stu-id="a5956-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="a5956-158">Middleware podporuje několik metod rozšíření, které umožňují přizpůsobit chování aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5956-158">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="a5956-159">UseStatusCodePages pomocí výrazu lambda</span><span class="sxs-lookup"><span data-stu-id="a5956-159">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="a5956-160">Přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> používá výraz lambda, který můžete použít ke zpracování vlastní logiku zpracování chyb a ručně zápisu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a5956-160">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="a5956-161">UseStatusCodePages řetězcem formátu</span><span class="sxs-lookup"><span data-stu-id="a5956-161">UseStatusCodePages with format string</span></span>

<span data-ttu-id="a5956-162">Přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> obsahu typ a formát řetězce, který můžete použít pro přizpůsobení obsahu textu, typ a odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a5956-162">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="a5956-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="a5956-163">UseStatusCodePagesWithRedirects</span></span>

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*><span data-ttu-id="a5956-164">:</span><span class="sxs-lookup"><span data-stu-id="a5956-164">:</span></span>

* <span data-ttu-id="a5956-165">Odešle *302 - nalezen* stavový kód na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a5956-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="a5956-166">Přesměruje klienta do umístění, které jsou součástí adresy URL šablony.</span><span class="sxs-lookup"><span data-stu-id="a5956-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> <span data-ttu-id="a5956-167">se obvykle používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="a5956-167">is commonly used when the app:</span></span>

* <span data-ttu-id="a5956-168">Klient by se měla přesměrovat na jiný koncový bod, obvykle v případech, kde různé aplikace zpracovává chyby.</span><span class="sxs-lookup"><span data-stu-id="a5956-168">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="a5956-169">Pro web apps odráží adresního řádku prohlížeče klienta přesměrovaného koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a5956-169">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="a5956-170">Neměli zachovat a vrátí původní stavový kód odpovědi počáteční přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a5956-170">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="a5956-171">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="a5956-171">UseStatusCodePagesWithReExecute</span></span>

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*><span data-ttu-id="a5956-172">:</span><span class="sxs-lookup"><span data-stu-id="a5956-172">:</span></span>

* <span data-ttu-id="a5956-173">Vrátí původní stavový kód do klienta.</span><span class="sxs-lookup"><span data-stu-id="a5956-173">Returns the original status code to the client.</span></span>
* <span data-ttu-id="a5956-174">Generuje text odpovědi znovu spuštěním kanálu požadavku pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="a5956-174">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> <span data-ttu-id="a5956-175">se obvykle používá při aplikace:</span><span class="sxs-lookup"><span data-stu-id="a5956-175">is commonly used when the app should:</span></span>

* <span data-ttu-id="a5956-176">Zpracování žádosti bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a5956-176">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="a5956-177">Pro web apps odráží adresního řádku prohlížeče klienta na původně požadovanou koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a5956-177">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="a5956-178">Zachovat a vrátí původní kód stavu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a5956-178">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="a5956-179">Šablony mohou zahrnovat zástupný symbol (`{0}`) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="a5956-179">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="a5956-180">Šablona musí začínat lomítkem (`/`).</span><span class="sxs-lookup"><span data-stu-id="a5956-180">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="a5956-181">Při použití zástupného symbolu, potvrďte, že koncový bod (stránka nebo řadič) může zpracovat segmentu cesty.</span><span class="sxs-lookup"><span data-stu-id="a5956-181">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="a5956-182">Například stránky Razor pro chyby by měl přijmout hodnotu volitelná cesta segmentu se `@page` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="a5956-182">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="a5956-183">Koncový bod, který zpracovává chybu můžete získat původní adresu URL, který vytvořil chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a5956-183">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

```csharp
var statusCodeReExecuteFeature = HttpContext.Features.Get<IStatusCodeReExecuteFeature>();
var originalPathBase = statusCodeReExecuteFeature?.OriginalPathBase;
var originalPath = statusCodeReExecuteFeature?.OriginalPath;
var originalQueryString = statusCodeReExecuteFeature?.OriginalQueryString;
```

### <a name="disable-status-code-pages"></a><span data-ttu-id="a5956-184">Zakázat stav znakové stránky</span><span class="sxs-lookup"><span data-stu-id="a5956-184">Disable status code pages</span></span>

<span data-ttu-id="a5956-185">Stav znakové stránky je možné zakázat pro konkrétní požadavky v metodě obslužné rutiny pro stránky Razor nebo kontroler MVC.</span><span class="sxs-lookup"><span data-stu-id="a5956-185">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="a5956-186">Zakázat stav znakových stránek, pokus o načtení <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> z identifikátoru požadavku [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) kolekce a zakažte funkci, pokud je k dispozici:</span><span class="sxs-lookup"><span data-stu-id="a5956-186">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

### <a name="status-code-page-endpoints"></a><span data-ttu-id="a5956-187">Stavový kód stránku koncové body</span><span class="sxs-lookup"><span data-stu-id="a5956-187">Status code page endpoints</span></span>

<span data-ttu-id="a5956-188">Použití <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> přetížení body do koncového bodu v rámci aplikace, vytvořte zobrazení MVC nebo stránky Razor pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a5956-188">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="a5956-189">Například šablona aplikace Razor Pages vytváří následující stránky a modelu třídy stránky:</span><span class="sxs-lookup"><span data-stu-id="a5956-189">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="a5956-190">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a5956-190">*Error.cshtml*:</span></span>

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

<span data-ttu-id="a5956-191">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5956-191">*Error.cshtml.cs*:</span></span>

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

<span data-ttu-id="a5956-192">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5956-192">*Error.cshtml.cs*:</span></span>

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

## <a name="exception-handling-code"></a><span data-ttu-id="a5956-193">Kód zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="a5956-193">Exception-handling code</span></span>

<span data-ttu-id="a5956-194">Kód výjimky zpracování stránky může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="a5956-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="a5956-195">Často je vhodné pro produkční chybové stránky, které se skládají z čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="a5956-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="a5956-196">Také, mějte na paměti, jakmile jsou odeslány hlavičky odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a5956-196">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="a5956-197">Aplikaci nelze změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a5956-197">The app can't change the response's status code.</span></span>
* <span data-ttu-id="a5956-198">Žádné výjimce stránky nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="a5956-198">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="a5956-199">Odpovědi musí dokončit, nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="a5956-199">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="a5956-200">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="a5956-200">Server exception handling</span></span>

<span data-ttu-id="a5956-201">Kromě ve vaší aplikaci logiky zpracování výjimek [implementaci serveru](xref:fundamentals/servers/index) dokáže zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="a5956-201">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="a5956-202">Pokud server zachytí výjimku, před odesláním hlavičky odpovědi, odešle server *500 – Interní chyba serveru* odpovědi bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a5956-202">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="a5956-203">Pokud server zachytí výjimku po odeslání hlaviček odpovědí, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="a5956-203">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="a5956-204">Požadavky, které nejsou zpracovány aplikací jsou zpracovány serverem.</span><span class="sxs-lookup"><span data-stu-id="a5956-204">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="a5956-205">Jakoukoliv výjimku, která vyvolá se v případě, že server zpracovává žádost je zpracována výjimka serveru zpracování.</span><span class="sxs-lookup"><span data-stu-id="a5956-205">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="a5956-206">Aplikace vlastní chybové stránky, zpracování middleware a filtry výjimek nemají vliv na toto chování.</span><span class="sxs-lookup"><span data-stu-id="a5956-206">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="a5956-207">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="a5956-207">Startup exception handling</span></span>

<span data-ttu-id="a5956-208">Pouze hostování vrstvy dokáže zpracovat výjimky, které se provedou při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5956-208">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="a5956-209">Pomocí [webového hostitele](xref:fundamentals/host/web-host), můžete [konfigurace hostitele chování v reakci na chyby při spuštění](xref:fundamentals/host/web-host#detailed-errors) s `captureStartupErrors` a `detailedErrors` klíče.</span><span class="sxs-lookup"><span data-stu-id="a5956-209">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="a5956-210">Hostování můžete jenom zobrazit chybovou stránku pro chyby zaznamenané při spouštění, pokud dojde k chybě po adresa/port hostitele vazby.</span><span class="sxs-lookup"><span data-stu-id="a5956-210">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="a5956-211">Pokud z nějakého důvodu selže všechny vazby:</span><span class="sxs-lookup"><span data-stu-id="a5956-211">If any binding fails for any reason:</span></span>

* <span data-ttu-id="a5956-212">Hostování vrstvy zaznamená kritické výjimky.</span><span class="sxs-lookup"><span data-stu-id="a5956-212">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="a5956-213">Dotnet procesu dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a5956-213">The dotnet process crashes.</span></span>
* <span data-ttu-id="a5956-214">Žádná chybová stránka se zobrazí, když je aplikace spuštěná na [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="a5956-214">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="a5956-215">Při spuštění na [IIS](/iis) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), *502.5 – selhání procesu* vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) Pokud proces nelze spustit. .</span><span class="sxs-lookup"><span data-stu-id="a5956-215">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="a5956-216">Další informace naleznete v tématu <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="a5956-216">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="a5956-217">Informace o řešení problémů se spouštěním pomocí služby Azure App Service najdete v tématu <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="a5956-217">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="a5956-218">Zpracování chyb technologie ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a5956-218">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="a5956-219">[MVC](xref:mvc/overview) aplikace mají některé další možnosti pro zpracování chyb, jako je například konfigurace filtry výjimek a provedení ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="a5956-219">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="a5956-220">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="a5956-220">Exception filters</span></span>

<span data-ttu-id="a5956-221">Filtry výjimek se dá nakonfigurovat globálně nebo na základě na kontroler nebo akcích v aplikaci MVC.</span><span class="sxs-lookup"><span data-stu-id="a5956-221">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="a5956-222">Tyto filtry zpracování neošetřené výjimky, která během provádění akce kontroleru nebo jiný filtr.</span><span class="sxs-lookup"><span data-stu-id="a5956-222">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="a5956-223">Tyto filtry nejsou volány jinak.</span><span class="sxs-lookup"><span data-stu-id="a5956-223">These filters aren't called otherwise.</span></span> <span data-ttu-id="a5956-224">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="a5956-224">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="a5956-225">Filtry výjimek jsou užitečné pro soutisku výjimky, ke kterým dochází v rámci akce MVC, ale nejsou tak flexibilní jako Middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="a5956-225">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="a5956-226">Doporučujeme používat middleware.</span><span class="sxs-lookup"><span data-stu-id="a5956-226">We recommend using the middleware.</span></span> <span data-ttu-id="a5956-227">Použití filtrů jenom tam, kde potřebujete provádět zpracování chyb *jinak* závislosti na zvolené akci, která MVC.</span><span class="sxs-lookup"><span data-stu-id="a5956-227">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="a5956-228">Zpracování chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="a5956-228">Handle model state errors</span></span>

<span data-ttu-id="a5956-229">[Ověření modelu](xref:mvc/models/validation) vyvolá se před vyvoláním každé akce kontroleru a zodpovídá za metodu akce ke kontrole [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) a reagují odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="a5956-229">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="a5956-230">Některé aplikace zvolte dodržovat standardní zásady pro nakládání s [ověření modelu](xref:mvc/models/validation) chyby, v takovém případě [filtr](xref:mvc/controllers/filters) může být vhodné místo pro implementaci tuto zásadu.</span><span class="sxs-lookup"><span data-stu-id="a5956-230">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="a5956-231">Měli byste otestovat chování vaše akce se stavy modelu je neplatný.</span><span class="sxs-lookup"><span data-stu-id="a5956-231">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="a5956-232">Další informace naleznete v tématu <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="a5956-232">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5956-233">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a5956-233">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
