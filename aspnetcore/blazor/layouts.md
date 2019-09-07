---
title: ASP.NET Core rozložení Blazor
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/layouts
ms.openlocfilehash: 05a38c10e18407d50422192ab1ddf3ff4b0f3a5b
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800372"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core rozložení Blazor

Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)

Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace. Kopírování kódu těchto prvků do všech komponent aplikace není účinný přístup&mdash;pokaždé, když jeden z elementů vyžaduje aktualizaci, každá součást musí být aktualizována. Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu. *Rozložení* řeší tento problém.

Technicky, rozložení je pouze jiná komponenta. Rozložení je definováno v šabloně Razor nebo v C# kódu a může používat [datové vazby](xref:blazor/components#data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.

Chcete-li změnit *komponentu* na *rozložení*, součást:

* Dědí z `LayoutComponentBase`, který `Body` definuje vlastnost pro vykreslený obsah v rozložení.
* Používá syntaxe Razor `@Body` k určení umístění v označení rozložení, kde se obsah vykresluje.

Následující ukázka kódu ukazuje šablonu Razor komponenty layout *MainLayout. Razor*. Rozložení dědí `LayoutComponentBase` a `@Body` nastavuje mezi navigačním panelem a zápatím:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

V aplikaci založené na jedné z šablon `MainLayout` aplikace Blazor je součást (*MainLayout. Razor*) ve *sdílené* složce aplikace.

## <a name="default-layout"></a>Výchozí rozložení

Zadejte výchozí rozložení aplikace v `Router` komponentě v souboru App *. Razor* aplikace. Následující `Router` komponenta, která je poskytována výchozími šablonami Blazor, nastaví výchozí rozložení `MainLayout` na součást:

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Chcete-li zadat výchozí rozložení `NotFound` obsahu, `LayoutView` zadejte pro `NotFound` obsah:

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Další informace o `Router` komponentě naleznete v tématu <xref:blazor/routing>.

## <a name="specify-a-layout-in-a-component"></a>Určení rozložení v součásti

Použijte direktivu `@layout` Razor pro použití rozložení na komponentu. Kompilátor převede `@layout` `LayoutAttribute`na, který je použit pro třídu komponenty.

Obsah následující `MasterList` komponenty je vložen `MasterLayout` do pozice v umístění `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>Centralizovaný výběr rozložení

Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*. Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech šablonách Razor ve stejné složce a rekurzivně ve všech jejích podsložkách. Proto soubor *_Imports. Razor* , který obsahuje `@layout MyCoolLayout` , zajistí, aby všechny součásti ve složce používaly `MyCoolLayout`. Není nutné opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek. `@using`direktivy jsou také aplikovány na komponenty stejným způsobem.

Následující soubor *_Imports. Razor* importuje:

* `MyCoolLayout`.
* Všechny součásti Razor ve stejné složce a v jejích podsložkách.
* `BlazorApp1.Data` Obor názvů.
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro zobrazení a stránky Razor](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro soubory komponenty Razor.

## <a name="nested-layouts"></a>Vnořená rozložení

Aplikace se můžou skládat z vnořených rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.

Následující příklad ukazuje, jak použít vnořená rozložení. Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit. Komponenta odkazuje na `MasterListLayout`:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout`. Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde je vykresleno. `EpisodesComponent`je vykreslen, `@Body` kde se zobrazí:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Nakonec v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí. `MasterLayout` `MasterListLayout`tam, `EpisodesComponent` kde `@Body` se zobrazí, se vykreslí:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/layout>
