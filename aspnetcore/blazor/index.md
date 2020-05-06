---
title: Úvod do ASP.NET CoreBlazor
author: guardrex
description: Prozkoumejte BlazorASP.NET Core, způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 03/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/index
ms.openlocfilehash: ced3e2cc0428fccf6f0b2eba7a3f045e07002234
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771947"
---
# <a name="introduction-to-aspnet-core-blazor"></a>Úvod do ASP.NET CoreBlazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

*Vítá vás Blazor!*

Blazorje rozhraní pro sestavování interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:

* Vytvářejte bohatá interaktivní uživatelská rozhraní pomocí jazyka C# namísto JavaScriptu.
* Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.
* Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.
* Integrujte s moderními hostujícími platformami, jako je například [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Použití rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:

* Psát kód v jazyce C# namísto JavaScriptu.
* Využijte stávající ekosystém .NET knihoven .NET.
* Sdílejte logiku aplikace napříč serverem a klientem.
* Využijte výhod. Výkon, spolehlivost a zabezpečení sítě.
* Aplikace Visual Studio v systémech Windows, Linux a macOS vám zůstane produktivní.
* Sestavte se na společné sadě jazyků, platforem a nástrojů, které jsou stabilní, funkce s bohatou funkcí a snadno použitelné.

## <a name="components"></a>Komponenty

Blazoraplikace jsou založené na *komponentách*. Komponenta v Blazor nástroji je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.

Komponenty jsou třídy .NET integrované do sestavení .NET, která:

* Definujte flexibilní logiku vykreslování uživatelského rozhraní.
* Zpracování událostí uživatele.
* Může být vnořen a znovu použit.
* Lze sdílet a distribuovat jako [ Razor knihovny tříd](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).

Třída komponenty je obvykle napsána ve formě stránky se [Razor](xref:mvc/views/razor) značkami s příponou souboru *. Razor* . Komponenty v Blazor nástroji jsou formálně označovány jako * Razor komponenty*. Razorje syntaxe pro kombinování značek HTML pomocí kódu jazyka C# navrženého pro produktivitu vývojářů. Razorumožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [technologie IntelliSense](/visualstudio/ide/using-intellisense) . RazorStránky a MVC také používají Razor. Na rozdíl Razor od stránek a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.

Následující Razor kód ukazuje komponentu (*dialog. Razor*), která může být vnořena do jiné komponenty:

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

Obsah těla (`ChildContent`) a názvu (`Title`) v dialogovém okně je součástí komponenty, která tuto komponentu používá v uživatelském rozhraní. `OnYes`je metoda C# aktivovaná `onclick` událostí tlačítka.

Blazorpoužívá přirozené značky HTML pro kompozici uživatelského rozhraní. Prvky HTML určují komponenty a atributy značky přecházejí hodnoty do vlastností součásti.

V následujícím příkladu `Index` komponenta používá `Dialog` komponentu. `ChildContent`a `Title` jsou nastaveny pomocí atributů a obsahu `<Dialog>` elementu.

*Index. Razor*:

```razor
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Dialog se vykreslí, když se k nadřazenému elementu (*index. Razor*) přistupoval v prohlížeči:

![Komponenta dialogového okna vykreslená v prohlížeči](index/_static/dialog.png)

Pokud se tato komponenta používá v aplikaci, IntelliSense v [aplikaci Visual Studio](/visualstudio/ide/using-intellisense) a [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) zrychluje vývoj pomocí syntaxe a dokončování parametrů.

Komponenty vykreslí do reprezentace v paměti model DOM (Document Object Model) v prohlížeči (DOM), která se nazývá *strom vykreslování*, který se používá k aktualizaci uživatelského rozhraní flexibilním a efektivním způsobem.

## <a name="blazor-webassembly"></a>BlazorWebAssembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

BlazorWebAssembly je jediná stránka architektury aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET. BlazorWebové sestavení používá open Web Standards bez modulů plug-in a transpilation kódu a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.

Spouštění kódu .NET ve webových prohlížečích je umožněno webovým [sestavením](https://webassembly.org) (zkráceným *wasm*). WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění. WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.

Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, označovanému jako *interoperabilita JavaScriptu* (nebo *zprostředkovatel komunikace s JavaScriptem*). Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.

![BlazorRozhraní WebAssembly spouští kód .NET v prohlížeči pomocí webového sestavení.](index/_static/blazor-webassembly.png)

Blazor Když je aplikace WebAssembly sestavená a spuštěná v prohlížeči:

* Soubory a Razor soubory kódu jazyka C# jsou zkompilovány do sestavení .NET.
* Sestavení a modul runtime .NET se stáhnou do prohlížeče.
* BlazorWebAssembly spustí modul .NET runtime a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci. Blazor Modul runtime webového sestavení používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání modelu DOM a volání rozhraní API prohlížeče.

Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace. Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele. BlazorWebAssembly optimalizuje velikost datové části, aby se snížila doba stahování:

* Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [linkeru pro převodní jazyk (IL)](xref:host-and-deploy/blazor/configure-linker).
* Odpovědi HTTP jsou komprimovány.
* Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.

## <a name="blazor-server"></a>BlazorWebServer

Blazorodpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní. BlazorServer poskytuje podporu pro hostování Razor komponent na serveru aplikace v ASP.NET Core. Aktualizace uživatelského rozhraní se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.

Modul runtime zpracovává odeslání událostí uživatelského rozhraní z prohlížeče na server a po spuštění komponent aplikuje aktualizace uživatelského rozhraní odeslané serverem zpátky do prohlížeče.

Připojení, které Blazor Server používá ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScriptu.

![BlazorServer spouští na serveru .NET code a komunikuje s model DOM (Document Object Model) v klientovi přes SignalR připojení.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem. Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat. Kód jazyka c# může zavolat do kódu jazyka JavaScript a kód JavaScriptu může volat kód jazyka C#. Další informace najdete v těchto článcích:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Blazorimplementuje [.NET Standard 2,0](/dotnet/standard/net-standard). .NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET. Knihovny tříd .NET Standard lze sdílet napříč různými platformami .NET, jako jsou Blazornapříklad, .NET Framework, .NET Core, Xamarin, mono a Unity.

Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají výjimku <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* [Průvodce C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Odkazy Blazor Super](https://github.com/AdrienTorris/awesome-blazor) Community
