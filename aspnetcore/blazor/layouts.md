---
title: Blazor rozložení
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/layouts
ms.openlocfilehash: 42d5d03c50c525c3d819f6dac39109eff6534800
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614773"
---
# <a name="blazor-layouts"></a>Blazor rozložení

Podle [Rainer Stropek](https://www.timecockpit.com)

Aplikace obvykle obsahují více než jednu součást. Prvky, rozložení, jako jsou nabídky, zprávy o autorských právech a loga, musí být k dispozici pro všechny komponenty. Kopírování kódu z těchto elementů rozložení do všech součástí aplikace není efektivní přístup. Tato duplikace je obtížné udržovat a pravděpodobně povede k nekonzistentní obsah v čase. *Rozložení* tento problém vyřešit.

Technicky vzato rozložení je jenom další komponenty. Rozložení je definována šablona Razor nebo v C# kódu a může obsahovat [datové vazby](xref:blazor/components#data-binding), [injektáž závislostí](xref:blazor/dependency-injection)a další běžné funkce součástí.

Zapnout dva další aspekty *komponenty* do *rozložení*

* Komponenta rozložení musí dědit z `LayoutComponentBase`. `LayoutComponentBase` definuje `Body` vlastnost, která obsahuje obsah, který se vykreslí uvnitř rozložení.
* Používá součásti rozložení `Body` vlastnosti a určit, kde má být obsah textu vykreslen pomocí syntaxe Razor `@Body`. Při vykreslování, `@Body` nahrazuje obsah rozložení.

Následující příklad kódu ukazuje šablona Razor součásti rozložení. Všimněte si použití `LayoutComponentBase` a `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a>Použít rozložení v komponentě

Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu. Kompilátor převede tuto direktivu do `LayoutAttribute`, které platí pro třídu komponenty.

Následující příklad kódu ukazuje koncept. Obsah této součásti je vložen do *MasterLayout* v pozici `@Body`:

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a>Výběr centralizované rozložení

Všechny složky, které aplikace aplikace může volitelně obsahovat soubor šablony s názvem *_ViewImports.cshtml*. Kompilátor obsahuje direktivy zadané v souboru importy zobrazení všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách. Proto *_ViewImports.cshtml* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce použití *MainLayout* rozložení. Není nutné opakovaně přidat `@layout` ke všem *.razor* soubory.

Všimněte si, že používá výchozí šablonu *_ViewImports.cshtml* mechanismus pro výběr rozložení. Nově vytvořenou aplikaci, která obsahuje *_ViewImports.cshtml* soubor *součásti/stránky* složky.

## <a name="nested-layouts"></a>Vnořené rozložení

Aplikace můžou zahrnovat vnořené rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například je možné vnoření rozložení tak, aby odrážely struktura víceúrovňových nabídky.

Následující ukázky kódu ukazují, jak používat vnořené rozložení. *EpisodesComponent.cshtml* souboru je komponenta zobrazit. Všimněte si, že součást odkazuje rozložení `MasterListLayout`.

*EpisodesComponent.cshtml*:

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

*MasterListLayout.cshtml* poskytuje soubor `MasterListLayout`. Rozložení odkazuje na jiné rozložení, `MasterLayout`, ve kterém se bude vložen.

*MasterListLayout.cshtml*:

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

Nakonec `MasterLayout` obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, zápatí a hlavní nabídky.

*MasterLayout.cshtml*:

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
