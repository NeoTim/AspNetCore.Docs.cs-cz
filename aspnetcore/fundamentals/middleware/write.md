---
title: Zápis vlastního middlewaru ASP.NET Core
author: rick-anderson
description: Informace o psaní vlastního middlewaru ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901683"
---
# <a name="write-custom-aspnet-core-middleware"></a>Zápis vlastního middlewaru ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je sestaven do kanálu služby aplikace pro zpracování požadavků a odpovědí. ASP.NET Core nabízí bohaté nastavit integrované middlewarových komponent, ale v některých případech můžete chtít napsat vlastního middlewaru.

## <a name="middleware-class"></a>Třída middlewaru

Middleware je obecně zapouzdřené v třídě a vystavený s metodou rozšíření. Vezměte v úvahu následující middlewaru, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

Předchozí kód ukázkové slouží k předvedení vytváření komponenta middlewaru. ASP.NET Core lokalizace integrovanou podporu najdete na webu <xref:fundamentals/localization>.

Middleware můžete otestovat předáním jazykovou verzi. Například, `http://localhost:7997/?culture=no`.

Následující kód přesune delegáta middleware pro třídu:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

Middleware `Task` název metody musí být `Invoke`. V technologii ASP.NET Core 2.0 nebo novější, název může být buď `Invoke` nebo `InvokeAsync`.

::: moniker-end

## <a name="middleware-extension-method"></a>Metoda rozšíření middlewaru

Poskytuje následující metody rozšíření middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

Následující kód volá middleware z `Startup.Configure`:

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

Postupujte podle middleware [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) zveřejněním závislých 've svém konstruktoru. Middleware je vytvořen jednou za *dobu životnosti aplikace*. Najdete v článku [závislosti na požadavku](#per-request-dependencies) části, pokud je potřeba sdílet s middlewaru v rámci žádost o služby.

Middlewarových komponent lze vyřešit jejich závislosti z [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) prostřednictvím parametry konstruktoru. [UseMiddleware&lt;T&gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) můžete také přijmout přímo další parametry.

## <a name="per-request-dependencies"></a>Závislosti na základě žádosti

Protože middlewaru je vytvořen při spuštění aplikace, nikoli jednotlivých žádostí, *obor* služby životního cyklu používat middleware konstruktory nejsou sdíleny s jinými typy vložený závislostí při každém požadavku. Pokud musíte sdílet *obor* služby mezi middlewaru a ostatními typy, přidejte tyto služby `Invoke` podpis metody. `Invoke` Metoda může přijímat další parametry, které se vyplní podle DI:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
