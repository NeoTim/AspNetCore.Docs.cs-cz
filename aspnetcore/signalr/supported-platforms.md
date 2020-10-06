---
title: ASP.NET Core SignalR podporované platformy
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754499"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>ASP.NET Core SignalR podporované platformy

## <a name="server-system-requirements"></a>Požadavky na systém serveru

SignalR pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.

## <a name="javascript-client"></a>Klient JavaScriptu

[Klient jazyka JavaScript](xref:signalr/javascript-client) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:

| Prohlížeč                          | Verze         |
| -------------------------------- | --------------- |
| Apple Safari, včetně iOS      | Aktivní&dagger; |
| Google Chrome, včetně Androidu | Aktivní&dagger; |
| Microsoft Edge                   | Aktivní&dagger; |
| Mozilla Firefox                  | Aktivní&dagger; |

&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.

## <a name="net-client"></a>Klient .NET

[Klient .NET](xref:signalr/dotnet-client) běží na libovolné platformě podporované nástrojem ASP.NET Core. [Vývojáři Xamarin můžou například SignalR použít](https://github.com/aspnet/Announcements/issues/305) k sestavování aplikací pro Android pomocí Xamarin. Android 8.4.0.1 a novějších aplikací pro iOS pomocí Xamarin. iOS 11.14.0.4 a novějších.

Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším. Další přenosy jsou podporované na všech platformách.

## <a name="java-client"></a>Klient Java

[Klient Java](xref:signalr/java-client) podporuje Java 8 a novější verze.

## <a name="unsupported-clients"></a>Nepodporované klienty

Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální. Nejsou aktuálně podporované a nemusí být nikdy.

* [Klient C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
