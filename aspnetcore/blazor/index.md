---
title: Úvod do ASP.NET Core Blazor
author: guardrex
description: Prozkoumejte ASP.NET Core Blazor, způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta s .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/index
ms.openlocfilehash: 9f43735fc02ba9f8da4ba27c8b5763d587b5429a
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159921"
---
# <a name="introduction-to-aspnet-core-opno-locblazor"></a>Úvod do ASP.NET Core Blazor

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

aplikace Blazor jsou založené na *komponentách*. Součást v Blazor je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.

Komponenty jsou třídy .NET integrované do sestavení .NET, která:

* Definujte flexibilní logiku vykreslování uživatelského rozhraní.
* Zpracování událostí uživatele.
* Může být vnořen a znovu použit.
* Lze sdílet a distribuovat jako [knihovny tříd Razor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).

Třída komponenty je obvykle napsána ve formě stránky značek [Razor](xref:mvc/views/razor) s příponou souboru *. Razor* . Komponenty v Blazor jsou formálně označovány jako *komponenty Razor*. Razor je syntaxe pro kombinování značek HTML s C# kódem navrženým pro produktivitu vývojářů. Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou [technologie IntelliSense](/visualstudio/ide/using-intellisense) . Razor Pages a MVC také používají syntaxi Razor. Na rozdíl od Razor Pages a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.

Následující kód Razor znázorňuje komponentu (*dialog. Razor*), která může být vnořena do jiné komponenty:

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

Obsah těla dialogového okna (`ChildContent`) a název (`Title`) je součástí komponenty, která tuto komponentu používá v uživatelském rozhraní. `OnYes` je C# metoda aktivovaná v události `onclick` tlačítka.

Blazor používá přirozené značky HTML pro kompozici uživatelského rozhraní. Prvky HTML určují komponenty a atributy značky přecházejí hodnoty do vlastností součásti.

V následujícím příkladu `Index` komponenta používá komponentu `Dialog`. `ChildContent` a `Title` jsou nastaveny pomocí atributů a obsahu elementu `<Dialog>`.

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

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly je jednostránková architektura aplikací pro vytváření interaktivních webových aplikací na straně klienta s .NET. Blazor webové sestavení používá open Web Standards bez modulů plug-in a transpilation kódu a funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.

Spouštění kódu .NET ve webových prohlížečích je umožněno webovým [sestavením](https://webassembly.org) (zkráceným *wasm*). WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění. WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.

Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, označovanému jako *interoperabilita JavaScriptu* (nebo *zprostředkovatel komunikace s JavaScriptem*). Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.

![[! Evřít. NO-LOC (Blazor)] WebAssembly spouští kód .NET v prohlížeči pomocí WebAssembly.](index/_static/blazor-webassembly.png)

Při sestavení a spuštění aplikace Blazor WebAssembly v prohlížeči:

* C#soubory kódu a soubory Razor jsou kompilovány do sestavení .NET.
* Sestavení a modul runtime .NET se stáhnou do prohlížeče.
* Blazor WebAssembly spustí modul runtime .NET a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci. Modul runtime Blazorho webového sestavení používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání modelu DOM a volání rozhraní API prohlížeče.

Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace. Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele. Blazor WebAssembly optimalizuje velikost datové části, aby se snížila doba stahování:

* Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [linkeru pro převodní jazyk (IL)](xref:host-and-deploy/blazor/configure-linker).
* Odpovědi HTTP jsou komprimovány.
* Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.

## <a name="opno-locblazor-server"></a>Server Blazor

Blazor odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní. Blazor Server poskytuje podporu pro hostování komponent Razor na serveru aplikace v ASP.NET Core. Aktualizace uživatelského rozhraní jsou zpracovávány prostřednictvím [SignalRho](xref:signalr/introduction) připojení.

Modul runtime zpracovává odeslání událostí uživatelského rozhraní z prohlížeče na server a po spuštění komponent aplikuje aktualizace uživatelského rozhraní odeslané serverem zpátky do prohlížeče.

Připojení, které používá Blazor Server ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScriptu.

![[! Evřít. NO-LOC (Blazor)] Server spouští .NET kód na serveru a komunikuje s model DOM (Document Object Model) na klientovi přes [! Evřít. Připojení NO-LOC (Signal)]](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem. Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat. C#kód může zavolat do kódu JavaScriptu a JavaScriptový kód může zavolat do C# kódu. Další informace najdete v tématu <xref:blazor/javascript-interop>.

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Blazor implementuje [.NET Standard 2,0](/dotnet/standard/net-standard). .NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET. Knihovny tříd .NET Standard mohou být sdíleny napříč různými platformami .NET, například Blazor, .NET Framework, .NET Core, Xamarin, mono a Unity.

Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Super Blazor](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy
