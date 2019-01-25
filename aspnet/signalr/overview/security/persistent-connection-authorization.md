---
uid: signalr/overview/security/persistent-connection-authorization
title: Ověřování a autorizace trvalých připojení SignalR | Dokumentace Microsoftu
author: bradygaster
description: Toto téma popisuje, jak vynutit autorizaci na trvalé připojení. Obecné informace o integraci zabezpečení do aplikace SignalR...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7dab28f4720b34082f71e487c64af88a8ba01e6c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835619"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a>Ověřování a autorizace trvalých připojení SignalR
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma popisuje, jak vynutit autorizaci na trvalé připojení. Obecné informace o integraci zabezpečení do aplikace funkci SignalR naleznete v tématu [Úvod do zabezpečení](introduction-to-security.md).
>
> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Předchozích verzích tohoto tématu
>
> Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


## <a name="enforce-authorization"></a>Vynutit autorizaci

K vynucení pravidel autorizace, při použití [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) je nutné přepsat `AuthorizeRequest` metody. Nelze použít `Authorize` atribut s trvalé připojení. `AuthorizeRequest` Metoda je volána rozhraním SignalR před každým požadavkem k ověření, že je uživatel oprávnění k provedení požadované akce. `AuthorizeRequest` Metoda není volána z klienta; místo toho ověření uživatele přes standardní ověřovací mechanismus vaší aplikace.

Následující příklad ukazuje, jak omezit požadavky na ověření uživatelé.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Můžete přidat libovolný vlastní autorizace logiku v metodě AuthorizeRequest; například kontroluje, zda uživatel patří do určité role.
