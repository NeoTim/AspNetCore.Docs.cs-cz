---
title: Pomocná rutina značek distribuované mezipaměti v ASP.NET Core
author: pkellner
description: Naučte se používat pomocníka značek distribuované mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: e5100d7244600358186b653073990985f48434a7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809052"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Pomocná rutina značek distribuované mezipaměti v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net) a [Luke Latham](https://github.com/guardrex)

Pomocný pomocník značek distribuované mezipaměti umožňuje významně zvýšit výkon aplikace ASP.NET Core uložením jejího obsahu do mezipaměti distribuovaných mezipamětí.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

Pomocná rutina značky distribuované mezipaměti dědí ze stejné základní třídy jako pomocníka značky mezipaměti. Všechny pomocné atributy [značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) jsou k dispozici pro pomocnou nápovědu k distribuovaným značkám.

Pomocný pomocník značek distribuované mezipaměti používá [vkládání konstruktoru](xref:fundamentals/dependency-injection#constructor-injection-behavior). Rozhraní <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> je předáno do konstruktoru pomocné rutiny značky distribuované mezipaměti. Pokud se v `Startup.ConfigureServices` (*Startup.cs*) nevytvoří žádná konkrétní implementace `IDistributedCache`, použije pomocník značek distribuované mezipaměti stejného poskytovatele v paměti pro ukládání dat uložených v mezipaměti jako [pomocníka značky mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

## <a name="distributed-cache-tag-helper-attributes"></a>Pomocné atributy značek distribuované mezipaměti

### <a name="attributes-shared-with-the-cache-tag-helper"></a>Atributy sdílené pomocí pomocníka značky mezipaměti

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

Pomocná rutina značky distribuované mezipaměti dědí ze stejné třídy jako pomocník značek cache. Popisy těchto atributů naleznete v tématu [Podpora značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="name"></a>name

| Typ atributu | Příklad                               |
| -------------- | ------------------------------------- |
| String         | `my-distributed-cache-unique-key-101` |

`name` se vyžaduje. Atribut `name` se používá jako klíč pro každou uloženou instanci mezipaměti. Na rozdíl od pomocníka značky mezipaměti, který přiřazuje klíč mezipaměti ke každé instanci na základě názvu a umístění stránky Razor na stránce Razor, pomocná rutina značky distribuované mezipaměti je založena pouze na svém klíči v atributu `name`.

Příklad:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Implementace IDistributedCache pomocníka značek distribuované mezipaměti

Existují dvě implementace <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> integrovány do ASP.NET Core. Jedna je založena na SQL Server a druhá je založena na Redis. K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Podrobnosti o těchto implementacích najdete na adrese <xref:performance/caching/distributed>. Obě implementace zahrnují nastavení instance `IDistributedCache` v `Startup`.

Nejsou k dispozici žádné atributy značek přímo spojené s použitím žádné konkrétní implementace `IDistributedCache`.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
