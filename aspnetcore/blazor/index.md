---
title: Úvod do ASP.NET jádraBlazor
author: guardrex
description: Prozkoumejte BlazorASP.NET Core , způsob, jak vytvořit interaktivní webové uživatelské rozhraní na straně klienta s rozhraním .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 6d2e95cd2ec92f97a97cb558fb39e4540450c766
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405956"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a>Úvod do ASP.NET jádraBlazor

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

*Vítejte! Blazor*

Blazorje rámec pro vytváření interaktivního webového uživatelského rozhraní na straně klienta s rozhraním .NET:

* Vytvořte bohaté interaktivní umělá použití jazyka C# místo JavaScriptu.
* Sdílejte logiku aplikace na straně serveru a klienta napsanou v rozhraní .NET.
* Vykreslete ui jako HTML a CSS pro širokou podporu prohlížeče, včetně mobilních prohlížečů.
* Integrujte se s moderními hostingovými platformami, jako je [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Použití rozhraní .NET pro vývoj webu na straně klienta nabízí následující výhody:

* Místo JavaScriptu napište kód v jazyce C#.
* Využijte existující ekosystém .NET knihoven .NET.
* Sdílejte logiku aplikace napříč serverem a klientem.
* Využijte výhod . net výkon, spolehlivost a zabezpečení.
* Zůstaňte produktivní díky Visual Studiu ve Windows, Linuxu a macOS.
* Nazákladě společné sady jazyků, architektur a nástrojů, které jsou stabilní, bohaté na funkce a snadno použitelné.

## <a name="components"></a>Komponenty

Blazoraplikace jsou *založeny*na komponentách . Komponenta Blazor v aplikaci je prvek ui, například stránka, dialog nebo formulář pro zadávání dat.

Součásti jsou třídy .NET integrované do sestavení .NET, které:

* Definujte flexibilní logiku vykreslování ui.
* Zpracování uživatelských událostí.
* Může být vnořen a znovu použit.
* Lze sdílet a distribuovat jako [knihovny třídrazor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).

Třída komponenty je obvykle napsána ve formě stránky [značek Razor](xref:mvc/views/razor) s *příponou .razor.* Komponenty Blazor v jsou formálně *označovány*jako komponenty Razor . Razor je syntaxe pro kombinaci značek HTML s kódem Jazyka C# určeným pro produktivitu vývojářů. Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [Technologie IntelliSense.](/visualstudio/ide/using-intellisense) Razor Pages a MVC také používají Razor. Na rozdíl od Razor Pages a MVC, které jsou postaveny na modelu požadavku a odpovědi, komponenty se používají speciálně pro logiku a složení uživatelského panelu na straně klienta.

Následující značka Razor ukazuje komponentu (*Dialog.razor*), kterou lze vnořit do jiné součásti:

```razor
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

Obsah těla dialogového`ChildContent`okna (`Title`) a název ( ) jsou poskytovány komponentou, která používá tuto komponentu v jejím unovém uzly. `OnYes`je metoda Jazyka C# svolaná `onclick` událostí tlačítka.

Blazorpoužívá přirozené značky HTML pro kompozici ui. Elementy HTML určují komponenty a atributy značky předávají hodnoty vlastnostem komponenty.

V následujícím příkladu `Index` komponenta `Dialog` používá komponentu. `ChildContent`a `Title` jsou nastaveny atributy a `<Dialog>` obsah prvku.

*Index.holicí strojek*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Dialogové okno se vykreslí, když je rodič (*Index.razor*) přístupný v prohlížeči:

![Komponenta dialogového okna vykreslená v prohlížeči](index/_static/dialog.png)

Při použití této součásti v aplikaci intelliSense v [sadě Visual Studio](/visualstudio/ide/using-intellisense) a Visual Studio [Code](https://code.visualstudio.com/docs/editor/intellisense) urychluje vývoj pomocí syntaxe a dokončení parametrů.

Součásti vykreslují do reprezentace v paměti prohlížeče Document Object Model (DOM) nazývaného *strom vykreslení*, který se používá k aktualizaci ui flexibilním a efektivním způsobem.

## <a name="opno-locblazor-webassembly"></a>BlazorWebová sestava

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebAssembly je jednostránkový rámec aplikací pro vytváření interaktivních webových aplikací na straně klienta s rozhraním .NET. BlazorWebAssembly používá otevřené webové standardy bez pluginů nebo transpilace kódu a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.

Spuštění kódu .NET uvnitř webových prohlížečů je možné pomocí [WebAssembly](https://webassembly.org) (zkráceně *wasm*). WebAssembly je kompaktní formát bytecode optimalizovaný pro rychlé stahování a maximální rychlost provádění. WebAssembly je otevřený webový standard a je podporován ve webových prohlížečích bez pluginů.

WebAssembly kód může přistupovat k plné funkčnosti prohlížeče přes JavaScript, volal *JavaScript interop* ( nebo *JavaScript interop*). Kód .NET spuštěný prostřednictvím webové sestavy v prohlížeči běží v izolovaném prostoru JavaScriptu prohlížeče s ochranou, kterou izolovaného prostoru poskytuje proti škodlivým akcím v klientském počítači.

![BlazorWebAssembly spustí kód .NET v prohlížeči s WebAssembly.](index/_static/blazor-webassembly.png)

Když Blazor je aplikace WebAssembly vytvořena a spuštěna v prohlížeči:

* Soubory kódu Jazyka C# a Razor jsou kompilovány do sestavení .NET.
* Sestavení a za běhu .NET jsou staženy do prohlížeče.
* BlazorWebAssembly zavede zaběhovou dobu .NET a nakonfiguruje dobu runtime tak, aby načítaná sestavení pro aplikaci. Runtime Blazor WebAssembly používá javascriptový interop ke zpracování manipulace s dom a volání rozhraní API prohlížeče.

Velikost publikované aplikace, její *velikost datové části*, je kritickým faktorem výkonu pro použití aplikace. Vytvoření velké aplikace trvá poměrně dlouho, než se stáhne do prohlížeče, což snižuje uživatelské prostředí. BlazorWebAssembly optimalizuje velikost datové části, aby se zkrátila doba stahování:

* Nepoužívaný kód je odebrán z aplikace, když je publikován [linkerem zprostředkující jazyk (IL).](xref:host-and-deploy/blazor/configure-linker)
* Odpovědi HTTP jsou komprimovány.
* Doba běhu rozhraní .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.

## <a name="opno-locblazor-server"></a>BlazorServer

Blazorodděluje logiku vykreslování komponent od způsobu použití aktualizací ui. BlazorServer poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core. Aktualizace ui jsou [SignalR](xref:signalr/introduction) zpracovávány přes připojení.

Runtime zpracovává odesílání událostí uznacího z prohlížeče na server a použije aktualizace ui odeslané serverem zpět do prohlížeče po spuštění komponent.

Připojení používané Blazor serverem ke komunikaci s prohlížečem se také používá ke zpracování volání interop jazyka JavaScript.

![BlazorServer spouští kód .NET na serveru a spolupracuje s objektovým modelem dokumentu na straně klienta SignalR přes připojení](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

U aplikací, které vyžadují javascriptové knihovny třetích stran a přístup k apim prohlížeče, komponenty spolupracují s JavaScriptem. Komponenty jsou schopny používat libovolnou knihovnu nebo rozhraní API, které je JavaScript schopen používat. Kód Jazyka C# může volat do kódu JavaScript u a kód JavaScriptu může volat do kódu Jazyka C#. Další informace najdete v těchto článcích:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a standard .NET

Blazorimplementuje [.NET Standard 2.0](/dotnet/standard/net-standard). .NET Standard je formální specifikace rozhraní API ROZHRANÍ .NET, která jsou společná v implementacích rozhraní .NET. Knihovny tříd .NET Standard lze sdílet na různých Blazorplatformách .NET, například , .NET Framework, .NET Core, Xamarin, Mono a Unity.

Rozhraní API, která nejsou použitelná uvnitř webového prohlížeče (například přístup k systému souborů, <xref:System.PlatformNotSupportedException>otevření soketu a zřetězení) vyvolat .

## <a name="additional-resources"></a>Další zdroje

* [Webová sestava](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [Průvodce C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Úžasné Blazor ](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy
