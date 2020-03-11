---
title: Řetězce účelu v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se v rozhraních API ochrany ASP.NET Corech dat používají řetězce účelu.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664722"
---
# <a name="purpose-strings-in-aspnet-core"></a>Řetězce účelu v ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Komponenty, které spotřebovávají `IDataProtectionProvider` musí předat metodě `CreateProtector` jedinečný parametr pro *účely* . *Parametr* pro účely je podstatou zabezpečení systému ochrany dat, protože poskytuje izolaci mezi spotřebiteli šifrování, a to i v případě, že jsou kořenové kryptografické klíče stejné.

Když příjemce zadá účel, použije se spolu s kořenovými šifrovacími klíči k odvození kryptografických podklíčů, které jsou pro tohoto příjemce jedinečné. Tím se uživatel izoluje od všech ostatních zprostředkovatelů kryptografie v aplikaci: žádná jiná komponenta nemůže číst své datové části a nemůže číst žádné další datové části součásti. Tato izolace také vykresluje neproveditelné celé kategorie útoků proti komponentě.

![Příklad diagramu účelu](purpose-strings/_static/purposes.png)

V diagramu výše se `IDataProtector` instance a a B **nedají** načítat jiné datové části.

Řetězec účelu nemusí být tajný. Měl by být jednoduše jedinečný v tom smyslu, že žádná jiná dobře fungující součást nikdy neposkytne stejný řetězec účelu.

>[!TIP]
> Použití oboru názvů a názvu typu součásti, která využívá rozhraní API pro ochranu dat, je dobré pravidlo, protože v praxi nebudou tyto informace nikdy kolidovat.
>
>Komponenta vytvořená společností Contoso, která je zodpovědná za tokeny nosiče minting, může jako svůj účel použít contoso. Security. BearerToken. Nebo dokonce lepší – může jako řetězec účelu použít contoso. Security. BearerToken. v1. Připojení čísla verze umožňuje budoucí verzi použít contoso. Security. BearerToken. v2 jako svůj účel a různé verze by se od sebe od sebe od sebe od té chvíle zcela odlišily jako datové části.

Vzhledem k tomu, že parametr pro účely `CreateProtector` je pole řetězců, bylo výše uvedené místo určení jako `[ "Contoso.Security.BearerToken", "v1" ]`. To umožňuje vytvořit hierarchii účelů a otevřít možnost scénářů víceklientské architektury se systémem ochrany dat.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Součásti by neumožňovaly, aby nedůvěryhodný vstup uživatele byl jediným zdrojem vstupu pro řetěz účelu.
>
>Představte si třeba komponentu contoso. Messaging. SecureMessage, která zodpovídá za ukládání zabezpečených zpráv. Pokud komponenta zabezpečeného zasílání zpráv byla volána `CreateProtector([ username ])`, pak může uživatel se zlými úmysly vytvořit účet s uživatelským jménem "contoso. Security. BearerToken" při pokusu o získání komponenty pro volání `CreateProtector([ "Contoso.Security.BearerToken" ])`, což neúmyslně způsobovat zabezpečený systém zasílání zpráv, aby mentolová datové části, které by mohly být vnímané jako ověřovací tokeny.
>
>K dispozici je `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`řetězec lepších účelů pro komponentu zasílání zpráv, která poskytuje správnou izolaci.

K izolaci poskytované a chování `IDataProtectionProvider`, `IDataProtector`a účely patří následující:

* Pro daný objekt `IDataProtectionProvider` `CreateProtector` metoda vytvoří objekt `IDataProtector` jednoznačně svázán s objektem `IDataProtectionProvider`, který jej vytvořil, a parametrem pro účely, který byl předán do metody.

* Parametr účelu nesmí mít hodnotu null. (Pokud je účel zadán jako pole, znamená to, že pole nesmí mít nulovou délku a všechny elementy pole nesmí mít hodnotu null.) Účel prázdného řetězce je technicky povolený, ale nedoporučuje se.

* Dva argumenty pro účely jsou ekvivalentní pouze v případě, že obsahují stejné řetězce (pomocí ordinálního porovnávání) ve stejném pořadí. Jeden argument účelu je ekvivalentní k odpovídajícímu poli účelu s jedním prvkem.

* Dva `IDataProtector` objekty jsou ekvivalentní pouze v případě, že jsou vytvořeny z ekvivalentních `IDataProtectionProvider` objektů se stejnými parametry pro účely.

* Pro daný objekt `IDataProtector` volání `Unprotect(protectedData)` vrátí původní `unprotectedData` pouze v případě, že `protectedData := Protect(unprotectedData)` pro ekvivalentní objekt `IDataProtector`.

> [!NOTE]
> Nezvažujeme případ, kdy některá součást úmyslně zvolí řetězec účelu, u kterého se ví, že je v konfliktu s jinou komponentou. Taková součást by v podstatě byla považována za škodlivou a tento systém nebude poskytovat záruky zabezpečení v případě, že je v rámci pracovního procesu již spuštěn škodlivý kód.
