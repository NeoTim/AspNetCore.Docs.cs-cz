---
title: Úvod do Blazor v ASP.NET Core
author: guardrex
description: Prozkoumejte službu ASP.NET Core Blazor způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614818"
---
# <a name="introduction-to-blazor"></a>Úvod do Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

## <a name="razor-components"></a>Komponenty Razor

Model hostingu na straně serveru z Blazor, *Razor komponenty*, je způsob, jak vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:

* Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.
* Sdílet logiku na straně serveru a na straně klienta aplikací napsaných pomocí rozhraní .NET.
* Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.

Součásti Razor podporuje zařízení core vyžaduje většina aplikací, včetně:

* Parametry
* Zpracování událostí
* Vytváření datových vazeb
* Směrování
* Injektáž závislostí
* Rozložení
* Šablon
* Kaskádové hodnoty

Součásti Razor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní. ASP.NET Core Razor součásti v .NET Core 3.0 přidává podporu pro hostování součásti Razor na server v aplikaci ASP.NET Core. Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.

Modul runtime:

* Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.
* Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.

Připojení používané komponenty Razor ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.

![Razor komponenty běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/aspnet-core-razor-components.png)

Další informace naleznete v tématu <xref:blazor/hosting-models#server-side-hosting-model>.

## <a name="components"></a>Komponenty

A *Razor komponenty* je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data. Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní. Součásti můžete vnořit a znovu použít.

Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET. Třída je obvykle napsané v podobě značek stránky Razor s *.razor* příponu souboru (Razor součástí) nebo stránka značky Razor s *.cshtml* příponu (Blazor).

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

Při této součásti se používá jinde v aplikaci, technologie IntelliSense urychluje vývoj aplikací pomocí syntaxe a parametrů dokončení.

Komponenty vykreslování do v paměti reprezentace prohlížeč DOM volána *vykreslení stromu* , který potom slouží k aktualizaci uživatelského rozhraní tak flexibilní a efektivní.

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript. Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny. .NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET. Blazor implementuje rozhraní .NET Standard 2.0. Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw <xref:System.PlatformNotSupportedException>. Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako jsou Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.

## <a name="blazor"></a>Blazor

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor je architektura jednostránkové aplikace pro vytváření interaktivních webových aplikací na straně klienta s .NET. Blazor používá otevřené webové standardy bez transpilation moduly plug-in nebo kódu. Blazor funguje v všechny moderní webové prohlížeče, včetně mobilních.

Pomocí rozhraní .NET v prohlížeči pro vývoj webů na straně klienta nabízí celou řadu výhod:

* **C#jazyk**: Psaní kódu v C# místo JavaScriptu.
* **.NET Ecosystem**: Využijte stávající ekosystém knihoven .NET.
* **Kompletní vývojové**: Sdílené složky serveru a na straně klienta logiku.
* **Rychlost a škálovatelnost**: .NET byla vytvořena pro výkon, spolehlivost a zabezpečení.
* **Špičkové nástroje**: Produktivní práci s aplikací Visual Studio ve Windows, Linuxu a macOS.
* **Stabilitu a konzistence**:  Sestavení na commonset jazyků, architektur a nástroje, které jsou stabilní, plně funkční a snadno se používá.

Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*). WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in. WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.

WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript. Ve stejnou dobu .NET kód proveden prostřednictvím WebAssembly běží v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.

![Blazor běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor.png)

Při Blazor aplikace je vytvořená a spustit v prohlížeči:

* C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.
* Sestavení a modul .NET runtime se stáhnou do prohlížeče.
* Blazor bootstraps modul .NET runtime a konfiguruje modul runtime k načtení sestavení pro aplikaci. Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor prostřednictvím zprostředkovatele komunikace s objekty jazyka JavaScript.

Blazor podporuje zařízení core vyžaduje většina aplikací, včetně:

* Parametry
* Zpracování událostí
* Vytváření datových vazeb
* Směrování
* Injektáž závislostí
* Rozložení
* Šablon
* Kaskádové hodnoty

Ke snížení objemu stažených aplikací, nepoužitý kód je odebrána z aplikace když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/blazor/configure-linker).

Blazor na straně klienta je model hostingu na straně klienta. Protože Blazor odděluje komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, je v tom, jak se dají hostovat Blazor flexibilitu. Použití ASP.NET [Razor komponenty](#razor-components) k hostiteli Razor komponent na serveru v aplikaci ASP.NET Core, kde se zpracovávají aktualizace uživatelského rozhraní pomocí připojení SignalR. Další informace naleznete v tématu <xref:blazor/hosting-models#server-side-hosting-model>. 

Velikost datové části je důležité výkonu koeficient pro použitelnost aplikace míra. Blazor optimalizuje velikost datové části snížit dobu stahování:

* Nevyužité části sestavení .NET se odeberou během procesu sestavení.
* Odpovědi protokolu HTTP jsou komprimované.
* Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.

[Součásti Razor](#razor-components) poskytuje menší velikost datové části než Blazor pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET. Razor součásti aplikace sloužit pouze značky soubory a statické prostředky klientům.

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](http://webassembly.org/)
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
