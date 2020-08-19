---
title: Co je nového v ASP.NET Core 3,1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f25cb38a3390600664980933caaf1003b533b2b6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627569"
---
# <a name="whats-new-in-aspnet-core-31"></a>Co je nového v ASP.NET Core 3,1

Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,1 s odkazy na příslušnou dokumentaci.

## <a name="partial-class-support-for-no-locrazor-components"></a>Podpora částečné třídy pro Razor součásti

Razor komponenty se nyní generují jako částečné třídy. Kód pro Razor komponentu lze zapsat pomocí souboru kódu na pozadí definovaného jako dílčí třídy namísto definice veškerého kódu pro komponentu v jednom souboru. Další informace naleznete v tématu [Podpora částečné třídy](xref:blazor/components/index#partial-class-support).

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor Pomocná komponenta značek komponenty a předání parametrů komponentám nejvyšší úrovně

V Blazor with ASP.NET Core 3,0 byly komponenty vykresleny do stránek a zobrazení pomocí pomocníka jazyka HTML ( `Html.RenderComponentAsync` ). V ASP.NET Core 3,1 vykreslete komponentu ze stránky nebo zobrazení pomocí nového pomocníka značky komponenty:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Pomocník HTML zůstane v ASP.NET Core 3,1 podporován, ale doporučuje se pomocník značek komponent.

Blazor Server aplikace teď můžou předat parametry na nejvyšší úrovni během počátečního vykreslování. Dříve jste mohli předat parametry do komponenty nejvyšší úrovně pomocí [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). V této verzi jsou podporovány obě [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) i [RenderMode. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) . Všechny zadané hodnoty parametrů jsou serializovány jako JSON a zahrnuty do počáteční odezvy.

Například fáze PreRender `Counter` komponentu s přírůstnou velikostí ( `IncrementAmount` ):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Další informace najdete v tématu [integrace součástí do Razor stránek a aplikací MVC](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>Podpora sdílených front v HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) podporuje vytváření anonymních front požadavků. V ASP.NET Core 3,1 jsme přidali možnost vytvořit nebo připojit se k existujícímu pojmenovanému frontě HTTP.sys požadavků. Vytvoření nebo připojení k existující pojmenované frontě žádostí HTTP.sys umožňuje scénáře, ve kterých je proces řadiče HTTP.sys, který vlastní frontu, nezávisle na procesu naslouchacího procesu. Díky této nezávislosti je možné zachovat stávající připojení a zařadit do fronty požadavky mezi restarty procesu naslouchacího procesu:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>Průlomové změny pro SameSite cookie s

Chování SameSite s se cookie změnilo tak, aby odráželo nadcházející změny prohlížeče. To může mít vliv na scénáře ověřování, jako je AzureAd, OpenIdConnect nebo WsFederation. Další informace naleznete v tématu <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Zakázat výchozí akce pro události v Blazor aplikacích

`@on{EVENT}:preventDefault`Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva. V následujícím příkladu je znemožněna výchozí akce zobrazení znaku klíče v textovém poli:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Další informace najdete v tématu [prevence výchozích akcí](xref:blazor/components/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Zastavit šíření událostí v Blazor aplikacích

`@on{EVENT}:stopPropagation`Pro zastavení šíření události použijte atribut direktiva. V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z podřízeného `<div>` objektu z rozšiřování do nadřazené položky `<div>` :

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

Další informace najdete v tématu [zastavení šíření událostí](xref:blazor/components/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Podrobné chyby při Blazor vývoji aplikací

Když Blazor aplikace nefunguje správně během vývoje, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace. Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky žlutý pruh:

* Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.
* V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.

Další informace najdete v tématu [podrobné chyby při vývoji](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).
