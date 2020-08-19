---
title: Pomocná rutina značek distribuované mezipaměti v ASP.NET Core
author: pkellner
description: Naučte se používat pomocníka značek distribuované mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 67e5b7ef09525063da6e6b7dfce6fd084d279869
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633900"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="bd851-103">Pomocná rutina značek distribuované mezipaměti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd851-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="bd851-104">Od [Petra Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="bd851-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="bd851-105">Pomocný pomocník značek distribuované mezipaměti umožňuje významně zvýšit výkon aplikace ASP.NET Core uložením jejího obsahu do mezipaměti distribuovaných mezipamětí.</span><span class="sxs-lookup"><span data-stu-id="bd851-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="bd851-106">Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .</span><span class="sxs-lookup"><span data-stu-id="bd851-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="bd851-107">Pomocná rutina značky distribuované mezipaměti dědí ze stejné základní třídy jako pomocníka značky mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="bd851-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="bd851-108">Všechny pomocné atributy [značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) jsou k dispozici pro pomocnou nápovědu k distribuovaným značkám.</span><span class="sxs-lookup"><span data-stu-id="bd851-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="bd851-109">Pomocný pomocník značek distribuované mezipaměti používá [vkládání konstruktoru](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="bd851-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="bd851-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Rozhraní je předáno konstruktoru pomocné značky distribuované mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="bd851-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="bd851-111">Pokud `IDistributedCache` v (Startup.cs) není vytvořena žádná konkrétní implementace `Startup.ConfigureServices` , použije pomocník značek distribuované mezipaměti stejného poskytovatele v paměti pro ukládání dat uložených v mezipaměti jako [pomocníka značky mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="bd851-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="bd851-112">Pomocné atributy značek distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="bd851-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="bd851-113">Atributy sdílené pomocí pomocníka značky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="bd851-113">Attributes shared with the Cache Tag Helper</span></span>

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

<span data-ttu-id="bd851-114">Pomocná rutina značky distribuované mezipaměti dědí ze stejné třídy jako pomocník značek cache.</span><span class="sxs-lookup"><span data-stu-id="bd851-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="bd851-115">Popisy těchto atributů naleznete v tématu [Podpora značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bd851-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="bd851-116">name</span><span class="sxs-lookup"><span data-stu-id="bd851-116">name</span></span>

| <span data-ttu-id="bd851-117">Typ atributu</span><span class="sxs-lookup"><span data-stu-id="bd851-117">Attribute Type</span></span> | <span data-ttu-id="bd851-118">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd851-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="bd851-119">String</span><span class="sxs-lookup"><span data-stu-id="bd851-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="bd851-120">`name` je povinné.</span><span class="sxs-lookup"><span data-stu-id="bd851-120">`name` is required.</span></span> <span data-ttu-id="bd851-121">`name`Atribut se používá jako klíč pro každou uloženou instanci mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="bd851-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="bd851-122">Na rozdíl od pomocníka značky mezipaměti, který přiřazuje klíč mezipaměti ke každé instanci na základě Razor názvu a umístění stránky na Razor stránce, pomocník značek distribuované mezipaměti pouze zakládá svůj klíč na atribut `name` .</span><span class="sxs-lookup"><span data-stu-id="bd851-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="bd851-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="bd851-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="bd851-124">Implementace IDistributedCache pomocníka značek distribuované mezipaměti</span><span class="sxs-lookup"><span data-stu-id="bd851-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="bd851-125">Existují dvě implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> integrované v systému ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bd851-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="bd851-126">Jedna je založena na SQL Server a druhá je založena na Redis.</span><span class="sxs-lookup"><span data-stu-id="bd851-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="bd851-127">K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="bd851-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="bd851-128">Podrobnosti o těchto implementacích najdete na adrese <xref:performance/caching/distributed> .</span><span class="sxs-lookup"><span data-stu-id="bd851-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="bd851-129">Obě implementace zahrnují nastavení instance `IDistributedCache` v `Startup` .</span><span class="sxs-lookup"><span data-stu-id="bd851-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="bd851-130">Nejsou k dispozici žádné atributy značek přímo spojené s použitím žádné konkrétní implementace `IDistributedCache` .</span><span class="sxs-lookup"><span data-stu-id="bd851-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd851-131">Další materiály</span><span class="sxs-lookup"><span data-stu-id="bd851-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
