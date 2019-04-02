---
title: Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core
author: guardrex
description: Další informace o použití middlewaru silného typu pomocí implementace aktivaci založenou na objekt pro vytváření v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 9305616ce3f2ef49cf9dfcab719f673c0fb4b51e
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809163"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> představuje rozšíření bod pro [middleware](xref:fundamentals/middleware/index) aktivace.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> rozšiřující metody zkontrolovat, pokud middlewarem registrovaného typu implementuje <xref:Microsoft.AspNetCore.Http.IMiddleware>. Pokud ano, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaný v kontejneru se používá k překladu <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace založené na konvenci middlewaru. Middleware je zaregistrovaný jako [služby s vymezeným oborem nebo přechodné](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru aplikace služby.

Výhody:

* Aktivace každý požadavek klienta (vkládání vymezené služby)
* Silné typování middlewaru

<xref:Microsoft.AspNetCore.Http.IMiddleware> je aktivovaná, každý požadavek klienta (připojení), takže vymezené služby můžete vloženy do konstruktoru middlewaru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> Definuje middleware pro kanál žádosti o aplikace. [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) metoda zpracovává požadavky a vrátí <xref:System.Threading.Tasks.Task> , který představuje spuštění middlewaru.

Middleware aktivoval konvence:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivoval <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Rozšíření jsou vytvořeny pro middlewares:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Není možné předat objekty factory aktivuje middleware s <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovat objekt pro vytváření se přidá do kontejneru integrované v `Startup.ConfigureServices`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Obě middlewares zaregistrovaní v kanálu zpracování žádostí v `Startup.Configure`:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru. Implementace objektu factory middlewaru je zaregistrovaný v kontejneru jako služba s vymezeným oborem.

Výchozí hodnota <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementaci <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, najdete v [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) balíčku.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
