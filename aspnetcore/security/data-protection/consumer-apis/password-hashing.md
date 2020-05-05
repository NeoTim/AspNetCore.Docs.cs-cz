---
title: Hesla hash v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat hesla pro ochranu ASP.NET Core dat pomocí rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 6a5e0e4378241671905f2a759aad88372901e7d2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769777"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hesla hash v ASP.NET Core

Základ kódu ochrany dat zahrnuje balíček *Microsoft. AspNetCore. Cryptography.* Key, který obsahuje funkce odvození kryptografického klíče. Tento balíček je samostatná součást a nemá žádné závislosti na zbytku systému ochrany dat. Dá se použít zcela nezávisle. Zdroj existuje společně se základem kódu ochrany dat jako pohodlí.

Balíček aktuálně nabízí metodu `KeyDerivation.Pbkdf2` , která umožňuje hash hesla pomocí [algoritmu PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Toto rozhraní API je velmi podobné .NET Framework existujícímu [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité odlišnosti:

1. `KeyDerivation.Pbkdf2` Metoda podporuje využívání více PRFs `HMACSHA1`(aktuálně, `HMACSHA256`a `HMACSHA512`), zatímco `Rfc2898DeriveBytes` typ podporuje `HMACSHA1`pouze.

2. `KeyDerivation.Pbkdf2` Metoda zjistí aktuální operační systém a pokusí se zvolit optimalizovanou implementaci rutiny, která poskytuje mnohem lepší výkon v některých případech. (V systému Windows 8 nabízí přibližně 10x propustnost `Rfc2898DeriveBytes`.)

3. `KeyDerivation.Pbkdf2` Metoda vyžaduje, aby volající určil všechny parametry (Salt, PRF a Count iterace). `Rfc2898DeriveBytes` Typ poskytuje výchozí hodnoty pro tyto.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Podívejte se na [zdrojový kód](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) pro Identity `PasswordHasher` typ ASP.NET Core pro reálný případ použití.
