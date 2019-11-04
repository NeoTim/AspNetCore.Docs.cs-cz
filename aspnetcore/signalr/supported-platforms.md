---
title: Podporované platformy pro přijímače ASP.NET Core
author: bradygaster
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426969"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Podporované platformy pro přijímače ASP.NET Core

## <a name="server-system-requirements"></a>Požadavky na systém serveru

Návěstí pro ASP.NET Core podporuje jakoukoli serverovou platformu, která ASP.NET Core podporuje.

## <a name="javascript-client"></a>Klient JavaScriptu

[Klient jazyka JavaScript](https://www.npmjs.com/package/@aspnet/signalr) běží na NodeJS 8 a novějších verzích a v následujících prohlížečích:

| Prohlížeee                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Aktuální&dagger; |
| Mozilla Firefox                 | Aktuální&dagger; |
| Google Chrome; zahrnuje Android | Aktuální&dagger; |
| Prohlížeče zahrnuje iOS            | Aktuální&dagger; |
| Microsoft Internet Explorer     | odst              |

&dagger;*aktuální* odkazuje na nejnovější verzi prohlížeče.

## <a name="net-client"></a>Klient .NET

[Klient .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) běží na libovolné platformě podporované nástrojem ASP.NET Core. [Vývojáři Xamarin můžou například použít signalizaci](https://github.com/aspnet/Announcements/issues/305) pro sestavování aplikací pro Android pomocí Xamarin. Android 8.4.0.1 a novějších aplikací pro iOS pomocí Xamarin. iOS 11.14.0.4 a novějších.

Pokud server používá službu IIS, přenos pomocí protokolu WebSockets vyžaduje službu IIS 8,0 nebo novější v systému Windows Server 2012 nebo novějším. Další přenosy jsou podporované na všech platformách.

## <a name="java-client"></a>Klient Java

[Klient Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) podporuje Java 8 a novější verze.

## <a name="unsupported-clients"></a>Nepodporované klienty

Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální. Nejsou aktuálně podporované a nemusí být nikdy.

* [C++Služba](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Klient SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
