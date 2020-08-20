---
title: Hlavičky kontextu v ASP.NET Core
author: rick-anderson
description: Přečtěte si podrobnosti o implementaci ASP.NET Core hlaviček kontextu ochrany dat.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2f07db4b7d8bca9f64aee5d60e88fc92dc8965eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633705"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="e7ea1-103">Hlavičky kontextu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7ea1-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="e7ea1-104">Pozadí a teorie</span><span class="sxs-lookup"><span data-stu-id="e7ea1-104">Background and theory</span></span>

<span data-ttu-id="e7ea1-105">V systému ochrany dat označuje "klíč" objekt, který může poskytovat ověřené šifrovací služby.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="e7ea1-106">Každý klíč je identifikován jedinečným ID (identifikátor GUID) a obsahuje informace o algoritmu IT a Entropic materiál.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="e7ea1-107">Je vhodné, aby každý klíč měl jedinečnou entropii, ale systém ho nedokáže vyhovět, a potřebujeme také pro vývojáře, kteří můžou změnit klíčového prstence ručně úpravou algoritmových informací stávajícího klíče ve službě Key Ring.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="e7ea1-108">Aby bylo možné dosáhnout našich požadavků na zabezpečení, má systém ochrany dat koncept [kryptografické flexibility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), který umožňuje bezpečné použití jedné entropicové hodnoty napříč několika kryptografickými algoritmy.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="e7ea1-109">Většina systémů, které podporují kryptografickou flexibilitu, tak, že zahrnuje některé identifikační informace o algoritmu uvnitř datové části.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="e7ea1-110">Identifikátor OID tohoto algoritmu je obecně dobrým kandidátem.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="e7ea1-111">Jedním z problémů, na které jsme narazili, je to, že existuje několik způsobů, jak zadat stejný algoritmus: "AES" (CNG) a spravované algoritmy AES, AesManaged, AesCryptoServiceProvider, AesCng a RijndaelManaged (zadané konkrétní parametry) jsou vlastně stejné a musíme udržovat mapování všech těchto objektů na správný identifikátor OID.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="e7ea1-112">Pokud vývojář chtěl poskytnout vlastní algoritmus (nebo dokonce i jinou implementaci AES!), musí nám sdělit svůj identifikátor OID.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="e7ea1-113">Tento dodatečný registrační krok usnadňuje konfiguraci systému, zejména bolestivý.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="e7ea1-114">Krok zpět, rozhodli jsme se, že jsme se k problému přistupovali z nesprávného směru.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="e7ea1-115">Identifikátor OID oznamuje, co je to algoritmus, ale ve skutečnosti to nezáleží.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="e7ea1-116">Pokud musíme použít jednu Entropic hodnotu bezpečně ve dvou různých algoritmech, není nutné, aby bylo možné zjistit, jaké jsou algoritmy skutečně.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="e7ea1-117">To, co se ve skutečnosti zajímá, je způsob, jakým se chovají.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="e7ea1-118">Libovolný dát symetrický algoritmus šifry je také silný pseudonáhodných permutace (PRP): Opravte vstupy (klíč, režim zřetězení, IV, prostý text) a výstup šifrovaného textu se neshoduje s tím, že se pravděpodobnost zahlcení od všech ostatních algoritmů symetrického šifry, které mají stejné vstupy.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="e7ea1-119">Podobně platí, že jakákoli funkce dát s klíčem hash je také silnou pseudonáhodných funkcí (PRF) a předána pevně daná vstupní sada. její výstup bude bez jakýchkoli dalších funkcí hash s klíčem naprosto jedinečný.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="e7ea1-120">Tento koncept silných PRPs a PRFs používáme k sestavení hlavičky kontextu.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="e7ea1-121">Tato hlavička kontextu v podstatě funguje jako stabilní kryptografický otisk přes používané algoritmy pro jakoukoli danou operaci a poskytuje kryptografickou flexibilitu, kterou vyžaduje systém ochrany dat.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="e7ea1-122">Tato hlavička je reprodukovatelná a používá se později jako součást [procesu odvození podklíče](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="e7ea1-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="e7ea1-123">Existují dva různé způsoby, jak vytvořit hlavičku kontextu v závislosti na režimech fungování základních algoritmů.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="e7ea1-124">Šifrování CBC a ověřování HMAC</span><span class="sxs-lookup"><span data-stu-id="e7ea1-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="e7ea1-125">Záhlaví kontextu se skládá z následujících součástí:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="e7ea1-126">[16 bitů] Hodnota 00 00, což je označení "CBC Encryption + HMAC Authentication".</span><span class="sxs-lookup"><span data-stu-id="e7ea1-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="e7ea1-127">[32 bitů] Délka klíče (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="e7ea1-128">[32 bitů] Velikost bloku (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="e7ea1-129">[32 bitů] Délka klíče (v bajtech, Big Endian) algoritmu HMAC.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="e7ea1-130">(V současné době je velikost klíče vždy shodná s velikostí Digest.)</span><span class="sxs-lookup"><span data-stu-id="e7ea1-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="e7ea1-131">[32 bitů] Velikost algoritmu Digest (v bajtech, Big Endian) algoritmu HMAC.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="e7ea1-132">`EncCBC(K_E, IV, "")`, což je výstup algoritmu symetrického bloku šifrování, který předali prázdný vstup řetězce a kde IV je nulový vektor.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="e7ea1-133">Konstrukce `K_E` je popsána níže.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="e7ea1-134">`MAC(K_H, "")`, což je výstup algoritmu HMAC, který předaný prázdný řetězec vstupu.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="e7ea1-135">Konstrukce `K_H` je popsána níže.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="e7ea1-136">V ideálním případě můžeme předat všem nulovým vektorům pro `K_E` a `K_H` .</span><span class="sxs-lookup"><span data-stu-id="e7ea1-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="e7ea1-137">Chceme se ale vyhnout situaci, kdy základní algoritmus kontroluje existenci slabých klíčů před provedením jakýchkoli operací (zejména DES a 3DES), což vylučuje použití jednoduchého nebo opakovaného vzoru, jako je nulový vektor.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="e7ea1-138">Místo toho používáme NIST SP800-108 KDF v režimu počítadla (viz [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) s klíčem s nulovou délkou, popiskem a kontextem a HMACSHA512 jako základní PRF.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="e7ea1-139">Odvozené `| K_E | + | K_H |` bajty výstupu a pak rozloží výsledek do a do `K_E` `K_H` sebe.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="e7ea1-140">Matematicky je znázorněna následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="e7ea1-141">Příklad: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="e7ea1-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="e7ea1-142">Příklad: Zvažte případ, kde algoritmus symetrického bloku šifry je AES-192-CBC a ověřovací algoritmus je HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="e7ea1-143">Systém vygeneruje hlavičku kontextu pomocí následujících kroků.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="e7ea1-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , kde `| K_E | = 192 bits` a `| K_H | = 256 bits` podle určených algoritmů.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="e7ea1-145">To vede k `K_E = 5BB6..21DD` a `K_H = A04A..00A9` v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="e7ea1-146">V dalším kroku COMPUTE `Enc_CBC (K_E, IV, "")` pro AES-192-CBC `IV = 0*` a na `K_E` výše uvedené.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="e7ea1-147">Dále COMPUTE `MAC(K_H, "")` pro HMACSHA256 `K_H` , které jsou uvedené výše.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="e7ea1-148">Tím se vytvoří úplné záhlaví kontextu níže:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="e7ea1-149">Toto kontextové záhlaví je kryptografický otisk páru ověřených šifrovacích algoritmů (AES-192-CBC Encryption + HMACSHA256 Validation).</span><span class="sxs-lookup"><span data-stu-id="e7ea1-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="e7ea1-150">Komponenty, jak je popsáno [výše](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , jsou následující:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="e7ea1-151">Značka `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="e7ea1-152">délka šifrovacího klíče bloku `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="e7ea1-153">velikost bloku šifry bloku `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="e7ea1-154">Délka klíče HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="e7ea1-155">velikost Digest HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="e7ea1-156">zablokuje výstup PRP (Block Cipher) `(F4 74 - DB 6F)` a</span><span class="sxs-lookup"><span data-stu-id="e7ea1-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="e7ea1-157">výstup HMAC PRF `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="e7ea1-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="e7ea1-158">Záhlaví kontextu ověřování CBC + HMAC je tvořeno stejným způsobem bez ohledu na to, zda jsou implementace algoritmů poskytovány rozhraním Windows CNG nebo spravovanými typy SymmetricAlgorithm a KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="e7ea1-159">To umožňuje aplikacím běžícím na různých operačních systémech spolehlivě sestavovat stejné záhlaví kontextu, i když se implementace algoritmů liší od operačních systémech.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="e7ea1-160">(V praxi nemusí být KeyedHashAlgorithm správným HMAC.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="e7ea1-161">Může to být libovolný typ algoritmu hash s klíčem.)</span><span class="sxs-lookup"><span data-stu-id="e7ea1-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="e7ea1-162">Příklad: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="e7ea1-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="e7ea1-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , kde `| K_E | = 192 bits` a `| K_H | = 160 bits` podle určených algoritmů.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="e7ea1-164">To vede k `K_E = A219..E2BB` a `K_H = DC4A..B464` v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="e7ea1-165">Dále COMPUTE `Enc_CBC (K_E, IV, "")` pro algoritmus 3DES-192-CBC `IV = 0*` a výše uvedené `K_E` .</span><span class="sxs-lookup"><span data-stu-id="e7ea1-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="e7ea1-166">Dále COMPUTE `MAC(K_H, "")` pro HMACSHA1 `K_H` , které jsou uvedené výše.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="e7ea1-167">Tím dojde k vytvoření úplného kontextu, který je kryptografickým otiskem ověřeného páru šifrovacího algoritmu (3DES-192-CBC šifrování + HMACSHA1 ověření), jak je uvedeno níže:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="e7ea1-168">Komponenty se rozdělí takto:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-168">The components break down as follows:</span></span>

* <span data-ttu-id="e7ea1-169">Značka `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="e7ea1-170">délka šifrovacího klíče bloku `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="e7ea1-171">velikost bloku šifry bloku `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="e7ea1-172">Délka klíče HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="e7ea1-173">velikost Digest HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="e7ea1-174">zablokuje výstup PRP (Block Cipher) `(AB B1 - E1 0E)` a</span><span class="sxs-lookup"><span data-stu-id="e7ea1-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="e7ea1-175">výstup HMAC PRF `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="e7ea1-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="e7ea1-176">Galois/režim čítače šifrování + ověřování</span><span class="sxs-lookup"><span data-stu-id="e7ea1-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="e7ea1-177">Záhlaví kontextu se skládá z následujících součástí:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="e7ea1-178">[16 bitů] Hodnota 00 01, což je označení "GCM Encryption + Authentication".</span><span class="sxs-lookup"><span data-stu-id="e7ea1-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="e7ea1-179">[32 bitů] Délka klíče (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="e7ea1-180">[32 bitů] Velikost hodnoty nonce (v bajtech, Big Endian), která se používá během ověřených šifrovacích operací.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="e7ea1-181">(Pro náš systém je to opraveno při velikosti nonce = 96 bitů.)</span><span class="sxs-lookup"><span data-stu-id="e7ea1-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="e7ea1-182">[32 bitů] Velikost bloku (v bajtech, Big Endian) algoritmu symetrického bloku šifrování.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="e7ea1-183">(Pro GCM to je opraveno v bloku Size = 128 bitů.)</span><span class="sxs-lookup"><span data-stu-id="e7ea1-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="e7ea1-184">[32 bitů] Velikost značky ověřování (v bajtech, Big Endian) vytvořeného pomocí ověřené šifrovací funkce.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="e7ea1-185">(Pro náš systém je to pevně nastavená na velikost značky = 128 bitů.)</span><span class="sxs-lookup"><span data-stu-id="e7ea1-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="e7ea1-186">[128 bitů] Značka `Enc_GCM (K_E, nonce, "")` , která je výstupem algoritmu symetrického bloku šifrování, který předali prázdný vstup řetězce a kde hodnota nonce je 96 celý vektor s hodnotou 0.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="e7ea1-187">`K_E` je odvozený pomocí stejného mechanismu jako ve scénáři ověřování CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="e7ea1-188">Vzhledem k tomu, že `K_H` zde není v hraní, máme v podstatě `| K_H | = 0` a algoritmus se sbalí do níže uvedeného formuláře.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="e7ea1-189">Příklad: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="e7ea1-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="e7ea1-190">First – let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , kde `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="e7ea1-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="e7ea1-191">Dále vypočítají značku ověřování `Enc_GCM (K_E, nonce, "")` pro AES-256-GCM `nonce = 096` a `K_E` výše uvedenou.</span><span class="sxs-lookup"><span data-stu-id="e7ea1-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="e7ea1-192">Tím se vytvoří úplné záhlaví kontextu níže:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="e7ea1-193">Komponenty se rozdělí takto:</span><span class="sxs-lookup"><span data-stu-id="e7ea1-193">The components break down as follows:</span></span>

* <span data-ttu-id="e7ea1-194">Značka `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="e7ea1-195">délka šifrovacího klíče bloku `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="e7ea1-196">velikost hodnoty nonce `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="e7ea1-197">velikost bloku šifry bloku `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="e7ea1-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="e7ea1-198">velikost značky ověřování `(00 00 00 10)` a</span><span class="sxs-lookup"><span data-stu-id="e7ea1-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="e7ea1-199">Značka ověřování, ze které se spouští blokové šifry `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="e7ea1-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
