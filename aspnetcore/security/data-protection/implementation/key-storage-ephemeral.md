---
title: Zprostředkovatelé dočasné ochrany dat v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core zprostředkovatelů ochrany před dočasnými daty.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: 22a332230e15256dc33fd1d06f2da3ea8d34d3bc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776887"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Zprostředkovatelé dočasné ochrany dat v ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Existují situace, kdy aplikace potřebuje throwaway `IDataProtectionProvider`. Vývojář může například experimentovat pouze v jednorázových konzolových aplikacích, nebo je samotná aplikace přechodná (je to skriptový projekt nebo test jednotky). Pro podporu těchto scénářů obsahuje balíček [Microsoft. AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) typ `EphemeralDataProtectionProvider`. Tento typ poskytuje základní implementaci, `IDataProtectionProvider` jejíž úložiště klíčů je uchováváno pouze v paměti a není zapsáno do žádného záložního úložiště.

Každá instance `EphemeralDataProtectionProvider` používá vlastní jedinečný hlavní klíč. `IDataProtector` Proto pokud kořenový adresář `EphemeralDataProtectionProvider` vygeneruje chráněnou datovou část, lze tuto datovou část odemknout pouze pomocí ekvivalentu `IDataProtector` (pro stejný řetěz [účelu](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) root ve stejné `EphemeralDataProtectionProvider` instanci.

Následující příklad ukazuje vytvoření instance `EphemeralDataProtectionProvider` a její použití k ochraně a neochraně dat.

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
