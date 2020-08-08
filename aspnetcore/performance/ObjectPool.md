---
title: Opakované použití objektu s fond objectpool v ASP.NET Core
author: rick-anderson
description: Tipy pro zvýšení výkonu v aplikacích ASP.NET Core s využitím fond objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 1f57bc4662296333b3d2c659c057230548541b91
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020402"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Opakované použití objektu s fond objectpool v ASP.NET Core

[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)a [Günther Foidl](https://github.com/gfoidl)

<xref:Microsoft.Extensions.ObjectPool>je součástí infrastruktury ASP.NET Core, která podporuje udržování skupiny objektů v paměti pro opakované použití, nikoli pro uvolnění objektů do paměti.

Fond objektů můžete chtít použít, pokud jsou objekty, které jsou spravovány:

- Nákladné pro přidělení/inicializaci.
- Představuje nějaký omezený prostředek.
- Používá se prediktivní a často.

Například ASP.NET Core Framework používá fond objektů na některých místech k opakovanému použití <xref:System.Text.StringBuilder> instancí. `StringBuilder`přidělí a spravuje vlastní vyrovnávací paměti pro uchovávání znakových dat. ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich využití přináší výhody výkonu.

Sdružování objektů nezvyšuje výkon vždy:

- Pokud nejsou náklady na inicializaci objektu vysoké, obvykle je pomalejší pro získání objektu z fondu.
- Objekty spravované fondem nejsou nepřidělené, dokud se fond nerozdělí.

Používejte sdružování objektů až po shromáždění údajů o výkonu pomocí reálných scénářů vaší aplikace nebo knihovny.

::: moniker range="< aspnetcore-3.0"
**Upozornění: `ObjectPool` neimplementuje se `IDisposable` . Nedoporučujeme ho používat s typy, které vyžadují vyřazení.** `ObjectPool`v ASP.NET Core 3,0 a novějších verzích podporuje `IDisposable` .
::: moniker-end

**Poznámka: fond objectpool neomezuje počet objektů, které budou přiděleny, omezuje počet objektů, které budou zachovány.**

## <a name="concepts"></a>Koncepty

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>– základní abstrakce fondu objektů. Slouží k získání a vrácení objektů.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-Implementujte tuto metodu pro přizpůsobení způsobu vytvoření objektu a způsobu jeho *resetování* při návratu do fondu. To lze předat do fondu objektů, který vytvoříte přímo.... ANI

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>slouží jako továrna pro vytváření fondů objektů.
<!-- REview, there is no ObjectPoolProvider<T> -->

Fond objectpool se dá v aplikaci použít několika způsoby:

* Vytváří se instance fondu.
* Registrace fondu v rámci [vkládání závislostí](xref:fundamentals/dependency-injection) (di) jako instance.
* Registrace `ObjectPoolProvider<>` v di a její použití jako továrny.

## <a name="how-to-use-objectpool"></a>Jak používat fond objectpool

Volání <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> pro získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> vrácení objektu.  Neexistuje žádný požadavek, abyste vrátili všechny objekty. Pokud objekt nevrátíte, bude uvolněn z paměti.

::: moniker range=">= aspnetcore-3.0"
Při <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> použití a `T` implementuje `IDisposable` :

* Položky, které ***nejsou vráceny do*** fondu, budou uvolněny.
* Když je fond uvolněný pomocí DI, všechny položky ve fondu se vyřadí.

Poznámka: po odstranění fondu:

* Volání `Get` vyvolá výjimku `ObjectDisposedException` .
* `return`odstraní danou položku.

::: moniker-end

## <a name="objectpool-sample"></a>Ukázka fond objectpool

Následující kód:

* Přidá `ObjectPoolProvider` do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) (di).
* Přidá a nakonfiguruje `ObjectPool<StringBuilder>` do kontejneru di.
* Přidá `BirthdayMiddleware` .

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Následující kód implementuje`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
