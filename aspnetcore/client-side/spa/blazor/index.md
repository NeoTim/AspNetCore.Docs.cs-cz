---
title: Úvod do Blazor
author: guardrex
description: Prozkoumejte Blazor ASP.NET Core, nový způsob, jak vytvářet interaktivní aplikace na straně klienta s .NET, na kterých běží v prohlížeči s WebAssembly.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: spa/blazor/index
ms.openlocfilehash: 87e294a975e4168add046166a95e02e485db131d
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068245"
---
# <a name="introduction-to-blazor"></a>Úvod do Blazor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

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

Ke snížení objemu stažených aplikací, nepoužitý kód je odebrána z aplikace když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/razor-components-blazor/configure-linker).

Blazor je model hostingu na straně klienta pro součásti syntaxe Razor. Protože komponenty Razor oddělit komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, existuje určitá flexibilita v jak se dají hostovat součásti syntaxe Razor. Pomocí ASP.NET Core Razor součásti k hostiteli Razor komponent na serveru v aplikaci ASP.NET Core ve kterém se zpracovávají aktualizace uživatelského rozhraní pomocí připojení SignalR. Další informace naleznete v tématu <xref:razor-components/index> a <xref:razor-components/hosting-models#server-side-hosting-model>. 

## <a name="components"></a>Komponenty

A *Razor komponenty* je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data. Součásti zpracovávat události uživatele a definovat flexibilní logiku pro vykreslení uživatelského rozhraní. Součásti můžete vnořit a znovu použít.

Komponenty jsou součástí sestavení .NET, které lze sdílet a distribuovat jako balíčky NuGet třídy rozhraní .NET. Třídu lze zapsat buď v podobě značek stránky Razor (*.cshtml*) nebo jako C# třídy (*.cs*).

[Razor](xref:mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu. Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat. Stránky Razor a MVC zobrazení také používají syntaxi Razor. Na rozdíl od Razor Pages a zobrazení MVC, která jsou postavené na modelu žádost odpověď, používají součásti speciálně pro zpracování sestavení uživatelského rozhraní. Komponenty Razor můžete použít speciálně pro logika uživatelského rozhraní na straně klienta a skládání.

Následující kód je příklad vlastního dialogu komponenty v souboru Razor (*DialogComponent.cshtml*):

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
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

U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API Blazor spolupracuje s použitím jazyka JavaScript. Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny. .NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET. Blazor implementuje rozhraní .NET Standard 2.0. Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw <xref:System.PlatformNotSupportedException>. Knihovny tříd .NET standard můžete sdílet na různých platformách .NET, jako jsou Blazor, rozhraní .NET Framework, .NET Core, Xamarin, Mono a Unity.

## <a name="optimization"></a>Optimalizace

Velikost datové části je důležité výkonu koeficient pro použitelnost aplikace míra. Blazor optimalizuje velikost datové části snížit dobu stahování:

* Nevyužité části sestavení .NET se odeberou během procesu sestavení.
* Odpovědi protokolu HTTP jsou komprimované.
* Modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.

[Serverové komponenty Razor](xref:razor-components/index) poskytuje ještě menší velikost datové části než Blazor pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET. Razor součásti aplikace sloužit pouze značky soubory a statické prostředky klientům.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-components/index>
* [WebAssembly](http://webassembly.org/)
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
