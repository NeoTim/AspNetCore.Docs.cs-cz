---
title: Pomocná značka značky v ASP.NET Core MVC
author: pkellner
description: Naučte se používat pomocníka značky mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: db9e1a968588410f11e5f137dfdd4542df505ebc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662734"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Pomocná značka značky v ASP.NET Core MVC

Od [Petra Kellner](https://peterkellner.net)

Pomocník značek cache poskytuje možnost zlepšit výkon aplikace ASP.NET Core ukládáním obsahu do mezipaměti interního poskytovatele mezipaměti ASP.NET Core.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

Následující kód Razor ukládá do mezipaměti aktuální datum:

```cshtml
<cache>@DateTime.Now</cache>
```

První požadavek na stránku, která obsahuje pomocníka značek, zobrazí aktuální datum. Další požadavky zobrazí hodnotu uloženou v mezipaměti, dokud nevyprší platnost mezipaměti (výchozí 20 minut) nebo dokud nebude datum v mezipaměti vyřazeno z mezipaměti.

## <a name="cache-tag-helper-attributes"></a>Pomocné atributy značek mezipaměti

### <a name="enabled"></a>povolené

| Typ atributu  | Příklady        | Výchozí |
| --------------- | --------------- | ------- |
| Logická hodnota         | `true`, `false` | `true`  |

`enabled` určuje, zda je obsah uzavřený pomocníkem značky mezipaměti uložen do mezipaměti. Výchozí formát je `true`. Pokud je nastaveno na `false`, Vykreslený výstup **není uložen do mezipaměti** .

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

`expires-on` nastaví absolutní datum vypršení platnosti pro položku v mezipaměti.

Následující příklad ukládá do mezipaměti obsah pomocné rutiny značky cache až 5:02 PM, který je 29. ledna 2025:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>platnost vyprší – po

| Typ atributu | Příklad                      | Výchozí    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 minut |

`expires-after` nastaví dobu od první žádosti o dobu ukládání obsahu do mezipaměti.

Příklad:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Modul zobrazení Razor nastaví výchozí hodnotu `expires-after` na 20 minut.

### <a name="expires-sliding"></a>konec platnosti – klouzavé

| Typ atributu | Příklad                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Nastaví čas, kdy by měla být položka mezipaměti vyřazena, pokud k její hodnotě nedojde.

Příklad:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>různé – podle záhlaví

| Typ atributu | Příklady                                    |
| -------------- | ------------------------------------------- |
| Řetězec         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` přijímá seznam hodnot hlaviček oddělených čárkami, které aktivují aktualizaci mezipaměti, když se změní.

Následující příklad sleduje hodnotu hlavičky `User-Agent`. Ukázka ukládá obsah do mezipaměti pro všechny různé `User-Agent` prezentované webovému serveru:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>v závislosti na dotazu

| Typ atributu | Příklady             |
| -------------- | -------------------- |
| Řetězec         | `Make`, `Make,Model` |

`vary-by-query` v řetězci dotazu (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) přijímá seznam <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> oddělených čárkami (), který spustí aktualizaci mezipaměti, když se změní hodnota jakékoli uvedené klíčové změny.

Následující příklad sleduje hodnoty `Make` a `Model`. Ukázka ukládá obsah do mezipaměti pro každou jinou `Make` a `Model` prezentovanému webovému serveru:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>různé – podle směrování

| Typ atributu | Příklady             |
| -------------- | -------------------- |
| Řetězec         | `Make`, `Make,Model` |

`vary-by-route` přijímá seznam názvů parametrů trasy oddělených čárkami, které aktivují aktualizaci mezipaměti při změně hodnoty parametru dat trasy.

Příklad:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index. cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a>různé – podle souborů cookie

| Typ atributu | Příklady                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| Řetězec         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` přijímá seznam názvů souborů cookie oddělených čárkami, které aktivují aktualizaci mezipaměti, když se změní hodnoty souboru cookie.

Následující příklad monitoruje soubor cookie přidružený k ASP.NET Core identity. Při ověření uživatele spustí změna v souboru cookie identity aktualizaci mezipaměti:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>různé – podle uživatele

| Typ atributu  | Příklady        | Výchozí |
| --------------- | --------------- | ------- |
| Logická hodnota         | `true`, `false` | `true`  |

`vary-by-user` určuje, zda je mezipaměť obnovena při změně přihlášeného uživatele (nebo objektu zabezpečení kontextu). Aktuální uživatel je také známý jako objekt zabezpečení kontextu požadavku a lze jej zobrazit v zobrazení Razor odkazem na `@User.Identity.Name`.

Následující příklad sleduje aktuálně přihlášeného uživatele, aby aktivoval aktualizaci mezipaměti:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Použití tohoto atributu udržuje obsah v mezipaměti prostřednictvím cyklu přihlášení a odhlášení. Pokud je hodnota nastavena na `true`, cyklus ověřování zruší platnost mezipaměti pro ověřeného uživatele. Platnost mezipaměti je zrušena, protože při ověření uživatele je vygenerována nová jedinečná hodnota souboru cookie. Mezipaměť je udržována pro anonymní stav, pokud není přítomen žádný soubor cookie nebo vypršela platnost souboru cookie. Pokud uživatel **není ověřený,** zachová se mezipaměť.

### <a name="vary-by"></a>různé – podle

| Typ atributu | Příklad  |
| -------------- | -------- |
| Řetězec         | `@Model` |

`vary-by` umožňuje přizpůsobení dat uložených v mezipaměti. Pokud se změní objekt odkazovaný hodnotou řetězce atributu, je aktualizován obsah pomocné rutiny značky cache. K tomuto atributu jsou často přiřazeny řetězcové zřetězení hodnot modelu. To má za následek situaci, kdy aktualizace jakékoli zřetězené hodnoty neověřuje mezipaměť.

Následující příklad předpokládá, že metoda kontroleru vykreslování zobrazení sečte celočíselnou hodnotu dvou parametrů trasy, `myParam1` a `myParam2`a vrátí součet jako jednu vlastnost modelu. Při změně tohoto součtu se obsah pomocné rutiny značky mezipaměti vykresluje a ukládá do mezipaměti znovu.  

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

*Index. cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Typ atributu      | Příklady                               | Výchozí  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` poskytuje pokyny k vyřazení mezipaměti integrovanému poskytovateli mezipaměti. Webový server vyřadí `Low` položky mezipaměti jako první, když dojde k přetížení paměti.

Příklad:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Atribut `priority` nezaručuje konkrétní úroveň uchovávání mezipaměti. `CacheItemPriority` je pouze návrh. Nastavením tohoto atributu na `NeverRemove` nezaručí, že položky uložené v mezipaměti jsou vždy zachované. Další informace najdete v tématech v části [Další zdroje](#additional-resources) informací.

Pomocník značek cache je závislý na [službě mezipaměť paměti](xref:performance/caching/memory). Pomocník značek cache přidá službu, pokud nebyla přidána.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
