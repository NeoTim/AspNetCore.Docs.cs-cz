---
title: Funkce SignalR technologie ASP.NET Core podporované platformy
author: tdykstra
description: Další informace o podporovaných platforem pro funkci SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/12/2018
uid: signalr/supported-platforms
ms.openlocfilehash: be3d4d0049395fb2499bd0b4aac126e953ce7910
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861716"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Funkce SignalR technologie ASP.NET Core podporované platformy

## <a name="server-system-requirements"></a>Požadavky na systém Server

Funkce SignalR technologie ASP.NET Core podporuje serverová platforma, která podporuje ASP.NET Core.

## <a name="javascript-client"></a>Javascriptový klient

[Javascriptový klient](https://www.npmjs.com/package/@aspnet/signalr) běží v prostředí NodeJS 8 a novějších verzí a následujících prohlížečů:

| Prohlížeč                         | Version |
| ------------------------------- | ------- |
| Microsoft Edge                  | aktuální |
| Mozilla Firefox                 | aktuální |
| Google Chrome; zahrnuje Android | aktuální |
| Safari; zahrnuje iOS            | aktuální |
| Microsoft Internet Explorer     | 11      |
 
## <a name="net-client"></a>.NET client

[Klienta .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) běží na žádnou platformu podporovanou nástrojem ASP.NET Core. Například [Xamarin mohou vývojáři SignalR](https://github.com/aspnet/Announcements/issues/305) pro vytváření aplikací pro Android pomocí Xamarin.Android 8.4.0.1 a později a aplikace pro iOS pomocí Xamarin.iOS 11.14.0.4 a novější.

Pokud na serveru běží služby IIS, přenosové protokoly Websocket vyžaduje službu IIS 8.0 nebo vyšší na Windows Server 2012 nebo vyšší. Jiné přenosy jsou podporované na všech platformách.

## <a name="java-client"></a>Klientskou sadou Java

[Klientskou sadou Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) podporuje Javou 8 a novější verze.

## <a name="unsupported-clients"></a>Nepodporovaná klientů

Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální. Tyto nejsou aktuálně podporovány a mohou být nikdy.

* [Kódu klienta jazyka C++](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [SWIFT klienta](https://github.com/moozzyk/SignalR-Client-Swift)
