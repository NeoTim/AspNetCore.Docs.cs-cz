---
title: Úvod do komponent Razor
author: guardrex
description: Prozkoumejte Blazor, pomocí rozhraní .NET web C#/Razor a kód HTML, který běží v prohlížeči s WebAssembly.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/index
ms.openlocfilehash: 0f7a4b2ec404b6ceec2e643fea9e0635de6ad716
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668106"
---
# <a name="introduction-to-razor-components"></a>Úvod do komponent Razor

Podle [Steve Sanderson](http://blog.stevensanderson.com), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

*ASP.NET Core Razor komponenty* provede na straně serveru v ASP.NET Core, zatímco *Blazor* (Razor součásti spustit klienta) je experimentální webová platforma .NET pomocí C#/Razor a kód HTML, který běží v Prohlížeč s WebAssembly. Blazor poskytuje všechny výhody architekturu uživatelského rozhraní na straně klienta web pomocí rozhraní .NET na straně klienta.

Vývoj pro web je vylepšené ve spoustě ohledů v průběhu let, ale vytváření moderních webových aplikací stále představuje výzvy. V prohlížeči pomocí rozhraní .NET nabízí celou řadu výhod, které vám mohou pomoci vývoj pro web jednodušší a produktivnější:

* **Stabilitu a konzistence**: .NET poskytuje napříč platformami, které jsou stabilní, plně funkční a snadno se používá standardizované programovací rozhraní.
* **Moderní jazyky inovativní**: jazyků .NET neustále vylepšujeme inovativní novými funkcemi jazyka.
* **Špičkové nástroje**: Produktové řady Visual Studio nabízí báječný vývojové prostředí .NET napříč platformami ve Windows, Linuxu a macOS.
* **Rychlost a škálovatelnost**: má silné historie výkonu, spolehlivosti a zabezpečení pro vývoj aplikací pro .NET. Pomocí kompletního řešení .NET je snazší vytvářet rychlé, spolehlivé a zabezpečené aplikace.
* **Kompletní vývoje, který využívá stávající dovednosti: pište**: C#/ Razor vývojáři použít své stávající C#/kódu Razor dovednosti k zápisu na straně klienta a sdílené složky serveru a logiky na straně klienta mezi aplikacemi.
* **Podpora prohlížeče široké**: Součásti Razor vykreslení uživatelského rozhraní jako běžný značky a JavaScript. Blazor běží na rozhraní .NET v prohlížeči pomocí otevřených webových standardů se žádné moduly plug-in a transpilation žádný kód. Blazor funguje v všechny moderní webové prohlížeče, včetně mobilních.

## <a name="hosting-models"></a>Modelech hostování

### <a name="server-side-hosting-model"></a>Model hostingu na straně serveru

Protože komponenty Razor oddělit komponenty vykreslování logiku z použití aktualizace uživatelského rozhraní, existuje určitá flexibilita v jak se dají hostovat součásti syntaxe Razor. ASP.NET Core Razor součásti v .NET Core 3.0 přidává podporu pro hostování součásti Razor na server v aplikaci ASP.NET Core, kde jsou všechny aktualizace uživatelského rozhraní zpracovány pomocí připojení SignalR. Modul runtime zpracovává odeslání události uživatelského rozhraní z prohlížeče na server a poté použije aktualizace uživatelského rozhraní odeslané serverem zpět do prohlížeče po spuštění součásti. Stejné připojení slouží také ke zpracování spolupráce volání JavaScriptu.

![Razor komponenty běží kód .NET na serveru a komunikuje s modelu objektu dokumentu na straně klienta pomocí připojení SignalR](index/_static/aspnet-core-razor-components.png)

Další informace naleznete v tématu <xref:razor-components/hosting-models#server-side-hosting-model>.

### <a name="client-side-hosting-model"></a>Model hostingu na straně klienta

Spouštění kódu .NET uvnitř webových prohlížečů je možné díky relativně nové technologie [WebAssembly](http://webassembly.org) (zkrácené *wasm*). WebAssembly je standardní webovému rozhraní open a je podporován ve webových prohlížečích bez modulů plug-in. WebAssembly je formát compact bajtového kódu optimalizovaný pro rychlé stažení a spuštění maximální rychlost.

WebAssembly kód může přistupovat k úplné funkce aplikace v prohlížeči pomocí zprostředkovatele komunikace s objekty jazyka JavaScript. Ve stejnou dobu spuštění kódu WebAssembly v stejné důvěryhodné izolovaného prostoru jako jazyka JavaScript, aby se zabránilo škodlivé akce v klientském počítači.

![Blazor běží v prohlížeči s WebAssembly kód .NET.](index/_static/blazor.png)

Při Blazor aplikace je vytvořená a spustit v prohlížeči:

1. C#soubory kódu a Razor soubory jsou zkompilovány do sestavení .NET.
1. Sestavení a modul .NET runtime se stáhnou do prohlížeče.
1. Blazor bootstrap modul .NET runtime pomocí jazyka JavaScript a nakonfiguruje se načíst odkazy na požadovaná sestavení modulu runtime. Dokument object model (DOM) manipulace s prohlížeči volání rozhraní API a jsou zpracovány tímto modulem Blazor prostřednictvím vzájemná funkční spolupráce jazyka JavaScript.

Pro podporu starších prohlížečích, které nepodporují WebAssembly, můžete použít komponenty technologie ASP.NET Core Razor [model hostingu na straně serveru](#server-side-hosting-model).

Další informace naleznete v tématu <xref:razor-components/hosting-models#client-side-hosting-model>.

## <a name="components"></a>Komponenty

Aplikace pro *komponenty*. Součást je část uživatelského rozhraní, jako je například formulář položka stránky, dialogové okno nebo data. Součásti lze vnořené, znovu použít a sdílet mezi projekty.

A *komponenty* je třída rozhraní .NET. Třídy mohou být napsány buď přímo, jako C# třídy (*\*.cs*), nebo běžně ve formě značek stránky Razor (*\*.cshtml*).

[Razor](/aspnet/core/mvc/views/razor) je syntaxe pro kombinování značka jazyka HTML s C# kódu. Razor je navržená pro produktivitu vývojářů, umožňuje vývojářům přepínat mezi značky a C# ve stejném souboru s [IntelliSense](/visualstudio/ide/using-intellisense) podporovat. Následující kód je příklad základní dialogové okno vlastní komponenty v souboru Razor (*DialogComponent.cshtml*):

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

Může být součástí:

* Vnořené.
* Vytvořené pomocí syntaxe Razor (*\*.cshtml*) nebo C# (*\*.cs*) kód.
* Sdílené úložiště přes knihovny tříd.
* Testování částí bez nutnosti prohlížeč modelu DOM.

## <a name="infrastructure"></a>Infrastruktura

Součásti Razor nabízí základní zařízení, které vyžadují většina aplikací, včetně:

* Rozložení
* Směrování
* Injektáž závislostí

Všechny tyto funkce jsou volitelné. Pokud některou z těchto funkcí se nepoužívá v aplikaci, implementace je odebrána z aplikace, když se publikuje [Intermediate Language (IL) Linkeru](xref:host-and-deploy/razor-components/configure-linker).

## <a name="code-sharing-and-net-standard"></a>Sdílení kódu a .NET Standard

Aplikace můžete odkazovat a využívat stávající [.NET Standard](/dotnet/standard/net-standard) knihovny. .NET standard je formální specifikaci rozhraní API pro .NET, které jsou společné pro implementace .NET. Se podporuje .NET standard 2.0 nebo vyšší. Rozhraní API, které nejsou použitelné ve webovém prohlížeči (například přístup k systému souborů, otevřeme soket, dělení na vlákna a další funkce) throw [PlatformNotSupportedException](/dotnet/api/system.platformnotsupportedexception). Knihovny tříd .NET standard je možné sdílet napříč kódu serveru a v aplikacích založených na prohlížeči.

## <a name="javascript-interop"></a>Zprostředkovatel komunikace s objekty jazyka JavaScript

U aplikací, které vyžadují knihovny třetích stran jazyka JavaScript a prohlížeč rozhraní API WebAssembly slouží pro spolupráci s použitím jazyka JavaScript. Razor komponenty jsou schopny použití jakékoli knihovnu nebo rozhraní API, které jazyk JavaScript je možné použít. C#kód může volat do kódu jazyka JavaScript a kód jazyka JavaScript může volat do C# kódu. Další informace najdete v tématu [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop).

## <a name="optimization"></a>Optimalizace

Pro aplikace na straně klienta je důležité velikost datové části. Blazor optimalizuje velikost datové části snížit dobu stahování. Například nevyužité části sestavení .NET se odeberou během procesu sestavení, jsou komprimované odpovědi protokolu HTTP a modul runtime rozhraní .NET a sestavení jsou uložené v mezipaměti v prohlížeči.

Součásti Razor poskytuje ještě menší velikost datové části než Blazor pomocí sestavení, sestavení aplikace a serverové modulu runtime .NET. Razor komponenty aplikace sloužit pouze značky, skripty a šablony stylů klientům.

## <a name="deployment"></a>Nasazení

Použijte Blazor vytvořit čistě samostatnou aplikaci na straně klienta nebo kompletní aplikace ASP.NET Core, která obsahuje server a klientské aplikace:

* V **samostatnou aplikaci na straně klienta**, Blazor aplikace je zkompilován *dist* složku, která obsahuje pouze statické soubory. Soubory je možné hostovat na Azure App Service, stránkách Githubu, služby IIS (nakonfigurovaný jako statický souborový server), servery Node.js a mnoho dalších serverů a služeb. .NET nemusí na serveru v produkčním prostředí.
* V **aplikace ASP.NET Core předprodukčních**, kód mohou sdílet mezi serverem a klientské aplikace. Výsledné aplikace ASP.NET Core Razor komponenty, která obsluhuje uživatelského rozhraní na straně klienta a ostatní koncové body rozhraní API na straně serveru, dají vytvořit a nasadit na jakékoli cloudové nebo místní hostitele nepodporuje ASP.NET Core.

## <a name="suggest-a-feature-or-file-a-bug-report"></a>Navrhnout funkce nebo soubor hlášení o chybě

Chcete-li zaslat návrhy a souborů zpráv o chybách, [otevřete problém](https://github.com/aspnet/AspNetCore/issues/new). Obecnou nápovědu a získejte odpovědi z komunity, zapojte se do konverzace na [Gitteru](https://gitter.im/aspnet/Blazor).

## <a name="additional-resources"></a>Další zdroje

* [WebAssembly](http://webassembly.org/)
* [Průvodce jazykem C#](/dotnet/csharp/)
* [Razor](/aspnet/core/mvc/views/razor)
* [HTML](https://www.w3.org/html/)
