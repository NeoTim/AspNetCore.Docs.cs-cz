---
title: ASP.NET Core SignalR klienti
author: bradygaster
description: Seznamte se s funkcemi, které jsou podporované různými ASP.NET Core SignalR klienty.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/client-features
ms.openlocfilehash: a759e473ff7ffaebd0eb9309f37a959d0e06a466
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438955"
---
# <a name="aspnet-core-signalr-clients"></a>Klienti signalizace ASP.NET Core

## <a name="versioning-support-and-compatibility"></a>Správa verzí, podpora a kompatibilita

Klienti signalizace se dodávají spolu se serverovými součástmi a mají shodné se správou verzí. Libovolný podporovaný klient se může bezpečně připojit k jakémukoli podporovanému serveru a jakékoli problémy s kompatibilitou by byly považovány za chyby, které mají být opraveny. Klienti signalizace jsou podporováni ve stejném životním cyklu podpory jako zbytek .NET Core. Podrobnosti najdete [v tématu Zásady podpory pro .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

Mnoho funkcí vyžaduje kompatibilního klienta **a** serveru. V níže uvedené tabulce najdete minimální verze různých funkcí.

Verze signalizace ve verzi 1. x jsou namapovány na verze 2,1 a 2,2 .NET Core a mají stejnou životnost. Pro verzi 3. x a vyšší se v nástroji Signal verze přesně shoduje s ostatními rozhraním .NET a má stejný životní cyklus podpory.

| Verze signálu | Verze .NET Core | Úroveň podpory | Konec podpory |
| - | - | - | - |
| 1,0. x | 2.1. x | Dlouhodobá podpora | 21. srpna 2021 |
| 1.1. x | 2.2. x | Konec života | 23. prosince 2019 |
| 3. x nebo vyšší | *stejné jako verze signalizace* | Další informace najdete v tématu [zásady podpory pro .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) . |

**Poznámka:** V ASP.NET Core 3,0 se klient jazyka JavaScript *přesunul* do `@microsoft/signalr` balíčku npm.

## <a name="feature-distribution"></a>Distribuce funkcí

V následující tabulce jsou uvedené funkce a podpora pro klienty, kteří nabízejí podporu v reálném čase. Pro každou funkci je uvedena *minimální* verze, která podporuje tuto funkci. Pokud není uvedená žádná verze, tato funkce se nepodporuje.

| Příznak | Server | Klient .NET | Klient JavaScriptu | Klient Java |
| ---- | :-: | :-: | :-: | :-: |
| SignalRPodpora služeb Azure |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streamování ze serveru na klienta](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streamování klienta na server](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Automatické opětovné připojení ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Přenos přes WebSockets |2.1.0|1.0.0|1.0.0|1.0.0|
| Přenos událostí odeslaných serverem |2.1.0|1.0.0|1.0.0|❌|
| Přenos dlouhého cyklického dotazování |2.1.0|1.0.0|1.0.0|3.0.0|
| Protokol centra JSON |2.1.0|1.0.0|1.0.0|1.0.0|
| Protokol centra MessagePack |2.1.0|1.0.0|1.0.0|❌|

Podpora pro povolení dalších funkcí klienta je sledována v [našem sledování problémů](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Další zdroje

* [Začínáme s nástrojem SignalR pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
