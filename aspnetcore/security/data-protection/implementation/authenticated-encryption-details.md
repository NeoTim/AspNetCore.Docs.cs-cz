---
title: Podrobnosti o ověřeném šifrování v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ověřovaného šifrování ASP.NET Core ochrany dat.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: ed75ab235a95a88bbe60615526137b4c2bb719ef
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630845"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Podrobnosti o ověřeném šifrování v ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Volání IDataProtector. Protect jsou ověřené operace šifrování. Metoda Protect nabízí utajení i pravost a je svázána s řetězem účelu, který byl použit k odvození této konkrétní instance IDataProtector z kořenového IDataProtectionProvider.

IDataProtector. Protect používá parametr Byte [] ve formátu prostého textu a vytváří chráněnou datovou část (bajt []), jejíž formát je popsán níže. (K dispozici je také přetížení metody rozšíření, které přijímá parametr řetězce prostého textu a vrátí datovou část chráněnou řetězcem. Pokud se používá toto rozhraní API, bude mít formát Protected Payload stále následující strukturu, ale bude [zakódovaný base64url](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Formát chráněné datové části

Formát chráněné datové části se skládá ze tří primárních součástí:

* 32 hlavička Magic, která identifikuje verzi systému ochrany dat.

* Identifikátor 128 klíče, který identifikuje klíč použitý k ochraně konkrétní datové části.

* Zbývající část chráněné datové části je [specifická pro šifru zapouzdřenou tímto klíčem](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). V následujícím příkladu klíč představuje šifrovací modul AES-256-CBC + HMACSHA256 a datová část je dále rozdělena následujícím způsobem:
  * Modifikátor 128 bitového klíče.
  * 128 inicializační vektor.
  * 48 bajtů AES-256-CBC pro výstup.
  * Ověřovací značka HMACSHA256.

Ukázka chráněná datová část je znázorněna níže.

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

Z formátu datové části nad první 32 bity nebo 4 bajty jsou hlavičkou Magic identifikující verzi (09 F0 C9 F0).

Dalších 128 bitů, neboli 16 bajtů, je identifikátor klíče (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57).

Zbývající část obsahuje datovou část a je specifická pro použitý formát.

> [!WARNING]
> Všechna datová zatížení chráněná k danému klíči budou začínat stejným hlavičkou (Magic Value, ID klíče). Správci mohou tuto skutečnost využít k tomu, aby byly diagnostické účely při vygenerování datové části přibližné. Například výše uvedená datová část odpovídá klíči {0c819c80-6619-4019-9536-53f8aaffee57}. Pokud po kontrole úložiště klíčů zjistíte, že toto datum aktivace konkrétního klíče bylo 2015-01-01 a datum vypršení jeho platnosti bylo 2015-03-01, pak je vhodné předpokládat, že v rámci tohoto okna byla vygenerována datová část (v případě, že nebyla úmyslně poškozena), podělte nebo využijte malý Fudge faktor na obou stranách.
