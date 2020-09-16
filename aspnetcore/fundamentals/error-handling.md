---
title: Zpracování chyb v ASP.NET Core
author: rick-anderson
description: Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: da7f50b27e447b86bd8a06851b767488d51b7050
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592888"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="4dfd9-103">Zpracování chyb v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4dfd9-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4dfd9-104">[Kirka Larkin](https://twitter.com/serpent5), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4dfd9-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4dfd9-105">Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="4dfd9-106">Podívejte se <xref:web-api/handle-errors> na webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="4dfd9-107">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="4dfd9-108">([Stažení](xref:index#how-to-download-a-sample).) Karta síť v nástrojích pro vývojáře v prohlížeči F12 je užitečná při testování ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="4dfd9-109">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="4dfd9-109">Developer Exception Page</span></span>

<span data-ttu-id="4dfd9-110">*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="4dfd9-111">Šablony ASP.NET Core generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="4dfd9-112">Předchozí zvýrazněný kód povoluje stránku s výjimkou vývojářů, když aplikace běží ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4dfd9-113">Šablony jsou na <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> začátku v kanálu middlewaru, aby mohl zachytit výjimky vyvolané v middlewaru, který následuje.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="4dfd9-114">Předchozí kód povoluje stránku výjimky vývojářů ***pouze*** v případě, že aplikace běží ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-114">The preceding code enables the Developer Exception Page ***only*** when the app runs in the Development environment.</span></span> <span data-ttu-id="4dfd9-115">Podrobné informace o výjimce by se neměly veřejně zobrazovat, pokud je aplikace spuštěná v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="4dfd9-116">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4dfd9-117">Stránka s výjimkou vývojářů obsahuje následující informace o výjimce a žádosti:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="4dfd9-118">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="4dfd9-118">Stack trace</span></span>
* <span data-ttu-id="4dfd9-119">Parametry řetězce dotazu, pokud existují</span><span class="sxs-lookup"><span data-stu-id="4dfd9-119">Query string parameters if any</span></span>
* <span data-ttu-id="4dfd9-120">Cookies if</span><span class="sxs-lookup"><span data-stu-id="4dfd9-120">Cookies if any</span></span>
* <span data-ttu-id="4dfd9-121">Hlavičky</span><span class="sxs-lookup"><span data-stu-id="4dfd9-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="4dfd9-122">Stránka obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-122">Exception handler page</span></span>

<span data-ttu-id="4dfd9-123">Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro [produkční prostředí](xref:fundamentals/environments), zavolejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="4dfd9-124">Tato výjimka zpracovává middleware:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-124">This exception handling middleware:</span></span>

* <span data-ttu-id="4dfd9-125">Zachycuje a zaznamenává výjimky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="4dfd9-126">Znovu spustí žádost v alternativním kanálu pomocí uvedené cesty.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="4dfd9-127">Požadavek se znovu nespustí, pokud byla odpověď spuštěná.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="4dfd9-128">Kód vygenerovaný šablonou znovu spustí požadavek pomocí `/Error` cesty.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="4dfd9-129">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> přidá middleware pro zpracování výjimek v Nevývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="4dfd9-130">RazorŠablona aplikace stránky poskytuje chybovou stránku (*. cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( `ErrorModel` ) ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="4dfd9-131">V případě aplikace MVC obsahuje šablona projektu `Error` metodu akce a zobrazení chyb pro domovský kontroler.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="4dfd9-132">Neoznačovat metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="4dfd9-133">Explicitní příkazy zabraňují, aby některé žádosti dosáhly metody Action.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="4dfd9-134">Povolí anonymní přístup k metodě, pokud by se měli neověření uživatelé zobrazit v zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="4dfd9-135">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="4dfd9-135">Access the exception</span></span>

<span data-ttu-id="4dfd9-136">Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě požadavku v obslužné rutině chyby.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="4dfd9-137">Následující kód přidá `ExceptionMessage` na výchozí *stránky/Error. cshtml. cs* vygenerované šablonami ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="4dfd9-138">Neslouží **klientům** informace o citlivých chybách.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="4dfd9-139">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="4dfd9-140">Chcete-li otestovat výjimku v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="4dfd9-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="4dfd9-141">Nastavte prostředí na produkční.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-141">Set the environment to production.</span></span>
* <span data-ttu-id="4dfd9-142">Odebere komentáře z `webBuilder.UseStartup<Startup>();` *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="4dfd9-143">Vyberte **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="4dfd9-144">Lambda obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-144">Exception handler lambda</span></span>

<span data-ttu-id="4dfd9-145">Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="4dfd9-146">Použití lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="4dfd9-147">Následující kód používá lambda pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="4dfd9-148">**Neobsluhujte** citlivé informace o chybách z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> klientů ani do nich <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="4dfd9-149">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="4dfd9-150">Testování výrazu lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="4dfd9-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="4dfd9-151">Nastavte prostředí na produkční.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-151">Set the environment to production.</span></span>
* <span data-ttu-id="4dfd9-152">Odebere komentáře z `webBuilder.UseStartup<StartupLambda>();` *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="4dfd9-153">Vyberte **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="4dfd9-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="4dfd9-154">UseStatusCodePages</span></span>

<span data-ttu-id="4dfd9-155">Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy chyb HTTP, například *404 – Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="4dfd9-156">Když aplikace nalezne chybový stav HTTP 400-499, který nemá tělo, vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="4dfd9-157">Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="4dfd9-158">Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových kódů, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> v `Startup.Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="4dfd9-159">Zavolejte `UseStatusCodePages` před middlewarem zpracování žádosti.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="4dfd9-160">Například volejte `UseStatusCodePages` před middleware statických souborů a middleware koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="4dfd9-161">Pokud se `UseStatusCodePages` nepoužívá, navigace na adresu URL bez koncového bodu vrátí chybovou zprávu závislou na prohlížeči, která indikuje, že koncový bod nejde najít.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="4dfd9-162">Přejděte například na `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="4dfd9-163">Při `UseStatusCodePages` volání funkce prohlížeč vrátí:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="4dfd9-164">`UseStatusCodePages` se obvykle nepoužívá v produkčním prostředí, protože vrací zprávu, která není užitečná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="4dfd9-165">Testování `UseStatusCodePages` v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span><span class="sxs-lookup"><span data-stu-id="4dfd9-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="4dfd9-166">Nastavte prostředí na produkční.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-166">Set the environment to production.</span></span>
* <span data-ttu-id="4dfd9-167">Odebere komentáře z `webBuilder.UseStartup<StartupUseStatusCodePages>();` *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="4dfd9-168">Vyberte odkazy na domovské stránce na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="4dfd9-169">UseStatusCodePages s formátovacím řetězcem</span><span class="sxs-lookup"><span data-stu-id="4dfd9-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="4dfd9-170">Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá typ obsahu a řetězec formátu:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="4dfd9-171">V předchozím kódu `{0}` je zástupný symbol pro kód chyby.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="4dfd9-172">`UseStatusCodePages` řetězec formátu se obvykle nepoužívá v produkčním prostředí, protože vrací zprávu, která není užitečná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="4dfd9-173">Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupFormat>();` v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="4dfd9-174">UseStatusCodePages s výrazem lambda</span><span class="sxs-lookup"><span data-stu-id="4dfd9-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="4dfd9-175">Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="4dfd9-176">`UseStatusCodePages` výraz lambda se obvykle nepoužívá v produkčním prostředí, protože vrací zprávu, která není užitečná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="4dfd9-177">Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupStatusLambda>();` v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="4dfd9-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="4dfd9-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="4dfd9-179"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="4dfd9-180">Odešle klientovi stavový kód [302](https://developer.mozilla.org/docs/Web/HTTP/Status/302) .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="4dfd9-181">Přesměruje klienta na koncový bod zpracování chyb uvedený v šabloně URL.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="4dfd9-182">Koncový bod pro zpracování chyb obvykle zobrazí informace o chybě a vrátí HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="4dfd9-183">Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v předchozím kódu.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="4dfd9-184">Pokud šablona URL začíná na `~` (vlnovku), `~` je nahrazena aplikací `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="4dfd9-185">Při zadávání koncového bodu v aplikaci vytvořte zobrazení MVC nebo Razor stránku pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-185">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="4dfd9-186">RazorPříklad stránky naleznete v tématu [Pages/MyStatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-186">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="4dfd9-187">Tato metoda se běžně používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="4dfd9-188">By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="4dfd9-189">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="4dfd9-190">Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="4dfd9-191">Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupSCredirect>();` v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="4dfd9-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="4dfd9-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="4dfd9-193"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="4dfd9-194">Vrátí původní stavový kód klientovi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="4dfd9-195">Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="4dfd9-196">Pokud je v aplikaci zadaný koncový bod, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-196">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="4dfd9-197">Zajistěte, aby bylo `UseStatusCodePagesWithReExecute` `UseRouting` možné žádost přesměrovat na stavovou stránku.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="4dfd9-198">RazorPříklad stránky naleznete v tématu [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-198">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="4dfd9-199">Tato metoda se běžně používá v případě, kdy by měla aplikace:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="4dfd9-200">Zpracuje požadavek bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="4dfd9-201">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="4dfd9-202">Zachovejte a vraťte původní stavový kód s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="4dfd9-203">Šablony URL a řetězce dotazů můžou obsahovat zástupný symbol `{0}` pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="4dfd9-204">Šablona adresy URL musí začínat na `/` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="4dfd9-205">Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="4dfd9-206">RazorPříklad stránky naleznete v tématu [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-206">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="4dfd9-207">Chcete-li otestovat `UseStatusCodePages` [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), odstraňte komentáře z `webBuilder.UseStartup<StartupSCreX>();` v *program.cs*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="4dfd9-208">Zakázat stavové stránky</span><span class="sxs-lookup"><span data-stu-id="4dfd9-208">Disable status code pages</span></span>

<span data-ttu-id="4dfd9-209">Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte atribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="4dfd9-210">Chcete-li zakázat stránky stavového kódu pro konkrétní požadavky v Razor metodě obslužné rutiny stránky nebo v KONTROLERU MVC, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="4dfd9-210">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="4dfd9-211">Kód pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-211">Exception-handling code</span></span>

<span data-ttu-id="4dfd9-212">Kód na stránkách zpracování výjimek může také vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="4dfd9-213">Produkční chybové stránky by se měly důkladně testovat a měly by se věnovat zvýšené péči, aby nedocházelo k vygenerování výjimek jejich vlastních.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="4dfd9-214">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="4dfd9-214">Response headers</span></span>

<span data-ttu-id="4dfd9-215">Po odeslání hlaviček pro odpověď:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="4dfd9-216">Aplikace nemůže změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="4dfd9-217">Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="4dfd9-218">Odpověď musí být dokončena nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="4dfd9-219">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="4dfd9-219">Server exception handling</span></span>

<span data-ttu-id="4dfd9-220">Kromě logiky zpracování výjimek v aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat určité výjimky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="4dfd9-221">Pokud server před odesláním hlaviček odpovědi zachytí výjimku, server odešle `500 - Internal Server Error` odpověď bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="4dfd9-222">Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="4dfd9-223">Požadavky, které nejsou zpracovávány aplikací, jsou zpracovávány serverem.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="4dfd9-224">Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="4dfd9-225">Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="4dfd9-226">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="4dfd9-226">Startup exception handling</span></span>

<span data-ttu-id="4dfd9-227">Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="4dfd9-228">Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="4dfd9-229">Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="4dfd9-230">Pokud vazba neproběhne úspěšně:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-230">If binding fails:</span></span>

* <span data-ttu-id="4dfd9-231">Vrstva hostování zaznamená kritickou výjimku.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="4dfd9-232">Proces dotnet selže.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="4dfd9-233">Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4dfd9-234">Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="4dfd9-235">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="4dfd9-236">Chybová stránka databáze</span><span class="sxs-lookup"><span data-stu-id="4dfd9-236">Database error page</span></span>

<span data-ttu-id="4dfd9-237">Filtr výjimek stránky pro vývojáře databáze `AddDatabaseDeveloperPageExceptionFilter` zachycuje výjimky týkající se databáze, které lze vyřešit pomocí Entity Framework Core migrace.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="4dfd9-238">Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích pro vyřešení problému.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="4dfd9-239">Tato stránka je povolena pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="4dfd9-240">Následující kód byl vygenerován Razor šablonami ASP.NET Core stránky, pokud byly zadány jednotlivé uživatelské účty:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-240">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="4dfd9-241">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-241">Exception filters</span></span>

<span data-ttu-id="4dfd9-242">V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="4dfd9-243">V Razor aplikacích stránky je lze nakonfigurovat globálně nebo podle modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-243">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="4dfd9-244">Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="4dfd9-245">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="4dfd9-246">Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako vestavěné [middleware pro zpracování výjimek](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="4dfd9-247">Doporučujeme použít `UseExceptionHandler` , pokud nepotřebujete provádět zpracování chyb odlišně v závislosti na tom, která akce MVC je vybrána.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="4dfd9-248">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="4dfd9-248">Model state errors</span></span>

<span data-ttu-id="4dfd9-249">Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4dfd9-250">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4dfd9-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="4dfd9-251">Tím, že  [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4dfd9-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4dfd9-252">Tento článek se věnuje běžným přístupům ke zpracování chyb v ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="4dfd9-253">Podívejte se <xref:web-api/handle-errors> na webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="4dfd9-254">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="4dfd9-255">([Stažení](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="4dfd9-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="4dfd9-256">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="4dfd9-256">Developer Exception Page</span></span>

<span data-ttu-id="4dfd9-257">*Stránka s výjimkou vývojáře* zobrazuje podrobné informace o výjimkách žádostí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="4dfd9-258">Šablony ASP.NET Core generují následující kód:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="4dfd9-259">Předchozí kód povoluje stránku s výjimkou vývojářů, když aplikace běží ve [vývojovém prostředí](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4dfd9-260">Šablony jsou umístěné <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> před jakýmkoli middlewarem, takže výjimky jsou zachyceny v middlewaru, který následuje.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="4dfd9-261">Předchozí kód povoluje stránku výjimky vývojářů **pouze v případě, že aplikace běží ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="4dfd9-262">Podrobné informace o výjimce by se neměly veřejně zobrazovat, pokud je aplikace spuštěná v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="4dfd9-263">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4dfd9-264">Stránka s výjimkou vývojářů obsahuje následující informace o výjimce a žádosti:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="4dfd9-265">Trasování zásobníku</span><span class="sxs-lookup"><span data-stu-id="4dfd9-265">Stack trace</span></span>
* <span data-ttu-id="4dfd9-266">Parametry řetězce dotazu, pokud existují</span><span class="sxs-lookup"><span data-stu-id="4dfd9-266">Query string parameters if any</span></span>
* <span data-ttu-id="4dfd9-267">Cookies if</span><span class="sxs-lookup"><span data-stu-id="4dfd9-267">Cookies if any</span></span>
* <span data-ttu-id="4dfd9-268">Hlavičky</span><span class="sxs-lookup"><span data-stu-id="4dfd9-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="4dfd9-269">Stránka obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-269">Exception handler page</span></span>

<span data-ttu-id="4dfd9-270">Chcete-li nakonfigurovat vlastní stránku zpracování chyb pro produkční prostředí, použijte middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="4dfd9-271">Middleware:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-271">The middleware:</span></span>

* <span data-ttu-id="4dfd9-272">Zachycuje a zaznamenává výjimky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="4dfd9-273">Znovu spustí požadavek v alternativním kanálu pro uvedenou stránku nebo kontroler.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="4dfd9-274">Požadavek se znovu nespustí, pokud byla odpověď spuštěná.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="4dfd9-275">Kód vygenerovaný šablonou znovu spustí požadavek na `/Error` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="4dfd9-276">V následujícím příkladu <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> přidá middleware pro zpracování výjimek v Nevývojovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="4dfd9-277">RazorŠablona aplikace stránky poskytuje chybovou stránku (*. cshtml*) a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> třídu ( `ErrorModel` ) ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-277">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="4dfd9-278">V případě aplikace MVC obsahuje šablona projektu metodu chybové akce a zobrazení chyby v domovském řadiči.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="4dfd9-279">Neoznačovat metodu akce obslužné rutiny chyb pomocí atributů metody HTTP, jako je například `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="4dfd9-280">Explicitní příkazy brání, aby některé žádosti dosáhly metody.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="4dfd9-281">Povolí anonymní přístup k metodě, pokud by se měli neověření uživatelé zobrazit v zobrazení chyby.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="4dfd9-282">Přístup k výjimce</span><span class="sxs-lookup"><span data-stu-id="4dfd9-282">Access the exception</span></span>

<span data-ttu-id="4dfd9-283">Použijte <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pro přístup k výjimce a původní cestě požadavku v řadiči obslužné rutiny chyb nebo na stránce:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="4dfd9-284">Neslouží **klientům** informace o citlivých chybách.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="4dfd9-285">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="4dfd9-286">Chcete-li aktivovat předchozí stránku zpracování výjimek, nastavte prostředí na produkci a vynuťte výjimku.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="4dfd9-287">Lambda obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-287">Exception handler lambda</span></span>

<span data-ttu-id="4dfd9-288">Alternativou ke [stránce vlastní obslužné rutiny výjimek](#exception-handler-page) je poskytnout lambda výraz <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="4dfd9-289">Použití lambda umožňuje přístup k chybě před vrácením odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="4dfd9-290">Zde je příklad použití výrazu lambda pro zpracování výjimek:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="4dfd9-291">V předchozím kódu `await context.Response.WriteAsync(new string(' ', 512));` je přidán prohlížeč Internet Explorer, aby se zobrazila chybová zpráva, nikoli chybová zpráva IE.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="4dfd9-292">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="4dfd9-293">**Neobsluhujte** citlivé informace o chybách z <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> klientů ani do nich <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="4dfd9-294">Obsluha chyb je bezpečnostní riziko.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="4dfd9-295">Chcete-li zobrazit výsledek lambda zpracování výjimek v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), použijte `ProdEnvironment` `ErrorHandlerLambda` direktivy preprocesoru a a vyberte možnost **aktivovat výjimku** na domovské stránce.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="4dfd9-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="4dfd9-296">UseStatusCodePages</span></span>

<span data-ttu-id="4dfd9-297">Ve výchozím nastavení aplikace ASP.NET Core neposkytuje stavovou stránku pro stavové kódy HTTP, například *404-Nenalezeno*.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="4dfd9-298">Aplikace vrátí stavový kód a prázdné tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="4dfd9-299">Chcete-li poskytnout stránky se stavovým kódem, použijte middleware stránky stavového kódu.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="4dfd9-300">Middleware zpřístupňuje balíček [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="4dfd9-301">Chcete-li povolit výchozí textové obslužné rutiny pro běžné chyby stavových kódů, zavolejte <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> v `Startup.Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="4dfd9-302">Volejte `UseStatusCodePages` před middlewarem zpracování požadavků (například middleware pro statický soubor a middleware Mvc).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="4dfd9-303">Pokud se `UseStatusCodePages` nepoužívá, navigace na adresu URL bez koncového bodu vrátí chybovou zprávu závislou na prohlížeči, která indikuje, že koncový bod nejde najít.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="4dfd9-304">Přejděte například na `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="4dfd9-305">Při `UseStatusCodePages` volání funkce prohlížeč vrátí:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="4dfd9-306">UseStatusCodePages s formátovacím řetězcem</span><span class="sxs-lookup"><span data-stu-id="4dfd9-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="4dfd9-307">Chcete-li přizpůsobit typ obsahu odpovědi a text, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá typ obsahu a řetězec formátu:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="4dfd9-308">UseStatusCodePages s výrazem lambda</span><span class="sxs-lookup"><span data-stu-id="4dfd9-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="4dfd9-309">Chcete-li určit vlastní kód pro zpracování chyb a psaní odezvy, použijte přetížení <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , které přijímá výraz lambda:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="4dfd9-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="4dfd9-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="4dfd9-311"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="4dfd9-312">Odešle klientovi stavový kód *302* .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="4dfd9-313">Přesměruje klienta na umístění zadané v šabloně adresy URL.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="4dfd9-314">Šablona adresy URL může obsahovat `{0}` zástupný symbol pro stavový kód, jak je znázorněno v příkladu.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="4dfd9-315">Pokud šablona URL začíná na `~` (vlnovku), `~` je nahrazena aplikací `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="4dfd9-316">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-316">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="4dfd9-317">RazorPříklad stránky naleznete v tématu *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-317">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="4dfd9-318">Tato metoda se běžně používá, když aplikace:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="4dfd9-319">By měl přesměrování klienta na jiný koncový bod, obvykle v případech, kdy se chyba zpracovává v jiné aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="4dfd9-320">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta přesměrovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="4dfd9-321">Neměl by zachovat a vracet původní stavový kód s počáteční odpovědí přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="4dfd9-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="4dfd9-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="4dfd9-323"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="4dfd9-324">Vrátí původní stavový kód klientovi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="4dfd9-325">Vygeneruje tělo odpovědi opakovaným spuštěním kanálu požadavků pomocí alternativní cesty.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="4dfd9-326">Pokud odkazujete na koncový bod v rámci aplikace, vytvořte zobrazení MVC nebo Razor stránku pro koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-326">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="4dfd9-327">Zajistěte, aby bylo `UseStatusCodePagesWithReExecute` `UseRouting` možné žádost přesměrovat na stavovou stránku.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="4dfd9-328">RazorPříklad stránky naleznete v tématu *Pages/StatusCode. cshtml* v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-328">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="4dfd9-329">Tato metoda se běžně používá v případě, kdy by měla aplikace:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="4dfd9-330">Zpracuje požadavek bez přesměrování na jiný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="4dfd9-331">V případě webových aplikací zobrazuje panel Adresa v prohlížeči klienta původní požadovaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="4dfd9-332">Zachovejte a vraťte původní stavový kód s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="4dfd9-333">Šablony URL a řetězce dotazu mohou obsahovat zástupný symbol ( `{0}` ) pro stavový kód.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="4dfd9-334">Šablona adresy URL musí začínat lomítkem ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="4dfd9-335">Při použití zástupného symbolu v cestě potvrďte, že koncový bod (stránka nebo kontroler) může zpracovat segment cesty.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="4dfd9-336">Například Razor Stránka pro chyby by měla přijmout volitelnou hodnotu segmentu cesty s touto `@page` direktivou:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-336">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="4dfd9-337">Koncový bod, který zpracovává chybu, může získat původní adresu URL, která generovala chybu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="4dfd9-338">Zakázat stavové stránky</span><span class="sxs-lookup"><span data-stu-id="4dfd9-338">Disable status code pages</span></span>

<span data-ttu-id="4dfd9-339">Chcete-li zakázat stránky stavového kódu pro řadič MVC nebo metodu akce, použijte [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="4dfd9-340">Chcete-li zakázat stránky stavového kódu pro konkrétní požadavky v Razor metodě obslužné rutiny stránky nebo v KONTROLERU MVC, použijte <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="4dfd9-340">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="4dfd9-341">Kód pro zpracování výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-341">Exception-handling code</span></span>

<span data-ttu-id="4dfd9-342">Kód na stránkách zpracování výjimek může vyvolat výjimky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="4dfd9-343">Pro produkční chybové stránky je často vhodné vytvořit pouze čistě statický obsah.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="4dfd9-344">Hlavičky odpovědi</span><span class="sxs-lookup"><span data-stu-id="4dfd9-344">Response headers</span></span>

<span data-ttu-id="4dfd9-345">Po odeslání hlaviček pro odpověď:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="4dfd9-346">Aplikace nemůže změnit stavový kód odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="4dfd9-347">Jakékoli stránky výjimek nebo obslužné rutiny nelze spustit.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="4dfd9-348">Odpověď musí být dokončena nebo bylo připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="4dfd9-349">Zpracování výjimek serveru</span><span class="sxs-lookup"><span data-stu-id="4dfd9-349">Server exception handling</span></span>

<span data-ttu-id="4dfd9-350">Kromě logiky zpracování výjimek ve vaší aplikaci může [implementace serveru http](xref:fundamentals/servers/index) zpracovat některé výjimky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="4dfd9-351">Pokud server zachytí výjimku před odesláním hlaviček odpovědi, server pošle *500 interní odpověď na chybu serveru* bez těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="4dfd9-352">Pokud server zachytí výjimku po odeslání hlaviček odpovědi, server ukončí připojení.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="4dfd9-353">Požadavky, které nejsou zpracovávány vaší aplikací, jsou zpracovávány serverem aplikace.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="4dfd9-354">Jakákoli výjimka, ke které dojde, když server zpracovává požadavek, je zpracována zpracováním výjimek serveru.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="4dfd9-355">Vlastní chybové stránky aplikace, middleware zpracování výjimek a filtry toto chování neovlivňují.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="4dfd9-356">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="4dfd9-356">Startup exception handling</span></span>

<span data-ttu-id="4dfd9-357">Pouze hostující vrstva může zpracovat výjimky, které probíhají při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="4dfd9-358">Hostitele je možné nakonfigurovat tak, aby [zachytával chyby při spuštění](xref:fundamentals/host/web-host#capture-startup-errors) a [zachytit podrobné chyby](xref:fundamentals/host/web-host#detailed-errors).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="4dfd9-359">Hostující vrstva může zobrazit chybovou stránku pro zachycenou chybu spuštění pouze v případě, že dojde k chybě po vazbě adresy hostitele/portu.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="4dfd9-360">Pokud vazba neproběhne úspěšně:</span><span class="sxs-lookup"><span data-stu-id="4dfd9-360">If binding fails:</span></span>

* <span data-ttu-id="4dfd9-361">Vrstva hostování zaznamená kritickou výjimku.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="4dfd9-362">Proces dotnet selže.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="4dfd9-363">Pokud je server HTTP [Kestrel](xref:fundamentals/servers/kestrel), nezobrazí se žádná chybová stránka.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="4dfd9-364">Při spuštění ve [službě IIS](/iis) (nebo Azure App Service) nebo v [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se v případě, že se proces nespustí 502,5, vrátí [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) *selhání procesu* .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="4dfd9-365">Další informace naleznete v tématu <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="4dfd9-366">Chybová stránka databáze</span><span class="sxs-lookup"><span data-stu-id="4dfd9-366">Database error page</span></span>

<span data-ttu-id="4dfd9-367">Middleware chybové stránky databáze zaznamenává výjimky týkající se databáze, které je možné vyřešit pomocí Entity Framework migrace.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="4dfd9-368">Pokud dojde k těmto výjimkám, je vygenerována odpověď HTML s podrobnostmi o možných akcích k vyřešení problému.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="4dfd9-369">Tato stránka by měla být povolena pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="4dfd9-370">Povolit stránku přidáním kódu do `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="4dfd9-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="4dfd9-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> vyžaduje balíček NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .</span><span class="sxs-lookup"><span data-stu-id="4dfd9-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="4dfd9-372">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="4dfd9-372">Exception filters</span></span>

<span data-ttu-id="4dfd9-373">V aplikacích MVC se filtry výjimek dají nakonfigurovat globálně nebo na základě jednoho řadiče nebo akce.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="4dfd9-374">V Razor aplikacích stránky je lze nakonfigurovat globálně nebo podle modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-374">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="4dfd9-375">Tyto filtry zpracovávají všechny neošetřené výjimky, ke kterým dojde během provádění akce kontroleru nebo jiného filtru.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="4dfd9-376">Další informace naleznete v tématu <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="4dfd9-377">Filtry výjimek jsou užitečné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí MVC, ale nejsou tak flexibilní jako middleware zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="4dfd9-378">Doporučujeme použít middleware.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-378">We recommend using the middleware.</span></span> <span data-ttu-id="4dfd9-379">Filtry používejte pouze v případě, že je třeba provést zpracování chyb odlišně v závislosti na vybrané akci MVC.</span><span class="sxs-lookup"><span data-stu-id="4dfd9-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="4dfd9-380">Chyby stavu modelu</span><span class="sxs-lookup"><span data-stu-id="4dfd9-380">Model state errors</span></span>

<span data-ttu-id="4dfd9-381">Informace o tom, jak zpracovat chyby stavu modelu, najdete v tématu [vázání modelů](xref:mvc/models/model-binding) a [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="4dfd9-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4dfd9-382">Další materiály</span><span class="sxs-lookup"><span data-stu-id="4dfd9-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
