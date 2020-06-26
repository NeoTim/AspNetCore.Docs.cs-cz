---
title: Řetězce účelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se v rozhraních API ochrany ASP.NET Corech dat používají řetězce účelu.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: b52961fd33ce2d3708754f73ea38456d8d5f8f3c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404285"
---
# <a name="purpose-strings-in-aspnet-core"></a>Řetězce účelu v ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Komponenty, které spotřebovávají, `IDataProtectionProvider` musí do metody předat jedinečný parametr pro *účely* `CreateProtector` . *Parametr* pro účely je podstatou zabezpečení systému ochrany dat, protože poskytuje izolaci mezi spotřebiteli šifrování, a to i v případě, že jsou kořenové kryptografické klíče stejné.

Když příjemce zadá účel, použije se spolu s kořenovými šifrovacími klíči k odvození kryptografických podklíčů, které jsou pro tohoto příjemce jedinečné. Tím se uživatel izoluje od všech ostatních zprostředkovatelů kryptografie v aplikaci: žádná jiná komponenta nemůže číst své datové části a nemůže číst žádné další datové části součásti. Tato izolace také vykresluje neproveditelné celé kategorie útoků proti komponentě.

![Příklad diagramu účelu](purpose-strings/_static/purposes.png)

V diagramu výše `IDataProtector` instance a a B **nemohou** číst jiné datové části, a to pouze vlastní.

Řetězec účelu nemusí být tajný. Měl by být jednoduše jedinečný v tom smyslu, že žádná jiná dobře fungující součást nikdy neposkytne stejný řetězec účelu.

>[!TIP]
> Použití oboru názvů a názvu typu součásti, která využívá rozhraní API pro ochranu dat, je dobré pravidlo, protože v praxi nebudou tyto informace nikdy kolidovat.
>
>Komponenta vytvořená společností Contoso, která je zodpovědná za tokeny nosiče minting, může jako svůj účel použít contoso. Security. BearerToken. Nebo dokonce lepší – může jako řetězec účelu použít contoso. Security. BearerToken. v1. Připojení čísla verze umožňuje budoucí verzi použít contoso. Security. BearerToken. v2 jako svůj účel a různé verze by se od sebe od sebe od sebe od té chvíle zcela odlišily jako datové části.

Vzhledem k tomu, že parametr pro účely `CreateProtector` je pole řetězců, výše uvedený je možné místo toho zadat jako `[ "Contoso.Security.BearerToken", "v1" ]` . To umožňuje vytvořit hierarchii účelů a otevřít možnost scénářů víceklientské architektury se systémem ochrany dat.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Součásti by neumožňovaly, aby nedůvěryhodný vstup uživatele byl jediným zdrojem vstupu pro řetěz účelu.
>
>Představte si třeba komponentu contoso. Messaging. SecureMessage, která zodpovídá za ukládání zabezpečených zpráv. Pokud by komponenta zabezpečeného zasílání zpráv volala `CreateProtector([ username ])` , pak uživatel se zlými úmysly může vytvořit účet s uživatelským jménem "contoso. Security. BearerToken" při pokusu o získání komponenty `CreateProtector([ "Contoso.Security.BearerToken" ])` , což neúmyslně způsobovat zabezpečený systém zasílání zpráv, aby mentolová datové části, které by mohly být vnímané jako ověřovací tokeny.
>
>Vhodným řetězcem pro komponentu zasílání zpráv je `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , což zajišťuje správnou izolaci.

K izolaci poskytuje a chování a `IDataProtectionProvider` `IDataProtector` slouží následující:

* Pro daný `IDataProtectionProvider` objekt `CreateProtector` Metoda vytvoří `IDataProtector` objekt jednoznačně vázaný na `IDataProtectionProvider` objekt, který jej vytvořil, a parametr pro účely, který byl předán metodě.

* Parametr účelu nesmí mít hodnotu null. (Pokud je účel zadán jako pole, znamená to, že pole nesmí mít nulovou délku a všechny elementy pole nesmí mít hodnotu null.) Účel prázdného řetězce je technicky povolený, ale nedoporučuje se.

* Dva argumenty pro účely jsou ekvivalentní pouze v případě, že obsahují stejné řetězce (pomocí ordinálního porovnávání) ve stejném pořadí. Jeden argument účelu je ekvivalentní k odpovídajícímu poli účelu s jedním prvkem.

* Dva `IDataProtector` objekty jsou ekvivalentní pouze v případě, že jsou vytvořeny z ekvivalentních `IDataProtectionProvider` objektů se stejnými parametry pro účely.

* Pro daný `IDataProtector` objekt volání `Unprotect(protectedData)` vrátí původní, `unprotectedData` Pokud a pouze `protectedData := Protect(unprotectedData)` v případě ekvivalentního `IDataProtector` objektu.

> [!NOTE]
> Nezvažujeme případ, kdy některá součást úmyslně zvolí řetězec účelu, u kterého se ví, že je v konfliktu s jinou komponentou. Taková součást by v podstatě byla považována za škodlivou a tento systém nebude poskytovat záruky zabezpečení v případě, že je v rámci pracovního procesu již spuštěn škodlivý kód.
