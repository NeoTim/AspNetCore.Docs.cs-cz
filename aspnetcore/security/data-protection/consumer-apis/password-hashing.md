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
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="b2146-103">Určení hodnoty hash hesel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2146-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="b2146-104">Ochrana dat základu kódu obsahuje balíček *Microsoft.AspNetCore.Cryptography.KeyDerivation* obsahující funkce odvození kryptografické klíče.</span><span class="sxs-lookup"><span data-stu-id="b2146-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="b2146-105">Tento balíček je součástí samostatné a nemá žádné závislosti na zbytek systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="b2146-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="b2146-106">To je možné zcela nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="b2146-106">It can be used completely independently.</span></span> <span data-ttu-id="b2146-107">Zdroj existuje vedle kódu ochrany dat pro potřeby základní.</span><span class="sxs-lookup"><span data-stu-id="b2146-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="b2146-108">Balíček v současné době nabízí metody `KeyDerivation.Pbkdf2` umožňuje výpočtu hodnoty hash hesla pomocí [PBKDF2 algoritmus](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="b2146-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="b2146-109">Toto rozhraní API je velmi podobná stávající rozhraní .NET Framework [Rfc2898DeriveBytes typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale existují tři důležité rozdíly:</span><span class="sxs-lookup"><span data-stu-id="b2146-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="b2146-110">`KeyDerivation.Pbkdf2` Metoda podporuje použití více PRFs (aktuálně `HMACSHA1`, `HMACSHA256`, a `HMACSHA512`), zatímco `Rfc2898DeriveBytes` zadejte podporuje pouze `HMACSHA1`.</span><span class="sxs-lookup"><span data-stu-id="b2146-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="b2146-111">`KeyDerivation.Pbkdf2` Metoda zjistí aktuální operační systém a pokusí se zvolte nejvíce optimalizované provádění rutiny poskytuje mnohem lepší výkon v některých případech.</span><span class="sxs-lookup"><span data-stu-id="b2146-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="b2146-112">(V systému Windows 8 nabízí přibližně 10 x propustnost `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="b2146-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="b2146-113">`KeyDerivation.Pbkdf2` Metoda vyžaduje volajícímu zadat všechny parametry (salt, PRF a počet iterací).</span><span class="sxs-lookup"><span data-stu-id="b2146-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="b2146-114">`Rfc2898DeriveBytes` Typ poskytuje tyto výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b2146-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="b2146-115">Zobrazit [zdrojový kód](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) pro ASP.NET Core Identity `PasswordHasher` typ pro každodenní praxe případu použití.</span><span class="sxs-lookup"><span data-stu-id="b2146-115">See the [source code](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
