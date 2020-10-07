---
title: Úvod do ASP.NET Core Blazor
author: guardrex
description: Prozkoumejte ASP.NET Core Blazor , způsob sestavení interaktivního webového uživatelského rozhraní na straně klienta pomocí .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: 7ad374fdc7452664e3367d6ef214fd4cebdf3b08
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805518"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a>Úvod do ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

*Vítá vás Blazor !*

Blazor je rozhraní pro sestavování interaktivního webového uživatelského rozhraní na straně klienta pomocí [.NET](/dotnet/standard/tour):

* Vytvářejte bohatá interaktivní uživatelská rozhraní pomocí [jazyka C#](/dotnet/csharp/) namísto [JavaScriptu](https://www.javascript.com).
* Sdílejte logiku aplikace na straně serveru a klienta napsanou v .NET.
* Vykreslete uživatelské rozhraní jako HTML a CSS pro podporu pro rozsáhlou prohlížeč, včetně mobilních prohlížečů.
* Integrujte s moderními hostujícími platformami, jako je například [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).

Použití rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:

* Psát kód v jazyce C# namísto JavaScriptu.
* Využijte stávající ekosystém .NET [knihoven .NET](/dotnet/standard/class-libraries).
* Sdílejte logiku aplikace napříč serverem a klientem.
* Využijte výhod. Výkon, spolehlivost a zabezpečení sítě.
* [Aplikace Visual Studio](https://visualstudio.microsoft.com) v systémech Windows, Linux a MacOS vám zůstane produktivní.
* Sestavte se na společné sadě jazyků, platforem a nástrojů, které jsou stabilní, funkce s bohatou funkcí a snadno použitelné.

## <a name="components"></a>Komponenty

Blazor aplikace jsou založené na *komponentách*. Komponenta v nástroji Blazor je prvek uživatelského rozhraní, jako je například stránka, dialog nebo formulář pro zadávání dat.

Komponenty jsou třídy .NET C# sestavené do [sestavení .NET](/dotnet/standard/assembly/) , která:

* Definujte flexibilní logiku vykreslování uživatelského rozhraní.
* Zpracování událostí uživatele.
* Může být vnořen a znovu použit.
* Lze sdílet a distribuovat jako [ Razor knihovny tříd](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).

Třída komponenty je obvykle napsána ve formě [Razor](xref:mvc/views/razor) stránky s označením s `.razor` příponou souboru. Komponenty v nástroji Blazor jsou formálně označovány jako * Razor komponenty*. Razor je syntaxe pro kombinování značek HTML pomocí kódu jazyka C# navrženého pro produktivitu vývojářů. Razor umožňuje přepínat mezi značkami HTML a C# ve stejném souboru s podporou programování [IntelliSense](/visualstudio/ide/using-intellisense) v aplikaci Visual Studio. Razor Stránky a MVC také používají Razor . Na rozdíl od Razor stránek a MVC, které jsou vytvořeny kolem modelu požadavků a odpovědí, se komponenty používají konkrétně pro logiku a sestavení uživatelského rozhraní na straně klienta.

Blazor používá přirozené značky HTML pro kompozici uživatelského rozhraní. Následující Razor kód ukazuje komponentu ( `Dialog.razor` ), která zobrazuje dialogové okno a zpracovává událost, když uživatel vybere tlačítko:

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

V předchozím příkladu `OnYes` je metoda C# aktivovaná `onclick` událostí tlačítka. Text ( `ChildContent` ) a title () v dialogovém okně `Title` jsou poskytovány následující komponentou, která tuto komponentu používá v uživatelském rozhraní.

`Dialog`Komponenta je vnořena do jiné komponenty pomocí značky jazyka HTML. V následujícím příkladu `Index` Komponenta ( `Pages/Index.razor` ) používá předchozí `Dialog` komponentu. `Title`Atribut tagu předává hodnotu pro název `Dialog` `Title` Vlastnosti komponenty.  `Dialog`Text součásti ( `ChildContent` ) je nastaven podle obsahu `<Dialog>` elementu. Při `Dialog` Přidání komponenty do `Index` komponenty [IntelliSense v aplikaci Visual Studio](/visualstudio/ide/using-intellisense) urychlí vývoj pomocí syntaxe a dokončování parametrů.

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Dialogové okno se vykreslí, když `Index` se k součásti dostanete v prohlížeči. Když uživatel vybere tlačítko, v konzole nástroje pro vývojáře v prohlížeči se zobrazí zpráva zapsaná `OnYes` metodou:

![Komponenta dialogového okna vykreslená v prohlížeči, která je vnořená v rámci součásti indexu. Konzola nástroje pro vývojáře v prohlížeči zobrazuje zprávu zapsanou v kódu jazyka C#, když uživatel vybere Ano! tlačítko v uživatelském rozhraní.](index/_static/dialog.png)

Komponenty vykreslí do reprezentace v paměti model DOM (Document Object Model) v prohlížeči [(DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) , která se nazývá *strom vykreslování*, který se používá k aktualizaci uživatelského rozhraní flexibilním a efektivním způsobem.

## Blazor WebAssembly

Blazor WebAssembly je [Architektura jednostránkové aplikace (Spa)](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) pro vytváření interaktivních webových aplikací na straně klienta s .NET. Blazor WebAssembly používá otevřené webové standardy bez modulů plug-in nebo opětovné kompilování kódu do jiných jazyků. Blazor WebAssembly funguje ve všech moderních webových prohlížečích, včetně mobilních prohlížečů.

Spuštění kódu .NET ve webových prohlížečích je umožněno webovým [sestavením](https://webassembly.org) (zkráceně `wasm` ). WebAssembly je formát kompaktního bajtu optimalizovaného pro rychlé stažení a maximální rychlost spuštění. WebAssembly je otevřený webový standard a podporuje se ve webových prohlížečích bez modulů plug-in.

Kód webového sestavení má přístup k úplným funkcím prohlížeče prostřednictvím JavaScriptu, který se označuje jako *interoperabilita JavaScriptu*, často se zkracuje na interoperabilitu *JavaScript* nebo *js*. Kód .NET spuštěný pomocí webového sestavení v prohlížeči se spouští v izolovaném prostoru JavaScript v prohlížeči s ochranou, že izolovaný prostor poskytuje proti škodlivým akcím v klientském počítači.

![::: No-Loc (Blazor WebAssembly)::: spustí kód .NET v prohlížeči pomocí webového sestavení.](index/_static/blazor-webassembly.png)

Když Blazor WebAssembly je aplikace sestavená a spuštěná v prohlížeči:

* Soubory a soubory kódu jazyka C# Razor jsou zkompilovány do sestavení .NET.
* Sestavení a [modul runtime .NET](/dotnet/framework/get-started/overview) se stáhnou do prohlížeče.
* Blazor WebAssembly spustí modul .NET runtime a nakonfiguruje modul runtime, aby načetl sestavení pro aplikaci. Blazor WebAssemblyModul runtime používá zprostředkovatele komunikace s JavaScriptem pro zpracování volání DOM a volání rozhraní API prohlížeče.

Velikost publikované aplikace, její *Velikost datové části*, je důležitým faktorem výkonu pro useability aplikace. Stažení velké aplikace může trvat poměrně dlouhou dobu, než se stáhne do prohlížeče, což snižuje činnost koncového uživatele. Blazor WebAssembly optimalizuje velikost datové části, aby se snížila doba stahování:

::: moniker range=">= aspnetcore-5.0"

* Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [oříznutí prostředního jazyka (IL)](xref:blazor/host-and-deploy/configure-trimmer).
* Odpovědi HTTP jsou komprimovány.
* Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Nepoužitý kód se z aplikace vyčerpá, když se publikuje pomocí [linkeru pro převodní jazyk (IL)](xref:blazor/host-and-deploy/configure-linker).
* Odpovědi HTTP jsou komprimovány.
* Modul runtime .NET a sestavení jsou ukládány do mezipaměti v prohlížeči.

::: moniker-end

## Blazor Server

Blazor odpojí logiku vykreslování komponenty od způsobu použití aktualizací uživatelského rozhraní. *Blazor Server* poskytuje podporu pro hostování Razor komponent na serveru aplikace v ASP.NET Core. Aktualizace uživatelského rozhraní se zpracovávají přes [SignalR](xref:signalr/introduction) připojení.

Modul runtime zpracovává:

* Odesílání událostí uživatelského rozhraní z prohlížeče na server.
* Použití aktualizací uživatelského rozhraní pro vykreslenou komponentu, která je poslána zpět serverem.

Připojení, které používá Blazor Server ke komunikaci s prohlížečem, slouží také ke zpracování volání interoperability JavaScript.

![::: No-Loc (Blazor Server)::: spustí .NET kód na serveru a spolupracuje s model DOM (Document Object Model) na klientovi přes::: No-Loc (signál)::: Connection](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

Pro aplikace, které vyžadují knihovny JavaScript třetích stran a přístup k rozhraním API prohlížeče, komponenty, které spolupracují s JavaScriptem. Komponenty umožňují použití libovolné knihovny nebo rozhraní API, které může JavaScript používat. Kód jazyka c# může [zavolat do kódu jazyka JavaScript](xref:blazor/call-javascript-from-dotnet)a kód JavaScriptu může [volat kód jazyka c#](xref:blazor/call-dotnet-from-javascript).

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Blazor implementuje [.NET Standard](/dotnet/standard/net-standard), který umožňuje Blazor projektům odkazovat na knihovny, které odpovídají specifikacím .NET Standard. .NET Standard je formální specifikace rozhraní .NET API, která jsou společná pro implementace v rozhraní .NET. Knihovny tříd .NET Standard lze sdílet napříč různými platformami .NET, jako jsou například Blazor , .NET Framework, .NET Core, Xamarin, mono a Unity.

Rozhraní API, která nejsou platná v rámci webového prohlížeče (například přístup k systému souborů, otevření soketu a vlákna) vyvolávají výjimku <xref:System.PlatformNotSupportedException> .

## <a name="additional-resources"></a>Další zdroje informací

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [C# Guide](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
* [Super Blazor ](https://github.com/AdrienTorris/awesome-blazor) komunitní odkazy
