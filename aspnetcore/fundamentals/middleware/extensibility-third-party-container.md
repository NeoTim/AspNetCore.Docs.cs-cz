---
title: Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s aktivací založenou na výrobě a kontejnerem třetí strany v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9a551777c58ba8eae1cf04758e1631710ff16e51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774467"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany. Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware`najdete v <xref:fundamentals/middleware/extensibility>tématu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace předvádí aktivaci middlewaru `IMiddlewareFactory` pomocí implementace `SimpleInjectorMiddlewareFactory`. Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).

Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu (`key`). Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.

> [!NOTE]
> Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) . Použití jednoduchého injektoru není potvrzením. Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru. Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.

V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance. Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.

Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`musí provádět několik úloh:

* Nastavení jednoduchého kontejneru pro nástřik.
* Zaregistrujte objekt pro vytváření a middlewaru.
* Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Middleware je zaregistrován v kanálu zpracování žádosti v `Startup.Configure`nástroji:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany. Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware`najdete v <xref:fundamentals/middleware/extensibility>tématu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace předvádí aktivaci middlewaru `IMiddlewareFactory` pomocí implementace `SimpleInjectorMiddlewareFactory`. Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).

Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu (`key`). Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.

> [!NOTE]
> Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) . Použití jednoduchého injektoru není potvrzením. Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru. Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.

V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance. Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.

Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`musí provádět několik úloh:

* Nastavení jednoduchého kontejneru pro nástřik.
* Zaregistrujte objekt pro vytváření a middlewaru.
* Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Middleware je zaregistrován v kanálu zpracování žádosti v `Startup.Configure`nástroji:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Aktivace middlewaru založená na objektu factory](xref:fundamentals/middleware/extensibility)
* [Úložiště GitHub pro jednoduché vložení](https://github.com/simpleinjector/SimpleInjector)
* [Dokumentace k jednoduchému vstřikovacímu zařízení](https://simpleinjector.readthedocs.io/en/latest/index.html)
