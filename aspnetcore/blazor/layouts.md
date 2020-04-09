---
title: ASP.NET Blazor základní rozložení
author: guardrex
description: Přečtěte si, jak vytvořit opakovaně Blazor použitelné komponenty rozložení pro aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660410"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.NET Blazor základní rozložení

Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)

Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a loga společnosti, jsou obvykle součástí celkového rozložení aplikace a používají je všechny součásti v aplikaci. Kopírování kódu těchto prvků do všech součástí aplikace není efektivní přístup&mdash;pokaždé, když jeden z prvků vyžaduje aktualizaci, musí být aktualizovány všechny součásti. Tato duplikace je obtížné udržet a může vést k nekonzistentnímu obsahu v průběhu času. *Rozložení* vyřešit tento problém.

Technicky je rozložení jen další součástí. Rozložení je definováno v šabloně Razor nebo v kódu Jazyka C# a může používat [datové vazby](xref:blazor/data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.

Chcete-li *komponentu* přeměnit na *rozvržení*, součást:

* Dědí `LayoutComponentBase`z , který `Body` definuje vlastnost pro vykreslený obsah uvnitř rozložení.
* Syntaxe `@Body` Razor k určení umístění ve značkách rozložení, kde je obsah vykreslen.

Následující ukázka kódu ukazuje šablonu Razor komponenty rozložení *MainLayout.razor*. Rozložení dědí `LayoutComponentBase` a `@Body` nastavuje mezi navigační panel a zápatí:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

V aplikaci založené na Blazor jedné ze `MainLayout` šablon aplikace je komponenta (*MainLayout.razor*) ve složce *Shared* aplikace.

## <a name="default-layout"></a>Výchozí rozložení

Zadejte výchozí rozložení `Router` aplikace v komponentě v souboru *App.razor* aplikace. Následující `Router` komponenta, která je k Blazor dispozici ve výchozích šablonách, nastaví výchozí rozložení na komponentu: `MainLayout`

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Chcete-li zadat `NotFound` výchozí rozložení `LayoutView` obsahu, zadejte pro `NotFound` obsah:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Další informace o `Router` součásti <xref:blazor/routing>naleznete v tématu .

Zadání rozložení jako výchozího rozložení ve směrovači je užitečné, protože může být přepsáno na základě jednotlivých komponent nebo složek. Raději pomocí směrovače nastavit výchozí rozložení aplikace, protože je to nejobecnější technika.

## <a name="specify-a-layout-in-a-component"></a>Určení rozložení v součásti

Použití razor `@layout` směrnice použít rozložení komponenty. Kompilátor převede `@layout` `LayoutAttribute`na , který je použit na třídu komponenty.

Obsah následující `MasterList` součásti se vkládá `MasterLayout` do polohy `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Zadání rozložení přímo v součásti přepíše *výchozí sadu rozvržení* ve směrovači nebo direktivu `@layout` importovnou z *_Imports.razor*.

## <a name="centralized-layout-selection"></a>Centralizovaný výběr rozvržení

Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports.razor*. Kompilátor obsahuje direktivy zadané v souboru importů ve všech šablonách Razor ve stejné složce a rekurzivně ve všech jeho podsložkách. Proto *soubor _Imports.razor* obsahující `@layout MyCoolLayout` zajišťuje, že všechny součásti ve `MyCoolLayout`složce použít . Není třeba opakovaně přidávat `@layout MyCoolLayout` do všech souborů *.razor* ve složce a podsložkách. `@using`stejným způsobem se stejným způsobem uplatňují i směrnice.

Následující *_Imports.razor* soubor importuje:

* `MyCoolLayout`.
* Všechny komponenty Razor ve stejné složce a ve všech podsložkách.
* Obor `BlazorApp1.Data` názvů.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Soubor *_Imports.razor* je podobný [souboru _ViewImports.cshtml pro zobrazení a stránky Razor,](xref:mvc/views/layout#importing-shared-directives) ale je aplikován speciálně na soubory komponent Razor.

Určení rozložení v *souboru _Imports.razor* přepíše rozložení určené jako *výchozí rozložení*směrovače .

## <a name="nested-layouts"></a>Vnořená rozložení

Aplikace se mohou skládat z vnořených rozložení. Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení. Například vnoření rozložení se používají k vytvoření víceúrovňové struktury nabídky.

Následující příklad ukazuje, jak používat vnořená rozložení. Soubor *EpisodesComponent.razor* je komponenta, která se má zobrazit. Komponenta odkazuje `MasterListLayout`na :

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Soubor *MasterListLayout.razor* poskytuje `MasterListLayout`soubor . Rozložení odkazuje na jiné `MasterLayout`rozložení , kde je vykresleno. `EpisodesComponent`je vykreslena `@Body` tam, kde se objeví:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Nakonec `MasterLayout` v *MasterLayout.razor* obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, hlavní nabídka a zápatí. `MasterListLayout`s `EpisodesComponent` vykreslením, `@Body` kde se objeví:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Sdílení rozložení Razor Pages s integrovanými komponentami

Když jsou směrovatelné součásti integrovány do aplikace Razor Pages, sdílené rozložení aplikace lze použít s komponentami. Další informace naleznete v tématu <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/layout>
