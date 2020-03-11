---
title: Opakované použití objektu s fond objectpool v ASP.NET Core
author: rick-anderson
description: Tipy pro zvýšení výkonu v aplikacích ASP.NET Core s využitím fond objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666108"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Opakované použití objektu s fond objectpool v ASP.NET Core

[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)a [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> je součástí infrastruktury ASP.NET Core, která podporuje udržování skupiny objektů v paměti pro opakované použití, a ne umožnění uvolnění objektů do paměti.

Fond objektů můžete chtít použít, pokud jsou objekty, které jsou spravovány:

- Nákladné pro přidělení/inicializaci.
- Představuje nějaký omezený prostředek.
- Používá se prediktivní a často.

ASP.NET Core Framework například používá fond objektů na některých místech k opakovanému použití instancí <xref:System.Text.StringBuilder>. `StringBuilder` přiděluje a spravuje vlastní vyrovnávací paměti pro uchovávání znakových dat. ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich využití přináší výhody výkonu.

Sdružování objektů nezvyšuje výkon vždy:

- Pokud nejsou náklady na inicializaci objektu vysoké, obvykle je pomalejší pro získání objektu z fondu.
- Objekty spravované fondem nejsou nepřidělené, dokud se fond nerozdělí.

Používejte sdružování objektů až po shromáždění údajů o výkonu pomocí reálných scénářů vaší aplikace nebo knihovny.

**Upozornění: `ObjectPool` neimplementuje `IDisposable`. Nedoporučujeme ho používat s typy, které vyžadují vyřazení.**

**Poznámka: fond objectpool neomezuje počet objektů, které budou přiděleny, omezuje počet objektů, které budou zachovány.**

## <a name="concepts"></a>Koncepty

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> – abstrakce základního fondu objektů. Slouží k získání a vrácení objektů.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> – Implementujte tuto metodu pro přizpůsobení způsobu vytvoření objektu a způsobu jeho *resetování* při návratu do fondu. To lze předat do fondu objektů, který vytvoříte přímo.... ANI

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> slouží jako továrna pro vytváření fondů objektů.
<!-- REview, there is no ObjectPoolProvider<T> -->

Fond objectpool se dá v aplikaci použít několika způsoby:

* Vytváří se instance fondu.
* Registrace fondu v rámci [vkládání závislostí](xref:fundamentals/dependency-injection) (di) jako instance.
* Registrace `ObjectPoolProvider<>` v DI a její použití jako továrny.

## <a name="how-to-use-objectpool"></a>Jak používat fond objectpool

Zavolejte <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> pro získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> k vrácení objektu.  Neexistuje žádný požadavek, abyste vrátili všechny objekty. Pokud objekt nevrátíte, bude uvolněn z paměti.

## <a name="objectpool-sample"></a>Ukázka fond objectpool

Následující kód:

* Přidá `ObjectPoolProvider` do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) (di).
* Přidá a nakonfiguruje `ObjectPool<StringBuilder>` do kontejneru DI.
* Přidá `BirthdayMiddleware`.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Následující kód implementuje `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
