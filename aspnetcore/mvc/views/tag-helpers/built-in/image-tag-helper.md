---
title: Pomocník značek obrázku v ASP.NET Core
author: pkellner
description: Ukazuje, jak pracovat s pomocníkem značek obrázku.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 964072ad276f7e3e411ee41cb03a2efb9d05c585
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663994"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Pomocník značek obrázku v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net)

Pomocná pomůcka značek obrázku vylepšuje značku `<img>`, aby poskytovala chování busting cache pro soubory statických imagí.

Řetězec cache-busting je jedinečná hodnota, která představuje hodnotu hash souboru statické bitové kopie připojené k adrese URL prostředku. Jedinečný řetězec vyzve klienty (a některé proxy) k opětovnému načtení bitové kopie z hostitelského webového serveru a nikoli z mezipaměti klienta.

Pokud je zdroj obrázku (`src`) statickým souborem na hostitelském webovém serveru:

* Jedinečný řetězec busting cache je připojen jako parametr dotazu pro zdroj obrázku.
* Pokud se soubor na hostitelském webovém serveru změní, je vygenerována jedinečná adresa URL požadavku, která obsahuje aktualizovaný parametr žádosti.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Pomocné atributy značek obrázku

### <a name="src"></a>src

Chcete-li aktivovat pomocníka značky obrázku, je atribut `src` požadován u prvku `<img>`.

Zdroj obrázku (`src`) musí odkazovat na fyzický statický soubor na serveru. Pokud je `src` vzdálený identifikátor URI, parametr řetězce dotazu cache-busting se nevygeneruje.

### <a name="asp-append-version"></a>ASP – připojení – verze

Pokud je zadána `asp-append-version` s hodnotou `true` společně s atributem `src`, je vyvolána pomocná značka obrázku.

V následujícím příkladu je použita pomocná pomůcka značek obrázku:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Pokud statický soubor existuje v adresáři */wwwroot/images/* , VYGENEROVANÝ kód HTML je podobný následujícímu (hodnota hash bude odlišná):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Hodnota přiřazená parametru `v` je hodnota hash souboru *asplogo. png* na disku. Pokud webový server nemůže získat přístup pro čtení ke statickému souboru, není do atributu `src` ve vykresleném kódu přidán žádný parametr `v`.

## <a name="hash-caching-behavior"></a>Chování ukládání do mezipaměti hash

Pomocník značek image používá poskytovatele mezipaměti na místním webovém serveru k uložení vypočtené hodnoty hash `Sha512` daného souboru. Pokud se soubor vyžádá víckrát, hodnota hash se nepřepočítá. V případě, že je vypočítána hodnota hash `Sha512` souboru, je mezipaměť zrušena platností sledovacího procesu souboru, který je připojen k souboru. Při změně souboru na disku se vypočítává nový hash a ukládá do mezipaměti.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
