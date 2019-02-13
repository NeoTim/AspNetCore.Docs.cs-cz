---
title: Úvod do komponent Razor
author: guardrex
description: Prozkoumejte službu ASP.NET Core Razor komponenty, způsob, jak vytvářet interaktivní webové na straně klienta uživatelské rozhraní s využitím .NET v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: razor-components/index
ms.openlocfilehash: 04a73d33cee0deedaf3dc97395836a936b580fbd
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159523"
---
# <a name="introduction-to-razor-components"></a>Úvod do komponent Razor

Podle [Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

*Součásti Razor* je způsob, jak vytvářet interaktivní webové uživatelské rozhraní s .NET na straně klienta:

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

Další informace naleznete v tématu <xref:razor-components/hosting-models#server-side-hosting-model>.

*Blazor* je experimentální model hostingu na straně klienta součástí syntaxe Razor. Blazor běží na rozhraní .NET v prohlížeči pomocí otevřených webových standardů bez kódu nebo moduly plug-in transpilation. Další informace naleznete v tématu <xref:razor-components/hosting-models#client-side-hosting-model>.

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

U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API pro součásti spolupracovat s použitím jazyka JavaScript. Součásti jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).

## <a name="additional-resources"></a>Další zdroje

* <xref:spa/blazor/index>
* [WebAssembly](http://webassembly.org/)
* [Průvodce jazykem C#](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
