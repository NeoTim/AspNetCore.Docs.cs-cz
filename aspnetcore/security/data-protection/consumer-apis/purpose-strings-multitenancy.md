---
title: Hierarchie účelů a víceklientské aplikace v ASP.NET Core
author: rick-anderson
description: Seznamte se s hierarchií řetězců pro účely a víceklientské architektury, protože se týká rozhraní API ochrany ASP.NET Core dat.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 73edb8082d2df263bc1e6d73fee1360fa6840514
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776770"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hierarchie účelů a víceklientské aplikace v ASP.NET Core

Vzhledem k `IDataProtector` tomu `IDataProtectionProvider`, že je také implicitně a účelně lze zřetězit. V tomto smyslu `provider.CreateProtector([ "purpose1", "purpose2" ])` je ekvivalentem `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

To umožňuje určitým zajímavým hierarchickým vztahům prostřednictvím systému ochrany dat. V předchozím příkladu [contoso. Messaging. SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)může komponenta SecureMessage zavolat `provider.CreateProtector("Contoso.Messaging.SecureMessage")` po frontě dopředu a uložit výsledek do soukromého `_myProvider` pole. Budoucí ochrana se pak dají vytvořit prostřednictvím volání `_myProvider.CreateProtector("User: username")`a tato ochrana se použije k zabezpečení jednotlivých zpráv.

To lze také Překlopit. Vezměte v úvahu jednu logickou aplikaci, která hostuje více tenantů (CMS se jeví jako přiměřenou), a každý tenant se dá nakonfigurovat s vlastním systémem pro správu ověřování a stavu. Aplikace zastřešující má jednoho hlavního poskytovatele a volá `provider.CreateProtector("Tenant 1")` a `provider.CreateProtector("Tenant 2")` , aby každému tenantovi poskytoval svůj vlastní izolovaný řez systému ochrany dat. Klienti by pak mohli odvodit své vlastní ochrany v závislosti na svých vlastních potřebách, ale bez ohledu na to, jak je to obtížné, můžou vytvářet ochrany, které kolidují s jakýmkoli jiným klientem v systému. Graficky funguje tak, jak je znázorněno níže.

![Víceklientské účely](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> To předpokládá, že aplikace zastřešující řídí, která rozhraní API jsou k dispozici pro jednotlivé klienty a že klienti nemohou spouštět libovolný kód na serveru. Pokud může tenant spustit libovolný kód, mohl by provést soukromou reflexi k přerušení záruk izolace, nebo by mohl jednoduše přečíst hlavní klíč klíče přímo a odvodit jakékoli podklíče, které si chtějí.

Systém ochrany dat ve skutečnosti používá řazení více tenantů ve výchozí předem připravené konfiguraci. Ve výchozím nastavení jsou hlavní materiálové klíče uložené ve složce profilu uživatele účtu pracovního procesu (nebo v registru pro identity fondu aplikací služby IIS). V podstatě je ale poměrně běžné používat jeden účet ke spouštění více aplikací, takže všechny tyto aplikace budou mít na začátku sdílení obsahu hlavního klíče. Pro vyřešení toho systém ochrany dat automaticky vloží identifikátor jedinečnosti podle aplikace jako první prvek v řetězci celkového účelu. Tento implicitní účel slouží k [izolaci jednotlivých aplikací](xref:security/data-protection/configuration/overview#per-application-isolation) tím, že efektivně zpracovává každou aplikaci jako jedinečného tenanta v systému a proces vytváření ochrany vypadá stejně jako obrázek výše.
