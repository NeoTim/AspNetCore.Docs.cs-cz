---
title: Pomocná rutina značky obrázku v ASP.NET Core
author: pkellner
description: Ukazuje, jak pracovat s pomocná rutina značky obrázku.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 916a68c187cbf516a59d3c5d7578cdb6ada01b86
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468815"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Pomocná rutina značky obrázku v ASP.NET Core

Podle [Peter Kellner](http://peterkellner.net)

Pomocná rutina značky obrázku rozšiřuje `<img>` značky, které poskytují chování mezipaměti nejnovějších souborů statické bitové kopie.

Řetězec nejnovějších mezipaměti je jedinečná hodnota, který představuje hodnotu hash souboru statický obrázek připojí k adrese URL prostředku. Jedinečný řetězec vyzve klienty (a některé proxy servery), aby znovu načíst obrázek z hostitele webového serveru a ne z mezipaměti klienta.

Pokud zdroj obrázku (`src`) je statický soubor na webovém serveru hostitele:

* Jedinečné nejnovějších mezipaměti řetězec je připojen jako parametr dotazu na zdroj bitové kopie.
* Pokud se změní soubor na webovém serveru hostitele, je generována jedinečný požadavek URL, která zahrnuje parametr aktualizované požadavku.

Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Atributy pomocné rutiny značky obrázku

### <a name="src"></a>src

K aktivaci pomocná rutina značky obrázku `src` na je vyžadován atribut `<img>` elementu.

Zdroj obrázku (`src`) musí odkazovat na fyzický statický soubor na serveru. Pokud `src` je identifikátor URI vzdálené mezipaměti nejnovějších parametru řetězce dotazu se nevygeneroval.

### <a name="asp-append-version"></a>asp-append-version

Při `asp-append-version` zadán s parametrem `true` hodnotu spolu s `src` atribut pomocná rutina značky obrázku je vyvolána.

Následující příklad používá pomocná rutina značky obrázku:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Pokud ke statickému souboru v adresáři existuje */wwwroot/image/*, generovaný kód HTML je podobný následujícímu (hodnota hash se lišit):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Hodnota přiřazená k parametru `v` je hodnota hash *asplogo.png* souboru na disku. Pokud webový server nelze získat přístup pro čtení k statických souborů žádné `v` parametru se přidá do `src` atribut v vykreslované značky.

## <a name="hash-caching-behavior"></a>Hash – chování ukládání do mezipaměti

Pomocná rutina značky obrázku využívá poskytovatele mezipaměti v místní webový server k ukládání vypočítané `Sha512` hash daný soubor. Pokud soubor je požadováno více než jednou, není přepočítá hodnoty hash. Mezipaměť je ukončit platnost souboru sledovací proces, který se připojuje k souboru po souboru `Sha512` vypočítat hodnotu hash. Při změně souboru na disku, nová hodnota hash se počítá a uložili do mezipaměti.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
