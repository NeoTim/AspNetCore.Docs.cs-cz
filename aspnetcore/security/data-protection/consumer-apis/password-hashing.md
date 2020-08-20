---
title: Hesla hash v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat hesla pro ochranu ASP.NET Core dat pomocí rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 19263400397a9dfe2d9e6044109d6d063023f6f4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629857"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hesla hash v ASP.NET Core

Základ kódu ochrany dat zahrnuje balíček *Microsoft. AspNetCore. Cryptography.* Key, který obsahuje funkce odvození kryptografického klíče. Tento balíček je samostatná součást a nemá žádné závislosti na zbytku systému ochrany dat. Dá se použít zcela nezávisle. Zdroj existuje společně se základem kódu ochrany dat jako pohodlí.

Balíček aktuálně nabízí metodu `KeyDerivation.Pbkdf2` , která umožňuje hash hesla pomocí [algoritmu PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Toto rozhraní API je velmi podobné .NET Framework existujícímu [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité odlišnosti:

1. `KeyDerivation.Pbkdf2`Metoda podporuje využívání více PRFs (aktuálně `HMACSHA1` , `HMACSHA256` a `HMACSHA512` ), zatímco `Rfc2898DeriveBytes` Typ podporuje pouze `HMACSHA1` .

2. `KeyDerivation.Pbkdf2`Metoda zjistí aktuální operační systém a pokusí se zvolit optimalizovanou implementaci rutiny, která poskytuje mnohem lepší výkon v některých případech. (V systému Windows 8 nabízí přibližně 10x propustnost `Rfc2898DeriveBytes` .)

3. `KeyDerivation.Pbkdf2`Metoda vyžaduje, aby volající určil všechny parametry (Salt, PRF a Count iterace). `Rfc2898DeriveBytes`Typ poskytuje výchozí hodnoty pro tyto.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Podívejte se na [zdrojový kód](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) ASP.NET Core Identity `PasswordHasher` typu pro reálný případ použití.
