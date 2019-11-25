---
title: Podporované platformy ASP.NET Core SignalR
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9b9cf1d57d61c333c485f23b7ab952c66814d2aa
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317467"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>Podporované platformy ASP.NET Core SignalR

## <a name="server-system-requirements"></a>Požadavky na systém serveru

SignalR pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.

## <a name="javascript-client"></a>Klient JavaScriptu

[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:

| Browser                         | Version         |
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

* [C++Služba](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
