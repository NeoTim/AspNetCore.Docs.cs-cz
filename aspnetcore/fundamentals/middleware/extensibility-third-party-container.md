---
title: Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat middleware silného typu pomocí aktivace založené na objekt pro vytváření a kontejnerem jiného výrobce v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9e4d4c6c0232ebc51ad08923e10164262b652280
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901182"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Tento článek ukazuje, jak používat [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) a [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) jako bod rozšiřitelnosti pro [middleware](xref:fundamentals/middleware/index) aktivace s kontejnerem jiného výrobce. Úvodní informace o `IMiddlewareFactory` a `IMiddleware`, najdete v článku [middleware založený na objekt pro vytváření aktivace](xref:fundamentals/middleware/extensibility) tématu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace předvádí middleware aktivace pomocí `IMiddlewareFactory` implementaci `SimpleInjectorMiddlewareFactory`. Ukázka používá [jednoduché Injector](https://simpleinjector.org) kontejneru pro vkládání (DI) závislosti.

Ukázková implementace middlewaru zaznamenává hodnota poskytnutá parametru řetězce dotazu (`key`). Middleware použije objekt context vloženého databáze (s vymezeným oborem service) k zaznamenání hodnotu řetězce dotazu v databázi v paměti.

> [!NOTE]
> Tato ukázková aplikace používá [jednoduché Injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely. Využití jednoduché Injector není o potvrzení. Přístupy k aktivaci middleware popsaného v dokumentaci k jednoduché Injector a programu jednoduché Injector jsou doporučené problémy Githubu. Další informace najdete v tématu [jednoduché Injector dokumentaci](https://simpleinjector.readthedocs.io/en/latest/index.html) a [úložiště GitHub jednoduché Injector](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) poskytuje metody pro vytvoření middlewaru.

V ukázkové aplikaci se implementuje objekt pro vytváření middleware k vytvoření `SimpleInjectorActivatedMiddleware` instance. Objekt pro vytváření middleware používá jednoduché Injector kontejneru pro middleware:

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) definuje middleware pro kanál žádosti o aplikace.

Middleware aktivoval `IMiddlewareFactory` implementace (*Middleware/SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Vytvoření rozšíření pro middleware (*Middleware/MiddlewareExtensions.cs*):

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` třeba provést několik úloh:

* Nastavte jednoduché Injector kontejner.
* Zaregistrujte objekt pro vytváření a middlewaru.
* Zpřístupnit kontext databáze aplikace z jednoduchého Injector kontejneru pro stránky Razor.

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

Middleware je registrován v kanálu zpracování žádostí v `Startup.Configure`:

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Middleware založený na objekt pro vytváření aktivace](xref:fundamentals/middleware/extensibility)
* [Jednoduché úložiště Injector GitHub](https://github.com/simpleinjector/SimpleInjector)
* [Dokumentace k jednoduché Injector](https://simpleinjector.readthedocs.io/en/latest/index.html)
