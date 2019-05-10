---
title: Distribuovaná mezipaměť pomocná rutina značek v ASP.NET Core
author: pkellner
description: Další informace o použití distribuované mezipaměti pomocná rutina značek v.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: a5b33451a763c297c6d7885855a321c43435abb4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902625"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="25238-103">Distribuovaná mezipaměť pomocná rutina značek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25238-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="25238-104">Podle [Peter Kellner](http://peterkellner.net) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25238-104">By [Peter Kellner](http://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="25238-105">Pomocná rutina značek distribuované mezipaměti umožňuje výrazně zlepšit výkon vaší aplikace ASP.NET Core pomocí ukládání do mezipaměti obsah ke zdroji distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="25238-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="25238-106">Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="25238-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="25238-107">Distribuované mezipaměti pomocná rutina značek v dědí ze základní třídy stejné jako pomocné rutiny značky mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="25238-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="25238-108">Všechny [pomocná rutina značek v mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) atributy jsou k dispozici pomocné rutiny značky distribuován.</span><span class="sxs-lookup"><span data-stu-id="25238-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="25238-109">Používá distribuované mezipaměti pomocná rutina značek v [konstruktor vkládání](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="25238-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="25238-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Rozhraní je předána do konstruktoru distribuované mezipaměti pomocné rutiny značky společnosti.</span><span class="sxs-lookup"><span data-stu-id="25238-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="25238-111">Pokud žádné konkrétní implementaci `IDistributedCache` se vytvoří v `Startup.ConfigureServices` (*Startup.cs*), distribuované mezipaměti pomocná rutina značek v používá stejný zprostředkovatel v paměti pro ukládání dat uložených v mezipaměti, jako [pomocná rutina značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="25238-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="25238-112">Distribuovaná mezipaměť atributy pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="25238-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="25238-113">Atributy dostaly pomocné rutiny značky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="25238-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="25238-114">Distribuované mezipaměti pomocná rutina značek v dědí ze stejné třídy jako pomocná rutina značek v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="25238-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="25238-115">Popis těchto atributů najdete v článku [pomocná rutina značek v mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="25238-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="25238-116">name</span><span class="sxs-lookup"><span data-stu-id="25238-116">name</span></span>

| <span data-ttu-id="25238-117">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="25238-117">Attribute Type</span></span> | <span data-ttu-id="25238-118">Příklad</span><span class="sxs-lookup"><span data-stu-id="25238-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="25238-119">String</span><span class="sxs-lookup"><span data-stu-id="25238-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="25238-120">`name` je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="25238-120">`name` is required.</span></span> <span data-ttu-id="25238-121">`name` Atribut se používá jako klíč pro každou instanci uložených mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="25238-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="25238-122">Na rozdíl od mezipaměti pomocné rutiny značky, který přiřazuje klíč mezipaměti ke každé instanci, na základě názvu stránky Razor a umístění v stránky Razor, distribuované mezipaměti pomocná rutina značek v pouze základů svůj klíč atributu `name`.</span><span class="sxs-lookup"><span data-stu-id="25238-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="25238-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="25238-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="25238-124">Implementace distribuované mezipaměti IDistributedCache pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="25238-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="25238-125">Existují dvě implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> součástí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25238-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="25238-126">Jeden je založena na SQL Server, a druhý je založen na Redis.</span><span class="sxs-lookup"><span data-stu-id="25238-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="25238-127">Podrobnosti o těchto implementacích najdete <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="25238-127">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="25238-128">Obou implementacích zahrnují nastavení instance objektu `IDistributedCache` v `Startup`.</span><span class="sxs-lookup"><span data-stu-id="25238-128">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="25238-129">Neexistují žádné atributy značky konkrétně spojených s použitím žádnou konkrétní implementaci `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="25238-129">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25238-130">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="25238-130">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
