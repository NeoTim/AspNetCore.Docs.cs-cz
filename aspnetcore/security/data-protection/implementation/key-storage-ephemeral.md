---
title: Zprostředkovatelé dočasné ochrany dat v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core zprostředkovatelů ochrany před dočasnými daty.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: f51553385d9481a1e96fe3e1a14e51e470b0e735
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018257"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="0448a-103">Zprostředkovatelé dočasné ochrany dat v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0448a-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="0448a-104">Existují situace, kdy aplikace potřebuje throwaway `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="0448a-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="0448a-105">Vývojář může například experimentovat pouze v jednorázových konzolových aplikacích, nebo je samotná aplikace přechodná (je to skriptový projekt nebo test jednotky).</span><span class="sxs-lookup"><span data-stu-id="0448a-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="0448a-106">Pro podporu těchto scénářů obsahuje balíček [Microsoft. AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) typ `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="0448a-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="0448a-107">Tento typ poskytuje základní implementaci, `IDataProtectionProvider` jejíž úložiště klíčů je uchováváno pouze v paměti a není zapsáno do žádného záložního úložiště.</span><span class="sxs-lookup"><span data-stu-id="0448a-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="0448a-108">Každá instance `EphemeralDataProtectionProvider` používá vlastní jedinečný hlavní klíč.</span><span class="sxs-lookup"><span data-stu-id="0448a-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="0448a-109">Proto pokud `IDataProtector` kořenový adresář `EphemeralDataProtectionProvider` vygeneruje chráněnou datovou část, lze tuto datovou část odemknout pouze pomocí ekvivalentu `IDataProtector` (pro stejný řetěz [účelu](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) root ve stejné `EphemeralDataProtectionProvider` instanci.</span><span class="sxs-lookup"><span data-stu-id="0448a-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="0448a-110">Následující příklad ukazuje vytvoření instance `EphemeralDataProtectionProvider` a její použití k ochraně a neochraně dat.</span><span class="sxs-lookup"><span data-stu-id="0448a-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
