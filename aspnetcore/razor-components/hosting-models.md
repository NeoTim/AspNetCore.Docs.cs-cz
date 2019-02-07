---
title: Hostování modely součásti syntaxe Razor
author: guardrex
description: Vysvětlení Blazor na straně klienta a ASP.NET Core Razor součástmi hostování modely.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 9aba63860fd8c566b866bb1d3b50b29a7a33c6e8
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854468"
---
# <a name="razor-components-hosting-models"></a>Hostování modely součásti syntaxe Razor

Podle [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Součásti Razor je webová architektura navržený pro běh na straně klienta v prohlížeči na základě WebAssembly .NET runtime (*Blazor*) nebo na serveru ASP.NET Core (*ASP.NET Core Razor komponenty*). Bez ohledu na modelech hostování modelu, aplikace a komponenty *zůstávají stejné*. Tento článek popisuje dostupné modelech hostování.

## <a name="client-side-hosting-model"></a>Model hostingu na straně klienta

Hlavní model hostingu pro Blazor je spuštěné straně klienta v prohlížeči. V tomto modelu aplikace Blazor, jeho závislosti a modul .NET runtime se stáhnou do prohlížeče. Aplikace je proveden přímo v prohlížeči vlákno uživatelského rozhraní. Všechny aktualizace uživatelského rozhraní a zpracování událostí se stane v rámci stejného procesu. Prostředky aplikace můžete nasadit jako statické soubory pomocí libovolné webový server je upřednostňovaný (viz [hostitele a nasadit](xref:host-and-deploy/razor-components/index)).

![Blazor straně klienta: Blazor aplikace běží na vlákně uživatelského rozhraní v prohlížeči.](hosting-models/_static/client-side.png)

Chcete-li vytvořit aplikaci Blazor používá model hostování na straně klienta, použijte **Blazor** nebo **Blazor (ASP.NET Core v prostředí)** šablony projektů (`blazor` nebo `blazorhosted` šablony při použití [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu na příkazovém řádku). Zahrnout *blazor.webassembly.js* skriptu obslužné rutiny:

* Stahuje se modul .NET runtime, aplikace a jeho závislosti.
* Inicializace modulu runtime a spusťte tak aplikaci.

Model hostingu na straně klienta nabízí několik výhod. Blazor na straně klienta:

* Nemá žádné závislosti .NET na straně serveru.
* Má bohaté interaktivní uživatelské rozhraní.
* Plně využívá prostředky klienta a možnosti.
* Snižování zátěže pracovat ze serveru do klienta.
* Podporuje scénáře v režimu offline.

Existují nevýhody hostování na straně klienta. Blazor na straně klienta:

* Omezí aplikace funkcí v prohlížeči.
* Vyžaduje klienta s podporou, hardware a software (třeba podporu WebAssembly).
* Má větší velikost ke stažení a delší dobu načítání aplikace.
* Má menší vyspělejší modulu runtime .NET a nástroje podpory (třeba omezení v .NET Standard support a ladění).

Visual Studio obsahuje **Blazor (ASP.NET Core hostované)** šablona projektu pro vytvoření Blazor aplikace, která běží na WebAssembly a je hostovaná na serveru ASP.NET Core. Aplikace ASP.NET Core obsluhuje Blazor aplikaci pro klienty, ale jinak je samostatný proces. Aplikace na straně klienta Blazor můžete spolupracovat se serverem přes síť pomocí volání webového rozhraní API nebo připojení SignalR.

> [!IMPORTANT]
> Pokud aplikace na straně klienta Blazor slouží jako sub aplikace IIS hostované aplikace ASP.NET Core, zakažte zděděné obslužnou rutinu modul ASP.NET Core. Nastavení základní cesty aplikace v aplikaci Blazor *index.html* soubor do služby IIS alias používaný při konfiguraci podřízeným aplikacím ve službě IIS.
>
> Další informace najdete v tématu [základní cesty aplikace](xref:host-and-deploy/razor-components/index#app-base-path).

## <a name="server-side-hosting-model"></a>Model hostingu na straně serveru

V ASP.NET Core Razor komponenty na straně serveru model hostingu aplikace provádí na serveru z v rámci aplikace ASP.NET Core. Aktualizace uživatelského rozhraní, zpracování událostí a volání jazyka JavaScript jsou zpracovány prostřednictvím připojení SignalR.

![ASP.NET Core Razor součásti serverové: V prohlížeči komunikuje s aplikaci (už je hostovaná v rámci aplikace ASP.NET Core) na serveru pomocí připojení SignalR.](hosting-models/_static/server-side.png)

Chcete-li vytvořit Razor součásti aplikace pomocí model hostingu na straně serveru, použijte **Blazor (serverové v ASP.NET Core)** šablony (`blazorserver` při použití [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazového řádku). Aplikace ASP.NET Core hostitelem aplikace Razor komponenty na straně serveru a nastaví koncových bodů SignalR, ve kterém se klienti připojují. Aplikace ASP.NET Core odkazuje aplikaci `Startup` třídy přidejte:

* Služby Razor komponenty na straně serveru.
* Aplikace na žádost o zpracování kanálu.

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

*Blazor.server.js* skript&dagger; naváže připojení klienta. Je zodpovědností aplikace k zachování a obnovení stavu aplikace podle potřeby (například v případě ztráty připojení).

Model hostingu na straně serveru nabízí několik výhod:

* Umožňuje napsat celou aplikaci s využitím .NET a C# pomocí komponenty modelu.
* Poskytuje bohaté interaktivní chování a zabraňuje zbytečným stránka se aktualizuje.
* Má výrazného zmenšení velikosti aplikace než Blazor aplikace na straně klienta a načte mnohem rychleji.
* Součástí logiky můžete plně využít serverových funkcí, včetně použití libovolné rozhraní API .NET Core kompatibilní.
* V rozhraní .NET Core běží na serveru, takže existující .NET nástrojů, jako je ladění, funguje podle očekávání.
* Funguje s tencí klienti (například prohlížeče, které nepodporují WebAssembly a prostředků omezené zařízení).

Existují nevýhody hostování na straně serveru:

* Má vyšší latence: Každá interakce uživatele zahrnuje směrování v síti.
* Nabízí nepodporuje offline: Pokud klienta nepovede, aplikace přestane fungovat.
* Má omezit škálovatelnost: Server musí spravovat připojení více klientů a zpracování stavu klienta.
* Vyžaduje server služby ASP.NET Core pro obsluhu aplikace. Nasazení bez serveru (například ze sítě CDN) není možné.

&dagger;*Blazor.server.js* do následujícího umístění je publikován skriptu: *bin / {ladění | Verze} / {CÍLOVÁ ARCHITEKTURA} /publish/ {název aplikace}. Aplikace/dist/_architektura*.
