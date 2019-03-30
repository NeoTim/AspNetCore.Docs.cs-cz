---
title: Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core
author: guardrex
description: Další informace o použití middlewaru silného typu pomocí implementace aktivaci založenou na objekt pro vytváření v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: d29c4d3d72ddd8ec3c2a726ee35ae1dc82774537
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750527"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)/[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) je bod rozšiřitelnosti pro [middleware](xref:fundamentals/middleware/index) aktivace.

`UseMiddleware` rozšiřující metody zkontrolovat, pokud middlewarem registrovaného typu implementuje `IMiddleware`. Pokud ano, `IMiddlewareFactory` instance zaregistrovaný v kontejneru se používá k překladu `IMiddleware` implementace namísto použití logiky aktivace založené na konvenci middlewaru. Middleware je registrována jako služba s vymezeným oborem nebo přechodné v kontejneru aplikace služby.

Výhody:

* Aktivace každý požadavek klienta (vkládání vymezené služby)
* Silné typování middlewaru

`IMiddleware` je aktivovaná, každý požadavek klienta (připojení), takže vymezené služby můžete vloženy do konstruktoru middlewaru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/sample) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace předvádí middleware aktivoval:

* Konvence. Další informace o aktivaci konvenční middleware, najdete v článku [Middleware](xref:fundamentals/middleware/index) tématu.
* [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementace. Výchozí hodnota [MiddlewareFactory třídy](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory) aktivuje middleware.

Implementace middlewaru fungovat stejně jako a si poznamenejte hodnotu poskytovanou infrastrukturou parametru řetězce dotazu (`key`). Middlewares objekt context vloženého databáze (vymezené služby) slouží k zaznamenání hodnotu řetězce dotazu v databázi v paměti.

## <a name="imiddleware"></a>IMiddleware

[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) definuje middleware pro kanál žádosti o aplikace. [InvokeAsync (HttpContext, RequestDelegate)](/dotnet/api/microsoft.aspnetcore.http.imiddleware.invokeasync#Microsoft_AspNetCore_Http_IMiddleware_InvokeAsync_Microsoft_AspNetCore_Http_HttpContext_Microsoft_AspNetCore_Http_RequestDelegate_) metoda zpracovává požadavky a vrátí `Task` , který představuje spuštění middlewaru.

Middleware aktivoval konvence:

[!code-csharp[](extensibility/sample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Middleware aktivoval `MiddlewareFactory`:

[!code-csharp[](extensibility/sample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Rozšíření jsou vytvořeny pro middlewares:

[!code-csharp[](extensibility/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Není možné předat objekty factory aktivuje middleware s `UseMiddleware`:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Middleware aktivovat objekt pro vytváření se přidá do kontejneru integrované v *Startup.cs*:

[!code-csharp[](extensibility/sample/Startup.cs?name=snippet1&highlight=12)]

Obě middlewares zaregistrovaní v kanálu zpracování žádostí v `Configure`:

[!code-csharp[](extensibility/sample/Startup.cs?name=snippet2&highlight=14-15)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) poskytuje metody pro vytvoření middlewaru. Implementace objektu factory middlewaru je zaregistrovaný v kontejneru jako služba s vymezeným oborem.

Výchozí hodnota `IMiddlewareFactory` implementaci [MiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory), najdete v [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) balíčku.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
