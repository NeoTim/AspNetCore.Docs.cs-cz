---
title: Pomocná rutina značek v ASP.NET Core MVC do mezipaměti
author: pkellner
description: Další informace o použití pomocné rutiny značky mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 0273a9805dd5db5450f57dcf3fd4d952308df074
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856215"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Pomocná rutina značek v ASP.NET Core MVC do mezipaměti

Podle [Peter Kellner](https://peterkellner.net) a [Luke Latham](https://github.com/guardrex) 

Pomocná rutina značek mezipaměti umožňuje zlepšit výkon vaší aplikace ASP.NET Core pomocí ukládání do mezipaměti obsah k poskytovateli vnitřní mezipaměti ASP.NET Core.

Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

Následující kód Razor ukládá do mezipaměti na aktuální datum:

```cshtml
<cache>@DateTime.Now</cache>
```

První požadavek na stránku, který obsahuje pomocné rutiny značky zobrazuje aktuální datum. Další požadavky zobrazit hodnotu uloženou v mezipaměti, dokud mezipaměti vyprší (výchozí nastavení 20 minut) nebo data uložená v mezipaměti je odstraněn z mezipaměti.

## <a name="cache-tag-helper-attributes"></a>Atributy pomocné rutiny značky do mezipaměti

### <a name="enabled"></a>enabled

| Typ atributu  | Příklady        | Výchozí |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`enabled` Určuje, pokud obsah uzavřená v pomocné rutiny značky mezipaměti se ukládá do mezipaměti. Výchozí hodnota je `true`. Pokud hodnotu `false`, vykresleného výstupu je **není** uložené v mezipaměti.

Příklad:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| Typ atributu   | Příklad                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` Nastaví data absolutní vypršení platnosti položky v mezipaměti.

Následující příklad ukládá do mezipaměti obsah pomocné rutiny značky mezipaměti až do 17:02:00 na 29. ledna 2025:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| Typ atributu | Příklad                      | Výchozí    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 minut |

`expires-after` Nastaví dobu od prvního požadavku pro ukládání do mezipaměti obsah.

Příklad:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Nastaví výchozí zobrazovací modul Razor `expires-after` hodnota 20 minut.

### <a name="expires-sliding"></a>expires-sliding

| Typ atributu | Příklad                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Nastaví čas, který se položka mezipaměti by měla být vyřazena, pokud její hodnota nepřistupovalo.

Příklad:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>měnit podle hlavičky

| Typ atributu | Příklady                                    |
| -------------- | ------------------------------------------- |
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` přijímá čárkami oddělený seznam hodnot hlavičky, které aktivují aktualizace mezipaměti při změnách.

Následující příklad monitoruje hodnota hlavičky `User-Agent`. V příkladu ukládá do mezipaměti obsah pro každé jiné `User-Agent` uvedené na webový server:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>se liší podle dotazu

| Typ atributu | Příklady             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query` přijímá čárkami oddělený seznam <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> v řetězci dotazu (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), který aktivuje aktualizace mezipaměti, když zadejte hodnotu kterékoli uvedené změny klíče.

Následující příklad monitoruje hodnoty `Make` a `Model`. V příkladu ukládá do mezipaměti obsah pro každé jiné `Make` a `Model` uvedené na webový server:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>se liší podle postupu

| Typ atributu | Příklady             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route` přijímá čárkami oddělený seznam názvů parametr trasy, které aktivovat aktualizaci mezipaměti při změně hodnoty parametru dat trasy.

Příklad:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index.cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a>se liší podle cookie

| Typ atributu | Příklady                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` přijímá čárkami oddělený seznam názvů souborů cookie, které aktivují aktualizace mezipaměti, pokud se změní hodnoty souboru cookie.

Následující příklad monitoruje souboru cookie přidruženého k ASP.NET Core Identity. Když je uživatel ověřen, změny v souboru cookie Identity aktivuje aktualizace mezipaměti:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>se liší podle uživatele

| Typ atributu  | Příklady        | Výchozí |
| --------------- | --------------- | ------- |
| Boolean         | `true`, `false` | `true`  |

`vary-by-user` Určuje, zda mezipaměti resetuje při změně přihlášeného uživatele (nebo objekt kontextu zabezpečení). Aktuální uživatel se také označuje jako instanční objekt kontextu požadavku a lze je zobrazit v zobrazení Razor odkazem `@User.Identity.Name`.

Následující příklad monitoruje aktuálně přihlášeného uživatele k aktivaci aktualizace mezipaměti:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Pomocí tohoto atributu udržuje cyklu obsah v mezipaměti, prostřednictvím u přihlášení a odhlášení. Pokud je hodnota nastavená na `true`, ověřování cyklu zruší platnost mezipaměti pro ověřeného uživatele. Mezipaměť je neplatná, protože nová hodnota jedinečný soubor cookie se vygeneruje, když je ověření uživatele. Mezipaměť je zachován z důvodu anonymní stavu Pokud je k dispozici žádný soubor cookie nebo vypršela platnost souboru cookie. Pokud je uživatel **není** ověření do mezipaměti se zachová.

### <a name="vary-by"></a>se liší podle

| Typ atributu | Příklad  |
| -------------- | -------- |
| String         | `@Model` |

`vary-by` umožňuje přizpůsobení jaká data se uloží do mezipaměti. Při aktualizaci objektu, odkazuje atribut řetězec hodnotu změny, obsah pomocné rutiny značky mezipaměti. Často zřetězení hodnoty modelu jsou přiřazeny k tomuto atributu. Efektivně výsledkem scénář, ve kterém aktualizaci zřetězených hodnot zruší platnost mezipaměti.

V následujícím příkladu se předpokládá metoda kontroleru vykreslení zobrazení součtů celočíselnou hodnotu dva parametry trasy `myParam1` a `myParam2`a vrátí součet jako vlastnost jednoho modelu. Při změně tohoto součtu obsah pomocné rutiny značky mezipaměti je vykreslen a uložili do mezipaměti znovu.  

Akce:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index.cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priorita

| Typ atributu      | Příklady                               | Výchozí  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` Obsahuje pokyny k vyřazení mezipaměti integrovanou mezipaměť na poskytovatele. Webový server vyloučí `Low` nejprve mezipaměti položky, když je přetížena paměť.

Příklad:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

`priority` Atribut nezaručuje konkrétní úroveň mezipaměti dobu uchování. `CacheItemPriority` je jenom návrh. Nastavení tohoto atributu na `NeverRemove` nezaručuje, že se vždy zachovají položek v mezipaměti. Najdete v tématech [další prostředky](#additional-resources) části Další informace.

Pomocná rutina značek mezipaměti je závislá na [služby mezipaměti paměti](xref:performance/caching/memory). Pomocná rutina značek mezipaměti přidá službu, pokud nebyla přidána.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
