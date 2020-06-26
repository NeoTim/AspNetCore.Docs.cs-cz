---
title: Aktivace middlewaru založená na továrně v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s implementací aktivace na základě továrny v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 893dce119328acaa4ffd3087b94328017f5915ca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399904"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Aktivace middlewaru založená na továrně v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>rozšiřující metody kontrolují, jestli implementuje registrovaný typ middlewaru <xref:Microsoft.AspNetCore.Http.IMiddleware> . V takovém případě <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaná v kontejneru slouží k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích. Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.

Výhody:

* Aktivace na žádost klienta (vkládání oboru služeb)
* Silné zadání middlewaru

<xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivováno na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task> , která představuje spuštění middlewaru.

Middleware aktivované konvencí:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivované <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Pro middleware jsou vytvořena rozšíření:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Do middlewaru aktivovaného výrobou nelze předat objekty pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovaný v továrně se přidají do integrovaného kontejneru v `Startup.ConfigureServices` :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

V kanálu zpracování požadavků jsou zaregistrované middleware v `Startup.Configure` :

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru. Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.

Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>rozšiřující metody kontrolují, jestli implementuje registrovaný typ middlewaru <xref:Microsoft.AspNetCore.Http.IMiddleware> . V takovém případě <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaná v kontejneru slouží k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích. Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.

Výhody:

* Aktivace na žádost klienta (vkládání oboru služeb)
* Silné zadání middlewaru

<xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivováno na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace. Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task> , která představuje spuštění middlewaru.

Middleware aktivované konvencí:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivované <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Pro middleware jsou vytvořena rozšíření:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Do middlewaru aktivovaného výrobou nelze předat objekty pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovaný v továrně se přidají do integrovaného kontejneru v `Startup.ConfigureServices` :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

V kanálu zpracování požadavků jsou zaregistrované middleware v `Startup.Configure` :

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru. Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.

Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
