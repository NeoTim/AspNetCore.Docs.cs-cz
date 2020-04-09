---
title: Aktivace middlewaru na bázi továrny v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s implementací aktivace z výroby v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: abc6268584d12fe43d972c79a99316b94e8bee4b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663091"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Aktivace middlewaru na bázi továrny v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je rozšiřitelnost bod pro [middleware](xref:fundamentals/middleware/index) aktivace.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozšíření zkontrolujte, zda middleware <xref:Microsoft.AspNetCore.Http.IMiddleware>registrovaný typ implementuje . Pokud ano, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registrovaná v kontejneru <xref:Microsoft.AspNetCore.Http.IMiddleware> se používá k vyřešení implementace namísto použití logiky aktivace middlewaru založené na konvencích. Middleware je registrován jako [oborová nebo přechodná služba](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služeb aplikace.

Výhody:

* Aktivace na požadavek klienta (vkládání služeb s rozsahem)
* Silné psaní middleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivován na požadavek klienta (připojení), takže oborové služby mohou být vloženy do konstruktoru middleware.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace. Metoda [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a <xref:System.Threading.Tasks.Task> vrací, která představuje spuštění middlewaru.

Middleware aktivován konvencí:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivován: <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Rozšíření jsou vytvořeny pro middlewares:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Není možné předat objekty do továrně aktivovaného <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>middlewaru s:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovaný z výroby se přidá do `Startup.ConfigureServices`vestavěného kontejneru v:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middlewares jsou registrovány v `Startup.Configure`kanálu zpracování požadavků v :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware. Middleware factory implementace je registrována v kontejneru jako oborové služby.

Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, je nalezena v balíčku [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je rozšiřitelnost bod pro [middleware](xref:fundamentals/middleware/index) aktivace.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozšíření zkontrolujte, zda middleware <xref:Microsoft.AspNetCore.Http.IMiddleware>registrovaný typ implementuje . Pokud ano, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registrovaná v kontejneru <xref:Microsoft.AspNetCore.Http.IMiddleware> se používá k vyřešení implementace namísto použití logiky aktivace middlewaru založené na konvencích. Middleware je registrován jako [oborová nebo přechodná služba](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služeb aplikace.

Výhody:

* Aktivace na požadavek klienta (vkládání služeb s rozsahem)
* Silné psaní middleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivován na požadavek klienta (připojení), takže oborové služby mohou být vloženy do konstruktoru middleware.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace. Metoda [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a <xref:System.Threading.Tasks.Task> vrací, která představuje spuštění middlewaru.

Middleware aktivován konvencí:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivován: <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Rozšíření jsou vytvořeny pro middlewares:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Není možné předat objekty do továrně aktivovaného <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>middlewaru s:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovaný z výroby se přidá do `Startup.ConfigureServices`vestavěného kontejneru v:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Oba middlewares jsou registrovány v `Startup.Configure`kanálu zpracování požadavků v :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware. Middleware factory implementace je registrována v kontejneru jako oborové služby.

Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, je nalezena v balíčku [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
