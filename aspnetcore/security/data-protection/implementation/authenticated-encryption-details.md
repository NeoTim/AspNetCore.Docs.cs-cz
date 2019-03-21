---
title: Podrobnosti ověřeného šifrování v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace ochrany dat ASP.NET Core ověření šifrování.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208577"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Podrobnosti ověřeného šifrování v ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Volání IDataProtector.Protect jsou operace ověřeného šifrování. Metoda chránit nabízí důvěrnost a pravosti a je spojený s řetězci účel, který byl použit k odvození této konkrétní instance IDataProtector z jeho kořenové složky IDataProtectionProvider.

IDataProtector.Protect přebírá parametr ve formátu prostého textu byte [] a vytváří byte [] chráněné datovou část, jejichž formát je popsán níže. (Je také přetížení metody rozšíření, která použije parametr řetězce ve formátu prostého textu a vrátí řetězec chráněné datové části. Pokud se toto rozhraní API používá formát chráněné datové části budou mít dál následující strukturu, ale bude [kódováním base64url](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Formát chráněných datové části

Formát chráněných datové části se skládá z tři hlavní komponenty:

* Magic hlavička pro 32bitovou verzi, která identifikuje verzi systému ochrany dat.

* Identifikátor klíče 128-bit, který identifikuje klíč používaný k ochraně této konkrétní datové části.

* Zbývající část chráněné datové části je [specifické pro šifrování zapouzdřená pomocí tohoto klíče](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). V následujícím příkladu představuje klíč AES-256-CBC + HMACSHA256 šifrování a datové části je dále rozdělená následujícím způsobem:
  * Modifikátor klíče 128 bitů.
  * 128bitové inicializační vektor.
  * aspoň 48 bajtů AES-256-CBC výstupu.
  * HMACSHA256 ověřovací značka.

Chráněné ukázkovou datovou část je znázorněno níže.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Formát datové části nad prvních 32 bitů, tedy 4 bajty jsou magic záhlaví identifikaci verze (09 F0 C9 F0)

Další 128 bitů, tedy 16 bajtů je identifikátor klíče (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Zbývající obsahuje datovou část a je specifické pro použitý formát.

> [!WARNING]
> Všechny datové části chráněný, aby daný klíč bude začínat stejná hlavička 20bajtová (hodnota magic, id klíče). Správce můžete použít tuto skutečnost pro diagnostické účely aproximace při datovou část, která byla vygenerována. Například datové části výše odpovídá klíči {0c819c80-6619-4019-9536-53f8aaffee57}. Pokud po kontrole klíče úložiště zjistíte, že datum aktivace tento specifický klíč byl 2015-01-01 a datum vypršení platnosti je 2015-03-01, pak je možné logicky předpokládat, že datová část (Pokud není úmyslně) byl vygenerován v rámci tohoto okna, uveďte, nebo provést malá faktor hry fudge na obou stranách.
