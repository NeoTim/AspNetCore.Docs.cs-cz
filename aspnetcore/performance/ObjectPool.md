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
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="4c5e2-103">Opakované použití objektu s fond objectpool v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c5e2-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="4c5e2-104">[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4c5e2-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4c5e2-105"><xref:Microsoft.Extensions.ObjectPool> je součástí infrastruktury ASP.NET Core, která podporuje udržování skupiny objektů v paměti pro opakované použití, a ne umožnění uvolnění objektů do paměti.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="4c5e2-106">Fond objektů můžete chtít použít, pokud jsou objekty, které jsou spravovány:</span><span class="sxs-lookup"><span data-stu-id="4c5e2-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="4c5e2-107">Nákladné pro přidělení/inicializaci.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="4c5e2-108">Představuje nějaký omezený prostředek.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-108">Represent some limited resource.</span></span>
- <span data-ttu-id="4c5e2-109">Používá se prediktivní a často.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-109">Used predictably and frequently.</span></span>

<span data-ttu-id="4c5e2-110">ASP.NET Core Framework například používá fond objektů na některých místech k opakovanému použití instancí <xref:System.Text.StringBuilder>.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="4c5e2-111">`StringBuilder` přiděluje a spravuje vlastní vyrovnávací paměti pro uchovávání znakových dat.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="4c5e2-112">ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich využití přináší výhody výkonu.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="4c5e2-113">Sdružování objektů nezvyšuje výkon vždy:</span><span class="sxs-lookup"><span data-stu-id="4c5e2-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="4c5e2-114">Pokud nejsou náklady na inicializaci objektu vysoké, obvykle je pomalejší pro získání objektu z fondu.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="4c5e2-115">Objekty spravované fondem nejsou nepřidělené, dokud se fond nerozdělí.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="4c5e2-116">Používejte sdružování objektů až po shromáždění údajů o výkonu pomocí reálných scénářů vaší aplikace nebo knihovny.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="4c5e2-117">**Upozornění: `ObjectPool` neimplementuje `IDisposable`. Nedoporučujeme ho používat s typy, které vyžadují vyřazení.**</span><span class="sxs-lookup"><span data-stu-id="4c5e2-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="4c5e2-118">**Poznámka: fond objectpool neomezuje počet objektů, které budou přiděleny, omezuje počet objektů, které budou zachovány.**</span><span class="sxs-lookup"><span data-stu-id="4c5e2-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="4c5e2-119">Koncepty</span><span class="sxs-lookup"><span data-stu-id="4c5e2-119">Concepts</span></span>

<span data-ttu-id="4c5e2-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> – abstrakce základního fondu objektů.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="4c5e2-121">Slouží k získání a vrácení objektů.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-121">Used to get and return objects.</span></span>

<span data-ttu-id="4c5e2-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> – Implementujte tuto metodu pro přizpůsobení způsobu vytvoření objektu a způsobu jeho *resetování* při návratu do fondu.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="4c5e2-123">To lze předat do fondu objektů, který vytvoříte přímo.... ANI</span><span class="sxs-lookup"><span data-stu-id="4c5e2-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="4c5e2-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> slouží jako továrna pro vytváření fondů objektů.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="4c5e2-125">Fond objectpool se dá v aplikaci použít několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="4c5e2-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="4c5e2-126">Vytváří se instance fondu.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-126">Instantiating a pool.</span></span>
* <span data-ttu-id="4c5e2-127">Registrace fondu v rámci [vkládání závislostí](xref:fundamentals/dependency-injection) (di) jako instance.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="4c5e2-128">Registrace `ObjectPoolProvider<>` v DI a její použití jako továrny.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="4c5e2-129">Jak používat fond objectpool</span><span class="sxs-lookup"><span data-stu-id="4c5e2-129">How to use ObjectPool</span></span>

<span data-ttu-id="4c5e2-130">Zavolejte <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> pro získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> k vrácení objektu.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="4c5e2-131">Neexistuje žádný požadavek, abyste vrátili všechny objekty.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="4c5e2-132">Pokud objekt nevrátíte, bude uvolněn z paměti.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="4c5e2-133">Ukázka fond objectpool</span><span class="sxs-lookup"><span data-stu-id="4c5e2-133">ObjectPool sample</span></span>

<span data-ttu-id="4c5e2-134">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="4c5e2-134">The following code:</span></span>

* <span data-ttu-id="4c5e2-135">Přidá `ObjectPoolProvider` do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="4c5e2-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="4c5e2-136">Přidá a nakonfiguruje `ObjectPool<StringBuilder>` do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="4c5e2-137">Přidá `BirthdayMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="4c5e2-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="4c5e2-138">Následující kód implementuje `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="4c5e2-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
