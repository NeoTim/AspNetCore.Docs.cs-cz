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
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Zpracování chyb v ASP.NET Core webových rozhraní API

Tento článek popisuje, jak zpracovat a přizpůsobit zpracování chyb pomocí ASP.NET Core webových rozhraní API.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([Stažení](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Stránka s výjimkou pro vývojáře

[Stránka s výjimkou vývojáře](xref:fundamentals/error-handling) je užitečný nástroj k získání podrobných trasování zásobníku pro chyby serveru.

Stránka s výjimkou vývojářů zobrazuje odpověď v podobě prostého textu, pokud klient nepřijímá výstup ve formátu HTML. Příklad:

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> Stránku s výjimkou vývojářů povolte **jenom v případě, že aplikace běží ve vývojovém prostředí**. Nechcete veřejně sdílet podrobné informace o výjimkách, když aplikace běží v produkčním prostředí. Další informace o konfiguraci prostředí najdete v tématu <xref:fundamentals/environments>.

## <a name="exception-handler"></a>Obslužná rutina výjimky

V nevývojovém prostředí lze [middleware zpracování výjimek](xref:fundamentals/error-handling) použít k vytvoření datové části chyby:

1. V `Startup.Configure`aplikaci volejte <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> pro použití middlewaru:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Nakonfigurujte akci kontroleru, aby odpovídala `/error` cestě:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Předchozí `Error` akce odešle klientovi datovou část kompatibilní s [RFC7807](https://tools.ietf.org/html/rfc7807).

Middleware zpracování výjimek může také poskytnout podrobnější výstup vyjednaný z hlediska obsahu v místním vývojovém prostředí. Pomocí následujících kroků můžete vytvořit konzistentní formát datové části napříč vývojovým a produkčním prostředím:

1. V `Startup.Configure`nástroji Zaregistrujte instance middlewaru specifické pro prostředí:

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

Obsah odpovědi se dá změnit mimo kontroler. V rozhraní Web API ASP.NET 4. x jeden ze způsobů, jak to provést, <xref:System.Web.Http.HttpResponseException> byl typ použit. ASP.NET Core neobsahuje ekvivalentní typ. Podporu pro `HttpResponseException` je možné přidat pomocí následujících kroků:

1. Vytvořte známý typ výjimky s názvem `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Vytvořte filtr akcí s názvem `HttpResponseExceptionFilter`:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. Do `Startup.ConfigureServices`přidejte filtr akcí do kolekce Filters:

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

V případě řadičů webového rozhraní API aplikace MVC odpoví <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> s typem odpovědi, když se ověřování modelu nepovede. MVC používá výsledky <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> pro sestavení chybové odpovědi pro chybu ověřování. Následující příklad používá továrnu ke změně výchozího typu odpovědi na <xref:Microsoft.AspNetCore.Mvc.SerializableError>: `Startup.ConfigureServices`

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

*Výsledek chyby* je definován jako výsledek se stavovým kódem HTTP 400 nebo vyšší. Pro řadiče webového rozhraní API MVC transformuje výsledek chyby s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>výsledkem.

::: moniker range=">= aspnetcore-3.0"

Odpověď na chybu lze nakonfigurovat jedním z následujících způsobů:

1. [Implementovat ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Použití ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Implementovat ProblemDetailsFactory

MVC používá `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` k výrobě všech <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> instancí a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. To zahrnuje odpovědi na chyby klienta, odpovědi na chyby při ověřování a `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` pomocné <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> metody a.

Chcete-li přizpůsobit odpověď na podrobnosti o problému, zaregistrujte `Startup.ConfigureServices`vlastní implementaci `ProblemDetailsFactory` nástroje v nástroji:

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

Pomocí vlastnosti nakonfigurujte obsah `ProblemDetails` odpovědi. <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> Například následující kód v `Startup.ConfigureServices` nástroji `type` aktualizuje vlastnost pro odpovědi 404:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
