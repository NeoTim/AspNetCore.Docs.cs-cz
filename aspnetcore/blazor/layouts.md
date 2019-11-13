---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
no-loc:
- Blazor
uid: blazor/layouts
ms.openlocfilehash: 3546259fc6b622a6137a6baa8f446c5f43af1cab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962813"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.NET Core Blazor rozložení

Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)

Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace. Kopírování kódu těchto prvků do všech komponent aplikace není účinný přístup&mdash;pokaždé, když jeden z elementů vyžaduje aktualizaci, musí být každá součást aktualizována. Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu. *Rozložení* řeší tento problém.

Technicky, rozložení je pouze jiná komponenta. Rozložení je definováno v šabloně Razor nebo v C# kódu a může používat [datové vazby](xref:blazor/components#data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.

Chcete-li změnit *komponentu* na *rozložení*, součást:

* Dědí z `LayoutComponentBase`, který definuje vlastnost `Body` vykresleného obsahu v rámci rozložení.
* Používá `@Body` syntaxe Razor k určení umístění v označení rozložení, kde se obsah vykresluje.

Následující ukázka kódu ukazuje šablonu Razor komponenty layout *MainLayout. Razor*. Rozložení dědí `LayoutComponentBase` a nastaví `@Body` mezi navigačním panelem a zápatím:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

V aplikaci, která je založená na jedné z Blazor šablon aplikací, se `MainLayout` součást (*MainLayout. Razor*) nachází ve *sdílené* složce aplikace.

## <a name="default-layout"></a>Výchozí rozložení

Zadejte výchozí rozložení aplikace v součásti `Router` v souboru App *. Razor* aplikace. Následující součást `Router`, která je poskytována výchozími šablonami Blazor, nastaví výchozí rozložení na součást `MainLayout`:

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Chcete-li zadat výchozí rozložení pro `NotFound` obsah, zadejte `LayoutView` pro `NotFound` obsah:

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Další informace o komponentě `Router` najdete v tématu <xref:blazor/routing>.

Určení rozložení jako výchozího rozložení ve směrovači je užitečný postup, protože je možné ho přepsat pro jednotlivé komponenty nebo pro jednotlivé složky. Upřednostňujete použití směrovače k nastavení výchozího rozložení aplikace, protože se jedná o nejobecnější postup.

## <a name="specify-a-layout-in-a-component"></a>Určení rozložení v součásti

Použijte `@layout` direktivy Razor pro použití rozložení na komponentu. Kompilátor převede `@layout` na `LayoutAttribute`, který je použit pro třídu komponenty.

Obsah následující `MasterList` komponenty je vložen do `MasterLayout` na pozici `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Určením rozložení přímo v součásti dojde k přepsání *výchozí sady rozložení* ve směrovači nebo direktivy `@layout` naimportované z *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Centralizovaný výběr rozložení

Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*. Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech šablonách Razor ve stejné složce a rekurzivně ve všech jejích podsložkách. Proto soubor *_Imports. Razor* obsahující `@layout MyCoolLayout` zajistí, že všechny součásti ve složce používají `MyCoolLayout`. Nemusíte opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek. direktivy `@using` jsou také aplikovány na komponenty stejným způsobem.

Následující soubor *_Imports. Razor* importuje:

* `MyCoolLayout`.
* Všechny součásti Razor ve stejné složce a v jejích podsložkách.
* Obor názvů `BlazorApp1.Data`.
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro zobrazení a stránky Razor](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro soubory komponenty Razor.

Určení rozložení v *_Imports. Razor* přepíše rozložení zadané jako *výchozí rozložení*směrovače.

## <a name="nested-layouts"></a>Vnořená rozložení

Aplikace se můžou skládat z vnořených rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.

Následující příklad ukazuje, jak použít vnořená rozložení. Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit. Komponenta odkazuje na `MasterListLayout`:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout`. Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde je vykresleno. `EpisodesComponent` se vykreslí, kde se `@Body` zobrazí:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Nakonec `MasterLayout` v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí. `MasterListLayout` `EpisodesComponent` se vykreslí tam, kde se zobrazí `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/layout>
