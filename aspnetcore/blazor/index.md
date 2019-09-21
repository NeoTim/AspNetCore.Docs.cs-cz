---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte ASP.NET Core Blazor, způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 09/05/2019
uid: blazor/index
ms.openlocfilehash: 378892efafa6b979d7f92a5e559b2dc8bb12295d
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168257"
---
# <a name="introduction-to-blazor"></a>Úvod do Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

*Vítá vás Blazor!*

Blazor je architektura pro sestavování interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET:

* Vytvářejte bohatě interaktivní uživatelská rozhraní C# pomocí místo JavaScriptu.
* Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.
* Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.

Použití rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:

* C# Místo JavaScriptu napište kód.
* Využijte stávající ekosystém .NET knihoven .NET.
* Sdílejte logiku aplikace napříč serverem a klientem.
* Využijte výhod. Výkon, spolehlivost a zabezpečení sítě.
* Aplikace Visual Studio v systémech Windows, Linux a macOS vám zůstane produktivní.
* Sestavte se na společné sadě jazyků, platforem a nástrojů, které jsou stabilní, funkce s bohatou funkcí a snadno použitelné.

## <a name="components"></a>Komponenty

Aplikace Blazor jsou založené na *komponentách*. Komponenta v Blazor je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.

Komponenty jsou třídy .NET integrované do sestavení .NET, která:

* Definujte flexibilní logiku vykreslování uživatelského rozhraní.
* Zpracování událostí uživatele.
* Může být vnořen a znovu použit.
* Lze sdílet a distribuovat jako [knihovny tříd Razor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).

Třída komponenty je obvykle napsána ve formě stránky značek [Razor](xref:mvc/views/razor) s příponou souboru *. Razor* . Komponenty v Blazor jsou formálně označovány jako *komponenty Razor*. Razor je syntaxe pro kombinování značek HTML s C# kódem navrženým pro produktivitu vývojářů. Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [technologie IntelliSense](/visualstudio/ide/using-intellisense) . Razor Pages a MVC také používají syntaxi Razor. Na rozdíl od Razor Pages a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.

Následující kód Razor znázorňuje komponentu (*dialog. Razor*), která může být vnořena do jiné komponenty:

```cshtml
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

Obsah těla (`ChildContent`) a názvu (`Title`) v dialogovém okně je součástí komponenty, která tuto komponentu používá v uživatelském rozhraní. `OnYes`je C# metoda aktivovaná `onclick` událostí tlačítka.

Blazor používá přirozené značky HTML pro kompozici uživatelského rozhraní. Prvky HTML určují komponenty a atributy značky přecházejí hodnoty do vlastností součásti.

V následujícím příkladu `Index` komponenta `Dialog` používá komponentu. `ChildContent`a `Title` jsou nastaveny pomocí atributů a obsahu `<Dialog>` elementu.

*Index. Razor*:

```cshtml
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

## <a name="blazor-webassembly"></a>Blazor WebAssembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly je jediná stránka architektury aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET. Blazor WebAssembly používá otevřené webové standardy bez modulů plug-in a Code transpilation a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.

Spouštění kódu .NET ve webových prohlížečích je umožněno [](https://webassembly.org) webovým sestavením (zkráceným *wasm*). WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění. WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.

Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, označovanému jako *interoperabilita JavaScriptu* (nebo *zprostředkovatel komunikace*s JavaScriptem). Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.

![Blazor WebAssembly spouští kód .NET v prohlížeči pomocí WebAssembly.](index/_static/blazor-webassembly.png)

Když je aplikace Blazor WebAssembly sestavená a spuštěná v prohlížeči:

* C#soubory kódu a soubory Razor jsou kompilovány do sestavení .NET.
* Sestavení a modul runtime .NET se stáhnou do prohlížeče.
* Blazor WebAssembly spustí modul .NET runtime a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci. Modul runtime WebAssembly Blazor používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání modelu DOM a volání rozhraní API prohlížeče.

Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace. Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele. Blazor WebAssembly optimalizuje velikost datové části, aby se snížila doba stahování:

* Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí linkeru pro [převodní jazyk (IL)](xref:host-and-deploy/blazor/configure-linker).
* Odpovědi HTTP jsou komprimovány.
* Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.

## <a name="blazor-server"></a>Blazor Server

Blazor odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní. Blazor Server poskytuje podporu pro hostování komponent Razor na serveru v aplikaci ASP.NET Core. Aktualizace uživatelského rozhraní se zpracovávají přes [](xref:signalr/introduction) připojení k signalizaci.

Modul runtime zpracovává odeslání událostí uživatelského rozhraní z prohlížeče na server a po spuštění komponent aplikuje aktualizace uživatelského rozhraní odeslané serverem zpátky do prohlížeče.

Připojení, které používá server Blazor ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScriptu.

![Blazor Server spouští na serveru .NET code a komunikuje s model DOM (Document Object Model) v klientovi přes připojení k signalizaci.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem. Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat. C#kód může zavolat do kódu JavaScriptu a JavaScriptový kód může zavolat do C# kódu. Další informace naleznete v tématu <xref:blazor/javascript-interop>.

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Blazor implementuje [.NET Standard 2,0](/dotnet/standard/net-standard). .NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET. Knihovny tříd .NET Standard mohou být sdíleny napříč různými platformami .NET, například Blazor, .NET Framework, .NET Core, Xamarin, mono a Unity.

Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají výjimku <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
