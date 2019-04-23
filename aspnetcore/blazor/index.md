---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte službu ASP.NET Core Blazor způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/18/2019
uid: blazor/index
ms.openlocfilehash: 74eeb003c249fc9ff8267ac855455f875806ccd9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982994"
---
# <a name="introduction-to-blazor"></a>Úvod do Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Vítá vás Blazor!

Vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:

* Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.
* Sdílet logiku na straně serveru a klientské aplikace napsané v rozhraní .NET.
* Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.

Blazor podporuje většina aplikací vyžaduje základní scénáře:

* Parametry
* Zpracování událostí
* Vytváření datových vazeb
* Směrování
* Injektáž závislostí
* Rozložení
* Šablony
* Kaskádové hodnoty

## <a name="components"></a>Komponenty

A *komponenty* v Blazor je prvek uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data. Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní. Součásti můžete vnořit a znovu použít.

Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET. Komponentní třída jsou obvykle zapsána ve formě značek stránky Razor s *.razor* příponu souboru. Součástí Blazor jsou někdy označovány jako součásti syntaxe Razor.

[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu. Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat. Stránky Razor a MVC zobrazení také používají syntaxi Razor. Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní. Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.

Následující kód je příklad vlastního dialogu součásti:

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

Pokud tuto součást používáte jinde v aplikaci, technologie IntelliSense v [sady Visual Studio](https://visualstudio.microsoft.com/vs/) urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.

Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.

## <a name="blazor-server-side"></a>Blazor na straně serveru

Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní. Serverové Blazor poskytuje podporu pro hostování součásti Razor na serveru v aplikaci ASP.NET Core. Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.

Modul runtime:

* Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.
* Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.

Připojení používané objektem Blazor serverové ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.

![Blazor serveru běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/blazor-server-side.png)

Další informace naleznete v tématu <xref:blazor/hosting-models#server-side>.

## <a name="blazor-client-side"></a>Blazor na straně klienta

Blazor na straně klienta je architektura jednostránkové aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET. Blazor na straně klienta používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu. Na straně klienta funguje Blazor v všechny moderní webové prohlížeče, včetně mobilních.

Pomocí rozhraní .NET v prohlížeči pro vývoj webů na straně klienta nabízí celou řadu výhod:

* **C#jazyk**: Psaní kódu v C# místo JavaScriptu.
* **.NET Ecosystem**: Využijte stávající ekosystém knihoven .NET.
* **Kompletní vývojové**: Sdílené složky serveru a na straně klienta logiku.
* **Rychlost a škálovatelnost**: .NET byla vytvořena pro výkon, spolehlivost a zabezpečení.
* **Špičkové nástroje**: Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.
* **Stabilitu a konzistence**:  Sestavení na společnou sadu jazyky, platformy a nástroje, které jsou stabilní, plně funkční a snadno se používá.

Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*). WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in. WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.

WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript. Ve stejnou dobu .NET kód proveden prostřednictvím WebAssembly běží v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.

![Blazor na straně klienta běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor-client-side.png)

Aplikace na straně klienta Blazor při vytvoření a spuštění v prohlížeči:

* C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.
* Sestavení a modul .NET runtime se stáhnou do prohlížeče.
* Blazor klientské bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci. Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor na straně klienta pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.

Ke snížení objemu stažených aplikací, nepoužitý kód je odebrána z aplikace když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/blazor/configure-linker).

Blazor na straně klienta je model hostingu na straně klienta. Protože Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, je v tom, jak se dají hostovat Blazor flexibilitu. Použití [Blazor serverové](#blazor-server-side) Blazor hostitele na serveru v aplikaci ASP.NET Core, kde se zpracovávají aktualizace uživatelského rozhraní pomocí připojení SignalR. Další informace naleznete v tématu <xref:blazor/hosting-models#server-side>. 

Velikost datové části je důležité výkonu koeficient pro použitelnost aplikace míra. Klientské Blazor optimalizuje velikost datové části snížit dobu stahování:

* Nevyužité části sestavení .NET se odeberou během procesu sestavení.
* Odpovědi protokolu HTTP jsou komprimované.
* Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.

[Serverové Blazor](#blazor-server-side) poskytuje menší velikost datové části než Blazor na straně klienta pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET. Blazor serverové aplikace sloužit pouze soubory značek a statické prostředky klientům.

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript. Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny. .NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET. Blazor implementuje rozhraní .NET Standard 2.0. Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw <xref:System.PlatformNotSupportedException>. Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako je například Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](http://webassembly.org/)
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
