---
title: Podrobnosti o ověřeném šifrování v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ověřovaného šifrování ASP.NET Core ochrany dat.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: ebd784b493b7f283df2cc84a39113e3d714472a0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022066"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="e3b82-103">Podrobnosti o ověřeném šifrování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3b82-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="e3b82-104">Volání IDataProtector. Protect jsou ověřené operace šifrování.</span><span class="sxs-lookup"><span data-stu-id="e3b82-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="e3b82-105">Metoda Protect nabízí utajení i pravost a je svázána s řetězem účelu, který byl použit k odvození této konkrétní instance IDataProtector z kořenového IDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="e3b82-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="e3b82-106">IDataProtector. Protect používá parametr Byte [] ve formátu prostého textu a vytváří chráněnou datovou část (bajt []), jejíž formát je popsán níže.</span><span class="sxs-lookup"><span data-stu-id="e3b82-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="e3b82-107">(K dispozici je také přetížení metody rozšíření, které přijímá parametr řetězce prostého textu a vrátí datovou část chráněnou řetězcem.</span><span class="sxs-lookup"><span data-stu-id="e3b82-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="e3b82-108">Pokud se používá toto rozhraní API, bude mít formát Protected Payload stále následující strukturu, ale bude [zakódovaný base64url](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="e3b82-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="e3b82-109">Formát chráněné datové části</span><span class="sxs-lookup"><span data-stu-id="e3b82-109">Protected payload format</span></span>

<span data-ttu-id="e3b82-110">Formát chráněné datové části se skládá ze tří primárních součástí:</span><span class="sxs-lookup"><span data-stu-id="e3b82-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="e3b82-111">32 hlavička Magic, která identifikuje verzi systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="e3b82-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="e3b82-112">Identifikátor 128 klíče, který identifikuje klíč použitý k ochraně konkrétní datové části.</span><span class="sxs-lookup"><span data-stu-id="e3b82-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="e3b82-113">Zbývající část chráněné datové části je [specifická pro šifru zapouzdřenou tímto klíčem](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="e3b82-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="e3b82-114">V následujícím příkladu klíč představuje šifrovací modul AES-256-CBC + HMACSHA256 a datová část je dále rozdělena následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e3b82-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="e3b82-115">Modifikátor 128 bitového klíče.</span><span class="sxs-lookup"><span data-stu-id="e3b82-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="e3b82-116">128 inicializační vektor.</span><span class="sxs-lookup"><span data-stu-id="e3b82-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="e3b82-117">48 bajtů AES-256-CBC pro výstup.</span><span class="sxs-lookup"><span data-stu-id="e3b82-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="e3b82-118">Ověřovací značka HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="e3b82-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="e3b82-119">Ukázka chráněná datová část je znázorněna níže.</span><span class="sxs-lookup"><span data-stu-id="e3b82-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="e3b82-120">Z formátu datové části nad první 32 bity nebo 4 bajty jsou hlavičkou Magic identifikující verzi (09 F0 C9 F0).</span><span class="sxs-lookup"><span data-stu-id="e3b82-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="e3b82-121">Dalších 128 bitů, neboli 16 bajtů, je identifikátor klíče (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57).</span><span class="sxs-lookup"><span data-stu-id="e3b82-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="e3b82-122">Zbývající část obsahuje datovou část a je specifická pro použitý formát.</span><span class="sxs-lookup"><span data-stu-id="e3b82-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="e3b82-123">Všechna datová zatížení chráněná k danému klíči budou začínat stejným hlavičkou (Magic Value, ID klíče).</span><span class="sxs-lookup"><span data-stu-id="e3b82-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="e3b82-124">Správci mohou tuto skutečnost využít k tomu, aby byly diagnostické účely při vygenerování datové části přibližné.</span><span class="sxs-lookup"><span data-stu-id="e3b82-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="e3b82-125">Například výše uvedená datová část odpovídá klíči {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="e3b82-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="e3b82-126">Pokud po kontrole úložiště klíčů zjistíte, že toto datum aktivace konkrétního klíče bylo 2015-01-01 a datum vypršení jeho platnosti bylo 2015-03-01, pak je vhodné předpokládat, že v rámci tohoto okna byla vygenerována datová část (v případě, že nebyla úmyslně poškozena), podělte nebo využijte malý Fudge faktor na obou stranách.</span><span class="sxs-lookup"><span data-stu-id="e3b82-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
