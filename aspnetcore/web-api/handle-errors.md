---
title: Zpracování chyb v ASP.NET Core webových rozhraní API
author: pranavkm
description: Přečtěte si o zpracování chyb s ASP.NET Core webovými rozhraními API.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/handle-errors
ms.openlocfilehash: 7c641fb12e0d06ebd7bb3ce9f878f0469b4a3d8e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775060"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="f58f1-103">Zpracování chyb v ASP.NET Core webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="f58f1-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="f58f1-104">Tento článek popisuje, jak zpracovat a přizpůsobit zpracování chyb pomocí ASP.NET Core webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="f58f1-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="f58f1-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f58f1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="f58f1-106">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="f58f1-106">Developer Exception Page</span></span>

<span data-ttu-id="f58f1-107">[Stránka s výjimkou vývojáře](xref:fundamentals/error-handling) je užitečný nástroj k získání podrobných trasování zásobníku pro chyby serveru.</span><span class="sxs-lookup"><span data-stu-id="f58f1-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="f58f1-108">Používá <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> se k zachycení synchronních a asynchronních výjimek z kanálu HTTP a k vygenerování odpovědí na chyby.</span><span class="sxs-lookup"><span data-stu-id="f58f1-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="f58f1-109">K ilustraci zvažte následující akci kontroleru:</span><span class="sxs-lookup"><span data-stu-id="f58f1-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="f58f1-110">Spusťte následující `curl` příkaz, který otestuje předchozí akci:</span><span class="sxs-lookup"><span data-stu-id="f58f1-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f58f1-111">V ASP.NET Core 3,0 a novějších se na stránce s výjimkou vývojáře zobrazuje odpověď v podobě prostého textu, pokud klient nepožaduje výstup ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="f58f1-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="f58f1-112">Zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="f58f1-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="f58f1-113">Chcete-li místo toho zobrazit odpověď ve formátu HTML, `Accept` nastavte hlavičku požadavku HTTP na `text/html` typ média.</span><span class="sxs-lookup"><span data-stu-id="f58f1-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="f58f1-114">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f58f1-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="f58f1-115">Vezměte v úvahu následující úryvek z odpovědi HTTP:</span><span class="sxs-lookup"><span data-stu-id="f58f1-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f58f1-116">V ASP.NET Core 2,2 a starších verzích se na stránce s výjimkou vývojáře zobrazuje odpověď ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="f58f1-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="f58f1-117">Zvažte například následující výpis z odpovědi HTTP:</span><span class="sxs-lookup"><span data-stu-id="f58f1-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f58f1-118">Odpověď ve formátu HTML se bude užitečná při testování prostřednictvím nástrojů, jako je post.</span><span class="sxs-lookup"><span data-stu-id="f58f1-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="f58f1-119">Následující snímek obrazovky ukazuje jako text odpovědi v podobě prostého textu i formátu HTML v poli post:</span><span class="sxs-lookup"><span data-stu-id="f58f1-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Testování stránky s výjimkou vývojářů v předzálohovacím](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="f58f1-121">Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="f58f1-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="f58f1-122">Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f58f1-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="f58f1-123">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f58f1-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="f58f1-124">Obslužná rutina výjimky</span><span class="sxs-lookup"><span data-stu-id="f58f1-124">Exception handler</span></span>

