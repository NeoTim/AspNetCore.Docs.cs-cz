---
title: Rozložení Razor komponenty
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Blazor a Razor součásti aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/layouts
ms.openlocfilehash: fdb352701cf664dfb1efab5d05c37ee6a930cc4f
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345795"
---
# <a name="razor-components-layouts"></a>Rozložení Razor komponenty

Podle [Rainer Stropek](https://www.timecockpit.com)

Aplikace obvykle obsahují více než jednu stránku. Prvky, rozložení, jako jsou nabídky, zprávy o autorských právech a loga, musí být k dispozici na všech stránkách. Kopírování kódu z těchto elementů rozložení do všech stránek aplikace není efektivní řešení. Tato duplikace je obtížné udržovat a pravděpodobně povede k nekonzistentní obsah v čase. *Rozložení* tento problém vyřešit.

Technicky vzato rozložení je jenom další komponenty. Rozložení je definována šablona Razor nebo v C# kódu a může obsahovat další běžné funkce komponenty, vkládání závislostí a datové vazby. Zapnout dva další aspekty *komponenty* do *rozložení*:

* Komponenta rozložení musí dědit z `LayoutComponentBase`. `LayoutComponentBase` definuje `Body` vlastnost, která obsahuje obsah, který se vykreslí uvnitř rozložení.
* Používá součásti rozložení `Body` vlastnosti a určit, kde má být obsah textu vykreslen pomocí syntaxe Razor `@Body`. Při vykreslování, `@Body` nahrazuje obsah rozložení.

Následující příklad kódu ukazuje šablona Razor součásti rozložení. Všimněte si použití `LayoutComponentBase` a `@Body`:

```csharp
@inherits LayoutComponentBase

<header>
    <h1>ERP Master 3000</h1>
</header>

<nav>
    <a href="master-data">Master Data Management</a>
    <a href="invoicing">Invoicing</a>
    <a href="accounting">Accounting</a>
</nav>

@Body

<footer>
    &copy; by @CopyrightMessage
</footer>

@functions {
    public string CopyrightMessage { get; set; }
    ...
}
```

## <a name="use-a-layout-in-a-component"></a>Použít rozložení v komponentě

Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu. Kompilátor převede tuto direktivu do `LayoutAttribute`, které platí pro třídu komponenty.

Následující příklad kódu ukazuje koncept. Obsah této součásti je vložen do *MasterLayout* v pozici `@Body`:

```csharp
@layout MasterLayout

@page "/master-data"

<h2>Master Data Management</h2>
...
```

## <a name="centralized-layout-selection"></a>Výběr centralizované rozložení

Všechny složky, které aplikace aplikace může volitelně obsahovat soubor šablony s názvem *_ViewImports.cshtml*. Kompilátor obsahuje direktivy zadané v souboru importy zobrazení všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách. Proto *_ViewImports.cshtml* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce použití *MainLayout* rozložení. Není nutné opakovaně přidat `@layout` ke všem  *\*.cshtml* soubory.

Všimněte si, že používá výchozí šablonu *_ViewImports.cshtml* mechanismus pro výběr rozložení. Nově vytvořenou aplikaci, která obsahuje *_ViewImports.cshtml* soubor *stránky* složky.

## <a name="nested-layouts"></a>Vnořené rozložení

Aplikace můžou zahrnovat vnořené rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například je možné vnoření rozložení tak, aby odrážely struktura víceúrovňových nabídky.

Následující ukázky kódu ukazují, jak používat vnořené rozložení. *CustomersComponent.cshtml* souboru je komponenta zobrazit. Všimněte si, že součást odkazuje rozložení `MasterDataLayout`.

*CustomersComponent.cshtml*:

```csharp
@layout MasterDataLayout

@page "/master-data/customers"

<h1>Customer Maintenance</h1>
...
```

*MasterDataLayout.cshtml* poskytuje soubor `MasterDataLayout`. Rozložení odkazuje na jiné rozložení, `MainLayout`, ve kterém se bude vložen.

*MasterDataLayout.cshtml*:

```csharp
@layout MainLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master data module -->
    ...
</nav>

@Body
```

Nakonec `MainLayout` obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, zápatí a hlavní nabídky.

*MainLayout.cshtml*:

```csharp
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
