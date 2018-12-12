---
uid: signalr/overview/older-versions/hub-authorization
title: Ověřování a autorizace Center SignalR (SignalR 1.x) | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak omezit, kteří uživatelé nebo rolí se dostanete metod rozbočovače.
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 3d2dfc0e-eac2-4076-a468-325d3d01cc7b
msc.legacyurl: /signalr/overview/older-versions/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: 1bd61b8faff22db3337e9d9c1ed2532d3e64440d
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287465"
---
<a name="authentication-and-authorization-for-signalr-hubs-signalr-1x"></a>Ověřování a autorizace center SignalR (SignalR 1.x)
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma popisuje, jak omezit, kteří uživatelé nebo rolí se dostanete metod rozbočovače.


## <a name="overview"></a>Přehled

Toto téma obsahuje následující oddíly:

- [Povolit atribut](#authorizeattribute)
- [Vyžadovat ověřování pro všechna centra](#requireauth)
- [Vlastní autorizace](#custom)
- [Předat ověřovací informace pro klienty](#passauth)
- [Možnosti ověřování pro klienty .NET](#authoptions)

    - [Soubor cookie ověřování pomocí formulářů](#cookie)
    - [Ověřování Windows](#windows)
    - [Připojení záhlaví](#header)
    - [Certifikát](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a>Povolit atribut

Funkce SignalR poskytuje [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atribut určit, které uživatele nebo role mají přístup do centra nebo metody. Tento atribut je umístěn v `Microsoft.AspNet.SignalR` oboru názvů. Můžete použít `Authorize` atribut rozbočovač nebo konkrétní metody v rozbočovači. Pokud použijete `Authorize` atribut třídy rozbočovače, požadavek na autorizaci zadané platí pro všechny metody v rozbočovači. Ověření požadavků, které můžete použít různé typy jsou uvedeny níže. Bez `Authorize` atribut, všechny veřejné metody v rozbočovači jsou k dispozici pro klienta, který je připojený k rozbočovači.

Pokud jste definovali role ve vaší webové aplikaci s názvem "Admin", můžete zadat, že jenom uživatelé v této roli můžete přístup k centru s následujícím kódem.

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

Nebo můžete určit, že Centrum obsahuje jednu metodu, která je k dispozici všem uživatelům a druhá metoda, která je pouze ověřeným uživatelům k dispozici, jak je znázorněno níže.

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

Následující příklady různých autorizace situacích:

- `[Authorize]` – jen ověření uživatelé
- `[Authorize(Roles = "Admin,Manager")]` – jen ověření uživatelé v zadaných rolí
- `[Authorize(Users = "user1,user2")]` – pouze pro ověřené uživatele s ze zadaných uživatelských jmen
- `[Authorize(RequireOutgoing=false)]` – pouze pro ověřené uživatele můžete vyvolat centra, ale volání ze serveru zpátky do klientů nejsou omezeny autorizace, jako je třeba když jenom určitým uživatelům můžete odeslat zprávu, ale všechny ostatní zprávy. Vlastnost RequireOutgoing dá používat jedině pro celé centrum není pro jednotlivce metody v rozbočovači. Když RequireOutgoing není nastavená na hodnotu false, jenom uživatelé, kteří splní požadavek na autorizaci se nazývají ze serveru.

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a>Vyžadovat ověřování pro všechna centra

Můžete vyžadovat ověřování pro všechny rozbočovače a metody ve vaší aplikaci pomocí volání [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) metoda při spuštění aplikace. Tuto metodu můžete použít, když máte více rozbočovače a chcete vynutit požadavek na ověření pro všechny z nich. S touto metodou nelze zadat role, uživatele nebo odchozí autorizace. Můžete zadat pouze omezen přístup k metodám centra ověřeným uživatelům. Atribut Authorize však můžete použít na rozbočovače a metody zadat další požadavky. Všechny požadavky, které zadáte v atributech platí kromě základní požadavek ověřování.

Následující příklad ukazuje soubor Global.asax, který omezuje všech metod rozbočovače na ověřeným uživatelům.

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

Při volání `RequireAuthentication()` metoda po zpracování žádost SignalR, vyvolá výjimku SignalR `InvalidOperationException` výjimky. Tato výjimka je vyvolána, protože modul nelze přidat do kanálu rozbočovače přidán po zavolání kanálu. Předchozí příklad ukazuje volání `RequireAuthentication` metodu `Application_Start` metodu, která provádí jednou před prvního požadavku na zpracování.

<a id="custom"></a>

## <a name="customized-authorization"></a>Vlastní autorizace

Pokud je potřeba upravit, jak se určují autorizaci, můžete vytvořit třídu, která je odvozena z `AuthorizeAttribute` a přepsat [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) metody. Tato metoda je volána pro každý požadavek pro určení, zda je uživatel oprávnění k dokončení požadavku. Přepsané metody poskytují logiku potřebnou pro váš scénář autorizace. Následující příklad ukazuje, jak vynutit autorizaci prostřednictvím založené na deklaracích identity.

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a>Předat ověřovací informace pro klienty

Budete muset použít informace o ověřování v kódu, který běží na straně klienta. Předáním požadovaných informací při volání metody na straně klienta. Například metoda aplikace chat mohli předat jako parametr uživatelské jméno osoby, odesílání zpráv, jak je znázorněno níže.

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

Nebo můžete vytvořit objekt reprezentující informace o ověřování a tento objekt předat jako parametr, jak je znázorněno níže.

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

Nikdy byste neměli předávat id připojení pro jednoho klienta jiným klientům, protože ji uživatel se zlými úmysly třeba použít tak, aby napodoboval žádosti od tohoto klienta.

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a>Možnosti ověřování pro klienty .NET

Pokud máte klienta .NET, jako je například konzolové aplikace, která komunikuje s rozbočovači, který je omezen na ověřené uživatele, můžete předat přihlašovacími údaji v souboru cookie, záhlaví připojení nebo certifikát. Příklady v této části ukazují, jak použít tyto různé metody pro ověřování uživatele. Nejsou plně funkční aplikace SignalR. Další informace o klientech .NET s knihovnou SignalR naleznete v tématu [pokyny k rozhraní API Center – klient .NET](../guide-to-the-api/hubs-api-guide-net-client.md).

<a id="cookie"></a>

### <a name="cookie"></a>Soubor cookie

Při vašeho klienta .NET interakci s rozbočovači, který používá ověřování pomocí formulářů ASP.NET, musíte ručně nastavit ověřovacího souboru cookie pro připojení. Přidání souboru cookie `CookieContainer` vlastnost [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) objektu. Následující příklad ukazuje konzolovou aplikaci, která načte soubor cookie ověřování z webové stránky a přidá tento soubor cookie pro připojení. Adresa URL `https://www.contoso.com/RemoteLogin` v příkladu odkazuje na webovou stránku, která je potřeba vytvořit. Na stránce by načíst odeslaných uživatelské jméno a heslo a pokus o přihlášení uživatele s přihlašovacími údaji.

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

Konzolová aplikace odesílá pověření, která mají www.contoso.com/RemoteLogin který může odkazovat na prázdnou stránku, která obsahuje následující soubor kódu na pozadí.

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a>Ověřování systému Windows

Pokud používáte ověřování Windows, můžete předat přihlašovacích údajů aktuálního uživatele pomocí [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) vlastnost. Nastavit přihlašovací údaje pro připojení k hodnotě DefaultCredentials.

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a>Připojení záhlaví

Pokud vaše aplikace nepoužívá soubory cookie, můžete předat informace o uživateli v hlavičce připojení. Můžete například předat token v hlavičce připojení.

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

Poté v rozbočovači, bude ověřit token uživatele.

<a id="certificate"></a>

### <a name="certificate"></a>Certifikát

Můžete předat klientský certifikát k ověření uživatele. Přidání certifikátu při vytváření připojení. Následující příklad ukazuje jenom postup přidání klientský certifikát pro připojení. nezobrazuje se úplná konzolovou aplikaci. Používá [certifikátu x 509](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) třída, která poskytuje několik způsobů vytvoření certifikátu.

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
