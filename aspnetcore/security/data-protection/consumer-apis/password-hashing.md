---
title: Určení hodnoty hash hesel v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vypočítat hodnotu hash hesla, pomocí rozhraní API ASP.NET Core Data Protection.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902670"
---
# <a name="hash-passwords-in-aspnet-core"></a>Určení hodnoty hash hesel v ASP.NET Core

Ochrana dat základu kódu obsahuje balíček *Microsoft.AspNetCore.Cryptography.KeyDerivation* obsahující funkce odvození kryptografické klíče. Tento balíček je součástí samostatné a nemá žádné závislosti na zbytek systému ochrany dat. To je možné zcela nezávisle na sobě. Zdroj existuje vedle kódu ochrany dat pro potřeby základní.

Balíček v současné době nabízí metody `KeyDerivation.Pbkdf2` umožňuje výpočtu hodnoty hash hesla pomocí [PBKDF2 algoritmus](https://tools.ietf.org/html/rfc2898#section-5.2). Toto rozhraní API je velmi podobná stávající rozhraní .NET Framework [Rfc2898DeriveBytes typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité rozdíly:

1. `KeyDerivation.Pbkdf2` Metoda podporuje použití více PRFs (aktuálně `HMACSHA1`, `HMACSHA256`, a `HMACSHA512`), zatímco `Rfc2898DeriveBytes` zadejte podporuje pouze `HMACSHA1`.

2. `KeyDerivation.Pbkdf2` Metoda zjistí aktuální operační systém a pokusí se zvolte nejvíce optimalizované provádění rutiny poskytuje mnohem lepší výkon v některých případech. (V systému Windows 8 nabízí přibližně 10 x propustnost `Rfc2898DeriveBytes`.)

3. `KeyDerivation.Pbkdf2` Metoda vyžaduje volajícímu zadat všechny parametry (salt, PRF a počet iterací). `Rfc2898DeriveBytes` Typ poskytuje tyto výchozí hodnoty.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Zobrazit [zdrojový kód](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) pro ASP.NET Core Identity `PasswordHasher` typ pro každodenní praxe případu použití.
