---
title: Aktivace middlewaru s kontejnerem jiného výrobce v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak používat middleware silného typu s aktivací z výroby a kontejnerem jiného výrobce v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a5c5bf6dff6ef795add075df932dd625129ef793
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663133"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Aktivace middlewaru s kontejnerem jiného výrobce v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Tento článek ukazuje, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> jak <xref:Microsoft.AspNetCore.Http.IMiddleware> používat a jako bod rozšiřitelnosti pro aktivaci [middleware](xref:fundamentals/middleware/index) s kontejnerem jiného výrobce. Úvodní informace o písmenu `IMiddlewareFactory` `IMiddleware`a) <xref:fundamentals/middleware/extensibility>a d), viz .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace demonstruje aktivaci middlewaru implementací `IMiddlewareFactory` . `SimpleInjectorMiddlewareFactory` Ukázka používá kontejner vkládání závislostí [jednoduchého injektu](https://simpleinjector.org) (DI).

Implementace middlewaru ukázky zaznamenává hodnotu poskytovo parametrem řetězce dotazu (`key`). Middleware používá vložený kontext databáze (oborová služba) k zaznamenání hodnoty řetězce dotazu v databázi v paměti.

> [!NOTE]
> Ukázková aplikace používá [simple injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely. Použití jednoduchého injektoru není potvrzení. Aktivační přístupy middlewaru popsané v dokumentaci Simple Injector a problémy GitHubu doporučují správci jednoduchého injektoru. Další informace naleznete v [dokumentaci simple injector](https://simpleinjector.readthedocs.io/en/latest/index.html) a [simple injector github repozitář .](https://github.com/simpleinjector/SimpleInjector)

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware.

V ukázkové aplikaci je implementována `SimpleInjectorActivatedMiddleware` middleware továrna k vytvoření instance. Middleware továrna používá jednoduchý vstřikovací kontejner k vyřešení middleware:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace.

Middleware aktivován `IMiddlewareFactory` implementací (*Middleware/SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Rozšíření je vytvořen pro middleware (*Middleware/MiddlewareExtensions.cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`musí provést několik úkolů:

* Nastavte jednoduchý zásobník vstřikovače.
* Zaregistrujte tovární a middleware.
* Zpřístupněte kontext databáze aplikace z kontejneru Simple Injector.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Middleware je registrován v kanálu `Startup.Configure`zpracování požadavků v :

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek ukazuje, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> jak <xref:Microsoft.AspNetCore.Http.IMiddleware> používat a jako bod rozšiřitelnosti pro aktivaci [middleware](xref:fundamentals/middleware/index) s kontejnerem jiného výrobce. Úvodní informace o písmenu `IMiddlewareFactory` `IMiddleware`a) <xref:fundamentals/middleware/extensibility>a d), viz .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace demonstruje aktivaci middlewaru implementací `IMiddlewareFactory` . `SimpleInjectorMiddlewareFactory` Ukázka používá kontejner vkládání závislostí [jednoduchého injektu](https://simpleinjector.org) (DI).

Implementace middlewaru ukázky zaznamenává hodnotu poskytovo parametrem řetězce dotazu (`key`). Middleware používá vložený kontext databáze (oborová služba) k zaznamenání hodnoty řetězce dotazu v databázi v paměti.

> [!NOTE]
> Ukázková aplikace používá [simple injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely. Použití jednoduchého injektoru není potvrzení. Aktivační přístupy middlewaru popsané v dokumentaci Simple Injector a problémy GitHubu doporučují správci jednoduchého injektoru. Další informace naleznete v [dokumentaci simple injector](https://simpleinjector.readthedocs.io/en/latest/index.html) a [simple injector github repozitář .](https://github.com/simpleinjector/SimpleInjector)

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware.

V ukázkové aplikaci je implementována `SimpleInjectorActivatedMiddleware` middleware továrna k vytvoření instance. Middleware továrna používá jednoduchý vstřikovací kontejner k vyřešení middleware:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace.

Middleware aktivován `IMiddlewareFactory` implementací (*Middleware/SimpleInjectorActivatedMiddleware.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Rozšíření je vytvořen pro middleware (*Middleware/MiddlewareExtensions.cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`musí provést několik úkolů:

* Nastavte jednoduchý zásobník vstřikovače.
* Zaregistrujte tovární a middleware.
* Zpřístupněte kontext databáze aplikace z kontejneru Simple Injector.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Middleware je registrován v kanálu `Startup.Configure`zpracování požadavků v :

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Aktivace middlewaru na základě výroby](xref:fundamentals/middleware/extensibility)
* [Jednoduché úložiště Injector GitHub](https://github.com/simpleinjector/SimpleInjector)
* [Jednoduchá dokumentace injektoru](https://simpleinjector.readthedocs.io/en/latest/index.html)
