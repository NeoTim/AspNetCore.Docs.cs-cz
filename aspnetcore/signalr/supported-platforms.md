---
title: Podporované platformy ASP.NET Core SignalR
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668138"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Podporované platformy pro přijímače ASP.NET Core

## <a name="server-system-requirements"></a>Požadavky na systém serveru

Návěstí pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.

## <a name="javascript-client"></a>Klient JavaScriptu

[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:

| Prohlížeč                         | Verze         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Aktuální&dagger; |
| Mozilla Firefox                 | Aktuální&dagger; |
| Google Chrome; zahrnuje Android | Aktuální&dagger; |
| Prohlížeče zahrnuje iOS            | Aktuální&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*aktuální* odkazuje na nejnovější verzi prohlížeče.

## <a name="net-client"></a>Klient .NET

[Klient .NET](xref:signalr/dotnet-client) běží na libovolné platformě podporované nástrojem ASP.NET Core. [Vývojáři Xamarin můžou například použít SignalR](https://github.com/aspnet/Announcements/issues/305) pro sestavování aplikací pro Android pomocí aplikací Xamarin. Android 8.4.0.1 a novějších a iOS s použitím Xamarin. iOS 11.14.0.4 a novějších.

Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším. Další přenosy jsou podporované na všech platformách.

## <a name="java-client"></a>Klient Java

[Klient Java](xref:signalr/java-client) podporuje Java 8 a novější verze.

## <a name="unsupported-clients"></a>Nepodporované klienty

Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální. Nejsou aktuálně podporované a nemusí být nikdy.

* [C++Služba](https://github.com/aspnet/SignalR-Client-Cpp)

* [Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
