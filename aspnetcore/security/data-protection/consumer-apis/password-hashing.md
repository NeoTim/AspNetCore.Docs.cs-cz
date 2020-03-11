---
title: Hesla hash v ASP.NET Core
author: rick-anderson
description: Naučte se, jak používat hesla pro ochranu ASP.NET Core dat pomocí rozhraní API ochrany dat.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656049"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="3db18-103">Hesla hash v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3db18-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="3db18-104">Základ kódu ochrany dat zahrnuje balíček *Microsoft. AspNetCore. Cryptography.* Key, který obsahuje funkce odvození kryptografického klíče.</span><span class="sxs-lookup"><span data-stu-id="3db18-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="3db18-105">Tento balíček je samostatná součást a nemá žádné závislosti na zbytku systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="3db18-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="3db18-106">Dá se použít zcela nezávisle.</span><span class="sxs-lookup"><span data-stu-id="3db18-106">It can be used completely independently.</span></span> <span data-ttu-id="3db18-107">Zdroj existuje společně se základem kódu ochrany dat jako pohodlí.</span><span class="sxs-lookup"><span data-stu-id="3db18-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="3db18-108">Balíček v současnosti nabízí metodu `KeyDerivation.Pbkdf2`, která umožňuje hash hesla pomocí [algoritmu PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="3db18-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="3db18-109">Toto rozhraní API je velmi podobné .NET Framework existujícímu [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité odlišnosti:</span><span class="sxs-lookup"><span data-stu-id="3db18-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="3db18-110">Metoda `KeyDerivation.Pbkdf2` podporuje využívání více PRFs (aktuálně `HMACSHA1`, `HMACSHA256`a `HMACSHA512`), zatímco typ `Rfc2898DeriveBytes` podporuje pouze `HMACSHA1`.</span><span class="sxs-lookup"><span data-stu-id="3db18-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="3db18-111">Metoda `KeyDerivation.Pbkdf2` detekuje aktuální operační systém a pokusí se zvolit optimalizovanou implementaci rutiny, která poskytuje mnohem lepší výkon v některých případech.</span><span class="sxs-lookup"><span data-stu-id="3db18-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="3db18-112">(V systému Windows 8 nabízí 10x propustnost `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="3db18-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="3db18-113">Metoda `KeyDerivation.Pbkdf2` vyžaduje, aby volající určil všechny parametry (Salt, PRF a Count iterace).</span><span class="sxs-lookup"><span data-stu-id="3db18-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="3db18-114">Typ `Rfc2898DeriveBytes` poskytuje pro ně výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3db18-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="3db18-115">Podívejte se na [zdrojový kód](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) pro typ `PasswordHasher` ASP.NET Core identity pro reálný případ použití.</span><span class="sxs-lookup"><span data-stu-id="3db18-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
