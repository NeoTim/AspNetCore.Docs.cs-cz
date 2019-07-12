---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte službu ASP.NET Core Blazor způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 07/01/2019
uid: blazor/index
ms.openlocfilehash: e0af0f27d79973f10493251c3f6c6daebe1b99a8
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855771"
---
# <a name="introduction-to-blazor"></a>Úvod do Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

*Vítá vás Blazor!*

Blazor je architektura určená k vytváření interaktivní webové uživatelské rozhraní s .NET na straně klienta:

* Vytvářet bohaté interaktivní uživatelské rozhraní pomocí C# místo JavaScriptu.
* Sdílet logiku na straně serveru a klientské aplikace napsané v rozhraní .NET.
* Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.

Pomocí rozhraní .NET pro vývoj webů na straně klienta nabízí následující výhody:

* Psaní kódu v C# místo JavaScriptu.
* Využijte stávající ekosystému .NET knihoven .NET.
* Aplikace logiky sdílet mezi serverem a klientem.
* Výhody. NET jeho výkonu, spolehlivosti a zabezpečení.
* Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.
* Sestavení na společnou sadu jazyky, platformy a nástroje, které jsou stabilní, plně funkční a snadno se používá.

## <a name="components"></a>Komponenty

Blazor aplikací jsou založené na *komponenty*. Komponenta v Blazor je prvek uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data.

Komponenty jsou součástí sestavení .NET třídy rozhraní .NET, který:

* Definujte flexibilní logiku pro vykreslení uživatelského rozhraní.
* Zpracování událostí uživatele.
* Mohou být vnořené a znovu použít.
* Lze sdílet a distribuovat jako [knihovny tříd Razor](xref:razor-pages/ui-class) nebo [balíčky NuGet](/nuget/what-is-nuget).

Komponentní třída jsou obvykle zapsána ve formě [Razor](xref:mvc/views/razor) stránku značek *.razor* příponu souboru. Součástí Blazor formálně označují jako *Razor komponenty*. Razor je syntaxe pro kombinování značka jazyka HTML s C# kód určený pro produktivitu vývojářů. Razor umožňuje přepínat mezi značka jazyka HTML a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat. Stránky Razor a MVC také použít syntaxi Razor. Na rozdíl od Razor Pages a MVC, které jsou postavené na modelu žádost odpověď, používají součásti speciálně pro logika uživatelského rozhraní na straně klienta a skládání.

Následující kód Razor ukazuje komponentu (*Dialog.razor*), který může být vnořena do jiné součásti:

```cshtml
<div>
    <h1>@Title</h1>

    @ChildContent

    <button @onclick="@OnYes">Yes!</button>
</div>

@code {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button was selected.");
    }
}
```

Dialogové okno textový obsah (`ChildContent`) a název (`Title`) jsou poskytovány komponenta, která používá tuto součást ve svém uživatelském rozhraní. `OnYes` je C# metoda aktivované pomocí tlačítka `onclick` událostí.

Blazor používá přirozené značky HTML pro kompozici uživatelského rozhraní. Určete součásti elementů HTML a atributy značky předat hodnoty vlastnosti komponenty.

V následujícím příkladu `Index` komponenta používá `Dialog` komponenty. `ChildContent` a `Title` jsou nastavené tak, že atributy a obsah `<Dialog>` elementu.

*Index.razor*:

```cshtml
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<Dialog Title="Blazor">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

Dialogového okna je vykreslen během nadřazeného (*Index.razor*) je přístupná v prohlížeči:

![Komponenty dialogového okna vykreslení v prohlížeči](index/_static/dialog.png)

Při použití této součásti v aplikaci, technologie IntelliSense v [sady Visual Studio](/visualstudio/ide/using-intellisense) a [Visual Studio Code](https://code.visualstudio.com/docs/editor/intellisense) urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.

Komponenty vykreslování do reprezentaci v paměti z prohlížeče Document Object Model (DOM) volá *vykreslení stromu*, který se používá k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.

## <a name="blazor-client-side"></a>Blazor na straně klienta

Blazor na straně klienta je jednostránkové aplikace rozhraní pro vytváření aplikací pro interaktivní webové na straně klienta s .NET. Blazor na straně klienta používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu a pracuje ve všechny moderní webové prohlížeče, včetně mobilních.

Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](https://webassembly.org) (zkrácené *wasm*). WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost. WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in.

WebAssembly kód může přistupovat k úplné funkce aplikace prohlížeče prostřednictvím JavaScriptu, volá *vzájemná funkční spolupráce jazyka JavaScript* (nebo *zprostředkovatele komunikace s objekty jazyka JavaScript*). .NET kód proveden prostřednictvím WebAssembly v prohlížeči se spustí v sandboxu jazyka JavaScript v prohlížeči pomocí ochrany, které poskytuje izolovaný prostor proti škodlivé akce v klientském počítači.

![Blazor na straně klienta běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor-client-side.png)

Aplikace na straně klienta Blazor při vytvoření a spuštění v prohlížeči:

* C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.
* Sestavení a modul .NET runtime se stáhnou do prohlížeče.
* Blazor klientské bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci. Modul runtime Blazor na straně klienta používá zprostředkovatele komunikace s objekty jazyka JavaScript ke zpracování modelu DOM manipulaci a prohlížeče volání rozhraní API.

Velikost publikované aplikace její *velikost datové části*, je důležité výkonu koeficient pro použitelnost aplikace míra. Velké aplikace trvá poměrně dlouhou dobu stahování v prohlížeči, který snižuje činnost koncového uživatele. Klientské Blazor optimalizuje velikost datové části snížit dobu stahování:

* Nepoužitý kód je odebrána z aplikace, když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/blazor/configure-linker).
* Odpovědi protokolu HTTP jsou komprimované.
* Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.

## <a name="blazor-server-side"></a>Blazor na straně serveru

Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní. Serverové Blazor poskytuje podporu pro hostování součásti Razor na serveru v aplikaci ASP.NET Core. Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

Modul runtime zpracovává odeslání události uživatelského rozhraní z prohlížeče na server a aplikuje aktualizace uživatelského rozhraní odeslané serverem zpět do prohlížeče po spuštění součásti.

Připojení používané objektem Blazor serverové ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.

![Blazor serveru běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/blazor-server-side.png)

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

U aplikací, které vyžadují knihovny JavaScript třetích stran a přístup k prohlížeči rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript. Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace naleznete v tématu <xref:blazor/javascript-interop>.

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Implementuje Blazor [.NET Standard 2.0](/dotnet/standard/net-standard). .NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET. Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako je například Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.

Vyvolat rozhraní API, které nejsou použitelné v rámci webového prohlížeče (například přístup k systému souborů, otevřeme soket a práce s vlákny) <xref:System.PlatformNotSupportedException>.

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](https://webassembly.org/)
* <xref:blazor/hosting-models>
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
