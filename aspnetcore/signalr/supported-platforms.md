---
title: Funkce SignalR technologie ASP.NET Core podporované platformy
author: bradygaster
description: Další informace o podporovaných platforem pro funkci SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068219"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Funkce SignalR technologie ASP.NET Core podporované platformy

## <a name="server-system-requirements"></a>Požadavky na systém Server

Funkce SignalR technologie ASP.NET Core podporuje serverová platforma, která podporuje ASP.NET Core.

## <a name="javascript-client"></a>Klient JavaScriptu

[Javascriptový klient](https://www.npmjs.com/package/@aspnet/signalr) běží v prostředí NodeJS 8 a novějších verzí a následujících prohlížečů:

| Prohlížeč                         | Version |
| ------------------------------- | ------- |
| Microsoft Edge                  | aktuální |
| Mozilla Firefox                 | aktuální |
| Google Chrome; zahrnuje Android | aktuální |
| Safari; zahrnuje iOS            | aktuální |
| Microsoft Internet Explorer     | 11      |
 
## <a name="net-client"></a>Klient .NET

[Klienta .NET](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) běží na žádnou platformu podporovanou nástrojem ASP.NET Core. Například [Xamarin mohou vývojáři SignalR](https://github.com/aspnet/Announcements/issues/305) pro vytváření aplikací pro Android pomocí Xamarin.Android 8.4.0.1 a později a aplikace pro iOS pomocí Xamarin.iOS 11.14.0.4 a novější.

Pokud na serveru běží služby IIS, vyžaduje přenos objekty Websocket služby IIS 8.0 nebo novější na systému Windows Server 2012 nebo novější. Jiné přenosy jsou podporované na všech platformách.

## <a name="java-client"></a>Klient Java

[Klientskou sadou Java](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) podporuje Javou 8 a novější verze.

## <a name="unsupported-clients"></a>Nepodporovaná klientů

Následující klienti jsou k dispozici, ale jsou experimentální nebo neoficiální. Tyto nejsou aktuálně podporovány a mohou být nikdy.

* [Kódu klienta jazyka C++](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [SWIFT klienta](https://github.com/moozzyk/SignalR-Client-Swift)
