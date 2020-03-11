---
title: Aktivace middlewaru založená na továrně v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s implementací aktivace na základě továrny v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: abc6268584d12fe43d972c79a99316b94e8bee4b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663091"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Aktivace middlewaru založená na továrně v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> je bod rozšíření pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozšíření kontrolují, zda registrovaný typ middleware implementuje <xref:Microsoft.AspNetCore.Http.IMiddleware>. V takovém případě se instance <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zaregistrovaná v kontejneru používá k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích. Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.

Výhodnější

* Aktivace na žádost klienta (vkládání oboru služeb)
* Silné zadání middlewaru

<xref:Microsoft.AspNetCore.Http.IMiddleware> se aktivuje na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> definuje middleware pro kanál žádostí aplikace. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task>, která představuje spuštění middlewaru.

Middleware aktivované konvencí:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivováno <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Pro middleware jsou vytvořena rozšíření:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Není možné předávat objekty do middlewaru aktivovaného objektem Factory pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovaný v továrně se přidá do integrovaného kontejneru v `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middleware jsou registrovány v kanálu zpracování žádosti v `Startup.Configure`:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru. Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.

Výchozí implementace <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> je bod rozšíření pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> metody rozšíření kontrolují, zda registrovaný typ middleware implementuje <xref:Microsoft.AspNetCore.Http.IMiddleware>. V takovém případě se instance <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> zaregistrovaná v kontejneru používá k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích. Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.

Výhodnější

* Aktivace na žádost klienta (vkládání oboru služeb)
* Silné zadání middlewaru

<xref:Microsoft.AspNetCore.Http.IMiddleware> se aktivuje na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> definuje middleware pro kanál žádostí aplikace. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task>, která představuje spuštění middlewaru.

Middleware aktivované konvencí:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivováno <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Pro middleware jsou vytvořena rozšíření:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Není možné předávat objekty do middlewaru aktivovaného objektem Factory pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovaný v továrně se přidá do integrovaného kontejneru v `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middleware jsou registrovány v kanálu zpracování žádosti v `Startup.Configure`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru. Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.

Výchozí implementace <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