<span data-ttu-id="f58f1-125">V nevývojovém prostředí lze [middleware zpracování výjimek](xref:fundamentals/error-handling) použít k vytvoření datové části chyby:</span><span class="sxs-lookup"><span data-stu-id="f58f1-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="f58f1-126">V `Startup.Configure`aplikaci volejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> pro použití middlewaru:</span><span class="sxs-lookup"><span data-stu-id="f58f1-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="f58f1-127">Nakonfigurujte akci kontroleru, aby odpovídala `/error` cestě:</span><span class="sxs-lookup"><span data-stu-id="f58f1-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="f58f1-128">Předchozí `Error` akce odešle klientovi datovou část kompatibilní se [specifikací RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="f58f1-128">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="f58f1-129">Middleware zpracování výjimek může také poskytnout podrobnější výstup vyjednaný z hlediska obsahu v místním vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f58f1-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="f58f1-130">Pomocí následujících kroků můžete vytvořit konzistentní formát datové části napříč vývojovým a produkčním prostředím:</span><span class="sxs-lookup"><span data-stu-id="f58f1-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="f58f1-131">V `Startup.Configure`nástroji Zaregistrujte instance middlewaru specifické pro prostředí:</span><span class="sxs-lookup"><span data-stu-id="f58f1-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="f58f1-132">V předchozím kódu je middleware zaregistrován pomocí:</span><span class="sxs-lookup"><span data-stu-id="f58f1-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="f58f1-133">Trasa `/error-local-development` ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f58f1-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="f58f1-134">Trasa `/error` v prostředích, která nejsou vývojová.</span><span class="sxs-lookup"><span data-stu-id="f58f1-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="f58f1-135">Použít směrování atributů na akce kontroleru:</span><span class="sxs-lookup"><span data-stu-id="f58f1-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="f58f1-136">Použití výjimek pro úpravu odpovědi</span><span class="sxs-lookup"><span data-stu-id="f58f1-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="f58f1-137">Obsah odpovědi se dá změnit mimo kontroler.</span><span class="sxs-lookup"><span data-stu-id="f58f1-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="f58f1-138">V rozhraní Web API ASP.NET 4. x jeden ze způsobů, jak to provést, <xref:System.Web.Http.HttpResponseException> byl typ použit.</span><span class="sxs-lookup"><span data-stu-id="f58f1-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="f58f1-139">ASP.NET Core neobsahuje ekvivalentní typ.</span><span class="sxs-lookup"><span data-stu-id="f58f1-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="f58f1-140">Podporu pro `HttpResponseException` je možné přidat pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="f58f1-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="f58f1-141">Vytvořte známý typ výjimky s názvem `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="f58f1-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="f58f1-142">Vytvořte filtr akcí s názvem `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="f58f1-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="f58f1-143">Do `Startup.ConfigureServices`přidejte filtr akcí do kolekce Filters:</span><span class="sxs-lookup"><span data-stu-id="f58f1-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="f58f1-144">Odezva na chybu při ověřování</span><span class="sxs-lookup"><span data-stu-id="f58f1-144">Validation failure error response</span></span>

<span data-ttu-id="f58f1-145">V případě řadičů webového rozhraní API aplikace MVC odpoví <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> s typem odpovědi, když se ověřování modelu nepovede.</span><span class="sxs-lookup"><span data-stu-id="f58f1-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="f58f1-146">MVC používá výsledky <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> pro sestavení chybové odpovědi pro chybu ověřování.</span><span class="sxs-lookup"><span data-stu-id="f58f1-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="f58f1-147">Následující příklad používá továrnu ke změně výchozího typu odpovědi na <xref:Microsoft.AspNetCore.Mvc.SerializableError> `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f58f1-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="f58f1-148">Odezva na chybu klienta</span><span class="sxs-lookup"><span data-stu-id="f58f1-148">Client error response</span></span>

<span data-ttu-id="f58f1-149">*Výsledek chyby* je definován jako výsledek se stavovým kódem HTTP 400 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="f58f1-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="f58f1-150">Pro řadiče webového rozhraní API MVC transformuje výsledek chyby s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>výsledkem.</span><span class="sxs-lookup"><span data-stu-id="f58f1-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="f58f1-151">ASP.NET Core 2,1 generuje odpověď s podrobnostmi o problému, který je téměř kompatibilní se specifikací RFC 7807.</span><span class="sxs-lookup"><span data-stu-id="f58f1-151">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="f58f1-152">Pokud je důležité dodržování 100 procent, upgradujte projekt na ASP.NET Core 2,2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="f58f1-152">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f58f1-153">Odpověď na chybu lze nakonfigurovat jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="f58f1-153">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="f58f1-154">Implementovat ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="f58f1-154">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="f58f1-155">Použití ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="f58f1-155">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="f58f1-156">Implementovat ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="f58f1-156">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="f58f1-157">MVC používá `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` k výrobě všech instancí <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> a. <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails></span><span class="sxs-lookup"><span data-stu-id="f58f1-157">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="f58f1-158">To zahrnuje odpovědi na chyby klienta, odpovědi na chyby při ověřování a `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` pomocné <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> metody a.</span><span class="sxs-lookup"><span data-stu-id="f58f1-158">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="f58f1-159">Chcete-li přizpůsobit odpověď na podrobnosti o problému, zaregistrujte vlastní implementaci nástroje `ProblemDetailsFactory` v `Startup.ConfigureServices`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f58f1-159">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f58f1-160">Odpověď na chybu lze nakonfigurovat tak, jak je uvedeno v části [Use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="f58f1-160">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="f58f1-161">Použití ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="f58f1-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="f58f1-162">Pomocí <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> vlastnosti nakonfigurujte obsah `ProblemDetails` odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f58f1-162">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="f58f1-163">Například následující kód v `Startup.ConfigureServices` nástroji aktualizuje `type` vlastnost pro odpovědi 404:</span><span class="sxs-lookup"><span data-stu-id="f58f1-163">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
