---
title: Co je nového v ASP.NET Core 3,1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 4737747f84a59780fe70f63195f7580bd812e4de
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74734024"
---
# <a name="whats-new-in-aspnet-core-31"></a>Co je nového v ASP.NET Core 3,1

Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,1 s odkazy na příslušnou dokumentaci.

## <a name="partial-class-support-for-razor-components"></a>Podpora částečné třídy pro součásti Razor

Komponenty Razor se nyní generují jako částečné třídy. Kód pro komponentu Razor lze zapsat pomocí souboru kódu na pozadí definovaného jako dílčí třídy namísto definice veškerého kódu pro komponentu v jednom souboru. Další informace naleznete v tématu [Podpora částečné třídy](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor pomocníka značek komponenty a předání parametrů do komponent nejvyšší úrovně

V Blazor s ASP.NET Core 3,0 byly komponenty vykresleny do stránek a zobrazení pomocí pomocníka jazyka HTML (`Html.RenderComponentAsync`). V ASP.NET Core 3,1 vykreslete komponentu ze stránky nebo zobrazení pomocí nového pomocníka značky komponenty:

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Pomocník HTML zůstane v ASP.NET Core 3,1 podporován, ale doporučuje se pomocník značek komponent.

aplikace Blazor serveru teď můžou předat parametry na nejvyšší úrovni během počátečního vykreslování. Dříve jste mohli předat parametry do komponenty nejvyšší úrovně pomocí [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). V této verzi jsou podporovány obě [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) i [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) . Všechny zadané hodnoty parametrů jsou serializovány jako JSON a zahrnuty do počáteční odezvy.

Například fáze PreRender `Counter` komponentu s přírůstnou výší (`IncrementAmount`):

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Další informace najdete v tématu [integrace součástí do aplikací Razor Pages a MVC](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>Podpora sdílených front v HTTP. sys

[Http. sys](xref:fundamentals/servers/httpsys) podporuje vytváření anonymních front požadavků. V ASP.NET Core 3,1 jsme přidali možnost vytvořit nebo připojit k existující pojmenované frontě požadavků HTTP. sys. Vytvoření nebo připojení k existující pojmenované frontě požadavků HTTP. sys umožňuje scénáře, kde protokol HTTP. Proces řadiče sys, který vlastní frontu, je nezávislý na procesu naslouchacího procesu. Díky této nezávislosti je možné zachovat stávající připojení a zařadit do fronty požadavky mezi restarty procesu naslouchacího procesu:

[!code-csharp[](sample/Program.cs?name=snippet)]

<!-- TODO
## Breaking changes for SameSite cookies
-->

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Zakázat výchozí akce pro události v aplikacích Blazor

Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva `@on{EVENT}:preventDefault`. V následujícím příkladu je znemožněna výchozí akce zobrazení znaku klíče v textovém poli:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Další informace najdete v tématu [prevence výchozích akcí](xref:blazor/components#prevent-default-actions).

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Zastavení šíření událostí v Blazorch aplikacích

Pro zastavení šíření události použijte atribut direktiva `@on{EVENT}:stopPropagation`. V následujícím příkladu zabrání zaškrtnutí políčka události kliknutí z podřízeného `<div>` z rozšiřování do nadřazené `<div>`:

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Další informace najdete v tématu [zastavení šíření událostí](xref:blazor/components#stop-event-propagation).

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Podrobné chyby během Blazor vývoje aplikací

Když aplikace Blazor během vývoje nefunguje správně, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace. Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky zlatý pruh:

* Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.
* V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.

Další informace najdete v tématu [podrobné chyby při vývoji](xref:blazor/handle-errors#detailed-errors-during-development).
