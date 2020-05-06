---
title: Pomocná rutina značek distribuované mezipaměti v ASP.NET Core
author: pkellner
description: Naučte se používat pomocníka značek distribuované mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: df1daa68a3e18f7aad4507ce9526d76ff6a2114d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773913"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Pomocná rutina značek distribuované mezipaměti v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net)

Pomocný pomocník značek distribuované mezipaměti umožňuje významně zvýšit výkon aplikace ASP.NET Core uložením jejího obsahu do mezipaměti distribuovaných mezipamětí.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

Pomocná rutina značky distribuované mezipaměti dědí ze stejné základní třídy jako pomocníka značky mezipaměti. Všechny pomocné atributy [značek mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) jsou k dispozici pro pomocnou nápovědu k distribuovaným značkám.

Pomocný pomocník značek distribuované mezipaměti používá [vkládání konstruktoru](xref:fundamentals/dependency-injection#constructor-injection-behavior). <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Rozhraní je předáno konstruktoru pomocné značky distribuované mezipaměti. Pokud `Startup.ConfigureServices` v (*Startup.cs*) `IDistributedCache` není vytvořena žádná konkrétní implementace, použije pomocník značek distribuované mezipaměti stejného poskytovatele v paměti pro ukládání dat uložených v mezipaměti jako [pomocníka značky mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

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

### <a name="name"></a>jméno

| Typ atributu | Příklad                               |
| -------------- | ------------------------------------- |
| Řetězec         | `my-distributed-cache-unique-key-101` |

`name` je povinné. `name` Atribut se používá jako klíč pro každou uloženou instanci mezipaměti. Na rozdíl od pomocníka značky mezipaměti, který přiřazuje klíč mezipaměti ke každé instanci na základě Razor názvu a umístění stránky na Razor stránce, pomocník značek distribuované mezipaměti pouze zakládá svůj klíč na atribut `name`.

Příklad:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Implementace IDistributedCache pomocníka značek distribuované mezipaměti

Existují dvě implementace integrované v <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> systému ASP.NET Core. Jedna je založena na SQL Server a druhá je založena na Redis. K dispozici jsou také implementace třetích stran, například [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Podrobnosti o těchto implementacích najdete na adrese <xref:performance/caching/distributed>. Obě implementace zahrnují nastavení instance `IDistributedCache` v. `Startup`

Nejsou k dispozici žádné atributy značek přímo spojené s použitím žádné konkrétní `IDistributedCache`implementace.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
