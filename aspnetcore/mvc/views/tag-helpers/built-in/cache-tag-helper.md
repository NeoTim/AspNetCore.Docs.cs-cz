---
title: Pomocná značka značky v ASP.NET Core MVC
author: pkellner
description: Naučte se používat pomocníka značky mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: b1cab7ab8b491529ee4208d92fb30082be795eda
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635057"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Pomocná značka značky v ASP.NET Core MVC

Od [Petra Kellner](https://peterkellner.net)

Pomocník značek cache poskytuje možnost zlepšit výkon aplikace ASP.NET Core ukládáním obsahu do mezipaměti interního poskytovatele mezipaměti ASP.NET Core.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .

Následující Razor kód ukládá do mezipaměti aktuální datum:

```cshtml
<cache>@DateTime.Now</cache>
```

První požadavek na stránku, která obsahuje pomocníka značek, zobrazí aktuální datum. Další požadavky zobrazí hodnotu uloženou v mezipaměti, dokud nevyprší platnost mezipaměti (výchozí 20 minut) nebo dokud nebude datum v mezipaměti vyřazeno z mezipaměti.

## <a name="cache-tag-helper-attributes"></a>Pomocné atributy značek mezipaměti

### <a name="enabled"></a>enabled

| Typ atributu  | Příklady        | Výchozí |
| --------------- | --------------- | ------- |
| Logická hodnota         | `true`, `false` | `true`  |

`enabled` Určuje, zda je obsah uzavřený pomocníkem značky mezipaměti uložen do mezipaměti. Výchozí formát je `true`. Pokud je nastaveno na `false` , Vykreslený výstup **není** uložen v mezipaměti.

Příklad:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>vyprší – dne

| Typ atributu   | Příklad                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` Nastaví absolutní datum vypršení platnosti pro položku uloženou v mezipaměti.

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

RazorModul zobrazení nastaví výchozí `expires-after` hodnotu na 20 minut.

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
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` přijímá seznam hodnot hlaviček oddělených čárkami, které aktivují aktualizaci mezipaměti, když se změní.

V následujícím příkladu je sledována hodnota hlavičky `User-Agent` . Příklad ukládá obsah do mezipaměti pro všechny různé `User-Agent` prezentované webovému serveru:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>v závislosti na dotazu

| Typ atributu | Příklady             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query` přijme <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> v řetězci dotazu () seznam oddělený čárkami ( <xref:Microsoft.AspNetCore.Http.HttpRequest.Query*> ), který aktivuje aktualizaci mezipaměti, když hodnota jakékoli v uvedených klíčových změnách.

Následující příklad sleduje hodnoty `Make` a `Model` . Tento příklad ukládá obsah do mezipaměti pro všechny různé `Make` a `Model` prezentované webovému serveru:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>různé – podle směrování

| Typ atributu | Příklady             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route` přijme seznam názvů parametrů trasy oddělených čárkami, které aktivují aktualizaci mezipaměti při změně hodnoty parametru dat trasy.

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

### <a name="vary-by-no-loccookie"></a>různé – podlecookie

| Typ atributu | Příklady                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` přijme seznam názvů oddělených čárkami cookie , které při změně hodnot spouštějí aktualizaci mezipaměti cookie .

Následující příklad sleduje cookie přidruženou k ASP.NET Core Identity . Když je uživatel ověřený, změna v Identity cookie aktivační události spustí aktualizaci mezipaměti:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>různé – podle uživatele

| Typ atributu  | Příklady        | Výchozí |
| --------------- | --------------- | ------- |
| Logická hodnota         | `true`, `false` | `true`  |

`vary-by-user` Určuje, zda je mezipaměť obnovena při změně přihlášeného uživatele (nebo objektu zabezpečení kontextu). Aktuální uživatel je také známý jako objekt zabezpečení kontextu požadavku a lze jej zobrazit v Razor zobrazení odkazem `@User.Identity.Name` .

Následující příklad sleduje aktuálně přihlášeného uživatele, aby aktivoval aktualizaci mezipaměti:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Použití tohoto atributu udržuje obsah v mezipaměti prostřednictvím cyklu přihlášení a odhlášení. Pokud je hodnota nastavena na `true` , cyklus ověřování neověřuje mezipaměť pro ověřeného uživatele. Platnost mezipaměti je zrušena, protože cookie při ověření uživatele je vygenerována nová jedinečná hodnota. Mezipaměť je udržována pro anonymní stav, pokud cookie není přítomna nebo cookie vypršela její platnost. Pokud uživatel **není ověřený,** zachová se mezipaměť.

### <a name="vary-by"></a>různé – podle

| Typ atributu | Příklad  |
| -------------- | -------- |
| String         | `@Model` |

`vary-by` umožňuje přizpůsobení dat uložených v mezipaměti. Pokud se změní objekt odkazovaný hodnotou řetězce atributu, je aktualizován obsah pomocné rutiny značky cache. K tomuto atributu jsou často přiřazeny řetězcové zřetězení hodnot modelu. To má za následek situaci, kdy aktualizace jakékoli zřetězené hodnoty neověřuje mezipaměť.

Následující příklad předpokládá, že metoda kontroleru vykreslování zobrazení sečte celočíselnou hodnotu dvou parametrů trasy `myParam1` a `myParam2` vrátí součet jako vlastnost jednoho modelu. Při změně tohoto součtu se obsah pomocné rutiny značky mezipaměti vykresluje a ukládá do mezipaměti znovu.  

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

### <a name="priority"></a>upřednostněn

| Typ atributu      | Příklady                               | Výchozí  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` poskytuje pokyny k vyřazení mezipaměti do integrovaného poskytovatele mezipaměti. Webový server `Low` nejprve vyřadí položky mezipaměti, když dojde k přetížení paměti.

Příklad:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

`priority`Atribut nezaručuje konkrétní úroveň uchovávání mezipaměti. `CacheItemPriority` je pouze návrh. Nastavením tohoto atributu se `NeverRemove` nezaručí, že položky uložené v mezipaměti jsou vždycky zachované. Další informace najdete v tématech v části [Další zdroje](#additional-resources) informací.

Pomocník značek cache je závislý na [službě mezipaměť paměti](xref:performance/caching/memory). Pomocník značek cache přidá službu, pokud nebyla přidána.

## <a name="additional-resources"></a>Další materiály

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
