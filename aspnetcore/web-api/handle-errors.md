---
title: Zpracování chyb v ASP.NET Core webových rozhraní API
author: pranavkm
description: Přečtěte si o zpracování chyb s ASP.NET Core webovými rozhraními API.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/10/2019
uid: web-api/handle-errors
ms.openlocfilehash: c2dbc47b4495b7187aefbc62eb6d2f0c9683c2da
ms.sourcegitcommit: 29ace642ca0e1f0b48a18d66de266d8811df2b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74987828"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Zpracování chyb v ASP.NET Core webových rozhraní API

Tento článek popisuje, jak zpracovat a přizpůsobit zpracování chyb pomocí ASP.NET Core webových rozhraní API.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Stránka s výjimkou pro vývojáře

[Stránka s výjimkou vývojáře](xref:fundamentals/error-handling) je užitečný nástroj k získání podrobných trasování zásobníku pro chyby serveru. Používá <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> k zachycení synchronních a asynchronních výjimek z kanálu HTTP a k vygenerování odpovědí na chyby. K ilustraci zvažte následující akci kontroleru:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Spusťte následující příkaz `curl`, který otestuje předchozí akci:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core 3,0 a novějších se na stránce s výjimkou vývojáře zobrazuje odpověď v podobě prostého textu, pokud klient nepožaduje výstup ve formátu HTML. Zobrazí se výstup:

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

Chcete-li místo toho zobrazit odpověď ve formátu HTML, nastavte `Accept` hlavičce požadavku HTTP na typ média `text/html`. Příklad:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

Vezměte v úvahu následující úryvek z odpovědi HTTP:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

V ASP.NET Core 2,2 a starších verzích se na stránce s výjimkou vývojáře zobrazuje odpověď ve formátu HTML. Zvažte například následující výpis z odpovědi HTTP:

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

Odpověď ve formátu HTML se bude užitečná při testování prostřednictvím nástrojů, jako je post. Následující snímek obrazovky ukazuje jako text odpovědi v podobě prostého textu i formátu HTML v poli post:

![Testování stránky s výjimkou vývojářů v předzálohovacím](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**. Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.

## <a name="exception-handler"></a>Obslužná rutina výjimky

V nevývojovém prostředí lze [middleware zpracování výjimek](xref:fundamentals/error-handling) použít k vytvoření datové části chyby:

1. V `Startup.Configure`volejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> pro použití middlewaru:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Nakonfigurujte akci kontroleru tak, aby odpovídala trase `/error`:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Předchozí akce `Error` odešle klientovi datovou část kompatibilní se [specifikací RFC 7807](https://tools.ietf.org/html/rfc7807).

Middleware zpracování výjimek může také poskytnout podrobnější výstup vyjednaný z hlediska obsahu v místním vývojovém prostředí. Pomocí následujících kroků můžete vytvořit konzistentní formát datové části napříč vývojovým a produkčním prostředím:

1. V `Startup.Configure`Zaregistrujte instance middlewaru specifické pro prostředí pro zpracování výjimky:

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

    V předchozím kódu je middleware zaregistrován pomocí:

    * Trasa `/error-local-development` ve vývojovém prostředí.
    * Trasa `/error` v prostředích, která nejsou vývojová.
    
1. Použít směrování atributů na akce kontroleru:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>Použití výjimek pro úpravu odpovědi

Obsah odpovědi se dá změnit mimo kontroler. V rozhraní Web API ASP.NET 4. x jeden způsob, jak to provést, byl použit typ <xref:System.Web.Http.HttpResponseException>. ASP.NET Core neobsahuje ekvivalentní typ. Podporu pro `HttpResponseException` lze přidat pomocí následujících kroků:

1. Vytvořte známý typ výjimky s názvem `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Vytvořte filtr akcí s názvem `HttpResponseExceptionFilter`:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. V `Startup.ConfigureServices`přidejte filtr akcí do kolekce Filters:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Odezva na chybu při ověřování

V případě řadičů webového rozhraní API na webu MVC odpoví <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> typ odpovědi, když se ověřování modelu nezdařilo. MVC používá výsledky <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> k vytvoření chybové odpovědi pro chybu ověřování. Následující příklad používá objekt pro vytváření a změnu výchozího typu odpovědi na <xref:Microsoft.AspNetCore.Mvc.SerializableError> v `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Odezva na chybu klienta

*Výsledek chyby* je definován jako výsledek se stavovým kódem HTTP 400 nebo vyšší. Pro řadiče webového rozhraní API MVC transformuje výsledek z důvodu <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2,1 generuje odpověď s podrobnostmi o problému, který je téměř kompatibilní se specifikací RFC 7807. Pokud je důležité dodržování 100 procent, upgradujte projekt na ASP.NET Core 2,2 nebo novější.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Odpověď na chybu lze nakonfigurovat jedním z následujících způsobů:

1. [Implementovat ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Použití ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Implementovat ProblemDetailsFactory

MVC používá `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` k tvorbě všech instancí <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. To zahrnuje odpovědi na chyby klienta, odpovědi na chyby při selhání ověřování a pomocné metody `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem>.

Chcete-li přizpůsobit odpověď na podrobnosti o problému, zaregistrujte vlastní implementaci `ProblemDetailsFactory` v `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Odpověď na chybu lze nakonfigurovat tak, jak je uvedeno v části [Use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) .

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Použití ApiBehaviorOptions. ClientErrorMapping

K nakonfigurování obsahu odpovědi `ProblemDetails` použijte vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>. Například následující kód v `Startup.ConfigureServices` aktualizuje vlastnost `type` pro odpovědi 404:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
