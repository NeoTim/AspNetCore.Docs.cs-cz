---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: ba172282d0cd6371ebc94b4fda1c13aee14d6fbd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851989"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core Blazor rozložení

Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)

Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace. Kopírování kódu těchto prvků do všech součástí aplikace není účinný přístup. Pokaždé, když jeden z elementů vyžaduje aktualizaci, musí být každá součást aktualizována. Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu. *Rozložení* řeší tento problém.

Technicky, rozložení je pouze jiná komponenta. Rozložení je definováno v Razor šabloně nebo v kódu jazyka C# a může používat [datové vazby](xref:blazor/data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.

Chcete-li změnit *komponentu* na *rozložení*, součást:

* Dědí z <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , který definuje <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> vlastnost pro vykreslený obsah v rozložení.
* Používá Razor syntaxi `@Body` k určení umístění v označení rozložení, kde je obsah vykreslen.

Následující ukázka kódu ukazuje Razor šablonu komponenty rozložení *MainLayout. Razor*. Rozložení dědí <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> a nastavuje `@Body` mezi navigačním panelem a zápatím:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

V aplikaci založené na jedné ze Blazor šablon aplikací `MainLayout` je součást (*MainLayout. Razor*) ve *sdílené* složce aplikace.

## <a name="default-layout"></a>Výchozí rozložení

Zadejte výchozí rozložení aplikace v <xref:Microsoft.AspNetCore.Components.Routing.Router> komponentě v souboru App *. Razor* aplikace. Následující <xref:Microsoft.AspNetCore.Components.Routing.Router> Komponenta, která je poskytována výchozími Blazor šablonami, nastaví výchozí rozložení na `MainLayout` součást:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Chcete-li zadat výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, zadejte <xref:Microsoft.AspNetCore.Components.LayoutView> pro <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsah:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Další informace o <xref:Microsoft.AspNetCore.Components.Routing.Router> komponentě naleznete v tématu <xref:blazor/routing> .

Určení rozložení jako výchozího rozložení ve směrovači je užitečný postup, protože je možné ho přepsat pro jednotlivé komponenty nebo pro jednotlivé složky. Upřednostňujete použití směrovače k nastavení výchozího rozložení aplikace, protože se jedná o nejobecnější postup.

## <a name="specify-a-layout-in-a-component"></a>Určení rozložení v součásti

Použijte Razor direktivu `@layout` pro použití rozložení na komponentu. Kompilátor převede `@layout` na <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , který je použit pro třídu komponenty.

Obsah následující `MasterList` komponenty je vložen do `MasterLayout` pozice v umístění `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Určením rozložení přímo v součásti dojde k přepsání *výchozí sady rozložení* ve směrovači nebo `@layout` direktivy importované z *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Centralizovaný výběr rozložení

Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*. Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech Razor šablonách ve stejné složce a rekurzivně ve všech jejích podsložkách. Proto soubor *_Imports. Razor* `@layout MyCoolLayout` , který obsahuje, zajistí, aby všechny součásti ve složce používaly `MyCoolLayout` . Není nutné opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek. `@using`direktivy jsou také aplikovány na komponenty stejným způsobem.

Následující soubor *_Imports. Razor* importuje:

* `MyCoolLayout`.
* Všechny Razor součásti ve stejné složce a v jejích podsložkách.
* `BlazorApp1.Data`Obor názvů.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro Razor zobrazení a stránky](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro Razor soubory komponent.

Určení rozložení v *_Imports. Razor* přepíše rozložení zadané jako *výchozí rozložení*směrovače.

## <a name="nested-layouts"></a>Vnořená rozložení

Aplikace se můžou skládat z vnořených rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.

Následující příklad ukazuje, jak použít vnořená rozložení. Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit. Komponenta odkazuje na `MasterListLayout` :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout` . Rozložení odkazuje na jiné rozložení, `MasterLayout` , kde je vykresleno. `EpisodesComponent`je vykreslen, kde `@Body` se zobrazí:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Nakonec `MasterLayout` v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí. `MasterListLayout`tam, `EpisodesComponent` kde se zobrazí, se vykreslí `@Body` :

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Sdílení Razor rozložení stránek pomocí integrovaných komponent

Pokud jsou směrovatelný komponenty integrovány do Razor aplikace Pages, lze použít sdílené rozložení aplikace spolu s komponentami. Další informace naleznete v tématu <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/layout>
