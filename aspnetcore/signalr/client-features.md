---
title: Funkce klienta SignalR
author: bradygaster
description: Seznamte se s funkcemi, které různé klienti signalizace ASP.NET Core podporují.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301193"
---
# <a name="aspnet-core-signalr-client-features"></a>Klientské funkce nástroje ASP.NET Core Signal

## <a name="feature-distribution"></a>Distribuce funkcí

V následující tabulce jsou uvedené funkce a podpora pro klienty, kteří nabízejí podporu v reálném čase.

| Funkce | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Podpora služby signalizace Azure |✔|✔|✔|
| [Streamování ze serveru na klienta](xref:signalr/streaming)          |✔|✔|✔|
| [Streamování klienta na server](xref:signalr/streaming)          |✔|✔|✔|
| Automatické opětovné připojení ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |✔|✔| |
| Přenos přes WebSockets |✔|✔|✔|
| Přenos událostí odeslaných serverem |✔|✔| |
| Přenos dlouhého cyklického dotazování |✔|✔|✔|
| Protokol centra JSON |✔|✔|✔|
| Protokol centra MessagePack |✔|✔| |

Podpora automatického opětovného připojení v klientovi Java je sledována v [našem sledování problémů](https://github.com/aspnet/AspNetCore/issues/8711).

## <a name="additional-resources"></a>Další zdroje

* [Začínáme se službou Signal pro ASP.NET Core](xref:tutorials/signalr)
* [Podporované platformy](xref:signalr/supported-platforms)
* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
