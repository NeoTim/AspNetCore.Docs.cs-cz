---
title: Podrobnosti ověřeného šifrování v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti implementace ochrany dat ASP.NET Core ověření šifrování.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902661"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="e5589-103">Podrobnosti ověřeného šifrování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5589-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="e5589-104">Volání IDataProtector.Protect jsou operace ověřeného šifrování.</span><span class="sxs-lookup"><span data-stu-id="e5589-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="e5589-105">Metoda chránit nabízí důvěrnost a pravosti a je spojený s řetězci účel, který byl použit k odvození této konkrétní instance IDataProtector z jeho kořenové složky IDataProtectionProvider.</span><span class="sxs-lookup"><span data-stu-id="e5589-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="e5589-106">IDataProtector.Protect přebírá parametr ve formátu prostého textu byte [] a vytváří byte [] chráněné datovou část, jejichž formát je popsán níže.</span><span class="sxs-lookup"><span data-stu-id="e5589-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="e5589-107">(Je také přetížení metody rozšíření, která použije parametr řetězce ve formátu prostého textu a vrátí řetězec chráněné datové části.</span><span class="sxs-lookup"><span data-stu-id="e5589-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="e5589-108">Pokud se toto rozhraní API používá formát chráněné datové části budou mít dál následující strukturu, ale bude [kódováním base64url](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="e5589-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="e5589-109">Formát chráněných datové části</span><span class="sxs-lookup"><span data-stu-id="e5589-109">Protected payload format</span></span>

<span data-ttu-id="e5589-110">Formát chráněných datové části se skládá z tři hlavní komponenty:</span><span class="sxs-lookup"><span data-stu-id="e5589-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="e5589-111">Magic hlavička pro 32bitovou verzi, která identifikuje verzi systému ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="e5589-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="e5589-112">Identifikátor klíče 128-bit, který identifikuje klíč používaný k ochraně této konkrétní datové části.</span><span class="sxs-lookup"><span data-stu-id="e5589-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="e5589-113">Zbývající část chráněné datové části je [specifické pro šifrování zapouzdřená pomocí tohoto klíče](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="e5589-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="e5589-114">V následujícím příkladu představuje klíč AES-256-CBC + HMACSHA256 šifrování a datové části je dále rozdělená následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e5589-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="e5589-115">Modifikátor klíče 128 bitů.</span><span class="sxs-lookup"><span data-stu-id="e5589-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="e5589-116">128bitové inicializační vektor.</span><span class="sxs-lookup"><span data-stu-id="e5589-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="e5589-117">aspoň 48 bajtů AES-256-CBC výstupu.</span><span class="sxs-lookup"><span data-stu-id="e5589-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="e5589-118">HMACSHA256 ověřovací značka.</span><span class="sxs-lookup"><span data-stu-id="e5589-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="e5589-119">Chráněné ukázkovou datovou část je znázorněno níže.</span><span class="sxs-lookup"><span data-stu-id="e5589-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="e5589-120">Formát datové části nad prvních 32 bitů, tedy 4 bajty jsou magic záhlaví identifikaci verze (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="e5589-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="e5589-121">Další 128 bitů, tedy 16 bajtů je identifikátor klíče (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="e5589-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="e5589-122">Zbývající obsahuje datovou část a je specifické pro použitý formát.</span><span class="sxs-lookup"><span data-stu-id="e5589-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="e5589-123">Všechny datové části chráněný, aby daný klíč bude začínat stejná hlavička 20bajtová (hodnota magic, id klíče).</span><span class="sxs-lookup"><span data-stu-id="e5589-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="e5589-124">Správce můžete použít tuto skutečnost pro diagnostické účely aproximace při datovou část, která byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="e5589-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="e5589-125">Například datové části výše odpovídá klíči {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="e5589-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="e5589-126">Pokud po kontrole klíče úložiště zjistíte, že datum aktivace tento specifický klíč byl 2015-01-01 a datum vypršení platnosti je 2015-03-01, pak je možné logicky předpokládat, že datová část (Pokud není úmyslně) byl vygenerován v rámci tohoto okna, uveďte, nebo provést malá faktor hry fudge na obou stranách.</span><span class="sxs-lookup"><span data-stu-id="e5589-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
