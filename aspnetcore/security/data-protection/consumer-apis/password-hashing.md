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
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="059ad-103">Hesla hash v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="059ad-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="059ad-104">Základ kódu ochrany dat zahrnuje balíček *Microsoft. AspNetCore. Cryptography.* Key, který obsahuje funkce odvození kryptografického klíče.</span><span class="sxs-lookup"><span data-stu-id="059ad-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="059ad-105">Tento balíček je samostatná součást a nemá žádné závislosti na zbytku systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="059ad-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="059ad-106">Dá se použít zcela nezávisle.</span><span class="sxs-lookup"><span data-stu-id="059ad-106">It can be used completely independently.</span></span> <span data-ttu-id="059ad-107">Zdroj existuje společně se základem kódu ochrany dat jako pohodlí.</span><span class="sxs-lookup"><span data-stu-id="059ad-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="059ad-108">Balíček aktuálně nabízí metodu `KeyDerivation.Pbkdf2` , která umožňuje hash hesla pomocí [algoritmu PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="059ad-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="059ad-109">Toto rozhraní API je velmi podobné .NET Framework existujícímu [typu Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité odlišnosti:</span><span class="sxs-lookup"><span data-stu-id="059ad-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="059ad-110">`KeyDerivation.Pbkdf2` Metoda podporuje využívání více PRFs `HMACSHA1`(aktuálně, `HMACSHA256`a `HMACSHA512`), zatímco `Rfc2898DeriveBytes` typ podporuje `HMACSHA1`pouze.</span><span class="sxs-lookup"><span data-stu-id="059ad-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="059ad-111">`KeyDerivation.Pbkdf2` Metoda zjistí aktuální operační systém a pokusí se zvolit optimalizovanou implementaci rutiny, která poskytuje mnohem lepší výkon v některých případech.</span><span class="sxs-lookup"><span data-stu-id="059ad-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="059ad-112">(V systému Windows 8 nabízí přibližně 10x propustnost `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="059ad-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="059ad-113">`KeyDerivation.Pbkdf2` Metoda vyžaduje, aby volající určil všechny parametry (Salt, PRF a Count iterace).</span><span class="sxs-lookup"><span data-stu-id="059ad-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="059ad-114">`Rfc2898DeriveBytes` Typ poskytuje výchozí hodnoty pro tyto.</span><span class="sxs-lookup"><span data-stu-id="059ad-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="059ad-115">Podívejte se na [zdrojový kód](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) pro Identity `PasswordHasher` typ ASP.NET Core pro reálný případ použití.</span><span class="sxs-lookup"><span data-stu-id="059ad-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
