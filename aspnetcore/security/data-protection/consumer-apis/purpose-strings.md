---
title: Účelové řetězce v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak se používají účelové řetězce v rozhraní API ASP.NET Core Data Protection.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087531"
---
# <a name="purpose-strings-in-aspnet-core"></a>Účelové řetězce v ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Komponenty, které využívají `IDataProtectionProvider` musí projít jedinečný *účely* parametr `CreateProtector` metody. Účely *parametr* se vztahují na zabezpečení systému ochrany dat, protože nabízí izolaci mezi kryptografických příjemců, i v případě, že kryptografické klíče kořenového jsou stejné.

Když příjemce určuje účel, účel řetězec se používá spolu s kořenové kryptografické klíče k odvození kryptografických podklíče jedinečný tento příjemce. Tím se izolují uživatelů z jiných kryptografických spotřebitelů v aplikaci: žádná komponenta může číst její datové části a ho nelze číst datové části všechny ostatní komponenty. Tato izolace také vykresluje neřešitelné celé kategorie útoku vzhledem k součásti.

![Příklad diagramu účel](purpose-strings/_static/purposes.png)

V diagramu výše `IDataProtector` instancí A a B **nelze** číst druhé strany datové části, pouze své vlastní.

Řetězec účel nemusí být tajného kódu. By měl být jednoduše jedinečný v tom smyslu, že žádná komponenta dobře behaved někdy poskytnout stejný řetězec účel.

>[!TIP]
> Pomocí názvu oboru názvů a typ součásti spotřebovává data protection API je dobré říci, stejně jako v praxi, které tyto informace se nikdy jsou v konfliktu.
>
>Komponenty vytvořené Contoso, který je zodpovědný za minting nosné tokeny použít Contoso.Security.BearerToken jako její účel řetězec. Nebo – ještě lepší - Contoso.Security.BearerToken.v1 může použít jako datový typ string její účel. Přidávání čísla verze umožňuje budoucí verze se má použít Contoso.Security.BearerToken.v2 jako její účel a různé verze by naprosto izolované od sebe, co se týče datových částí přejděte.

Protože parametr účely `CreateProtector` je pole řetězců, výše by jste místo toho určený jako `[ "Contoso.Security.BearerToken", "v1" ]`. To umožňuje vytvoření hierarchie účelů a otevírá víceklientské architektury scénáře pomocí systému ochrany dat.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Komponenty by nemělo umožňovat nedůvěryhodným uživatelským vstupům jako jediný zdroj informací vstupu pro účely řetězec.
>
>Představte si třeba komponentu Contoso.Messaging.SecureMessage, který je zodpovědný za ukládání zabezpečených zpráv. Pokud byly zabezpečené součástí zasílání zpráv pro volání `CreateProtector([ username ])`, uživatel se zlými úmysly může vytvořit účet s uživatelským jménem "Contoso.Security.BearerToken" ve snaze získejte součást, kterou volání `CreateProtector([ "Contoso.Security.BearerToken" ])`, neúmyslně způsobující zabezpečené zasílání zpráv systém pro mint datových částí, které by mohly být vnímané jako ověřovací tokeny.
>
>Lepší účely řetězce pro komponentu zasílání zpráv by `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, což zajišťuje izolaci správné.

Izolace poskytovaná a chování `IDataProtectionProvider`, `IDataProtector`, a účely jsou následující:

* Pro danou `IDataProtectionProvider` objektu, `CreateProtector` metoda vytvoří `IDataProtector` objekt jednoznačně vázané na obě `IDataProtectionProvider` objekt, který jej vytvořil a účely parametr, který byl předán do metody.

* Účel parametr nesmí mít hodnotu null. (Pokud účely je zadán jako pole, to znamená, že pole nesmí mít nulovou délku, a všechny prvky pole musí mít hodnotu null.) Účel prázdný řetězec je technicky povoleno, ale se nedoporučuje.

* Dva účely argumenty jsou ekvivalentní a pouze v případě obsahují stejné řetězce (s použitím ordinálního porovnávání) ve stejném pořadí. Argument jednoúčelových je stejná jako odpovídající účely jedním prvkem pole.

* Dvě `IDataProtector` objekty jsou ekvivalentní a pouze v případě vytvoření z ekvivalent `IDataProtectionProvider` objektů s parametry ekvivalentní účely.

* Pro danou `IDataProtector` objektu, volání `Unprotect(protectedData)` vrátí původní `unprotectedData` pouze v případě `protectedData := Protect(unprotectedData)` ekvivalent `IDataProtector` objektu.

> [!NOTE]
> Nejsou nám vzhledem k tomu tento případ, kdy některé komponenty záměrně zvolí účel řetězec, který se ví, že jsou v konfliktu s jinou komponentou. Takové součásti v podstatě lze považovat za škodlivý a tento systém není určená k poskytování záruky zabezpečení, v případě, že škodlivý kód je již spuštěna v rámci služby pracovního procesu.
