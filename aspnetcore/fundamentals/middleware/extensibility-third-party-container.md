---
title: Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat middleware silného typu pomocí aktivace založené na objekt pro vytváření a kontejnerem jiného výrobce v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/03/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 4bc99b4c336aba611287c9fbe03d4252f8abee5b
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561652"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro [middleware](xref:fundamentals/middleware/index) aktivace s kontejnerem jiného výrobce. Úvodní informace o `IMiddlewareFactory` a `IMiddleware`, naleznete v tématu <xref:fundamentals/middleware/extensibility>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace předvádí middleware aktivace pomocí `IMiddlewareFactory` implementaci `SimpleInjectorMiddlewareFactory`. Ukázka používá [jednoduché Injector](https://simpleinjector.org) kontejneru pro vkládání (DI) závislosti.

Ukázková implementace middlewaru zaznamenává hodnota poskytnutá parametru řetězce dotazu (`key`). Middleware použije objekt context vloženého databáze (s vymezeným oborem service) k zaznamenání hodnotu řetězce dotazu v databázi v paměti.

> [!NOTE]
> Tato ukázková aplikace používá [jednoduché Injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely. Využití jednoduché Injector není o potvrzení. Přístupy k aktivaci middleware popsaného v dokumentaci k jednoduché Injector a programu jednoduché Injector jsou doporučené problémy Githubu. Další informace najdete v tématu [jednoduché Injector dokumentaci](https://simpleinjector.readthedocs.io/en/latest/index.html) a [úložiště GitHub jednoduché Injector](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.

V ukázkové aplikaci se implementuje objekt pro vytváření middleware k vytvoření `SimpleInjectorActivatedMiddleware` instance. Objekt pro vytváření middleware používá jednoduché Injector kontejneru pro middleware:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> Definuje middleware pro kanál žádosti o aplikace.

Middleware aktivoval `IMiddlewareFactory` implementace (*Middleware/SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Vytvoření rozšíření pro middleware (*Middleware/MiddlewareExtensions.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` třeba provést několik úloh:

* Nastavte jednoduché Injector kontejner.
* Zaregistrujte objekt pro vytváření a middlewaru.
* Kontext databáze aplikace zpřístupníte z jednoduchého Injector kontejneru.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Middleware je registrován v kanálu zpracování žádostí v `Startup.Configure`:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Middleware založený na objekt pro vytváření aktivace](xref:fundamentals/middleware/extensibility)
* [Jednoduché úložiště Injector GitHub](https://github.com/simpleinjector/SimpleInjector)
* [Dokumentace k jednoduché Injector](https://simpleinjector.readthedocs.io/en/latest/index.html)
