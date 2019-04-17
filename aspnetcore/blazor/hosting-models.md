---
title: Blazor modelech hostování
author: guardrex
description: Vysvětlení Blazor na straně klienta a ASP.NET Core Razor součástmi hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/hosting-models
ms.openlocfilehash: 0e7598c9f27201a989d1088764e09461a37beae4
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614780"
---
# <a name="blazor-hosting-models"></a>Blazor modelech hostování

Podle [Daniel Roth](https://github.com/danroth27)

Blazor je webové rozhraní navržené ke spuštění na straně klienta v prohlížeči na [WebAssembly](http://webassembly.org/)– na základě modulu runtime .NET (*Blazor*) nebo na serveru ASP.NET Core (*ASP.NET Core Razor součásti*). Bez ohledu na modelech hostování modelu, aplikace a komponenty *zůstávají stejné*. Tento článek popisuje dostupné modelech hostování:

* [Blazor na straně klienta](#client-side-hosting-model)
* [Komponenty na straně serveru ASP.NET Core Razor](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a>Model hostingu na straně klienta

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Hlavní model hostingu pro Blazor je spuštěné v prohlížeči na WebAssembly straně klienta. Aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče. Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní. Aktualizace uživatelského rozhraní a zpracování událostí dochází v rámci stejného procesu. Prostředky aplikace se nasadí jako statické soubory na webový server nebo služba je schopná obsluhuje statický obsah, na klienty.

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte jednu z následujících šablon:

* **Blazor** ([nové blazor dotnet](/dotnet/core/tools/dotnet-new)) &ndash; nasazený jako sada statické soubory.
* **Blazor (ASP.NET Core v prostředí)** ([nové blazorhosted dotnet](/dotnet/core/tools/dotnet-new)) &ndash; hostované serveru ASP.NET Core. Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty. Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo [SignalR](xref:signalr/introduction).

Šablony zahrnují *components.webassembly.js* skript, který zpracovává:

* Stahuje se modul .NET runtime, aplikace a závislostí aplikace.
* Inicializace modulu runtime a spusťte tak aplikaci.

Model hostingu na straně klienta nabízí několik výhod. Blazor na straně klienta:

* Nemá žádné závislosti .NET na straně serveru.
* Plně využívá prostředky klienta a možnosti.
* Snižování zátěže pracovat ze serveru do klienta.
* Podporuje scénáře v režimu offline.

Existují nevýhody hostování na straně klienta. Blazor na straně klienta:

* Omezí aplikace funkcí v prohlížeči.
* Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).
* Má větší velikost ke stažení a delší dobu načítání aplikace.
* Má menší modul .NET runtime a podpora nástrojů pro dospělé (například omezení [.NET Standard](/dotnet/standard/net-standard) podpory a ladění).

## <a name="server-side-hosting-model"></a>Model hostingu na straně serveru

S model hostingu na straně serveru aplikace spuštěné na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím [SignalR](xref:signalr/introduction) připojení.

![V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

Pokud chcete vytvořit aplikaci Blazor používá model hostování na straně serveru, použijte technologii ASP.NET Core **Razor komponenty** šablony ([nové razorcomponents dotnet](/dotnet/core/tools/dotnet-new)). Aplikace ASP.NET Core hostitelem aplikace Razor komponenty na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují. Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:

* Služby Razor komponenty na straně serveru.
* Aplikace na žádost o zpracování kanálu.

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

*Components.server.js* skript&dagger; naváže připojení klienta. Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).

Model hostingu na straně serveru nabízí několik výhod. Komponenty na straně serveru Razor:

* Výrazného zmenšení velikosti aplikace než Blazor aplikace na straně klienta a načtou mnohem rychleji.
* Můžete plně využít serverových funkcí, včetně použití libovolné rozhraní API .NET Core kompatibilní.
* Spusťte na .NET Core na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.
* Funguje s tencí klienti (například prohlížeče, které nepodporují WebAssembly a prostředků omezené zařízení).
* .NET /C# základu kódu, včetně kódu komponenty aplikace, není poskytováni, na klienty.

Existují nevýhody hostování na straně serveru. Komponenty na straně serveru Razor:

* Mají vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.
* Nabídky nepodporuje offline: Pokud klienta nepovede, aplikace přestane fungovat.
* Omezená škálovatelnost: Server musí spravovat připojení více klientů a zpracování stavu klienta.
* Vyžadují ASP.NET Core server k obsluze aplikace. Nasazení bez serveru (například ze sítě CDN) není možné.

&dagger;*Components.server.js* do následujícího umístění je publikován skriptu: *bin / {ladění | Verze} / {CÍLOVÁ ARCHITEKTURA} /publish/ {název aplikace}. Aplikace/dist/_architektura*.
