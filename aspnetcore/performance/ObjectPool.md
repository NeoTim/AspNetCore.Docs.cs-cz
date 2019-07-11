---
title: Opětovné použití objektu s Fond ObjectPool v ASP.NET Core
author: rick-anderson
description: Tipy pro zvýšení výkonu aplikace ASP.NET Core s využitím Fond ObjectPool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815514"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="83d1f-103">Opětovné použití objektu s Fond ObjectPool v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83d1f-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="83d1f-104">Podle [Steve Gordon](https://twitter.com/stevejgordon), [Ryanem Nowak](https://github.com/rynowak), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83d1f-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="83d1f-105"><xref:Microsoft.Extensions.ObjectPool> je součástí infrastruktury ASP.NET Core, která podporuje zachování skupiny objektů v paměti pro opakované použití místo objekty, které chcete být uvolňování paměti umožňuje shromažďovat.</span><span class="sxs-lookup"><span data-stu-id="83d1f-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="83d1f-106">Můžete chtít použít fondu objektů, pokud jsou objekty, které jsou spravovány:</span><span class="sxs-lookup"><span data-stu-id="83d1f-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="83d1f-107">Nákladné přidělit/initialize.</span><span class="sxs-lookup"><span data-stu-id="83d1f-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="83d1f-108">Představují některé omezené prostředků.</span><span class="sxs-lookup"><span data-stu-id="83d1f-108">Represent some limited resource.</span></span>
- <span data-ttu-id="83d1f-109">Použít předvídatelně a často.</span><span class="sxs-lookup"><span data-stu-id="83d1f-109">Used predictably and frequently.</span></span>

<span data-ttu-id="83d1f-110">Například rozhraní ASP.NET Core používá fondu objektů na některých místech opakovaně používat <xref:System.Text.StringBuilder> instancí.</span><span class="sxs-lookup"><span data-stu-id="83d1f-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="83d1f-111">`StringBuilder` přiděluje a spravuje vlastní vyrovnávací paměti pro uložení znaková data.</span><span class="sxs-lookup"><span data-stu-id="83d1f-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="83d1f-112">ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich opětovné použití zvyšuje výkon.</span><span class="sxs-lookup"><span data-stu-id="83d1f-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="83d1f-113">Sdružování objektů vždy nezlepší výkon:</span><span class="sxs-lookup"><span data-stu-id="83d1f-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="83d1f-114">Pokud je vysoké náklady na inicializaci objektu, je obvykle pomalejší, chcete-li získat objekt z fondu.</span><span class="sxs-lookup"><span data-stu-id="83d1f-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="83d1f-115">Objekty spravován fondem nejsou zrušení přidělení, dokud se zrušení přidělení fondu.</span><span class="sxs-lookup"><span data-stu-id="83d1f-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="83d1f-116">Pomocí sdružování objektů pouze po shromáždění dat o výkonu pomocí realistické scénáře pro svou aplikaci nebo knihovny.</span><span class="sxs-lookup"><span data-stu-id="83d1f-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="83d1f-117">**UPOZORNĚNÍ: `ObjectPool` Neimplementuje `IDisposable`. Nedoporučujeme ji používat s typy, které je třeba vyřazení.**</span><span class="sxs-lookup"><span data-stu-id="83d1f-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="83d1f-118">**POZNÁMKA: Fond ObjectPool nebude umisťovat žádné omezení počtu objektů, které se přidělují, nastavený limit počtu objektů, které si zachovají.**</span><span class="sxs-lookup"><span data-stu-id="83d1f-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="83d1f-119">Koncepty</span><span class="sxs-lookup"><span data-stu-id="83d1f-119">Concepts</span></span>

<span data-ttu-id="83d1f-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -Abstrakce fondu základní objekt.</span><span class="sxs-lookup"><span data-stu-id="83d1f-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="83d1f-121">Slouží k získání a vrátí objekty.</span><span class="sxs-lookup"><span data-stu-id="83d1f-121">Used to get and return objects.</span></span>

<span data-ttu-id="83d1f-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -Toto chcete přizpůsobit, jak je vytvořen objekt a jak je implementovat *resetování* při vrácen do fondu.</span><span class="sxs-lookup"><span data-stu-id="83d1f-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="83d1f-123">To mohou být předány do fondu objektů, které vytvoříte přímo... NEBO</span><span class="sxs-lookup"><span data-stu-id="83d1f-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="83d1f-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> funguje jako objekt pro vytváření fondů objektu.</span><span class="sxs-lookup"><span data-stu-id="83d1f-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="83d1f-125">Fond ObjectPool lze použít v aplikaci několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="83d1f-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="83d1f-126">Vytvoření fondu.</span><span class="sxs-lookup"><span data-stu-id="83d1f-126">Instantiating a pool.</span></span>
* <span data-ttu-id="83d1f-127">Registrace fondu v [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) jako instance.</span><span class="sxs-lookup"><span data-stu-id="83d1f-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="83d1f-128">Registrace `ObjectPoolProvider<>` v DI a používat jej jako objekt pro vytváření.</span><span class="sxs-lookup"><span data-stu-id="83d1f-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="83d1f-129">Jak používat Fond ObjectPool</span><span class="sxs-lookup"><span data-stu-id="83d1f-129">How to use ObjectPool</span></span>

<span data-ttu-id="83d1f-130">Volání <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> k získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> vrátit objekt.</span><span class="sxs-lookup"><span data-stu-id="83d1f-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="83d1f-131">Neexistuje žádný požadavek, že vrátí všechny objekty.</span><span class="sxs-lookup"><span data-stu-id="83d1f-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="83d1f-132">Pokud nechcete vracet objekt, bude vynuceno uvolnění paměti.</span><span class="sxs-lookup"><span data-stu-id="83d1f-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="83d1f-133">Fond ObjectPool vzorku</span><span class="sxs-lookup"><span data-stu-id="83d1f-133">ObjectPool sample</span></span>

<span data-ttu-id="83d1f-134">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="83d1f-134">The following code:</span></span>

* <span data-ttu-id="83d1f-135">Přidá `ObjectPoolProvider` k [injektáž závislostí](xref:fundamentals/dependency-injection) kontejnerů (DI).</span><span class="sxs-lookup"><span data-stu-id="83d1f-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="83d1f-136">Přidá a nakonfiguruje `ObjectPool<StringBuilder>` DI kontejneru.</span><span class="sxs-lookup"><span data-stu-id="83d1f-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="83d1f-137">Přidá `BirthdayMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="83d1f-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="83d1f-138">Následující kód implementuje `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="83d1f-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
