---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538539"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor rozložení

Podle [Rainer Stropek](https://www.timecockpit.com)

Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a loga společnosti, jsou obvykle součástí celkového rozložení vaší aplikace a používat všechny komponenty v aplikaci. Kopírování kódu z těchto elementů do všech součástí aplikace není efektivní přístup&mdash;pokaždé, když se jeden z elementů vyžaduje aktualizaci, musí se aktualizovat všechny komponenty. Je obtížné udržovat a může vést k nekonzistentním obsah v čase. *Rozložení* tento problém vyřešit.

Technicky vzato rozložení je jenom další komponenty. Rozložení je definována šablona Razor nebo v C# kódu a můžete použít [datové vazby](xref:blazor/components#data-binding), [injektáž závislostí](xref:blazor/dependency-injection)a další součásti scénáře.

Chcete-li *komponenty* do *rozložení*, součást:

* Dědí z `LayoutComponentBase`, která definuje `Body` vlastnost pro vykreslený obsah uvnitř rozložení.
* Používá syntaxi Razor `@Body` k určení umístění, ve značkách rozložení, ve kterém se vykreslí obsah.

Následující příklad kódu ukazuje šablona Razor rozložení součásti *MainLayout.razor*. Rozložení dědí `LayoutComponentBase` a nastaví `@Body` mezi na navigačním panelu a v zápatí je uvedené:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a>Určete rozložení v komponentě

Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu. Kompilátor převede `@layout` do `LayoutAttribute`, které platí pro třídu komponenty.

Obsah následující komponenty *MasterList.razor*, je vložen do `MainLayout` v pozici `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>Výběr centralizované rozložení

Všechny složky, které aplikace může volitelně obsahovat soubor šablony s názvem *_Imports.razor*. Kompilátor obsahuje direktivy zadané v souboru importu ve všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách. Proto *_Imports.razor* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce pomocí `MainLayout`. Není nutné opakovaně přidat `@layout MainLayout` ke všem *.razor* soubory ve složce a jejích podsložkách. `@using` direktivy jsou použita také na komponenty stejným způsobem.

Následující *_Imports.razor* souboru importu:

* `MainLayout`.
* Všechny součásti Razor ve stejné složce a všechny její podsložky.
* `BlazorApp1.Data` Oboru názvů.
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

*_Imports.razor* soubor je podobný [_ViewImports.cshtml souboru pro zobrazení syntaxe Razor a stránky](xref:mvc/views/layout#importing-shared-directives) ale platí konkrétně pro soubory součástí syntaxe Razor.

Použití šablon Blazor *_Imports.razor* souborů pro výběr rozložení. Obsahuje aplikaci vytvořené ze šablony Blazor *_Imports.razor* v kořenovém adresáři projektu a v souboru *stránky* složky.

## <a name="nested-layouts"></a>Vnořené rozložení

Aplikace můžou zahrnovat vnořené rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například vnoření rozložení slouží k vytvoření víceúrovňových nabídky struktury.

Následující příklad ukazuje, jak používat vnořené rozložení. *EpisodesComponent.razor* souboru je komponenta zobrazit. Odkazy na součásti `MasterListLayout`:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

*MasterListLayout.razor* poskytuje soubor `MasterListLayout`. Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde se vykreslí. `EpisodesComponent` kde je vykreslen `@Body` se zobrazí:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Nakonec `MasterLayout` v *MasterLayout.razor* obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, hlavní nabídky a zápatí. `MasterListLayout` s `EpisodesComponent` vykresleny kde `@Body` se zobrazí:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/layout>
