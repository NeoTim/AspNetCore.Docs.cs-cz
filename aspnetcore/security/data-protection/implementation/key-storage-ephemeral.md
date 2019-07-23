---
title: Zprostředkovatelé dočasné ochrany dat v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace ASP.NET Core zprostředkovatelé dočasné ochrany dat.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: e4b0014ab3bdbf90b91383e8a33102f94faa8153
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901626"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Zprostředkovatelé dočasné ochrany dat v ASP.NET Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Existují scénáře, kdy aplikace potřebuje throwaway `IDataProtectionProvider`. Například vývojář může být pouze experimentování v jednorázové konzolovou aplikaci, nebo vlastní aplikace je přechodná (je vytvořena nebo projekt testování částí). Pro podporu těchto scénářů [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) balíček obsahuje typ `EphemeralDataProtectionProvider`. Tento typ poskytuje základní implementaci `IDataProtectionProvider` jejichž klíče úložiště se nachází pouze v paměti a není zapsané do jakékoli záložního úložiště.

Každá instance `EphemeralDataProtectionProvider` používá svůj vlastní jedinečný hlavní klíč. Proto pokud `IDataProtector` kořenovým adresářem v `EphemeralDataProtectionProvider` generuje datové části je chráněný, tuto datovou část může být pouze zbaveny ekvivalentní `IDataProtector` (zadaný stejný [účel](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) řetězu) kořenovým adresářem v stejný `EphemeralDataProtectionProvider` instance.

Následující příklad ukazuje vytvoření instance `EphemeralDataProtectionProvider` a jeho použití k ochraně a zrušení ochrany dat.

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
