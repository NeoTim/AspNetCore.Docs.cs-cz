---
title: Začínáme s rozhraními API pro ochranu dat v ASP.NET Core
author: rick-anderson
description: Naučte se používat ASP.NET Core rozhraní API ochrany dat pro ochranu a ochranu dat v aplikaci.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774259"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Začínáme s rozhraními API pro ochranu dat v ASP.NET Core

<a name="security-data-protection-getting-started"></a>

V nejjednodušším případě ochrana dat sestává z následujících kroků:

1. Vytvořte ochranu dat od poskytovatele ochrany dat.

2. Zavolejte `Protect` metodu s daty, která chcete chránit.

3. Zavolejte `Unprotect` metodu s daty, která chcete převést zpět na prostý text.

Většina architektur a modelů aplikací, jako je například ASP.NET Core nebo SignalR, již konfigurují systém ochrany dat a přidávají ho do kontejneru služby, ke kterému přistupujete prostřednictvím injektáže závislosti. Následující příklad znázorňuje konfiguraci kontejneru služby pro vkládání závislostí a registraci zásobníku ochrany dat, příjem poskytovatele ochrany dat přes DI, vytvoření ochrany a ochrana před tím, než se data odeberou.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Při vytváření ochrany musíte zadat jeden nebo více [řetězců účelu](xref:security/data-protection/consumer-apis/purpose-strings). Řetězec účelu poskytuje izolaci mezi spotřebiteli. Například ochrana vytvořená pomocí řetězce "zelená" by nedokázala zrušit ochranu dat poskytovaných ochranou s účelem "fialové".

>[!TIP]
> Instance `IDataProtectionProvider` a `IDataProtector` jsou bezpečné pro přístup z více vláken pro více volajících. Je určeno, že jakmile komponenta získá odkaz `IDataProtector` na volání `CreateProtector`, použije tento odkaz na více volání `Protect` a. `Unprotect`
>
>Volání `Unprotect` bude vyvolat CryptographicException –, pokud nelze ověřit nebo dešifrovat chráněnou datovou část. Některé součásti můžou během operace zrušení ochrany ignorovat chyby; komponenta, která čte soubory cookie ověřování, může tuto chybu zpracovat a považovat požadavek za neúspěšného uložení souboru cookie. Součásti, které chtějí toto chování by měly specificky zachytit CryptographicException – místo požití všech výjimek.
