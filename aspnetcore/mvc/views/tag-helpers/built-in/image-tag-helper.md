---
title: Pomocník značek obrázku v ASP.NET Core
author: pkellner
description: Ukazuje, jak pracovat s pomocníkem značek obrázku.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: e1e3566f09c44d70650fa1f21e1921e76e46d384
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777407"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Pomocník značek obrázku v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net)

Pomocník značek image rozšiřuje `<img>` značku tak, aby poskytoval busting chování mezipaměti pro soubory statických imagí.

Řetězec cache-busting je jedinečná hodnota, která představuje hodnotu hash souboru statické bitové kopie připojené k adrese URL prostředku. Jedinečný řetězec vyzve klienty (a některé proxy) k opětovnému načtení bitové kopie z hostitelského webového serveru a nikoli z mezipaměti klienta.

Pokud je zdroj Image (`src`) statickým souborem na hostitelském webovém serveru:

* Jedinečný řetězec busting cache je připojen jako parametr dotazu pro zdroj obrázku.
* Pokud se soubor na hostitelském webovém serveru změní, je vygenerována jedinečná adresa URL požadavku, která obsahuje aktualizovaný parametr žádosti.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Pomocné atributy značek obrázku

### <a name="src"></a>src

Chcete-li aktivovat pomocníka značky image `src` , je atribut požadován pro `<img>` element.

Zdroj obrázku (`src`) musí odkazovat na fyzický statický soubor na serveru. Pokud `src` je to vzdálený identifikátor URI, parametr řetězce dotazu cache-busting se nevygeneruje.

### <a name="asp-append-version"></a>ASP – připojení – verze

Je `asp-append-version` -li parametr zadán `true` s hodnotou společně s `src` atributem, je vyvolána pomocná značka obrázku.

V následujícím příkladu je použita pomocná pomůcka značek obrázku:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Pokud statický soubor existuje v adresáři */wwwroot/images/*, VYGENEROVANÝ kód HTML je podobný následujícímu (hodnota hash bude odlišná):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Hodnota přiřazená parametru `v` je hodnota hash souboru *asplogo. png* na disku. Pokud webový server nemůže získat přístup pro čtení ke statickému souboru, není přidán žádný `v` parametr do `src` atributu ve vykresleném kódu.

## <a name="hash-caching-behavior"></a>Chování ukládání do mezipaměti hash

Pomocník značek image používá poskytovatele mezipaměti na místním webovém serveru k uložení vypočtené `Sha512` hodnoty hash daného souboru. Pokud se soubor vyžádá víckrát, hodnota hash se nepřepočítá. V případě, že je vypočtena `Sha512` hodnota hash souboru, je mezipaměť zrušena platností sledovacího procesu souboru připojeného k souboru. Při změně souboru na disku se vypočítává nový hash a ukládá do mezipaměti.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
