---
title: Opětovné použití objektu s Fond ObjectPool v ASP.NET Core
author: rick-anderson
description: Tipy pro zvýšení výkonu aplikace ASP.NET Core s využitím Fond ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 4/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 92106d5add7dbda36e451614429baa0db420f0e8
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724856"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>Opětovné použití objektu s Fond ObjectPool v ASP.NET Core

Podle [Steve Gordon](https://twitter.com/stevejgordon), [Ryanem Nowak](https://github.com/rynowak), a [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> je součástí infrastruktury ASP.NET Core, která podporuje zachování skupiny objektů v paměti pro opakované použití místo objekty, které chcete být uvolňování paměti umožňuje shromažďovat.

Můžete chtít použít fondu objektů, pokud jsou objekty, které jsou spravovány:

- Nákladné přidělit/initialize.
- Představují některé omezené prostředků.
- Použít předvídatelně a často.

Například rozhraní ASP.NET Core používá fondu objektů na některých místech opakovaně používat <xref:System.Text.StringBuilder> instancí. `StringBuilder` přiděluje a spravuje vlastní vyrovnávací paměti pro uložení znaková data. ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich opětovné použití zvyšuje výkon.

Sdružování objektů vždy nezlepší výkon:

- Pokud je vysoké náklady na inicializaci objektu, je obvykle pomalejší, chcete-li získat objekt z fondu.
- Objekty spravován fondem nejsou zrušení přidělení, dokud se zrušení přidělení fondu.

Pomocí sdružování objektů pouze po shromáždění dat o výkonu pomocí realistické scénáře pro svou aplikaci nebo knihovny.

**UPOZORNĚNÍ: `ObjectPool` Neimplementuje `IDisposable`. Nedoporučujeme ji používat s typy, které je třeba vyřazení.**

**POZNÁMKA: Fond ObjectPool nebude umisťovat žádné omezení počtu objektů, které se přidělují, nastavený limit počtu objektů, které si zachovají.**

## <a name="concepts"></a>Koncepty

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -Abstrakce fondu základní objekt. Slouží k získání a vrátí objekty.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -Toto chcete přizpůsobit, jak je vytvořen objekt a jak je implementovat *resetování* při vrácen do fondu. To mohou být předány do fondu objektů, které vytvoříte přímo... NEBO

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> funguje jako objekt pro vytváření fondů objektu.
<!-- REview, there is no ObjectPoolProvider<T> -->

Fond ObjectPool lze použít v aplikaci několika způsoby:

* Vytvoření fondu.
* Registrace fondu v [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) jako instance.
* Registrace `ObjectPoolProvider<>` v DI a používat jej jako objekt pro vytváření.

## <a name="how-to-use-objectpool"></a>Jak používat Fond ObjectPool

Volání <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> k získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> vrátit objekt.  Neexistuje žádný požadavek, že vrátí všechny objekty. Pokud nechcete vracet objekt, bude vynuceno uvolnění paměti.

## <a name="objectpool-sample"></a>Fond ObjectPool vzorku

Následující kód:

* Přidá `ObjectPoolProvider` k [injektáž závislostí](xref:fundamentals/dependency-injection) kontejnerů (DI).
* Přidá a nakonfiguruje `ObjectPool<StringBuilder>` DI kontejneru.
* Přidá `BirthdayMiddleware`.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Následující kód implementuje `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
