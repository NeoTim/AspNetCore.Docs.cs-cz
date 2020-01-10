---
title: Hesla hash v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat hesla pro ochranu ASP.NET Core dat pomocí rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828565"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hesla hash v ASP.NET Core

Základ kódu ochrany dat zahrnuje balíček *Microsoft. AspNetCore. Cryptography.* Key, který obsahuje funkce odvození kryptografického klíče. Tento balíček je samostatná součást a nemá žádné závislosti na zbytku systému ochrany dat. Dá se použít zcela nezávisle. Zdroj existuje společně se základem kódu ochrany dat jako pohodlí.

Balíček v současnosti nabízí metodu `KeyDerivation.Pbkdf2`, která umožňuje hash hesla pomocí [algoritmu PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Toto rozhraní API je velmi podobné .NET Framework existujícímu [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité odlišnosti:

1. Metoda `KeyDerivation.Pbkdf2` podporuje využívání více PRFs (aktuálně `HMACSHA1`, `HMACSHA256`a `HMACSHA512`), zatímco typ `Rfc2898DeriveBytes` podporuje pouze `HMACSHA1`.

2. Metoda `KeyDerivation.Pbkdf2` detekuje aktuální operační systém a pokusí se zvolit optimalizovanou implementaci rutiny, která poskytuje mnohem lepší výkon v některých případech. (V systému Windows 8 nabízí 10x propustnost `Rfc2898DeriveBytes`.)

3. Metoda `KeyDerivation.Pbkdf2` vyžaduje, aby volající určil všechny parametry (Salt, PRF a Count iterace). Typ `Rfc2898DeriveBytes` poskytuje pro ně výchozí hodnoty.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Podívejte se na [zdrojový kód](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) pro typ `PasswordHasher` ASP.NET Core identity pro reálný případ použití.
