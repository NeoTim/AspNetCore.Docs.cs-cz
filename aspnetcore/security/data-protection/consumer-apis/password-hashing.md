---
title: Hesla hash v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat hesla pro ochranu ASP.NET Core dat pomocí rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: bd4b8fcf6a5a16a86ada97bbd3519f872d1417b7
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602451"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hesla hash v ASP.NET Core

Základ kódu ochrany dat zahrnuje balíček *Microsoft. AspNetCore. Cryptography.* Key, který obsahuje funkce odvození kryptografického klíče. Tento balíček je samostatná součást a nemá žádné závislosti na zbytku systému ochrany dat. Dá se použít zcela nezávisle. Zdroj existuje společně se základem kódu ochrany dat jako pohodlí.

Balíček aktuálně nabízí metodu `KeyDerivation.Pbkdf2` , která umožňuje hash hesla pomocí algoritmu [PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Toto rozhraní API je velmi podobné .NET Framework existujícímu [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité odlišnosti:

1. `Rfc2898DeriveBytes` `HMACSHA512` `HMACSHA1` `HMACSHA256`Metoda podporuje využívání více PRFs (aktuálně, a), zatímco typ podporuje `HMACSHA1`pouze. `KeyDerivation.Pbkdf2`

2. `KeyDerivation.Pbkdf2` Metoda zjistí aktuální operační systém a pokusí se zvolit optimalizovanou implementaci rutiny, která poskytuje mnohem lepší výkon v některých případech. (V systému Windows 8 nabízí přibližně 10x propustnost `Rfc2898DeriveBytes`.)

3. `KeyDerivation.Pbkdf2` Metoda vyžaduje, aby volající určil všechny parametry (Salt, PRF a Count iterace). `Rfc2898DeriveBytes` Typ poskytuje výchozí hodnoty pro tyto.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Podívejte se na [zdrojový kód](https://github.com/aspnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) pro ASP.NET Core `PasswordHasher` typ identity pro reálný případ použití.
