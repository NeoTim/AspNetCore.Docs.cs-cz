---
title: Zpracování chyb v ASP.NET Core webových rozhraní API
author: pranavkm
description: Přečtěte si o zpracování chyb s ASP.NET Core webovými rozhraními API.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/25/2019
uid: web-api/handle-errors
ms.openlocfilehash: 9c5dd2f89e7351f386d1f0633c831952dc58e568
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278723"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="9a7ec-103">Zpracování chyb v ASP.NET Core webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="9a7ec-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="9a7ec-104">Tento článek popisuje, jak zpracovat a přizpůsobit zpracování chyb pomocí ASP.NET Core webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="9a7ec-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9a7ec-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="9a7ec-106">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="9a7ec-106">Developer Exception Page</span></span>

<span data-ttu-id="9a7ec-107">[Stránka s výjimkou vývojáře](xref:fundamentals/error-handling) je užitečný nástroj k získání podrobných trasování zásobníku pro chyby serveru.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span>

<span data-ttu-id="9a7ec-108">Stránka s výjimkou vývojářů zobrazuje odpověď v podobě prostého textu, pokud klient nepřijímá výstup ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-108">The Developer Exception Page displays a plain-text response if the client doesn't accept HTML-formatted output.</span></span> <span data-ttu-id="9a7ec-109">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-109">For example:</span></span>

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> <span data-ttu-id="9a7ec-110">Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-110">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="9a7ec-111">Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-111">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="9a7ec-112">Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-112">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="9a7ec-113">Obslužná rutina výjimky</span><span class="sxs-lookup"><span data-stu-id="9a7ec-113">Exception handler</span></span>

<span data-ttu-id="9a7ec-114">V nevývojovém prostředí lze [middleware zpracování výjimek](xref:fundamentals/error-handling) použít k vytvoření datové části chyby:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-114">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="9a7ec-115">V `Startup.Configure`aplikaci volejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> pro použití middlewaru:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-115">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="9a7ec-116">Nakonfigurujte akci kontroleru, aby odpovídala `/error` cestě:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-116">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="9a7ec-117">Předchozí `Error` akce odešle klientovi datovou část kompatibilní s [RFC7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="9a7ec-117">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="9a7ec-118">Middleware zpracování výjimek může také poskytnout podrobnější výstup vyjednaný z hlediska obsahu v místním vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-118">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="9a7ec-119">Pomocí následujících kroků můžete vytvořit konzistentní formát datové části napříč vývojovým a produkčním prostředím:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-119">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="9a7ec-120">V `Startup.Configure`nástroji Zaregistrujte instance middlewaru specifické pro prostředí:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-120">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="9a7ec-121">V předchozím kódu je middleware zaregistrován pomocí:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-121">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="9a7ec-122">Trasa `/error-local-development` ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-122">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="9a7ec-123">Trasa `/error` v prostředích, která nejsou vývojová.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-123">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="9a7ec-124">Použít směrování atributů na akce kontroleru:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-124">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="9a7ec-125">Použití výjimek pro úpravu odpovědi</span><span class="sxs-lookup"><span data-stu-id="9a7ec-125">Use exceptions to modify the response</span></span>

<span data-ttu-id="9a7ec-126">Obsah odpovědi se dá změnit mimo kontroler.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-126">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="9a7ec-127">V rozhraní Web API ASP.NET 4. x jeden ze způsobů, jak to provést, <xref:System.Web.Http.HttpResponseException> byl typ použit.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-127">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="9a7ec-128">ASP.NET Core neobsahuje ekvivalentní typ.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-128">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="9a7ec-129">Podporu pro `HttpResponseException` je možné přidat pomocí následujících kroků:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-129">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="9a7ec-130">Vytvořte známý typ výjimky s názvem `HttpResponseException`:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-130">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="9a7ec-131">Vytvořte filtr akcí s názvem `HttpResponseExceptionFilter`:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-131">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="9a7ec-132">Do `Startup.ConfigureServices`přidejte filtr akcí do kolekce Filters:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-132">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="9a7ec-133">Odezva na chybu při ověřování</span><span class="sxs-lookup"><span data-stu-id="9a7ec-133">Validation failure error response</span></span>

<span data-ttu-id="9a7ec-134">V případě řadičů webového rozhraní API aplikace MVC odpoví <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> s typem odpovědi, když se ověřování modelu nepovede.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-134">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="9a7ec-135">MVC používá výsledky <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> pro sestavení chybové odpovědi pro chybu ověřování.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-135">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="9a7ec-136">Následující příklad používá továrnu ke změně výchozího typu odpovědi na <xref:Microsoft.AspNetCore.Mvc.SerializableError>: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="9a7ec-136">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="9a7ec-137">Odezva na chybu klienta</span><span class="sxs-lookup"><span data-stu-id="9a7ec-137">Client error response</span></span>

<span data-ttu-id="9a7ec-138">*Výsledek chyby* je definován jako výsledek se stavovým kódem HTTP 400 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-138">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="9a7ec-139">Pro řadiče webového rozhraní API MVC transformuje výsledek chyby s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>výsledkem.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-139">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9a7ec-140">Odpověď na chybu lze nakonfigurovat jedním z následujících způsobů:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-140">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="9a7ec-141">Implementovat ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="9a7ec-141">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="9a7ec-142">Použití ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="9a7ec-142">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="9a7ec-143">Implementovat ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="9a7ec-143">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="9a7ec-144">MVC používá `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` k výrobě všech <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> instancí a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-144">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="9a7ec-145">To zahrnuje odpovědi na chyby klienta, odpovědi na chyby při ověřování a `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` pomocné <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> metody a.</span><span class="sxs-lookup"><span data-stu-id="9a7ec-145">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="9a7ec-146">Chcete-li přizpůsobit odpověď na podrobnosti o problému, zaregistrujte `Startup.ConfigureServices`vlastní implementaci `ProblemDetailsFactory` nástroje v nástroji:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-146">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9a7ec-147">Odpověď na chybu lze nakonfigurovat tak, jak je uvedeno v části [Use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .</span><span class="sxs-lookup"><span data-stu-id="9a7ec-147">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="9a7ec-148">Použití ApiBehaviorOptions. ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="9a7ec-148">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="9a7ec-149">Pomocí vlastnosti nakonfigurujte obsah `ProblemDetails` odpovědi. <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*></span><span class="sxs-lookup"><span data-stu-id="9a7ec-149">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="9a7ec-150">Například následující kód v `Startup.ConfigureServices` nástroji `type` aktualizuje vlastnost pro odpovědi 404:</span><span class="sxs-lookup"><span data-stu-id="9a7ec-150">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
