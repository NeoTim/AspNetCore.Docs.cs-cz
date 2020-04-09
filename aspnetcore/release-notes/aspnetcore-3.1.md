---
title: Co je nového v ASP.NET Core 3.1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662706"
---
# <a name="whats-new-in-aspnet-core-31"></a>Co je nového v ASP.NET Core 3.1

Tento článek upozorňuje na nejvýznamnější změny v ASP.NET jádrem 3.1 s odkazy na příslušnou dokumentaci.

## <a name="partial-class-support-for-razor-components"></a>Podpora částečné třídy pro komponenty Razor

Komponenty razor jsou nyní generovány jako částečné třídy. Kód pro komponentu Razor lze zapsat pomocí souboru s kódem na pozadí definovaného jako částečná třída, nikoli definování majedu pro komponentu v jednom souboru. Další informace naleznete [v tématu Podpora částečné třídy](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorPomocník s tagy komponent a předat parametry součástem nejvyšší úrovně

V Blazor ASP.NET Core 3.0, komponenty byly vykresleny do stránek`Html.RenderComponentAsync`a zobrazení pomocí HTML Helper ( ). V ASP.NET jádrem 3.1 vykreslte komponentu ze stránky nebo zobrazení pomocí nového pomocníka pro označení komponent:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Pomocník HTML zůstává podporován v ASP.NET jádrem 3.1, ale doporučuje se pomocník pro označení komponent.

BlazorServerové aplikace teď můžou během počátečního vykreslení předávat parametry součástem nejvyšší úrovně. Dříve bylo možné předat parametry pouze součásti nejvyšší úrovně pomocí [rendermode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). V této verzi jsou podporovány [rendermode.server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) i [RenderModel.ServerPrerendered.](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) Všechny zadané hodnoty parametrů jsou serializovány jako JSON a zahrnuty do počáteční odpovědi.

Například přednastavení `Counter` komponenty s přírůstkovou`IncrementAmount`částkou ( ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Další informace najdete [v tématu Integrace komponent do stránek Razor pages a aplikací MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Podpora sdílených front v souboru HTTP.sys

[Http.sys](xref:fundamentals/servers/httpsys) podporuje vytváření anonymních front požadavků. V ASP.NET jádrem 3.1 jsme přidali možnost vytvořit nebo připojit k existující frontě požadavků http.sys. Vytvoření nebo připojení k existující fronty požadavků s názvem HTTP.sys umožňuje scénáře, kde proces řadiče HTTP.sys, který vlastní frontu, je nezávislý na procesu naslouchací proces. Tato nezávislost umožňuje zachovat existující připojení a požadavky zařazené do fronty mezi restartováním procesu naslouchací proces:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Prolomení změn pro soubory cookie SameSite

Chování cookies SameSite se změnilo tak, aby odráželo nadcházející změny prohlížeče. To může ovlivnit scénáře ověřování, jako je AzureAd, OpenIdConnect nebo WsFederation. Další informace naleznete v tématu <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Zabránit výchozím akcím Blazor událostí v aplikacích

Pomocí `@on{EVENT}:preventDefault` atributu direktivy zabraňte výchozí akci události. V následujícím příkladu je zabráněno výchozí akci zobrazení znaku klíče v textovém poli:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Další informace naleznete v tématu [Zabránění výchozím akcím](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Zastavení šíření událostí Blazor v aplikacích

Pomocí `@on{EVENT}:stopPropagation` atributu direktiva zastavte šíření událostí. V následujícím příkladu zabráníte zaškrtnutí políčka, `<div>` aby se události z `<div>`podřízeného objektu nešířily do nadřazené položky :

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

Další informace naleznete v tématu [Stop šíření událostí](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Podrobné chyby při Blazor vývoji aplikací

Když Blazor aplikace během vývoje nefunguje správně, zobrazení podrobných informací o chybách z aplikace pomáhá při řešení potíží a řešení problému. Když dojde k Blazor chybě, aplikace zobrazí zlatý pruh v dolní části obrazovky:

* Během vývoje vás zlatá lišta přesměruje na konzolu prohlížeče, kde můžete vidět výjimku.
* Ve výrobě zlatý pruh upozorní uživatele, že došlo k chybě a doporučuje aktualizovat prohlížeč.

Další informace naleznete [v tématu Podrobné chyby během vývoje](xref:blazor/handle-errors#detailed-errors-during-development).
