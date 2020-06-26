---
title: Pomocná rutina značek distribuované mezipaměti v ASP.NET Core
author: pkellner
description: Naučte se používat pomocníka značek distribuované mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 4b8e393542c56502a825000773bbf714d91e4128
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399228"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="498f0-103">Pomocná rutina značek distribuované mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="498f0-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="498f0-104">Od [Petra Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="498f0-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="498f0-105">Pomocný pomocník značek distribuované mezipaměti umožňuje významně zvýšit výkon aplikace ASP.NET Core uložením jejího obsahu do mezipaměti distribuovaných mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="498f0-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="498f0-106">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="498f0-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="498f0-107">Pomocná rutina značky distribuované mezipaměti dědí ze stejné základní třídy jako pomocníka značky mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="498f0-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="498f0-108">Všechny pomocné atributy [značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) jsou k dispozici pro pomocnou nápovědu k distribuovaným značkám.</span><span class="sxs-lookup"><span data-stu-id="498f0-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="498f0-109">Pomocný pomocník značek distribuované mezipaměti používá [vkládání konstruktoru](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="498f0-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="498f0-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní je předáno konstruktoru pomocné značky distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="498f0-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="498f0-111">Pokud `IDistributedCache` v (Startup.cs) není vytvořena žádná konkrétní implementace `Startup.ConfigureServices` , použije pomocník značek distribuované mezipaměti stejného poskytovatele v paměti pro ukládání dat uložených v mezipaměti jako [pomocníka značky mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="498f0-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="498f0-112">Pomocné atributy značek distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="498f0-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="498f0-113">Atributy sdílené pomocí pomocníka značky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="498f0-113">Attributes shared with the Cache Tag Helper</span></span>

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

<span data-ttu-id="498f0-114">Pomocná rutina značky distribuované mezipaměti dědí ze stejné třídy jako pomocník značek cache.</span><span class="sxs-lookup"><span data-stu-id="498f0-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="498f0-115">Popisy těchto atributů naleznete v tématu [Podpora značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="498f0-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="498f0-116">name</span><span class="sxs-lookup"><span data-stu-id="498f0-116">name</span></span>

| <span data-ttu-id="498f0-117">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="498f0-117">Attribute Type</span></span> | <span data-ttu-id="498f0-118">Příklad</span><span class="sxs-lookup"><span data-stu-id="498f0-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="498f0-119">Řetězec</span><span class="sxs-lookup"><span data-stu-id="498f0-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="498f0-120">`name` je povinné.</span><span class="sxs-lookup"><span data-stu-id="498f0-120">`name` is required.</span></span> <span data-ttu-id="498f0-121">`name`Atribut se používá jako klíč pro každou uloženou instanci mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="498f0-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="498f0-122">Na rozdíl od pomocníka značky mezipaměti, který přiřazuje klíč mezipaměti ke každé instanci na základě Razor názvu a umístění stránky na Razor stránce, pomocník značek distribuované mezipaměti pouze zakládá svůj klíč na atribut `name` .</span><span class="sxs-lookup"><span data-stu-id="498f0-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="498f0-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="498f0-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="498f0-124">Implementace IDistributedCache pomocníka značek distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="498f0-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="498f0-125">Existují dvě implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> integrované v systému ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="498f0-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="498f0-126">Jedna je založena na SQL Server a druhá je založena na Redis.</span><span class="sxs-lookup"><span data-stu-id="498f0-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="498f0-127">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="498f0-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="498f0-128">Podrobnosti o těchto implementacích najdete na adrese <xref:performance/caching/distributed> .</span><span class="sxs-lookup"><span data-stu-id="498f0-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="498f0-129">Obě implementace zahrnují nastavení instance `IDistributedCache` v `Startup` .</span><span class="sxs-lookup"><span data-stu-id="498f0-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="498f0-130">Nejsou k dispozici žádné atributy značek přímo spojené s použitím žádné konkrétní implementace `IDistributedCache` .</span><span class="sxs-lookup"><span data-stu-id="498f0-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="498f0-131">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="498f0-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
