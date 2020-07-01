---
title: Opakované použití objektu s fond objectpool v ASP.NET Core
author: rick-anderson
description: Tipy pro zvýšení výkonu v aplikacích ASP.NET Core s využitím fond objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 9df7f370eb550172493478bcd8d94a9541926fec
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793562"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="8ff3d-103">Opakované použití objektu s fond objectpool v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ff3d-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="8ff3d-104">[Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)a [Günther Foidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="8ff3d-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="8ff3d-105"><xref:Microsoft.Extensions.ObjectPool>je součástí infrastruktury ASP.NET Core, která podporuje udržování skupiny objektů v paměti pro opakované použití, nikoli pro uvolnění objektů do paměti.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="8ff3d-106">Fond objektů můžete chtít použít, pokud jsou objekty, které jsou spravovány:</span><span class="sxs-lookup"><span data-stu-id="8ff3d-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="8ff3d-107">Nákladné pro přidělení/inicializaci.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="8ff3d-108">Představuje nějaký omezený prostředek.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-108">Represent some limited resource.</span></span>
- <span data-ttu-id="8ff3d-109">Používá se prediktivní a často.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-109">Used predictably and frequently.</span></span>

<span data-ttu-id="8ff3d-110">Například ASP.NET Core Framework používá fond objektů na některých místech k opakovanému použití <xref:System.Text.StringBuilder> instancí.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="8ff3d-111">`StringBuilder`přidělí a spravuje vlastní vyrovnávací paměti pro uchovávání znakových dat.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="8ff3d-112">ASP.NET Core pravidelně používá `StringBuilder` k implementaci funkcí a jejich využití přináší výhody výkonu.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="8ff3d-113">Sdružování objektů nezvyšuje výkon vždy:</span><span class="sxs-lookup"><span data-stu-id="8ff3d-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="8ff3d-114">Pokud nejsou náklady na inicializaci objektu vysoké, obvykle je pomalejší pro získání objektu z fondu.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="8ff3d-115">Objekty spravované fondem nejsou nepřidělené, dokud se fond nerozdělí.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="8ff3d-116">Používejte sdružování objektů až po shromáždění údajů o výkonu pomocí reálných scénářů vaší aplikace nebo knihovny.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="8ff3d-117">**Upozornění: `ObjectPool` neimplementuje se `IDisposable` . Nedoporučujeme ho používat s typy, které vyžadují vyřazení.**</span><span class="sxs-lookup"><span data-stu-id="8ff3d-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="8ff3d-118">`ObjectPool`v ASP.NET Core 3,0 a novějších verzích podporuje `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="8ff3d-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="8ff3d-119">**Poznámka: fond objectpool neomezuje počet objektů, které budou přiděleny, omezuje počet objektů, které budou zachovány.**</span><span class="sxs-lookup"><span data-stu-id="8ff3d-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="8ff3d-120">Koncepty</span><span class="sxs-lookup"><span data-stu-id="8ff3d-120">Concepts</span></span>

<span data-ttu-id="8ff3d-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>– základní abstrakce fondu objektů.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="8ff3d-122">Slouží k získání a vrácení objektů.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-122">Used to get and return objects.</span></span>

<span data-ttu-id="8ff3d-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-Implementujte tuto metodu pro přizpůsobení způsobu vytvoření objektu a způsobu jeho *resetování* při návratu do fondu.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="8ff3d-124">To lze předat do fondu objektů, který vytvoříte přímo.... ANI</span><span class="sxs-lookup"><span data-stu-id="8ff3d-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="8ff3d-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>slouží jako továrna pro vytváření fondů objektů.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="8ff3d-126">Fond objectpool se dá v aplikaci použít několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="8ff3d-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="8ff3d-127">Vytváří se instance fondu.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-127">Instantiating a pool.</span></span>
* <span data-ttu-id="8ff3d-128">Registrace fondu v rámci [vkládání závislostí](xref:fundamentals/dependency-injection) (di) jako instance.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="8ff3d-129">Registrace `ObjectPoolProvider<>` v di a její použití jako továrny.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="8ff3d-130">Jak používat fond objectpool</span><span class="sxs-lookup"><span data-stu-id="8ff3d-130">How to use ObjectPool</span></span>

<span data-ttu-id="8ff3d-131">Volání <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> pro získání objektu a <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> vrácení objektu.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="8ff3d-132">Neexistuje žádný požadavek, abyste vrátili všechny objekty.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="8ff3d-133">Pokud objekt nevrátíte, bude uvolněn z paměti.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="8ff3d-134">Při <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> použití a `T` implementuje `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="8ff3d-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="8ff3d-135">Položky, které ***nejsou vráceny do*** fondu, budou uvolněny.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-135">Items that are ***not*** returned to the pool will be disposed.</span></span>
* <span data-ttu-id="8ff3d-136">Když je fond uvolněný pomocí DI, všechny položky ve fondu se vyřadí.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-136">When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="8ff3d-137">Poznámka: po odstranění fondu:</span><span class="sxs-lookup"><span data-stu-id="8ff3d-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="8ff3d-138">Volání `Get` vyvolá výjimku `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="8ff3d-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="8ff3d-139">`return`odstraní danou položku.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="8ff3d-140">Ukázka fond objectpool</span><span class="sxs-lookup"><span data-stu-id="8ff3d-140">ObjectPool sample</span></span>

<span data-ttu-id="8ff3d-141">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="8ff3d-141">The following code:</span></span>

* <span data-ttu-id="8ff3d-142">Přidá `ObjectPoolProvider` do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="8ff3d-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="8ff3d-143">Přidá a nakonfiguruje `ObjectPool<StringBuilder>` do kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="8ff3d-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="8ff3d-144">Přidá `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="8ff3d-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="8ff3d-145">Následující kód implementuje`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="8ff3d-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
