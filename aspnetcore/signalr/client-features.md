---
title: Funkce klienta SignalR
author: bradygaster
description: Seznamte se s funkcemi, které různé klienti signalizace ASP.NET Core podporují.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925354"
---
# <a name="aspnet-core-signalr-client-features"></a>Klientské funkce nástroje ASP.NET Core Signal

## <a name="feature-distribution"></a>Distribuce funkcí

V následující tabulce jsou uvedené funkce a podpora pro klienty, kteří nabízejí podporu v reálném čase. Pro každou funkci je uvedena *minimální* verze, která podporuje tuto funkci. Pokud není uvedená žádná verze, tato funkce se nepodporuje.

| Funkce | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Podpora služby signalizace Azure |1.0.0|1.0.0|1.0.0|
| [Streamování ze serveru na klienta](xref:signalr/streaming)          |1.0.0|1.0.0|1.0.0|
| [Streamování klienta na server](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|
| Automatické opětovné připojení ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|❌|
| Přenos přes WebSockets |1.0.0|1.0.0|1.0.0|
| Přenos událostí odeslaných serverem |1.0.0|1.0.0|❌|
| Přenos dlouhého cyklického dotazování |1.0.0|1.0.0|3.0.0|
| Protokol centra JSON |1.0.0|1.0.0|1.0.0|
| Protokol centra MessagePack |1.0.0|1.0.0|❌|

Podpora automatického opětovného připojení v klientovi Java je sledována v [našem sledování problémů](https://github.com/aspnet/AspNetCore/issues/8711).

## <a name="additional-resources"></a>Další zdroje

* [Začínáme se službou Signal pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
