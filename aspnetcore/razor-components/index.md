---
title: Úvod do komponent Razor
author: guardrex
description: Prozkoumejte službu ASP.NET Core Razor komponenty, pomocí rozhraní .NET web C#/Razor a HTML.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/09/2019
uid: razor-components/index
ms.openlocfilehash: e8d75a0647704f1ff05e70a96abbb2e448868165
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56102926"
---
# <a name="introduction-to-razor-components"></a>Úvod do komponent Razor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

*Součásti Razor* je nový způsob, jak vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:

* Vytvářet bohaté interaktivní uživatelských rozhraní pomocí C# místo JavaScriptu.
* Sdílet logiku na straně serveru a na straně klienta aplikací napsaných pomocí rozhraní .NET.
* Vykreslení uživatelského rozhraní jako HTML a CSS pro podporu široké prohlížeče, včetně mobilních.

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

Součásti Razor podporují zařízení core vyžaduje většina aplikací, včetně:

* Parametry
* Zpracování událostí
* Vytváření datových vazeb
* Směrování
* Injektáž závislostí
* Rozložení
* Šablon
* Kaskádové hodnoty

## <a name="javascript-interop"></a>Interoperabilita JavaScriptu

Pro aplikace, které vyžadují knihovny jazyka JavaScript a prohlížeč rozhraní API třetích stran nastavení spolupráci komponenty Razor s použitím jazyka JavaScript. Razor komponenty jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).

## <a name="hosting-models"></a>Modely hostingu

### <a name="server-side-hosting-model"></a>Model hostingu na straně serveru

Protože komponenty Razor oddělit komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, existuje určitá flexibilita v jak se dají hostovat součásti syntaxe Razor. ASP.NET Core Razor součásti v .NET Core 3.0 přidává podporu pro hostování součásti Razor na server v aplikaci ASP.NET Core. Aktualizace uživatelského rozhraní jsou zpracovány prostřednictvím připojení SignalR.

Modul runtime:

* Obslužné rutiny odeslání události uživatelského rozhraní z prohlížeče na server.
* Platí uživatelského rozhraní aktualizací odeslané serverem zpět do prohlížeče po spuštění součásti.

Připojení používané komponenty Razor ke komunikaci s prohlížeči slouží také ke zpracování spolupráce volání JavaScriptu.

![Razor komponenty běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/aspnet-core-razor-components.png)

Další informace naleznete v tématu <xref:razor-components/hosting-models#server-side-hosting-model>.

### <a name="client-side-hosting-model"></a>Model hostingu na straně klienta

*Blazor* je experimentální model hostingu na straně klienta součástí syntaxe Razor. Blazor běží na rozhraní .NET v prohlížeči pomocí otevřených webových standardů bez kódu nebo moduly plug-in transpilation. Blazor funguje v všechny moderní webové prohlížeče, včetně mobilních.

Spouštění kódu .NET uvnitř webových prohlížečů je možné podle [WebAssembly](http://webassembly.org) (zkrácené *wasm*). WebAssembly je otevřít web, standard a podporovaných webových prohlížečů bez modulů plug-in. WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.

WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript. Ve stejnou dobu spuštění kódu WebAssembly v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.

![Blazor běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor.png)

Při Blazor aplikace je vytvořená a spustit v prohlížeči:

* C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.
* Sestavení a modul .NET runtime se stáhnou do prohlížeče.
* Blazor bootstrap modul .NET runtime pomocí jazyka JavaScript a konfiguruje modul runtime k načtení sestavení. Volání dokumentu Object Model (DOM) manipulaci a prohlížeč rozhraní API jsou zpracovány tímto modulem Blazor prostřednictvím zprostředkovatele komunikace s objekty jazyka JavaScript.

Pro podporu starších prohlížečích, které nepodporují WebAssembly, použijte [model hostingu na straně serveru](#server-side-hosting-model).

Další informace naleznete v tématu <xref:razor-components/hosting-models#client-side-hosting-model>.

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](http://webassembly.org/)
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
