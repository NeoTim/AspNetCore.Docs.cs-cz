---
title: Opakované použití objektu s fond objectpool v ASP.NET Core
author: rick-anderson
description: Tipy pro zvýšení výkonu v aplikacích ASP.NET Core s využitím fond objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 004ca5724517bf3fbf6512c0b9653793f4e0f702
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85241007"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="2d6c8-103">Opakované použití objektu s fond objectpool v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d6c8-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="2d6c8-104">[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d6c8-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2d6c8-105"><xref:Microsoft.Extensions.ObjectPool>je součástí infrastruktury ASP.NET Core, která podporuje udržování skupiny objektů v paměti pro opakované použití, nikoli pro uvolnění objektů do paměti.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="2d6c8-106">Fond objektů můžete chtít použít, pokud jsou objekty, které jsou spravovány:</span><span class="sxs-lookup"><span data-stu-id="2d6c8-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="2d6c8-107">Nákladné pro přidělení/inicializaci.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="2d6c8-108">Představuje nějaký omezený prostředek.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-108">Represent some limited resource.</span></span>
- <span data-ttu-id="2d6c8-109">Používá se prediktivní a často.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-109">Used predictably and frequently.</span></span>

<span data-ttu-id="2d6c8-110">Například ASP.NET Core Framework používá fond objektů na některých místech k opakovanému použití <xref:System.Text.StringBuilder> instancí.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="2d6c8-111">`StringBuilder`přidělí a spravuje vlastní vyrovnávací paměti pro uchovávání znakových dat.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="2d6c8-112">ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich využití přináší výhody výkonu.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="2d6c8-113">Sdružování objektů nezvyšuje výkon vždy:</span><span class="sxs-lookup"><span data-stu-id="2d6c8-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="2d6c8-114">Pokud nejsou náklady na inicializaci objektu vysoké, obvykle je pomalejší pro získání objektu z fondu.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="2d6c8-115">Objekty spravované fondem nejsou nepřidělené, dokud se fond nerozdělí.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="2d6c8-116">Používejte sdružování objektů až po shromáždění údajů o výkonu pomocí reálných scénářů vaší aplikace nebo knihovny.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="2d6c8-117">**Upozornění: `ObjectPool` neimplementuje se `IDisposable` . Nedoporučujeme ho používat s typy, které vyžadují vyřazení.**</span><span class="sxs-lookup"><span data-stu-id="2d6c8-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="2d6c8-118">**Poznámka: fond objectpool neomezuje počet objektů, které budou přiděleny, omezuje počet objektů, které budou zachovány.**</span><span class="sxs-lookup"><span data-stu-id="2d6c8-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="2d6c8-119">Koncepty</span><span class="sxs-lookup"><span data-stu-id="2d6c8-119">Concepts</span></span>

<span data-ttu-id="2d6c8-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>– základní abstrakce fondu objektů.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="2d6c8-121">Slouží k získání a vrácení objektů.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-121">Used to get and return objects.</span></span>

<span data-ttu-id="2d6c8-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-Implementujte tuto metodu pro přizpůsobení způsobu vytvoření objektu a způsobu jeho *resetování* při návratu do fondu.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="2d6c8-123">To lze předat do fondu objektů, který vytvoříte přímo.... ANI</span><span class="sxs-lookup"><span data-stu-id="2d6c8-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="2d6c8-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>slouží jako továrna pro vytváření fondů objektů.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="2d6c8-125">Fond objectpool se dá v aplikaci použít několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="2d6c8-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="2d6c8-126">Vytváří se instance fondu.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-126">Instantiating a pool.</span></span>
* <span data-ttu-id="2d6c8-127">Registrace fondu v rámci [vkládání závislostí](xref:fundamentals/dependency-injection) (di) jako instance.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="2d6c8-128">Registrace `ObjectPoolProvider<>` v di a její použití jako továrny.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="2d6c8-129">Jak používat fond objectpool</span><span class="sxs-lookup"><span data-stu-id="2d6c8-129">How to use ObjectPool</span></span>

<span data-ttu-id="2d6c8-130">Volání <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> pro získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> vrácení objektu.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="2d6c8-131">Neexistuje žádný požadavek, abyste vrátili všechny objekty.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="2d6c8-132">Pokud objekt nevrátíte, bude uvolněn z paměti.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="2d6c8-133">Ukázka fond objectpool</span><span class="sxs-lookup"><span data-stu-id="2d6c8-133">ObjectPool sample</span></span>

<span data-ttu-id="2d6c8-134">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="2d6c8-134">The following code:</span></span>

* <span data-ttu-id="2d6c8-135">Přidá `ObjectPoolProvider` do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="2d6c8-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="2d6c8-136">Přidá a nakonfiguruje `ObjectPool<StringBuilder>` do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="2d6c8-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="2d6c8-137">Přidá `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="2d6c8-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="2d6c8-138">Následující kód implementuje`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="2d6c8-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
